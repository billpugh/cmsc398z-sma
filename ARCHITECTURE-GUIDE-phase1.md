# Phase 1: Flask Monolithic Application

## Goal

Build a fully functional social media prototype using Flask with server-side rendering. The emphasis is on rapid prototyping - get features working quickly with AI assistance while understanding the architectural decisions you're making.

## Expected Time: 1-1.5 hours

## How to Use This Guide

1. **Read this README** - Understand the architecture and design decisions (15 minutes)
2. **Work through `TODO-phase1.md`** - Step-by-step implementation guide with Claude
3. **Reference `../SETUP-AUTHENTICATION.md`** - Detailed authentication setup when needed

This README explains **WHY** and **WHAT**. The TODO.md guides **HOW** with AI assistance.

---

## Architecture Overview

### What is a Monolithic Architecture?

In a monolithic Flask application:
- **Single application** - All code runs in one Python process
- **Flask** handles routing, business logic, AND HTML rendering
- **Jinja2 templates** generate HTML on the server
- **SQLite database** stores all persistent data
- **Flask sessions** manage user authentication state
- **Server-side rendering** - Complete HTML pages returned to browser

```
Browser Request → Flask App → Database
                      ↓
              Jinja2 Template → HTML Response
```

**Advantages:**
- ✅ Simple to understand and debug
- ✅ Perfect for prototyping
- ✅ Easy deployment (single application)
- ✅ No need for API design yet

**Trade-offs:**
- ❌ Tight coupling between frontend and backend
- ❌ Hard to support multiple clients (web, mobile, etc.)
- ❌ Must reload page for updates (no dynamic updates)

**This is intentional** - you'll address these in Phase 2 with a separated architecture!

---

## Key Architectural Decisions

### Decision 1: Authentication Method

You'll choose ONE of three authentication approaches. This is a learning opportunity to understand trade-offs:

#### Option A: Development Mode (No Real Auth)
**Time**: 5 minutes | **Complexity**: Very Low

- Simple session-based user switching
- No password, OAuth, or email required
- Perfect for learning features first

**When to use**: First web app, want to focus on features, local development only

**Trade-off**: NOT suitable for public deployment

**Migration path**: Easy to upgrade to real auth later (same database schema)

---

#### Option B: Google OAuth (Recommended for Portfolio)
**Time**: 15-20 minutes | **Complexity**: Medium

- Users log in with existing Google accounts
- No password management or storage needed
- Production-ready authentication

**When to use**: Want to deploy publicly, building portfolio project

**Requires**: Google Cloud Console project setup (guided in SETUP-AUTHENTICATION.md)

**What you learn**: OAuth 2.0 flow, third-party authentication, redirect URIs

---

#### Option C: Email Magic Links (Advanced)
**Time**: 30-45 minutes | **Complexity**: High

- Passwordless authentication (like Slack, Medium)
- Send login link via email
- Tokens expire after 15 minutes

**When to use**: Want to learn email infrastructure, avoid Google dependency

**Requires**: Email service (Gmail App Password or SendGrid)

**What you learn**: Email sending, secure tokens, transactional email services, time-based expiration

---

### Decision 2: Access Control Model

This affects who can view your deployed app:

#### Option A: Fully Private
**All routes require login** - Simpler implementation

- Only invited users can create accounts
- Only logged-in users can see anything
- Uniform `@login_required` on all routes

**Implementation**:
```python
@app.route('/')
@login_required  # Must login to see feed
def index():
    posts = get_personalized_feed(current_user)
    return render_template('feed.html', posts=posts)
```

**When to use**: Private community, internal tool, prefer simpler permissions

---

#### Option B: Public Reading, Private Writing (Recommended)
**Anyone can view, login required to interact**

- Anyone can browse posts and profiles (read-only)
- Must login to create posts, follow users, etc.
- Invite-only for posting prevents spam

**Implementation**:
```python
@app.route('/')
# NO @login_required - public reading
def index():
    posts = Post.query.order_by(Post.created_at.desc()).limit(50).all()
    return render_template('feed.html', posts=posts)

@app.route('/posts', methods=['POST'])
@login_required  # MUST login to create
def create_post():
    # Only authenticated users can post
    ...
```

**When to use**: Portfolio project (shareable link!), want to showcase work publicly

**Real-world examples**: Twitter allows public reading, Medium, DEV.to, GitHub

**What you learn**: Granular access control (read vs. write permissions)

---

### Decision 3: Invite System

Prevents random strangers from creating accounts and posting content.

#### Option A: invites.txt file (Simpler)
- Simple text file with allowed emails
- One email per line
- Easy to manage

#### Option B: Database table (More Features)
- Track who invited whom
- Track when invite was used
- Can build "invite friends" feature later
- More complex queries

