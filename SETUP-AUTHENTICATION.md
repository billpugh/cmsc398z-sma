# Authentication Setup Guide

This guide explains how to set up authentication for your social media application in both Phase 1 (Flask) and Phase 2 (FastAPI).

## Table of Contents

1. [Understanding SECRET_KEY](#understanding-secret_key)
2. [Public vs Private App: An Important Decision](#public-vs-private-app-an-important-decision)
3. [Authentication Options Overview](#authentication-options-overview)
4. [Option 1: Development Mode (No Authentication)](#option-1-development-mode-no-authentication)
5. [Option 2: Google OAuth (Recommended)](#option-2-google-oauth-recommended)
6. [Option 3: Email Magic Links (Advanced)](#option-3-email-magic-links-advanced)
7. [Migrating from Development Mode to Real Authentication](#migrating-from-development-mode-to-real-authentication)
8. [Phase 2 Considerations (JWT Tokens)](#phase-2-considerations-jwt-tokens)

---

## Understanding SECRET_KEY

### What is SECRET_KEY?

The `SECRET_KEY` is a cryptographic secret used to sign and verify security-sensitive data:

- **Phase 1 (Flask)**: Signs session cookies to prevent tampering
  - Without it: Anyone could forge a session cookie and impersonate any user
  - With it: Cookies are cryptographically signed and verified on each request

- **Phase 2 (FastAPI)**: Signs JWT authentication tokens
  - Without it: Anyone could create valid tokens for any user
  - With it: Only your server can create valid tokens

### Security Implications

⚠️ **If your SECRET_KEY is compromised:**
- Attackers can impersonate any user
- All existing sessions/tokens must be invalidated
- Users must log in again

### Generating a Secure SECRET_KEY

**NEVER use a predictable value** like "change-this-to-a-random-secret-key" or "mysecretkey123".

**Generate a cryptographically secure random key:**

```bash
# Generate a 64-character hex string (32 bytes)
uv run python -c "import secrets; print(secrets.token_hex(32))"
```

Example output:
```
a3f8b2c1d5e9f7a0b4c8d2e6f0a4b8c2d6e0f4a8b2c6d0e4f8a2b6c0d4e8f2a6
```

**Add this to your `.env` file** (NEVER commit `.env` to git):

```env
SECRET_KEY=a3f8b2c1d5e9f7a0b4c8d2e6f0a4b8c2d6e0f4a8b2c6d0e4f8a2b6c0d4e8f2a6
```

### Important Notes

- **Persistence**: Use the same SECRET_KEY across server restarts. If it changes, all users are logged out.
- **Per-Environment**: Use different SECRET_KEY values for development vs. production.
- **Storage**: Keep in `.env` file (which should be in `.gitignore`). For production, use environment variables or secret management services.

---

## Public vs Private App: An Important Decision

Before choosing your authentication approach, you need to make a key architectural decision: **Who should be able to view content in your app?**

### Option A: Fully Private App (Authentication Required for Everything)

**All routes require login:**
- `/` (feed) - Login required
- `/profile/<username>` - Login required
- `/posts` - Login required
- Only invited users can create accounts
- Only logged-in users can view anything

**Pros:**
- ✅ Maximum privacy and control
- ✅ Simple permission model (if logged in, can access)
- ✅ No unexpected visitors or content scraping
- ✅ Invite-only creates exclusive community

**Cons:**
- ❌ Can't easily share your work for portfolio
- ❌ Potential recruiters/friends can't view without account
- ❌ Less useful as a demonstration piece

**Best for:** Private communities, internal tools, apps with sensitive content

---

### Option B: Public Reading, Private Writing (Recommended for Portfolio)

**Public routes (no login required):**
- `/` or `/feed` - View recent posts from all users
- `/profile/<username>` - View any user's profile and their posts
- Essentially: **Anyone with the link can browse and read**

**Protected routes (login required):**
- `POST /posts` - Create new posts
- `/follow/<username>` - Follow/unfollow users
- `/settings` - Edit your profile
- Essentially: **Must be logged in to create/modify content**

**Invite system:**
- Anyone can view (read-only)
- Only invited users can create accounts and post

**Pros:**
- ✅ Perfect for portfolio - share link with recruiters/friends
- ✅ Shows off your work publicly
- ✅ Still controls who can post (invite-only prevents spam)
- ✅ Read-only access is safe (no unwanted modifications)

**Cons:**
- ❌ Content is publicly visible (consider what you post!)
- ❌ Slightly more complex permission logic

**Best for:** Portfolio projects, demonstration apps, content you want to showcase

---

### Implementation Impact

Your choice affects which routes need the `@login_required` decorator (Flask) or `Depends(get_current_user)` dependency (FastAPI):

#### Fully Private App (Option A)

**Flask Example:**
```python
@app.route('/')
@login_required  # ← Must be logged in to see feed
def index():
    posts = get_feed_posts(current_user.id)
    return render_template('feed.html', posts=posts)

@app.route('/profile/<username>')
@login_required  # ← Must be logged in to view profiles
def profile(username):
    user = User.query.filter_by(username=username).first_or_404()
    return render_template('profile.html', user=user)

@app.route('/posts', methods=['POST'])
@login_required  # ← Must be logged in to create posts
def create_post():
    # ... create post logic
    pass
```

**All routes protected - simple and consistent.**

#### Public Reading, Private Writing (Option B)

**Flask Example:**
```python
@app.route('/')
# NO @login_required - anyone can view feed
def index():
    # Show all recent posts (not personalized)
    posts = Post.query.order_by(Post.created_at.desc()).limit(50).all()
    return render_template('feed.html', posts=posts)

@app.route('/profile/<username>')
# NO @login_required - anyone can view profiles
def profile(username):
    user = User.query.filter_by(username=username).first_or_404()
    posts = Post.query.filter_by(user_id=user.id).all()
    return render_template('profile.html', user=user, posts=posts)

@app.route('/posts', methods=['POST'])
@login_required  # ← MUST be logged in to create posts
def create_post():
    # Only authenticated users can post
    post = Post(content=request.form['content'], user_id=current_user.id)
    db.session.add(post)
    db.session.commit()
    return redirect(url_for('index'))

@app.route('/follow/<username>', methods=['POST'])
@login_required  # ← MUST be logged in to follow
def follow(username):
    # Only authenticated users can follow
    # ... follow logic
    pass
```

**Read operations public, write operations protected.**

---

### UI Considerations

#### For Public Reading Apps:

Your templates should show different options based on login state:

```html
<!-- In your base template or navbar -->
{% if current_user.is_authenticated %}
    <!-- Logged in user sees: -->
    <a href="/create-post">Create Post</a>
    <a href="/profile/{{ current_user.username }}">My Profile</a>
    <a href="/logout">Logout</a>
{% else %}
    <!-- Anonymous visitor sees: -->
    <a href="/login">Login to Post</a>
    <p>Viewing as guest - <a href="/login">login</a> to create content</p>
{% endif %}
```

**On post cards:**
```html
<div class="post">
    <p>{{ post.content }}</p>
    <p>by {{ post.user.username }}</p>

    {% if current_user.is_authenticated %}
        <!-- Show like/comment buttons to logged-in users -->
        <button>Like</button>
        <button>Comment</button>
    {% else %}
        <!-- Encourage login for interaction -->
        <p><a href="/login">Login</a> to like and comment</p>
    {% endif %}
</div>
```

---

### Recommendation

**For this class project:**

We recommend **Option B (Public Reading, Private Writing)** because:

1. **Portfolio value**: You can include this link on your resume and GitHub
2. **Demo-friendly**: Show to friends, family, potential employers without creating accounts for them
3. **Learning opportunity**: Practice implementing granular access control
4. **Still secure**: Invite-only posting prevents spam and unwanted content
5. **Realistic**: Many production apps work this way (Twitter, Medium, DEV.to all allow public reading)

**Exception:** Choose Option A (Fully Private) if:
- You're building a truly private community app
- You want to practice session-based access control exclusively
- You prefer simpler, uniform authentication requirements

---

### Making the Choice

You can decide this **after** implementing authentication:

1. **Start with fully private** (all routes protected) - simpler to begin
2. **Test your app** with authentication working
3. **Then decide** if you want public reading
4. **Remove `@login_required`** from read-only routes if desired
5. **Update templates** to show appropriate UI based on login state

**You can always change your mind later - it's just a matter of which routes have `@login_required` decorators!**

---

## Authentication Options Overview

Choose based on your goals and timeline:

| Option | Time to Setup | Complexity | Use Case | Migration Path |
|--------|---------------|------------|----------|----------------|
| **Development Mode** | 5 minutes | Very Low | Learning features first, demo only | Easy to add auth later |
| **Google OAuth** | 15-20 minutes | Medium | Real app, simplest production auth | Production-ready |
| **Email Magic Links** | 30-45 minutes | High | Learning email workflows, no Google dependency | Production-ready with email service |

### Recommendation by Scenario

- **First time building a web app?** → Start with Development Mode, add Google OAuth later
- **Want to deploy publicly?** → Use Google OAuth
- **Want to learn email authentication?** → Use Email Magic Links (but expect more complexity)
- **Short on time?** → Development Mode or Google OAuth

---

## Option 1: Development Mode (No Authentication)

### When to Use This

✅ **Good for:**
- Learning the core features first (posts, following, feed)
- Rapid prototyping on your local machine
- Demo purposes (run locally, show to friends, then shut down)
- Understanding the app architecture before adding auth complexity

⚠️ **NOT suitable for:**
- Public deployment (anyone can access)
- Leaving running on PythonAnywhere or other hosting
- Any production use case

### Setup Instructions

#### Phase 1 (Flask)

1. **Install minimal dependencies** (`pyproject.toml`):

```toml
[project]
dependencies = [
    "flask>=3.0.0",
    "flask-sqlalchemy>=3.1.0",
    "python-dotenv>=1.0.0",
]
```

2. **Configure `.env` file**:

```env
FLASK_APP=app.py
FLASK_ENV=development
SECRET_KEY=dev-only-not-secure-random-key-123
DATABASE_URL=sqlite:///instance/app.db
```

3. **Create a simple "dev login" endpoint** (`app.py`):

```python
from flask import Flask, session, request, redirect, url_for, render_template
from flask_sqlalchemy import SQLAlchemy
from dotenv import load_dotenv
import os

load_dotenv()

app = Flask(__name__)
app.config['SECRET_KEY'] = os.getenv('SECRET_KEY')
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('DATABASE_URL')

db = SQLAlchemy(app)

# Development-only: Simple user switching
@app.route('/dev-login', methods=['GET', 'POST'])
def dev_login():
    """Development-only login - DO NOT USE IN PRODUCTION"""
    if request.method == 'POST':
        username = request.form.get('username')
        # In dev mode, just trust the username
        user = User.query.filter_by(username=username).first()
        if not user:
            # Create user on the fly in dev mode
            user = User(username=username, email=f"{username}@example.com")
            db.session.add(user)
            db.session.commit()

        # Store user_id in session
        session['user_id'] = user.id
        return redirect(url_for('index'))

    # Show list of existing users + option to create new
    users = User.query.all()
    return render_template('dev_login.html', users=users)

@app.route('/logout')
def logout():
    session.pop('user_id', None)
    return redirect(url_for('dev_login'))

# Helper to get current user
def get_current_user():
    user_id = session.get('user_id')
    if user_id:
        return User.query.get(user_id)
    return None

# Protect routes by checking session
@app.route('/')
def index():
    user = get_current_user()
    if not user:
        return redirect(url_for('dev_login'))
    # ... rest of your feed logic
    return render_template('index.html', user=user)
```

4. **Create a simple login template** (`templates/dev_login.html`):

```html
<!DOCTYPE html>
<html>
<head>
    <title>Dev Login</title>
</head>
<body>
    <h1>Development Login</h1>
    <p><strong>WARNING: This is development mode only!</strong></p>

    <h2>Login as Existing User</h2>
    <ul>
    {% for user in users %}
        <li>
            <form method="POST" style="display: inline;">
                <input type="hidden" name="username" value="{{ user.username }}">
                <button type="submit">Login as {{ user.username }}</button>
            </form>
        </li>
    {% endfor %}
    </ul>

    <h2>Or Create New User</h2>
    <form method="POST">
        <input type="text" name="username" placeholder="Username" required>
        <button type="submit">Create & Login</button>
    </form>
</body>
</html>
```

#### Phase 2 (FastAPI)

For Phase 2, create a simplified token system:

```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import HTTPBearer, HTTPAuthCredentials

app = FastAPI()
security = HTTPBearer()

# Development-only: Simple token = username
@app.post("/dev-login")
def dev_login(username: str):
    """Development-only: Returns username as token"""
    # In production, this would verify credentials and return JWT
    # For dev, we just return the username as the "token"
    return {"access_token": username, "token_type": "bearer"}

# Dependency to get current user from "token"
def get_current_user_dev(credentials: HTTPAuthCredentials = Depends(security)):
    # In dev mode, token is just the username
    username = credentials.credentials
    # Optionally verify user exists in DB
    return {"username": username}

# Protected endpoints use the dependency
@app.get("/api/posts")
def get_posts(current_user = Depends(get_current_user_dev)):
    # current_user is available here
    return {"posts": []}
```

### Important Notes for Development Mode

- 🚨 **Add prominent warnings in your UI** that this is development mode
- 🚨 **Never deploy this publicly** - no password required = anyone can access
- ✅ **Easy migration path**: Replace `dev_login()` with real OAuth/email auth later
- ✅ **Database schema stays the same**: Just swap out authentication logic

---

## Option 2: Google OAuth (Recommended)

### Why Google OAuth?

✅ **Advantages:**
- No password management (Google handles security)
- Users already have Google accounts
- Fast setup (15-20 minutes)
- Production-ready
- No email infrastructure needed

⚠️ **Considerations:**
- Requires Google account (most students have one)
- Dependency on Google's service
- Need to set up Google Cloud project

### Setup Instructions

#### Step 1: Generate SECRET_KEY

```bash
uv run python -c "import secrets; print(secrets.token_hex(32))"
```

Add to `.env`:
```env
SECRET_KEY=<your-generated-key-here>
```

#### Step 2: Create Google Cloud Project

1. **Go to [Google Cloud Console](https://console.cloud.google.com/)**

2. **Create a new project:**
   - Click "Select a Project" (top left)
   - Click "New Project"
   - Name it: "Social Media App" (or whatever you prefer)
   - Click "Create"

3. **Enable Google+ API:**
   - In the search bar, type "Google+ API"
   - Click "Google+ API"
   - Click "Enable"
   - (This allows your app to get user profile information)

#### Step 3: Configure OAuth Consent Screen

1. **Navigate to OAuth consent screen:**
   - Left sidebar: "APIs & Services" → "OAuth consent screen"

2. **Choose User Type:**
   - Select "External" (allows anyone with a Google account)
   - Click "Create"

3. **Fill out App Information:**
   - **App name**: "Social Media App" (or your app name)
   - **User support email**: Your email
   - **Developer contact information**: Your email
   - Click "Save and Continue"

4. **Scopes (Step 2):**
   - Click "Add or Remove Scopes"
   - Select these scopes:
     - `userinfo.email`
     - `userinfo.profile`
     - `openid`
   - Click "Update" → "Save and Continue"

5. **Test users (Step 3):**
   - Add your email address (and any others who need to test)
   - Click "Save and Continue"

6. **Summary:**
   - Review and click "Back to Dashboard"

#### Step 4: Create OAuth Credentials

1. **Navigate to Credentials:**
   - Left sidebar: "APIs & Services" → "Credentials"

2. **Create OAuth Client ID:**
   - Click "+ Create Credentials" (top)
   - Select "OAuth client ID"

3. **Configure OAuth Client:**
   - **Application type**: "Web application"
   - **Name**: "Social Media Web Client"

   - **Authorized JavaScript origins** (for Phase 1):
     ```
     http://localhost:5000
     ```
     (Add production URL later: `https://yourusername.pythonanywhere.com`)

   - **Authorized redirect URIs** (for Phase 1):
     ```
     http://localhost:5000/auth/callback
     ```
     (Add production URL later: `https://yourusername.pythonanywhere.com/auth/callback`)

   - Click "Create"

4. **Copy Your Credentials:**
   - You'll see a popup with:
     - **Client ID**: Looks like `123456789-abc123.apps.googleusercontent.com`
     - **Client Secret**: Looks like `GOCSPX-abc123xyz789`
   - Copy both (you can always view them again later)

#### Step 5: Configure Your Application

**Add to `.env` file:**

```env
FLASK_APP=app.py
FLASK_ENV=development
SECRET_KEY=<your-generated-secret-key>
DATABASE_URL=sqlite:///instance/app.db

# Google OAuth
GOOGLE_CLIENT_ID=123456789-abc123.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-abc123xyz789
```

**Update `pyproject.toml`:**

```toml
[project]
dependencies = [
    "flask>=3.0.0",
    "flask-sqlalchemy>=3.1.0",
    "flask-login>=0.6.3",
    "authlib>=1.3.0",
    "python-dotenv>=1.0.0",
]
```

#### Step 6: Implement OAuth in Flask

Create `auth.py`:

```python
from authlib.integrations.flask_client import OAuth
from flask import url_for, redirect, session
from flask_login import login_user, logout_user, current_user
import os

oauth = OAuth()

def init_oauth(app):
    oauth.init_app(app)

    oauth.register(
        name='google',
        client_id=os.getenv('GOOGLE_CLIENT_ID'),
        client_secret=os.getenv('GOOGLE_CLIENT_SECRET'),
        server_metadata_url='https://accounts.google.com/.well-known/openid-configuration',
        client_kwargs={'scope': 'openid email profile'}
    )

def create_auth_routes(app, db, User):
    @app.route('/login')
    def login():
        redirect_uri = url_for('auth_callback', _external=True)
        return oauth.google.authorize_redirect(redirect_uri)

    @app.route('/auth/callback')
    def auth_callback():
        token = oauth.google.authorize_access_token()
        user_info = token.get('userinfo')

        if not user_info:
            return "Failed to get user info", 400

        email = user_info.get('email')
        name = user_info.get('name')

        # Check if user is invited (implement your invite logic)
        if not is_invited(email):
            return "Sorry, this app is invite-only", 403

        # Find or create user
        user = User.query.filter_by(email=email).first()
        if not user:
            user = User(
                email=email,
                username=email.split('@')[0],  # Generate from email
                display_name=name
            )
            db.session.add(user)
            db.session.commit()

        # Log in user with Flask-Login
        login_user(user)
        return redirect(url_for('index'))

    @app.route('/logout')
    def logout():
        logout_user()
        return redirect(url_for('login'))

def is_invited(email):
    """Check if email is in invite list"""
    # Option 1: Check invites.txt file
    if os.path.exists('invites.txt'):
        with open('invites.txt', 'r') as f:
            invited_emails = [line.strip() for line in f]
            return email in invited_emails

    # Option 2: Check database (if using Invites table)
    # from models import Invite
    # return Invite.query.filter_by(email=email, used=False).first() is not None

    return False
```

**Update `app.py`:**

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager, login_required
from auth import init_oauth, create_auth_routes
from dotenv import load_dotenv
import os

load_dotenv()

app = Flask(__name__)
app.config['SECRET_KEY'] = os.getenv('SECRET_KEY')
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('DATABASE_URL')

db = SQLAlchemy(app)

# Initialize Flask-Login
login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'

# Initialize OAuth
init_oauth(app)

# Import models
from models import User

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

# Create auth routes
create_auth_routes(app, db, User)

# Your protected routes
@app.route('/')
@login_required
def index():
    # ... your code here
    pass
```

**Update `models.py`:**

```python
from flask_login import UserMixin
from datetime import datetime

class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(120), unique=True, nullable=False)
    username = db.Column(db.String(80), unique=True, nullable=False)
    display_name = db.Column(db.String(120))
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    # UserMixin provides: is_authenticated, is_active, is_anonymous, get_id()
```

#### Step 7: Test Your OAuth Flow

1. **Start your Flask app:**
   ```bash
   flask run
   ```

2. **Visit `http://localhost:5000/login`**

3. **You should be redirected to Google's login page**

4. **After logging in, you'll be redirected back to your app**

5. **Check that `current_user` is set in your routes**

### Common Issues with Google OAuth

**"Redirect URI mismatch":**
- Make sure redirect URI in Google Console exactly matches your route
- Include `http://` or `https://`
- Port number must match (`:5000` for Flask default)

**"App not verified":**
- In development with test users, you'll see a warning screen
- Click "Advanced" → "Go to [App Name] (unsafe)" (it's safe, just not verified)
- For production, you can submit for verification (not needed for class project)

**"Access blocked: This app's request is invalid":**
- Check that scopes in OAuth consent screen match your code
- Verify `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` are correct

---

## Option 3: Email Magic Links (Advanced)

### Why Email Magic Links?

✅ **Advantages:**
- No dependency on Google or other OAuth providers
- No passwords to manage (passwordless authentication)
- Users don't need existing accounts with third parties
- Learning opportunity: sending emails, token generation, email infrastructure

⚠️ **Considerations:**
- Requires email sending infrastructure
- More setup complexity (30-45 minutes)
- Email deliverability challenges (spam filters)
- Need to handle token expiration and security

### Two Approaches

#### Approach A: Gmail App Passwords (Development Only)

**Use this for:** Quick local testing, learning the flow

**⚠️ DO NOT USE FOR PRODUCTION** - App passwords have broad access to your Gmail account.

##### Setup Gmail App Password

1. **Enable 2-Factor Authentication on your Google Account:**
   - Go to [Google Account Security](https://myaccount.google.com/security)
   - Click "2-Step Verification"
   - Follow prompts to enable 2FA (required for App Passwords)

2. **Generate an App Password:**
   - Go to [App Passwords](https://myaccount.google.com/apppasswords)
   - Select "Mail" and "Other (Custom name)"
   - Name it: "Social Media Dev App"
   - Click "Generate"
   - **Copy the 16-character password** (e.g., `abcd efgh ijkl mnop`)

3. **Add to `.env`:**
   ```env
   MAIL_SERVER=smtp.gmail.com
   MAIL_PORT=587
   MAIL_USE_TLS=True
   MAIL_USERNAME=your-email@gmail.com
   MAIL_PASSWORD=abcdefghijklmnop
   MAIL_DEFAULT_SENDER=your-email@gmail.com
   ```

**Security Notes:**
- ✅ App passwords are more secure than your account password
- ✅ Can be revoked without changing your main password
- ✅ Scoped to specific services (SMTP only)
- ⚠️ Still gives access to send emails from your account
- ⚠️ Should not be used for production apps

#### Approach B: Transactional Email Service (Production Recommended)

**Use this for:** Production deployment, reliable delivery

##### Option 1: SendGrid (Recommended)

**Free tier:** 100 emails/day (plenty for class project)

**Setup:**

1. **Sign up at [SendGrid](https://signup.sendgrid.com/)**

2. **Verify your email address**

3. **Create an API Key:**
   - Go to Settings → API Keys
   - Click "Create API Key"
   - Name: "Social Media App"
   - Permissions: "Restricted Access"
     - Enable "Mail Send" → "Full Access"
   - Click "Create & View"
   - **Copy the API key** (starts with `SG.`)

4. **Verify Sender Identity:**
   - Go to Settings → Sender Authentication
   - Choose "Single Sender Verification" (easiest for class project)
   - Add your email address
   - Verify it via email

5. **Update `.env`:**
   ```env
   SENDGRID_API_KEY=SG.your-api-key-here
   MAIL_DEFAULT_SENDER=your-verified-email@gmail.com
   ```

6. **Update `pyproject.toml`:**
   ```toml
   dependencies = [
       # ... other dependencies
       "sendgrid>=6.11.0",
   ]
   ```

##### Option 2: Mailgun

**Free tier:** 100 emails/day (requires credit card)

**Setup:**

1. **Sign up at [Mailgun](https://signup.mailgun.com/)**
2. **Verify email and add credit card** (not charged for free tier)
3. **Get credentials:**
   - Go to Sending → Domain Settings
   - Copy SMTP credentials
4. **Add to `.env`:**
   ```env
   MAIL_SERVER=smtp.mailgun.org
   MAIL_PORT=587
   MAIL_USE_TLS=True
   MAIL_USERNAME=postmaster@your-sandbox-domain.mailgun.org
   MAIL_PASSWORD=your-mailgun-password
   ```

### Implementation

#### Step 1: Install Dependencies

```toml
[project]
dependencies = [
    "flask>=3.0.0",
    "flask-sqlalchemy>=3.1.0",
    "flask-mail>=0.9.1",
    "itsdangerous>=2.1.2",
    "python-dotenv>=1.0.0",
]

[project.optional-dependencies]
sendgrid = ["sendgrid>=6.11.0"]
```

Install:
```bash
# For Gmail App Password approach:
uv sync

# For SendGrid approach (with optional dependencies):
uv sync --extra sendgrid
```

#### Step 2: Configure Flask-Mail

Create `email_auth.py`:

```python
from flask_mail import Mail, Message
from itsdangerous import URLSafeTimedSerializer
from flask import url_for, render_template_string
import os

mail = Mail()

def init_mail(app):
    app.config['MAIL_SERVER'] = os.getenv('MAIL_SERVER')
    app.config['MAIL_PORT'] = int(os.getenv('MAIL_PORT', 587))
    app.config['MAIL_USE_TLS'] = os.getenv('MAIL_USE_TLS', 'True') == 'True'
    app.config['MAIL_USERNAME'] = os.getenv('MAIL_USERNAME')
    app.config['MAIL_PASSWORD'] = os.getenv('MAIL_PASSWORD')
    app.config['MAIL_DEFAULT_SENDER'] = os.getenv('MAIL_DEFAULT_SENDER')

    mail.init_app(app)

def generate_magic_link_token(email, secret_key):
    """Generate a secure token for email magic link"""
    serializer = URLSafeTimedSerializer(secret_key)
    return serializer.dumps(email, salt='magic-link')

def verify_magic_link_token(token, secret_key, max_age=900):
    """Verify token (default max_age=900 seconds = 15 minutes)"""
    serializer = URLSafeTimedSerializer(secret_key)
    try:
        email = serializer.loads(token, salt='magic-link', max_age=max_age)
        return email
    except:
        return None

def send_magic_link(email, app):
    """Send magic link email"""
    token = generate_magic_link_token(email, app.config['SECRET_KEY'])
    magic_link = url_for('magic_link_login', token=token, _external=True)

    msg = Message(
        subject="Your login link",
        recipients=[email],
        body=f"Click here to log in: {magic_link}\n\nThis link expires in 15 minutes."
    )

    mail.send(msg)
```

#### Step 3: Create Auth Routes

Add to `app.py`:

```python
from email_auth import init_mail, send_magic_link, verify_magic_link_token
from flask import request, redirect, url_for, render_template, flash

# Initialize mail
init_mail(app)

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        email = request.form.get('email')

        # Check if invited
        if not is_invited(email):
            flash('Sorry, this app is invite-only')
            return render_template('login.html')

        # Send magic link
        try:
            send_magic_link(email, app)
            flash(f'Login link sent to {email}. Check your email!')
        except Exception as e:
            flash(f'Failed to send email: {str(e)}')

        return render_template('login.html')

    return render_template('login.html')

@app.route('/magic-link/<token>')
def magic_link_login(token):
    email = verify_magic_link_token(token, app.config['SECRET_KEY'])

    if not email:
        flash('Invalid or expired login link')
        return redirect(url_for('login'))

    # Find or create user
    user = User.query.filter_by(email=email).first()
    if not user:
        user = User(
            email=email,
            username=email.split('@')[0]
        )
        db.session.add(user)
        db.session.commit()

    # Log in user
    session['user_id'] = user.id
    return redirect(url_for('index'))
```

#### Step 4: Create Login Template

`templates/login.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <h1>Login</h1>

    {% with messages = get_flashed_messages() %}
        {% if messages %}
            {% for message in messages %}
                <p>{{ message }}</p>
            {% endfor %}
        {% endif %}
    {% endwith %}

    <form method="POST">
        <input type="email" name="email" placeholder="Enter your email" required>
        <button type="submit">Send Login Link</button>
    </form>
</body>
</html>
```

#### Step 5: Test Email Flow

1. **Start your app:**
   ```bash
   flask run
   ```

2. **Go to `/login`**

3. **Enter your email**

4. **Check your inbox** (and spam folder!)

5. **Click the magic link**

6. **You should be logged in**

### Troubleshooting Email Magic Links

**Emails not arriving:**
- Check spam folder
- Verify sender email is verified (SendGrid/Mailgun)
- Check SMTP credentials are correct
- Look at Flask console for error messages

**"Token expired":**
- Default expiration is 15 minutes
- Generate a new link from `/login`

**SMTP authentication failed:**
- For Gmail: Make sure you're using App Password, not account password
- For Gmail: Verify 2FA is enabled
- For SendGrid: Verify API key has Mail Send permissions

---

## Migrating from Development Mode to Real Authentication

If you started with Development Mode, here's how to upgrade:

### Changes Needed

1. **Database Schema**: ✅ No changes needed
   - Your User model stays the same
   - Just remove any "dev mode" fields if you added them

2. **Session Management**: 🔄 Add Flask-Login

```python
# Replace this (dev mode):
def get_current_user():
    user_id = session.get('user_id')
    return User.query.get(user_id) if user_id else None

# With this (Flask-Login):
from flask_login import LoginManager, current_user, login_required

login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

# Now use @login_required decorator and current_user
```

3. **Auth Routes**: 🔄 Replace dev_login

```python
# Remove:
@app.route('/dev-login')
def dev_login():
    # ... dev code

# Add:
# (Google OAuth routes from Option 2)
# OR
# (Email magic link routes from Option 3)
```

4. **Templates**: 🔄 Update login page

```html
<!-- Remove dev login UI -->
<!-- Add real auth UI (Google button or email form) -->
```

5. **Environment Variables**: 🔄 Add auth credentials

```env
# Add (for Google OAuth):
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...

# OR (for Email Magic Links):
MAIL_USERNAME=...
MAIL_PASSWORD=...
```

### Migration Checklist

- [ ] Install auth dependencies (`authlib` or `flask-mail`)
- [ ] Generate proper SECRET_KEY
- [ ] Set up auth provider (Google Console or email service)
- [ ] Add auth credentials to `.env`
- [ ] Replace `dev_login()` with real auth routes
- [ ] Add Flask-Login integration
- [ ] Replace `get_current_user()` with `current_user`
- [ ] Update templates to use real login UI
- [ ] Test login/logout flow
- [ ] Remove dev login code
- [ ] Update `.gitignore` to exclude `.env`

---

## Phase 2 Considerations (JWT Tokens)

In Phase 2 (REST API with FastAPI), authentication works differently:

### Key Differences from Phase 1

| Aspect | Phase 1 (Flask) | Phase 2 (FastAPI) |
|--------|-----------------|-------------------|
| **Authentication** | Session cookies | JWT tokens |
| **State** | Stateful (server stores session) | Stateless (token contains all info) |
| **Storage** | Server-side session | Client-side (localStorage) |
| **Validation** | Check session on server | Verify token signature |

### JWT Token Flow

1. **User logs in** (via Google OAuth or email link)
2. **Server generates JWT token** with user info
3. **Client stores token** in localStorage
4. **Every API request includes token** in Authorization header:
   ```
   Authorization: Bearer <jwt-token>
   ```
5. **Server validates token** on each request

### Implementation Hints for Phase 2

**Backend (FastAPI):**

```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import HTTPBearer, HTTPAuthCredentials
from jose import jwt, JWTError
from datetime import datetime, timedelta
import os

SECRET_KEY = os.getenv('SECRET_KEY')
ALGORITHM = "HS256"
security = HTTPBearer()

def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=30)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def get_current_user(credentials: HTTPAuthCredentials = Depends(security)):
    token = credentials.credentials
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        user_id = payload.get("sub")
        if user_id is None:
            raise HTTPException(status_code=401)
        return user_id
    except JWTError:
        raise HTTPException(status_code=401)

@app.post("/auth/login")
def login(email: str):
    # Verify user (Google OAuth or email verification)
    # ...
    token = create_access_token({"sub": str(user.id)})
    return {"access_token": token, "token_type": "bearer"}

@app.get("/api/posts")
def get_posts(current_user_id = Depends(get_current_user)):
    # current_user_id is available here
    pass
```

**Frontend (React/Vue):**

```javascript
// After login, store token
localStorage.setItem('token', response.access_token);

// Include token in API requests
fetch('http://localhost:8000/api/posts', {
  headers: {
    'Authorization': `Bearer ${localStorage.getItem('token')}`
  }
})
```

### Phase 2 Development Mode

For Phase 2 development mode, you can skip JWT complexity:

```python
# Simple dev mode: Use username as "token"
@app.post("/dev-login")
def dev_login(username: str):
    return {"access_token": username, "token_type": "bearer"}

def get_current_user_dev(credentials: HTTPAuthCredentials = Depends(security)):
    # In dev mode, "token" is just the username
    return credentials.credentials
```

Then upgrade to real JWT when ready.

---

## Summary

### Recommended Learning Path

1. **Start with Development Mode**
   - Get posts, following, and feed working
   - Understand the app architecture
   - Deploy locally for demo

2. **Upgrade to Google OAuth**
   - Add real authentication
   - Deploy publicly to PythonAnywhere
   - 15-20 minutes additional work

3. **(Optional) Try Email Magic Links**
   - Learn email infrastructure
   - Understand passwordless auth
   - 30-45 minutes additional work

### Quick Reference

| Task | Command/Location |
|------|------------------|
| Generate SECRET_KEY | `uv run python -c "import secrets; print(secrets.token_hex(32))"` |
| Google Cloud Console | https://console.cloud.google.com/ |
| Gmail App Passwords | https://myaccount.google.com/apppasswords |
| SendGrid Signup | https://signup.sendgrid.com/ |
| Test Email Locally | Check spam folder! |

### Getting Help

If you're stuck:
1. Check error messages in Flask console
2. Verify `.env` file has all required variables
3. Confirm dependencies are installed
4. Check that redirect URIs match exactly (for OAuth)
5. Ask for help in office hours

---

**Next:** Return to TODO-phase1.md or TODO-phase2.md to continue building your app.
