# Phase 1 Flask App - Implementation TODO (REVISED)

**Goal**: Build a working social media prototype in ~2 hours, focusing on understanding web application architecture.

**Key Learning Objectives**:
1. Understand the three core components: **Objects** (models), **Actions** (routes), **Views** (templates)
2. Practice articulating features before implementing them
3. Experience iterative development: plan → implement → test → extend
4. Use AI tools effectively for rapid prototyping

---

## 🤖 For Claude/AI Assistants

This file guides you through a **structured conversation** with the student. Your role is to:

1. **Follow the conversational scripts exactly** - they're designed for pedagogy, not just speed
2. **Stop and wait for student responses** at marked points (🛑)
3. **Update DECISIONS-MADE-phase1.md** throughout to track choices
4. **Check understanding** before generating code
5. **Encourage articulation** - students should describe features in English before you code them

**Critical**: This is a teaching conversation, not just code generation. The goal is understanding, not just a working app.

---

## For Students

**How to use this file with Claude:**
1. Tell Claude: "Read TODO-phase1.md and guide me through Phase 1. Follow the conversational scripts."
2. Claude will ask you questions and wait for your responses
3. Take time to think about architecture - don't just say "yes" to everything
4. Ask for explanations when you don't understand
5. Test frequently!

**Expected time**: ~2 hours in class to complete base features + one additional feature

---

## Part 1: Quick Decisions (5 minutes)

### Task 1.1: App Name and Colors

🤖 **Claude: Generate 4-5 creative app name suggestions in different themes (Maryland-related, nature, tech, playful, etc.). Make these unique each time. Then say:**

> "Before we start coding, let's give your app some personality! This will only take a couple minutes.
>
> First, what would you like to name your social media app? Some ideas:
> - [Your generated suggestions organized by theme]
> - Or suggest your own!
>
> What name appeals to you?"

🛑 **STOP: Wait for student to choose name**

🤖 **Claude: Generate 5 distinct color schemes with creative names and specific hex colors. Vary the themes (e.g., school colors, nature-inspired, tech-modern, warm/cool palettes, gradients). Make these unique each time. Then say:**

> "Great! Now let's pick a color scheme. I'll generate CSS with your colors. Choose one:
>
> [Your 5 generated color schemes, each with:
>  - A creative name
>  - Primary color(s) with hex codes
>  - Accent color(s) with hex codes
>  - Brief description of the vibe]
>
> Which color scheme? (or describe your own)"

🛑 **STOP: Wait for color choice**

🤖 **Claude: Then update `phase1/DECISIONS-MADE-phase1.md`:**

```markdown
## Visual Design
**App Name**: [chosen name]
**Color Scheme**: [chosen scheme with reasoning]
**Date**: [today's date]
```

**Generate `shared/static/style.css`** with:
- CSS variables for chosen colors
- Basic responsive layout
- Simple component styles (buttons, cards, forms)
- Clean typography

**IMPORTANT - Navbar Button Visibility:**
When generating the CSS, ensure buttons inside `<nav>` have sufficient contrast with the navbar background:
- Common issue: navbar uses `background-color: var(--primary)` and buttons use `color: var(--primary)`, creating invisible text
- **Solution:** Add nav-specific button styles AFTER the general button styles to ensure proper contrast
- Example: If navbar background is dark, use light button text; if navbar background is light, use dark button text
- Make sure both normal and hover states maintain good contrast

The key principle: **navbar buttons must be clearly visible against the navbar background, whatever color scheme was chosen**.

Link this stylesheet in base template later.

- [ ] App name chosen
- [ ] Color scheme chosen
- [ ] Documented in DECISIONS-MADE
- [ ] style.css created

---

## Part 2: Environment Setup (10-15 minutes)

### Task 2.1: Install Dependencies

🤖 **Claude: For now, we're using simple dev-mode authentication (no real login). Navigate to the phase1 directory and sync dependencies:**

```bash
cd phase1
uv sync
```

**Note**: `uv` automatically manages the virtual environment - no need to activate it manually. Real authentication (Google OAuth, email magic links) can be added later as an optional feature.

- [ ] Dependencies installed
- [ ] No installation errors

---

### Task 2.2: Generate SECRET_KEY

🤖 **Claude: Before generating the secret, explain this to the student. Say:**

> "Before we can run your app, we need to generate a SECRET_KEY. Let me explain what this is and why it's important.
>
> **What is a SECRET_KEY?**
> - Your app needs to sign things like session cookies so that users can't tamper with them
> - Think of it like a signature that only your app knows how to create
> - If an attacker knew your SECRET_KEY, they could impersonate any user!
>
> **Why random?**
> - We generate a cryptographically random string (64 characters long)
> - Impossible for attackers to guess
> - Never use predictable values like "mysecretkey123"
>
> **Where does it go?**
> - Stored in a `.env` file (which is in .gitignore)
> - This file contains secrets and API keys that your app needs
> - **CRITICAL**: Never commit `.env` files to a public repository!
> - If secrets leak to GitHub, attackers can use them
>
> **This pattern is universal:**
> - Every web app has secrets (API keys, database passwords, etc.)
> - `.env` files keep them separate from code
> - Different secrets for development vs. production
>
> Do you have any questions about SECRET_KEY or environment variables before we generate one?"

🛑 **STOP: Wait for student questions. Answer them clearly.**

🤖 **Claude: After answering questions, say:**

> "Great! Now let's generate a SECRET_KEY for your app:"

```bash
uv run python -c "import secrets; print(secrets.token_hex(32))"
```