---

## Understanding the Data Model

### Core Entities

**User** - Represents a person using the app
- Stores identity (email, username)
- Display name for presentation
- Created timestamp

**Post** - A single message/status update
- References the User who created it (foreign key)
- Content (limited to 280 characters)
- Timestamp for sorting

**Follow** - Relationship between two users
- follower_id → User who is following
- followee_id → User being followed
- Prevents duplicates with unique constraint

**Invite** (optional) - Controls who can register
- Email address of invited person
- Who invited them
- Whether they've registered yet

### Database Relationships

```
User
  ├─→ Posts (one-to-many): user.posts
  ├─→ Followers (many-to-many through Follow)
  └─→ Following (many-to-many through Follow)

Post
  └─→ User (many-to-one): post.user

Follow
  ├─→ Follower User (many-to-one)
  └─→ Followee User (many-to-one)
```

**Why this matters**: Understanding relationships helps you write efficient queries and avoid N+1 problems.

---

## Core Features (MVP)

### 1. User Authentication & Access Control
- Users can log in/log out
- Sessions persist across requests
- Protected routes enforce authentication
- Invite system controls registration

**What you learn**: Session management, cookies, authentication state, access control patterns

---

### 2. Post Creation & Display
- Users can create short text posts (280 chars max)
- Posts include timestamp and author
- Posts display on feed and profiles
- Validation prevents empty/too-long posts

**What you learn**: Forms, POST requests, validation, database writes, flash messages

---

### 3. User Profiles
- View any user's profile page
- See username, join date, post count
- List all posts by that user
- Shows follower/following counts

**What you learn**: URL parameters, database queries, template rendering, user data aggregation

---

### 4. Following System
- Follow/unfollow other users
- See who you're following
- See who follows you
- Can't follow yourself
- Duplicate follows handled gracefully

**What you learn**: Many-to-many relationships, state management, button toggling, preventing duplicates

---

### 5. Personalized Feed
- Homepage shows relevant posts:
  - **Fully Private**: Your posts + posts from followed users
  - **Public Reading**: Recent posts from all users
- Sorted by timestamp (newest first)
- Limited to reasonable number (50)

**What you learn**: Complex queries with JOIN, filtering, sorting, pagination concepts

---

## Optional Extensions (If Time Permits)

- 📷 **Image Uploads** - Posts with photos (file handling, validation)
- ❤️ **Likes** - Like/unlike posts (additional many-to-many relationship)
- 💬 **Comments** - Nested content (tree structures)
- 🔍 **Search** - Find users/posts (full-text search, SQL LIKE)
- 🎨 **Better UI** - Polish with CSS frameworks (responsive design)
- 🧪 **Tests** - pytest for routes (testing patterns)

**Note**: These are truly optional! Focus on understanding core features first.

---

## Technology Stack Explained

### Flask
- Lightweight Python web framework
- Routing: Map URLs to Python functions
- Request/Response handling
- Template rendering with Jinja2

**Why Flask**: Simple, well-documented, AI tools know it well

---

### SQLAlchemy (ORM)
- Object-Relational Mapping - Write Python instead of SQL
- Automatic relationship handling
- Migration support (though not used in this project)

**Why ORM**: Type-safe, prevents SQL injection, more maintainable

---

### SQLite
- File-based database (no setup required)
- Single `.db` file in `instance/` directory
- Great for prototyping and learning

**Limitation**: Not ideal for production at scale (concurrent writes), but perfect for class projects

---

### Flask-Login
- Session management for authenticated users
- `current_user` proxy for logged-in user
- `@login_required` decorator
- Remembers users across requests

**How it works**: Stores user ID in signed cookie, loads User object on each request

---

### Jinja2
- Template engine for generating HTML
- Embed Python-like code in HTML:
  - `{{ variable }}` - Output value
  - `{% if condition %}` - Conditionals
  - `{% for item in items %}` - Loops
- Auto-escapes HTML (prevents XSS)

**Pattern**: Controller (Flask route) → Model (database query) → View (Jinja2 template)

---

## Security Considerations

Even as a prototype, avoid basic security issues:

### SECRET_KEY
**Most important!** Used to sign session cookies and tokens.

- ❌ NEVER use: "change-this-secret-key", "mysecretkey123", any memorizable value
- ✅ Generate: `python -c "import secrets; print(secrets.token_hex(32))"`
- ✅ Store in `.env` file (never commit to git)
- ✅ Minimum 64 hex characters (32 bytes)

**If compromised**: Attackers can impersonate any user!

---

### SQL Injection Prevention
- ✅ Always use SQLAlchemy ORM
- ❌ Never concatenate user input into SQL strings
- ❌ Never use `db.session.execute(f"SELECT * FROM users WHERE username = '{username}'")`
- ✅ Use `User.query.filter_by(username=username).first()`

---

### XSS Prevention
- ✅ Jinja2 auto-escapes HTML by default (good!)
- ❌ Don't use `|safe` filter on user-generated content
- ✅ User input displayed as text, not executed as HTML/JS

---

### File Upload Security (if implementing)
- ✅ Validate file extensions (whitelist, not blacklist)
- ✅ Limit file sizes (use Flask's `MAX_CONTENT_LENGTH`)
- ✅ Sanitize filenames (use `werkzeug.utils.secure_filename`)
- ❌ Don't trust user-supplied filenames directly

---

### Environment Variables
- ✅ `.env` in `.gitignore`
- ❌ Never commit credentials to git
- ✅ Use `.env.example` as template (with placeholder values)

---

## Common Patterns and Concepts

### Flash Messages
Temporary messages shown after actions (form submissions, errors):

```python
from flask import flash

flash('Post created successfully!', 'success')
flash('Error: Post is too long', 'error')
```

**Displayed in template**:
```html
{% with messages = get_flashed_messages(with_categories=true) %}
  {% for category, message in messages %}
    <div class="alert alert-{{ category }}">{{ message }}</div>
  {% endfor %}
{% endwith %}
```

---

### Redirects After POST
**Pattern**: POST request → Process → Redirect to GET

```python
@app.route('/posts', methods=['POST'])
def create_post():
    # Process form data
    post = Post(content=request.form['content'])
    db.session.add(post)
    db.session.commit()

    # Redirect to prevent duplicate submission on refresh
    return redirect(url_for('index'))
```

**Why**: Prevents form resubmission when user refreshes page

---

### Database Transactions
Changes aren't saved until you commit:

```python
# Make changes
user = User(username='alice')
db.session.add(user)

# Save to database
db.session.commit()

# Undo changes if error occurs
db.session.rollback()
```

---

### Template Inheritance
Avoid repeating HTML structure:

```html
<!-- base.html -->
<html>
  <head><title>{% block title %}{% endblock %}</title></head>
  <body>
    <nav>...</nav>
    {% block content %}{% endblock %}
  </body>
</html>

<!-- index.html -->
{% extends 'base.html' %}
{% block title %}Feed{% endblock %}
{% block content %}
  <h1>Your Feed</h1>
  ...
{% endblock %}
```

---

## Working Effectively with AI

### Good Prompting Practices

**✅ Be specific about context:**
```
I'm building a Flask app with Google OAuth authentication.
Add a feature where users can like posts.
Update the database model, create the route, and update the template.
```

**✅ Include error messages:**
```
I'm getting "NoneType has no attribute 'id'" when creating a post.
Here's my route: [paste code]
What's wrong?
```

**✅ Ask for explanations:**
```
Explain how the Follow model's unique constraint prevents duplicate follows.
```

**❌ Too vague:**
```
Make a social media app
Add authentication
Fix the bug
```

---

### Iterative Development
1. **One feature at a time** - Don't try to build everything at once
2. **Test frequently** - After each feature, verify it works
3. **Ask AI to explain** - Don't just copy code, understand it
4. **Debug with AI** - Paste errors and relevant code
5. **Iterate** - First version doesn't need to be perfect

---

### Understanding vs. Generating
**You should understand:**
- Why you chose your authentication method
- How access control works in your app
- What each database model represents
- How routes map to user actions
- Why certain security measures matter

**AI can help generate:**
- Boilerplate code structure
- Template HTML
- CSS styling
- Database query syntax
- Error handling code

**Balance**: Use AI for speed, but stop to understand key concepts.

---

## Project Structure

```
phase1/
├── app.py                 # Main Flask application, routes
├── models.py              # Database models (User, Post, Follow)
├── auth.py                # Authentication logic (if needed)
├── .env                   # Environment variables (SECRET_KEY, etc.)
├── static/
│   ├── css/
│   │   └── style.css     # Custom styling
│   └── uploads/          # User-uploaded images (if implemented)
└── templates/
    ├── base.html         # Base template (navigation, structure)
    ├── login.html        # Login page
    ├── index.html        # Homepage/feed
    └── profile.html      # User profile

shared/
├── database/
│   └── app.db            # SQLite database (created when app runs)
└── invites.txt           # Allowed emails (if using file-based invites)
```

---

## Common Issues and Solutions

### "No such table" errors
**Cause**: Database not initialized
**Solution**: Run `db.create_all()` in Flask shell

### Session issues / "user not logged in"
**Cause**: SECRET_KEY not set or changed
**Solution**: Check `.env` file has SECRET_KEY, restart Flask app

### Template not found
**Cause**: Template file not in `templates/` directory or misspelled
**Solution**: Verify file path and name match `render_template()` call

### Static files not loading (CSS, images)
**Cause**: File not in `static/` directory or wrong path
**Solution**: Use `{{ url_for('static', filename='css/style.css') }}` in templates

### Changes not reflected
**Cause**: Browser caching
**Solution**: Hard refresh (Cmd/Ctrl + Shift + R) or restart Flask in debug mode

---

## AI Prompting Tips

### Structuring Your Prompts

**Start with context:**
```
I'm building a Flask social media app with [your auth method] and [your access control model].
```

**Specify what you need:**
```
Create a route that allows users to [action].
```

**Mention constraints:**
```
- Should validate that post content is not empty and < 280 chars
- Should redirect to homepage after success
- Should flash an error message if validation fails
```

**Ask for explanations when needed:**
```
Also explain why we use db.session.commit() and when it's needed.
```

---

### Working Through the TODO

The `TODO-phase1.md` guides you through implementation. Here's how to use it with Claude:

1. **Read the section** - Understand what you're building
2. **Make decisions** (marked with 🎯) - Don't let AI choose these
3. **Copy the provided prompt** - Give Claude the context
4. **Review the code** - Don't just accept, understand it
5. **Test immediately** - Verify it works before moving on
6. **Ask questions** - If something is unclear, ask Claude to explain

---

## Checkpoint: Before Moving to Phase 2

Before starting Phase 2, you should have:

- [ ] **Working authentication** - Can login/logout successfully
- [ ] **Post creation** - Can create and view posts
- [ ] **User profiles** - Can view any user's profile
- [ ] **Following system** - Can follow/unfollow users
- [ ] **Personalized feed** - Shows appropriate posts based on access control model
- [ ] **Invite system** - Respects invite list (if using real auth)
- [ ] **Basic UI** - App looks presentable and usable
- [ ] **No critical bugs** - App runs without errors
- [ ] **Code committed** - Saved to git repository
- [ ] **Understanding** - Can explain how authentication and access control work

---

## Learning Objectives

By completing Phase 1, you should understand:

### Technical Skills
- ✅ Flask routing and request handling
- ✅ SQLAlchemy ORM and relationships
- ✅ Session-based authentication
- ✅ Server-side template rendering
- ✅ Form handling and validation
- ✅ Database queries and filtering

### Architectural Concepts
- ✅ Monolithic architecture pros/cons
- ✅ Server-side rendering pattern
- ✅ Stateful authentication (sessions)
- ✅ Access control models
- ✅ Data modeling and relationships

### Security Awareness
- ✅ SECRET_KEY importance
- ✅ SQL injection prevention
- ✅ XSS prevention
- ✅ Authentication vs authorization
- ✅ Environment variable security

---

## Reflection Questions

Before moving to Phase 2, consider:

1. **What architectural decisions did you make?**
   - Authentication method:
   - Access control model:
   - Invite system approach:

2. **What would be hard to change later?**
   - Database schema (requires migration)
   - Authentication method (requires code rewrite)
   - Access control (requires route changes)

3. **What limitations does this architecture have?**
   - Must reload page for updates
   - Can't easily support mobile app
   - Tight coupling of frontend and backend
   - Server renders every page

4. **How did you use AI effectively?**
   - What worked well:
   - What was challenging:
   - What did you learn:

**These limitations motivate Phase 2!** You'll address them with a separated REST API architecture.

---

## Next Steps

Once Phase 1 is complete:

1. **Test thoroughly** - Verify all features work
2. **Commit your code** - Save to git
3. **Take screenshots** - For portfolio/documentation
4. **Review your learning** - What did you understand?
5. **Move to Phase 2** - Read `TODO-phase2.md`

**Phase 2 Preview**: You'll build the same features with:
- FastAPI backend (REST API)
- React/Vue frontend (JavaScript)
- JWT authentication (stateless)
- Separated architecture

Then you'll **compare** and understand the trade-offs!

---

## Resources

**Flask Documentation**: https://flask.palletsprojects.com/
**SQLAlchemy**: https://docs.sqlalchemy.org/
**Jinja2**: https://jinja.palletsprojects.com/
**Flask-Login**: https://flask-login.readthedocs.io/

**For detailed setup instructions**:
- `TODO-phase1.md` - Step-by-step implementation
- [`../SETUP-AUTHENTICATION.md`](../SETUP-AUTHENTICATION.md) - Authentication setup details
- [`../deployment-guide.md`](../deployment-guide.md) - PythonAnywhere deployment

**Getting Help**:
- Paste errors into Claude with context
- Ask Claude to explain concepts
- Office hours
- Piazza

---

**Remember**: The goal is to understand architectural patterns and make informed decisions, not to write perfect code. Use AI to move quickly, but take time to understand the WHY behind the WHAT!