> "This generates 64 random hexadecimal characters. Copy this output - we'll paste it into your `.env` file in the next step."

- [ ] Explained SECRET_KEY concept
- [ ] Student understands why it must be secret
- [ ] Generated SECRET_KEY
- [ ] Copied to clipboard

---

### Task 2.3: Configure Environment

```bash
cp .env.example .env
```

Edit `.env`:
```env
FLASK_APP=app.py
FLASK_ENV=development
SECRET_KEY=<paste-your-generated-key-here>
```

**Note**: We're NOT setting DATABASE_URL here - we'll configure it with absolute paths in code.

- [ ] .env file created and configured
- [ ] SECRET_KEY is 64+ characters
- [ ] Verified .env is in .gitignore

---

### Task 2.4: Create Directory Structure

```bash
mkdir -p templates static/css static/uploads
touch static/uploads/.gitkeep
```

- [ ] Directories created

---

## Part 3: Feature Planning Conversation (10-15 minutes)

🤖 **Claude: This is a KEY pedagogical section. Follow the script carefully.**

### Step 3.1: Introduce the Framework

🤖 **Claude: Say exactly:**

> "Before we write any code, let's talk about how web applications are structured. Every feature in your app involves three core components:
>
> 1. **Objects (Models)**: The "things" your app stores in the database
>    - Example: A User has an id, email, username, display_name
>    - Each object is a Python class that maps to a database table
>
> 2. **Actions (Routes)**: The requests users can make to your server
>    - Example: POST /posts creates a new post
>    - Each action is a Python function decorated with @app.route()
>    - Actions often read/write objects to the database
>
> 3. **Views (Templates)**: The pages users see in their browser
>    - Example: index.html shows the feed of posts
>    - Each view is an HTML template that displays data
>
> A few key concepts:
> - Every object has a unique `id` (like User #42 or Post #1337)
> - Objects can reference other objects by id (Post #1337 was created by User #42)
> - When a user is logged in, the server knows their user id
> - Actions can take ids as parameters (e.g., GET /profile/42 shows User #42's profile)
>
> Do you have any questions about these three concepts before we continue?"

🛑 **STOP: Wait for questions. Answer clearly. Don't proceed until student indicates understanding.**

---

### Step 3.2: Present Minimal Features

🤖 **Claude: Say:**

> "Great! Let's define the minimal features we'll implement first. These will give you a working social media app:
>
> **Important note about authentication:**
> Before we list the features, I want to explain our approach to user login. Real authentication (like Google OAuth or email magic links) can be somewhat complicated to set up. So initially, we're going to use something called **"dev mode" authentication** - a simplified approach that's only suitable for development environments, not for real deployment.
>
> In dev mode:
> - You'll see a list of existing users and can "log in" as any of them
> - You can create new users with just an email and username (no password)
> - The app remembers who you are using sessions
> - **This is NOT secure** - anyone could pretend to be anyone else!
>
> Why start this way?
> - You can focus on learning how the app's features work first
> - You can test with multiple users easily
> - Adding real authentication later is one of the optional features you can choose
>
> This is a common development pattern: build features first with simplified auth, then upgrade to real authentication when you're ready to deploy.
>
> Does this approach make sense?

🛑 **STOP: Wait for confirmation or questions. Answer any concerns about the dev mode approach.**

🤖 **Claude: After student understands, say:**

> "Perfect! Now here are the minimal features:
>
> **Minimal Features**:
> 1. **Login screen** - simple dev mode (pick any user from a list, or create a new one)
> 2. **Login/logout actions** - set/clear session
> 3. **Create post** - write a text post (max 280 chars)
> 4. **Global feed** - see all recent posts from all users
> 5. **View profile** - see any user's profile with their posts
> 6. **Homepage** - shows the global feed
>
> We'll implement these first to get a working app. Then you'll choose an additional feature to add (which could be upgrading to real authentication if you want!).
>
> Would you like to see the list of possible additional features now, or are you ready to continue with these minimal features?"

🛑 **STOP: Wait for response**

**If student wants to see additional features:**

🤖 **Claude: Say:**

> "Here are some additional features you might add after the minimal set:
>
> **Follow System**:
> - Follow/unfollow other users
> - Show personalized feed (only posts from followed users)
> - Show follower/following counts
>
> **Like System**:
> - Like/unlike posts
> - Show like counts
> - Show which posts you've liked
>
> **Image Uploads**:
> - Attach images to posts
> - Display images in feed/profiles
>
> **Search**:
> - Search for users by username
> - Search posts by content
>
> **Real Authentication**:
> - Replace dev mode with Google OAuth
> - Or email magic links
>
> **Profile Editing**:
> - Edit display name
> - Add bio/avatar
>
> **Comments**:
> - Comment on posts
> - View comment threads
>
> Or suggest your own! You'll pick ONE of these after we implement the base features.
>
> Ready to continue with the minimal features?"

🛑 **STOP: Wait for confirmation**

---

### Step 3.3: Define Objects, Actions, Views (Together)

🤖 **Claude: Say:**

> "Now let's map out what objects, actions, and views we need for the minimal features. I'll describe each one - let me know if anything is unclear or if you'd suggest changes.
>
> **Objects (Models/Database Tables)**:
>
> 1. **User**
>    - id (unique identifier)
>    - email (unique)
>    - username (unique, for @mentions and URLs)
>    - display_name (shown in UI)
>    - created_at (when they joined)
>
> 2. **Post**
>    - id (unique identifier)
>    - user_id (who created it - references User.id)
>    - content (text, max 280 characters)
>    - created_at (timestamp)
>    - Relationship: post.user gives us the User object who created it
>
> **Actions (Routes/Endpoints)**:
>
> 1. **GET /dev-login** - Show dev login page (list of users + create new user form)
> 2. **POST /dev-login** - Handle login (set session['user_id'])
> 3. **POST /logout** - Clear session
> 4. **GET /** - Show homepage (global feed of all recent posts)
> 5. **POST /posts** - Create new post (requires login)
> 6. **GET /profile/<username>** - Show user profile with their posts
>
> **Views (Templates/Pages)**:
>
> 1. **base.html** - Layout template (navbar, flash messages, content block)
> 2. **dev_login.html** - Dev mode login page
> 3. **index.html** - Homepage with global feed + create post form
> 4. **profile.html** - User profile page
>
> Does this make sense? Any questions or suggestions for changes?"

🛑 **STOP: Wait for feedback. Discuss any confusion. Adjust if student has good suggestions.**

🤖 **Claude: After student is satisfied, update DECISIONS-MADE-phase1.md:**

```markdown
## Minimal Features Architecture

**Objects (Models)**:
- User: id, email, username, display_name, created_at
- Post: id, user_id, content, created_at

**Actions (Routes)**:
- GET /dev-login: Show login page
- POST /dev-login: Handle login
- POST /logout: Clear session
- GET /: Homepage with global feed
- POST /posts: Create new post
- GET /profile/<username>: Show user profile

**Views (Templates)**:
- base.html: Layout template
- dev_login.html: Login page
- index.html: Homepage/feed
- profile.html: User profile

**Date Designed**: [today]
```

- [ ] Objects described
- [ ] Actions described
- [ ] Views described
- [ ] Student understands the architecture
- [ ] Documented in DECISIONS-MADE

---

## Part 4: Implement Minimal Features (30-40 minutes)

### Task 4.1: Configure Database

🤖 **Claude: Create `phase1/database.py` with:**

**IMPORTANT**: We create a separate `database.py` file to avoid circular import issues. This is a common Flask architecture pattern.

**CRITICAL**: Configure Flask to serve `shared/static/style.css` from the shared directory. Do NOT copy it to `phase1/static/`.

```python
from flask import Flask, send_from_directory
from flask_sqlalchemy import SQLAlchemy
from pathlib import Path
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

app = Flask(__name__)
app.config['SECRET_KEY'] = os.getenv('SECRET_KEY')

# Path to shared static files (DO NOT COPY - serve from shared directory)
SHARED_STATIC = Path(__file__).parent.parent / "shared" / "static"

# Database configuration with absolute path
DB_DIR = Path(__file__).parent.parent / "shared" / "database"
DB_DIR.mkdir(parents=True, exist_ok=True)
DB_PATH = DB_DIR / "app.db"
app.config['SQLALCHEMY_DATABASE_URI'] = f'sqlite:///{DB_PATH}'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# Route to serve shared static files (style.css, etc.)
# This allows templates to use: <link rel="stylesheet" href="/shared/static/style.css">
@app.route('/shared/static/<path:filename>')
def shared_static(filename):
    return send_from_directory(SHARED_STATIC, filename)
```

🤖 **Claude: Create `phase1/app.py` with:**

```python
from database import app, db
import routes

if __name__ == '__main__':
    app.run(debug=True)
```

**Explain to student**:
- We use absolute paths for the database to avoid "unable to open database" errors.
- We split app configuration into `database.py` to avoid circular imports between `app.py` and `routes.py`.
- This pattern ensures routes are properly registered when the app starts.
- **Important:** The shared static route (`/shared/static/`) allows templates to load `style.css` from the shared directory. This keeps styles consistent between Phase 1 and Phase 2.

- [ ] database.py created
- [ ] app.py created
- [ ] Database path configured

---

### Task 4.2: Create Models

🤖 **Claude: Create `phase1/models.py`:**

```python
from database import db
from datetime import datetime

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(120), unique=True, nullable=False, index=True)
    username = db.Column(db.String(80), unique=True, nullable=False, index=True)
    display_name = db.Column(db.String(120), nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    # Relationship: user.posts gives all posts by this user
    posts = db.relationship('Post', backref='user', lazy='dynamic')

    def __repr__(self):
        return f'<User {self.username}>'

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False, index=True)
    content = db.Column(db.String(280), nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow, index=True)

    def __repr__(self):
        return f'<Post {self.id} by User {self.user_id}>'
```

🤖 **Claude: Say to student:**

> "I've created the models. Let's pause here - I'd like you to review the Post model.
>
> Open `models.py` and look at the Post class. Can you explain:
> 1. What does `user_id = db.Column(db.Integer, db.ForeignKey('user.id'))` mean?
> 2. What does the `index=True` do on created_at?
> 3. How would we get the User object who created a post?
>
> Take a minute to read the code and let me know your thoughts."

🛑 **STOP: Wait for student response. Answer their questions. Confirm understanding.**

- [ ] models.py created
- [ ] Student reviewed Post model
- [ ] Student understands foreign keys and relationships

---

### Task 4.3: Create Routes (Before Database Initialization!)

**IMPORTANT**: We create routes BEFORE initializing the database to avoid import errors.

🤖 **Claude: Create `phase1/routes.py` with all the routes defined in Step 3.3.**

**IMPORTANT**: Start the file with:
```python
from flask import render_template, request, redirect, url_for, flash, session
from pathlib import Path
from database import app, db  # Import from database, not app!
from models import User, Post
```

Include:
- Dev login page (shows list of existing users + form to create new user)
- Login/logout actions
- Homepage with global feed
- Create post action
- Profile page

**CRITICAL - Dev Mode Authentication**:
- **DO NOT** implement invite list checking in dev mode
- Anyone can create a test user with any email/username
- This is intentional - dev mode is for easy local testing
- Invite list checking will ONLY be added if student chooses "Real Authentication" as their additional feature

**Important**: Include flash messages for feedback, form validation, error handling.

After creating routes.py:

🤖 **Claude: Say to student:**

> "I've created all the routes. Let's review the 'create post' action together.
>
> Open `routes.py` and find the route decorated with `@app.route('/posts', methods=['POST'])`.
>
> Can you explain:
> 1. How does it know which user is creating the post?
> 2. What validation is happening?
> 3. What happens after the post is created (where does the user go)?
>
> Take a minute to read that function and let me know your thoughts."

🛑 **STOP: Wait for student to review. Answer questions. Confirm understanding.**

- [ ] routes.py created
- [ ] All actions implemented
- [ ] Student reviewed create post action
- [ ] Student understands session-based user tracking

---

### Task 4.4: Create Templates

🤖 **Claude: Create all templates defined in Step 3.3:**

1. `templates/base.html` - with navbar, flash messages, link to shared/static/style.css
2. `templates/dev_login.html` - list users + create user form
3. `templates/index.html` - feed + create post form (if logged in)
4. `templates/profile.html` - user info + their posts

Use the color scheme from Part 1. Keep it clean and simple.

**CRITICAL for base.html:** Link to stylesheet using:
```html
<link rel="stylesheet" href="/shared/static/style.css">
```

**DO NOT use:** `href="{{ url_for('static', filename='style.css') }}"` or copy style.css to `phase1/static/`. The stylesheet must remain in `shared/static/` so both phases can use it.

After creating templates:

🤖 **Claude: Say to student:**

> "Templates are created! Let's review the homepage template together.
>
> Open `templates/index.html`. Notice:
> 1. How does it check if someone is logged in?
> 2. Where does the create post form submit to?
> 3. How does it loop through posts to display them?
>
> Take a look and let me know if it makes sense."

🛑 **STOP: Wait for student to review. Answer questions.**

- [ ] All templates created
- [ ] Templates use shared style.css
- [ ] Student reviewed homepage template
- [ ] Student understands Jinja2 syntax basics

---

### Task 4.5: Initialize Database

**IMPORTANT**: Now that routes.py exists, we can safely initialize the database without import errors.

🤖 **Claude: Create a dev script to initialize the database. IMPORTANT: Must import ALL models and execute a query to force SQLite to write the file.**

**Create `dev_scripts/init_database.py` with:**
```python
"""Initialize the database with all tables."""
from database import app, db
from models import User, Post  # Import ALL models so SQLAlchemy knows about them

with app.app_context():
    db.create_all()
    db.session.execute(db.text("SELECT 1"))  # Force SQLite to write the file
    db.session.commit()
    print("✅ Database created successfully!")
    print(f"Tables created: {list(db.metadata.tables.keys())}")
```

**Then run it:**
```bash
uv run python dev_scripts/init_database.py
```

🤖 **Claude: Explain to student:**
- We create the database AFTER routes.py exists to avoid import errors
- `app.py` imports `routes`, so routes must exist before running any Python scripts
- `app.app_context()` - Flask needs context to know which database to use
- `db.create_all()` - Creates table schemas based on models
- `db.session.execute(db.text("SELECT 1"))` - Forces SQLite to actually write the file (SQLite is lazy and won't create the file until you write something)
- Must import ALL models before `create_all()` or tables won't be created
- Script is saved in `dev_scripts/` for easy re-running if needed

**Verify:**
```bash
ls -la ../shared/database/
```

You should see `app.db` file. If the directory is empty, the `db.session.execute()` + `commit()` steps were skipped.

- [ ] Database created at shared/database/app.db
- [ ] Database file exists (not just empty directory)
- [ ] No errors

---

### Task 4.6: Test Minimal Features

🤖 **Claude: First, check if port 5000 is available (common issue on macOS with AirPlay):**

> "Before we start the app, let's check if port 5000 is available. On macOS, Apple's AirPlay Receiver service often uses port 5000, which will prevent Flask from starting."

**Check port availability:**
```bash
lsof -i :5000
```

🤖 **Claude: If the port is in use (command shows output), say:**

> "Port 5000 is already in use! This is common on macOS due to AirPlay Receiver.
>
> You have two options:
> 1. **Use a different port** (recommended, faster) - We'll run Flask on port 5001 instead
> 2. **Disable AirPlay Receiver** - System Settings > General > AirDrop & Handoff > uncheck 'AirPlay Receiver'
>
> Let's use port 5001 to keep things simple."

**Update `app.py` to use port 5001:**
```python
if __name__ == '__main__':
    app.run(debug=True, port=5001)
```

🤖 **Claude: If port 5000 is available (no output), say:**

> "Great! Port 5000 is available. We can use Flask's default port."

**Now run the app:**

🤖 **Claude: Say:**

> "Time to test! Run your app:"

```bash
uv run python app.py
```

> "Then visit http://localhost:5000 (or http://localhost:5001 if you changed the port)
>
> Test these flows:
> 1. Go to /dev-login
> 2. Create a new user (your name + email)
> 3. Login as that user
> 4. Create a post
> 5. See it appear in the feed
> 6. Click on your username to see your profile
> 7. Logout
> 8. Notice you can still see the feed (read-only)
> 9. Try to create a post while logged out (should redirect to login)
>
> Let me know if anything doesn't work or if you have questions!"

🛑 **STOP: Wait for student to test. Debug any issues together. Make sure everything works before continuing.**

- [ ] App runs without errors
- [ ] Can create users via dev login
- [ ] Can login and logout
- [ ] Can create posts
- [ ] Posts appear in global feed
- [ ] Can view profiles
- [ ] All features work as expected

---

## Part 5: Plan Your First Additional Feature (15-20 minutes)

🤖 **Claude: Say:**

> "Excellent! Your app is working. Now comes the important part: you'll choose an additional feature to add, and **you'll describe what needs to be built before I implement it**.
>
> This helps you think architecturally - understanding what objects, actions, and views are needed.
>
> Here are the additional features you could add:
>
> 1. **Follow System** - Follow/unfollow users, personalized feed
> 2. **Like System** - Like/unlike posts, show like counts
> 3. **Image Uploads** - Attach images to posts
> 4. **Search** - Search users or posts
> 5. **Real Authentication** - Replace dev mode with Google OAuth or email magic links
> 6. **Profile Editing** - Edit display name, add bio
> 7. **Comments** - Add threaded comments on posts
> 8. **Your idea** - Suggest something else!
>
> Which feature would you like to add?"

🛑 **STOP: Wait for student to choose**

---

### Step 5.1: Explain Feature Complexity and Confirm Choice

🤖 **Claude: IMPORTANT - Before proceeding, explain what the chosen feature involves:**

**Focus on: (1) What the feature means from a user perspective, (2) Any requirements BEYOND objects/actions/views, (3) Complexity and time estimate. Do NOT give away the architectural design - students will figure that out in Step 5.2.**

**If student chose "Follow System":**
> "Great choice! Let me explain what this feature means:
>
> **User experience:** Users can choose to follow other users whose posts they're interested in. You could then show a personalized feed with only posts from people you follow (instead of the global feed).
>
> **Complexity:** Medium - straightforward feature using objects/actions/views
> **Time:** 15-20 minutes to implement
>
> This is very doable! Does this sound good, or would you like to hear about another feature first?"

**If student chose "Real Authentication" (Google OAuth or Email Magic Links):**
> "That's an ambitious choice! Real authentication is definitely possible, but let me explain what's involved:
>
> **What we'll need to set up:**
> - Google OAuth: Create a Google Cloud project, get API credentials, configure redirect URLs
> - Email Magic Links: Set up email service (Gmail App Password or SendGrid account), configure email templates
> - Update User model and login flows
> - Replace dev mode with real authentication
> - **Important security consideration:** Since you'll have real users from the internet, we need to control who can create accounts (to avoid spam/abuse)
>
> **Complexity:** High - requires external service setup and configuration
> **Time:** 30-45 minutes for OAuth, 40-60 minutes for email magic links
> **Setup needed:** Google Cloud Console OR email service configuration
>
> This is definitely achievable, but it's more involved than other features. The setup requires creating accounts with external services and configuring API keys.
>
> Given your time constraints, would you like to:
> - A) Go ahead with real authentication (I'll guide you through the setup)
> - B) Choose a different feature for now (you can always add auth later)
> - C) Hear about another feature's complexity first?"

**If student chooses to proceed with Real Authentication (option A):**

🤖 **Claude: Before implementation, ask these important questions:**

> "Great! Before we implement real authentication, let's make two important decisions:
>
> **Decision 1: Invite System**
> Since you'll have real authentication, you need to control who can create accounts (to prevent random people on the internet from signing up). How should we manage this?
>
> A) **Simple text file** (`shared/invites.txt`) - just a list of allowed email addresses (faster, good for small groups)
> B) **Database table** - can track who invited whom, when invites were used, generate invite codes (more features, takes longer)
>
> For a 2-hour project, I recommend Option A to start. You can always upgrade to a database-backed system later. Which would you prefer?"

🛑 **STOP: Wait for invite system choice**

🤖 **Claude: Document the choice and create the invite system:**

If text file chosen:
```bash
# Create shared/invites.txt with student's email and maybe a few others
```

If database chosen: Note that you'll add an Invite model during implementation.

Then continue:

> "**Decision 2: Access Control for Logged-Out Users**
> When someone visits your app without being logged in, what should they see?
>
> A) **Read-only access** - Can see posts and profiles, but can't create/like/comment (more open, good for public content)
> B) **Login required** - Must log in to see anything (more private, good for invite-only communities)
>
> There's no right answer - it depends on whether you want your app to be more like Twitter (public) or a private social network. You can also make this configurable with a simple variable later.
>
> Which approach feels right for your app?"

🛑 **STOP: Wait for access control choice**

🤖 **Claude: Document both decisions in DECISIONS-MADE-phase1.md:**

```markdown
## Authentication and Access Control Decisions

**Authentication Method**: Real authentication (Google OAuth / Email Magic Links)
**Chosen on**: [date]

**Invite System**: [Text file / Database table]
**Reasoning**: [Student's explanation or your recommendation]

**Access Control for Logged-Out Users**: [Read-only / Login required]
**Reasoning**: [Student's explanation]
- If read-only: Logged-out users can view posts/profiles but not interact
- If login required: All routes redirect to login except the landing page

**Implementation notes**:
- Invite system protects against spam/unauthorized signups
- Access control is enforced in route decorators
- Can be changed later with minimal code changes
```

Then proceed with authentication implementation.

**If student chose "Like System":**
> "Nice choice! Let me explain what this feature means:
>
> **User experience:** Users can like/unlike posts, and see how many likes each post has. You might also show which posts you've liked.
>
> **Complexity:** Low-Medium - straightforward feature using objects/actions/views
> **Time:** 15-20 minutes to implement
>
> This is a great choice for learning how user interactions work! Ready to proceed?"

**If student chose "Image Uploads":**
> "Good choice! Let me explain what this feature means:
>
> **User experience:** Users can attach images to their posts, making the app more visual (like Instagram).
>
> **Requirements beyond objects/actions/views:** File handling - saving uploaded files, validating file types/sizes, securing file storage
> **Complexity:** Medium - involves file upload handling in addition to database work
> **Time:** 20-25 minutes to implement
>
> This adds a nice visual element to your app! Sound good?"

**If student chose "Comments":**
> "Interesting choice! Let me explain what this feature means:
>
> **User experience:** Users can comment on posts, creating threaded discussions. Authors can delete their own comments.
>
> **Complexity:** Medium - nested UI elements to display comments under posts
> **Time:** 20-30 minutes to implement
>
> This makes your app more interactive! Ready to design it?"

**If student chose "Search":**
> "Practical choice! Let me explain what this feature means:
>
> **User experience:** Users can search for other users by username, or search posts by content. Results are displayed on a search results page.
>
> **Complexity:** Low-Medium - straightforward feature using objects/actions/views
> **Time:** 15-20 minutes to implement
>
> This is straightforward and useful! Sound good?"

**If student chose "Profile Editing":**
> "Good choice! Let me explain what this feature means:
>
> **User experience:** Users can edit their profile - add a bio, change display name, set an avatar image.
>
> **Complexity:** Low-Medium - straightforward feature using objects/actions/views
> **Time:** 15-25 minutes to implement
>
> This makes profiles more personal! Ready to proceed?"

**General pattern for any feature:**

🤖 **Claude: After explaining, say:**

> "Does this match what you were expecting? Would you like to proceed with [feature name], or would you like to hear about a different feature first?"

🛑 **STOP: Wait for student to confirm choice or ask about other features**

**If student wants to hear about another feature:** Explain that one and let them compare.

**If student confirms their choice:** Continue to Step 5.2.

---

### Step 5.2: Student Describes the Feature

🤖 **Claude: After student confirms their choice, say:**

> "Great choice! Now, thinking about objects, actions, and views:
>
> 1. **What new objects (models/database tables) do we need?**
>    - What properties should they have?
>    - How do they relate to existing objects?
>
> 2. **What new actions (routes) do we need?**
>    - What should each route do?
>    - Which ones require login?
>
> 3. **What needs to change in our views (templates)?**
>    - What new pages or sections?
>    - What buttons or forms to add?
>
> Take your time - describe this in your own words. Don't worry about exact code, just the concepts."

🛑 **STOP: Wait for student to articulate their design. This is the key learning moment.**

---

### Step 5.3: Discuss and Refine

🤖 **Claude: Review what student said. If incomplete, ask questions like:**

- "How do we prevent duplicate follows/likes?" (suggest unique constraint)
- "Should we show a count of followers on the profile?" (suggest yes)
- "What should the home page show now - all posts or just followed users?" (discuss tradeoffs)
- "Do we need a way to see all posts still?" (suggest global feed page)

**Work together to complete the design. Make it a dialogue, not a lecture.**

🛑 **Keep discussing until you have a complete design that student understands**

---

### Step 5.4: Document the Design

🤖 **Claude: Update DECISIONS-MADE-phase1.md with a new section:**

```markdown
## Additional Feature: [Feature Name]

**Chosen by student on**: [date]

**Why this feature**: [student's reasoning if provided]

**Design**:

**New Objects (Models)**:
- [List what was discussed]

**New Actions (Routes)**:
- [List what was discussed]

**Changes to Views (Templates)**:
- [List what was discussed]

**Other considerations**:
- [Any constraints, validation, etc. discussed]

**Student's understanding check**: [Note any good questions or insights student showed]
```

- [ ] Feature chosen
- [ ] Student articulated objects needed
- [ ] Student articulated actions needed
- [ ] Student articulated view changes needed
- [ ] Design discussed and refined
- [ ] Documented in DECISIONS-MADE

---

## Part 6: Implement Additional Feature (15-20 minutes)

🤖 **Claude: Now implement what was designed in Part 5, BUT do it incrementally:**

### Step 6.1: Create New Models (if any)

🤖 **Claude: If new models needed:**

> "First, let's add the new model(s) to models.py based on our design."

Generate the code, then:

> "I've added the [ModelName] model. Take a look at it - does it match what we discussed?
>
> Notice:
> - [Point out key design decisions]
> - [Point out any constraints or unique indexes]
>
> Look good?"

🛑 **STOP: Wait for student to review and approve**

---

**Then update database - choose migration strategy:**

🤖 **Claude: IMPORTANT - Assess if we need to migrate or recreate the database:**

**For most common features (Follow, Like, Comment), we can ADD the new table without deleting existing users/posts!**

#### Strategy 1: Migrate (Preferred - Preserves Data)

**Use when adding NEW tables or NULLABLE columns**

🤖 **Claude: Say:**

> "Good news! Since we're just adding a new table, I can preserve your existing users and posts. You won't need to recreate them!"

**Create `dev_scripts/add_[feature]_table.py`:**

Example for Follow table:
```python
"""Add Follow table to existing database without deleting users/posts."""
from database import app, db
from models import Follow  # Import ONLY the new model

with app.app_context():
    # Create only the new table, preserving existing data
    Follow.__table__.create(db.engine, checkfirst=True)
    db.session.commit()
    print("✅ Follow table added successfully!")
    print("✅ Your existing users and posts are preserved!")
    print(f"Tables in database: {list(db.metadata.tables.keys())}")
```

**Then run it:**
```bash
uv run python dev_scripts/add_follow_table.py
```

🤖 **Explain to student**:
- We're adding JUST the new table, not recreating everything
- Your test users and posts are still there!
- `checkfirst=True` prevents errors if the table already exists
- This is how real developers add features - migrations preserve data
- In production, we'd use tools like Alembic, but this accomplishes the same goal

**Verify:**
```bash
ls -la ../shared/database/
```

The `app.db` file should still exist with the same size or slightly larger.

- [ ] New model(s) created
- [ ] Student reviewed and approved
- [ ] New table added via migration
- [ ] **Existing users and posts preserved!**

---

#### Strategy 2: Recreate (Only When Necessary)

**Use ONLY when making structural changes to existing tables:**
- Making nullable columns required
- Changing column types
- Renaming columns (SQLite limitation)
- Removing columns

🤖 **Claude: Say:**

> "⚠️ Unfortunately, the changes we need to make require recreating the database. This means you'll need to create your test users and posts again.
>
> The changes are:
> - [Explain what's changing in existing tables]
>
> Should we proceed? You'll need to log back in and create posts again afterward."

🛑 **STOP: Wait for student confirmation**

**Create `dev_scripts/reset_database.py`:**
```python
"""Reset database - deletes existing database and creates new one with all tables."""
from database import app, db
from models import User, Post, Follow  # Import ALL models including new ones!
import os

with app.app_context():
    # Get database path
    db_path = app.config['SQLALCHEMY_DATABASE_URI'].replace('sqlite:///', '')
    print(f"Database path: {db_path}")

    # Delete old database
    db.session.remove()
    if os.path.exists(db_path):
        os.remove(db_path)
        print("✅ Old database deleted")

    # Create new database with ALL tables
    db.create_all()
    db.session.execute(db.text("SELECT 1"))  # Force SQLite to write file
    db.session.commit()
    print(f"✅ New database created with tables: {list(db.metadata.tables.keys())}")
    print("⚠️  You'll need to create test users and posts again")
```

**Then run it:**
```bash
uv run python dev_scripts/reset_database.py
```

🤖 **Explain to student**:
- We're recreating the database because of structural changes to existing tables
- This will delete all existing data (test users and posts)
- In production, we'd write a data migration to transform existing data
- Must import ALL models so SQLAlchemy knows about them
- Must execute a query + commit to force SQLite to actually write the file
- Script is saved in `dev_scripts/` so you can easily reset the database later if needed

**Verify database was created:**
```bash
ls -la ../shared/database/
```

Should see `app.db` file (not empty directory).

- [ ] New model(s) created
- [ ] Student reviewed and approved
- [ ] Student confirmed recreation is OK
- [ ] Database recreated with new table(s)
- [ ] Verified database file exists
- [ ] Student knows they need to create users again

---

### Step 6.2: Create New Actions (Routes)

🤖 **Claude: Add the new routes to routes.py:**

Generate them one at a time if there are multiple.

After each route:

> "I've added the [action name] route. Key points:
> - [Explain what it does]
> - [Explain any validation]
> - [Explain what happens after]
>
> Make sense?"

🛑 **STOP: After each major route, let student review**

- [ ] New routes added
- [ ] Student understands each route
- [ ] Validation and error handling included

---

### Step 6.3: Update Views (Templates)

🤖 **Claude: Update the necessary templates:**

List what you're changing:
- "Adding follow button to profile.html"
- "Changing index.html to show personalized feed"
- "Adding follower/following counts to profile"

Make the changes, then:

> "Templates updated! The main changes:
> - [List key changes]
>
> Let's test it out!"

- [ ] Templates updated
- [ ] UI changes match the design

---

### Step 6.4: Test the New Feature

🤖 **Claude: Say:**

> "Time to test your new feature! Here's what to test:
>
> [Provide specific test steps based on the feature]
>
> Run the app and try it out. Let me know how it goes!"

🛑 **STOP: Wait for testing. Debug issues together if needed.**

- [ ] Feature works as designed
- [ ] No errors
- [ ] Student successfully tested all flows

---

## Part 7: Code Review and Refinement (10 minutes)

🤖 **Claude: Say:**

> "Great work! Before we finish, I want to do a quick code review. This is a normal part of professional development - getting feedback on your code and learning new patterns.
>
> Let me take a look at your code to see if there are any patterns or best practices that could make it cleaner or easier to maintain."

🤖 **Claude: Review the codebase carefully. Focus on:**

**What to look for:**
1. **Repeated code** - Same or very similar logic in multiple places that would all need to change together
2. **Authentication checks** - Are there manual session checks in routes that could use Flask's `@login_required` decorator?
3. **Code clarity** - Would another developer easily understand what's happening?
4. **Duplicated validation** - Same validation logic repeated across routes
5. **Hard-coded values** - Magic numbers or strings that should be named constants

**What NOT to focus on:**
- Minor performance optimizations (like querying data twice) - not impactful at this scale
- Premature optimization
- Style nitpicks

**The decorator teaching moment:**

If routes have manual session checks (like `if 'user_id' not in session:`), this is the perfect time to introduce `@login_required`:

🤖 **Claude: Say:**

> "I want to show you a Flask pattern that will make your code cleaner and more maintainable. Let me show you your [route name] route."
>
> [Show the route with manual session checking]
>
> "See how you're checking `if 'user_id' not in session` at the start? Flask has a decorator pattern called `@login_required` that handles this for you. Here's what it looks like:"
>
> [Show example with decorator]
>
> "Benefits of this approach:
> - Makes it immediately obvious which routes require authentication (just look for the decorator!)
> - Reduces boilerplate - you don't repeat the check in every route
> - Follows Flask best practices
> - Easier to maintain - if you need to change how login works, you change it in one place
>
> Are you familiar with Python decorators? They're functions that wrap other functions to add behavior. Flask uses them extensively.
>
> Would you like me to refactor your routes to use `@login_required`? I'll show you the first one, then you can see how much cleaner it is."

🛑 **STOP: Wait for student response. Explain decorators if they're unfamiliar. Then refactor together.**

**For other issues found (if any):**

Frame them positively and focus on "why this matters":

🤖 **Claude: Say:**

> "I noticed [describe the issue in a specific location].
>
> This could be improved because: [explain the maintenance/clarity benefit]
>
> Here's a pattern that would help: [suggest solution]
>
> Does that make sense? Would you like to make this change?"

🛑 **STOP: Let student decide**

**Important:** Don't manufacture issues if the code is genuinely good. If decorators are the only thing to improve, that's perfectly fine!

- [ ] Code review completed
- [ ] Student understood issues found
- [ ] Student made improvements (or chose not to)
- [ ] Code is in good shape

---

## Part 8: Optional - Add More Features (If Time Permits)

🤖 **Claude: Say:**

> "Congratulations! You've completed the required work for Phase 1:
> ✅ Minimal features working
> ✅ One additional feature added
> ✅ Code reviewed and refined
>
> You can submit this work, or if you have time, you can add more features!
>
> Would you like to:
> A) Add another feature (we'll follow the same process: you describe it, then I implement)
> B) Improve existing features (add validation, better UI, etc.)
> C) Move on to wrapping up and submission"

🛑 **STOP: Wait for student choice**

**If adding another feature**: Go back to Part 5 (have them describe it first!)

**If improving**: Discuss what they want to improve and implement together

**If finishing**: Continue to Part 9

---

## Part 9: Wrap Up and Submission (5 minutes)

### Task 9.1: Create Test Data

🤖 **Claude: Help student create a few test users and posts so the app looks populated:**

```bash
python
>>> from database import app, db
>>> from models import User, Post
>>> with app.app_context():
...     # Create 2-3 users
...     # Create several posts
...     # Create follow relationships (if feature exists)
...     db.session.commit()
```

- [ ] Test data created
- [ ] App looks realistic when demo'd

---

### Task 9.2: Final Testing

- [ ] All features work end-to-end
- [ ] No console errors
- [ ] App is ready to show someone

---

### Task 9.3: Git Commit

```bash
git add .
git commit -m "Complete Phase 1: Flask social media app

Features implemented:
- [List your features]
- [Additional features added]

Architecture:
- [Your authentication choice]
- [Your access control model]
- SQLite database with [X] models

Built with AI assistance (Claude Code)
"
```

- [ ] Committed to git
- [ ] .gitignore includes .env

---

### Task 9.4: Review Documentation

🤖 **Claude: Say:**

> "Let's make sure your DECISIONS-MADE-phase1.md file is complete. It should document:
> - Visual design choices (name, colors)
> - Invite system choice
> - Minimal features architecture
> - Additional feature(s) design
> - Any other decisions you made
>
> This file is important - it shows your thinking process and helps if you start a new conversation with me.
>
> Take a look at phase1/DECISIONS-MADE-phase1.md - does it look complete?"

🛑 **STOP: Let student review**

- [ ] DECISIONS-MADE is complete
- [ ] Shows understanding of architecture

---

### Task 9.5: Take Screenshots (Optional)

**For your portfolio:**
- [ ] Screenshot of feed
- [ ] Screenshot of profile
- [ ] Screenshot of key feature

---

## Completion Checklist

### Required for Submission:
- [ ] Minimal features all working (login, posts, feed, profiles)
- [ ] One additional feature implemented
- [ ] Code reviewed and refined
- [ ] DECISIONS-MADE-phase1.md complete
- [ ] Code committed to git
- [ ] Can explain how objects/actions/views work

### Understanding Check:
Can you explain (in your own words):
- [ ] What is a model/object and how does it map to a database?
- [ ] What is a route/action and how does Flask handle requests?
- [ ] What is a template/view and how does data get displayed?
- [ ] How does session-based authentication work in your app?

**Time spent**: _____ (expected: ~2 hours for base + 1 feature)

---

## Reflection Questions

Answer these before moving to Phase 2:

1. **Which additional feature did you add and why?**

   _____________________________________

2. **What did you find most challenging?**

   _____________________________________

3. **How did thinking about objects/actions/views help (or not help)?**

   _____________________________________

4. **What would you do differently if you built this again?**

   _____________________________________

5. **What's one thing from the code review that surprised you?**

   _____________________________________

---

## Next Steps

✅ **Phase 1 Complete!**

**Before starting Phase 2:**
- [ ] Save all your Phase 1 code
- [ ] Commit everything to git
- [ ] Take a break!
- [ ] Read TODO-phase2.md

**Phase 2 Preview:**
- REST API with FastAPI
- JavaScript frontend (React/Vue)
- JWT authentication
- Compare architectural approaches

---

## Getting Help

**If stuck:**
1. Read error messages carefully
2. Check CLAUDE.md for common issues
3. Ask Claude to explain concepts
4. Office hours / Piazza

**Remember**: Understanding > completion. Take time to learn!
