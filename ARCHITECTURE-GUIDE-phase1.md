# Phase 1: Flask Monolithic Application

## Goal

Build a fully functional social media prototype using Flask with server-side rendering. The emphasis is on rapid prototyping - get features working quickly with AI assistance while understanding the architectural decisions you're making.

## Expected Time: 1-1.5 hours

## How to Use This Guide

1. **Read this README** - Understand the architecture and design decisions (15 minutes)
2. **Tell Claude to guide you through Phase 1** - Say: "Read TODO-phase1.md and guide me through Phase 1"
   - **Important**: `TODO-phase1.md` is a guide for Claude, not for you to read directly
   - Claude will follow it to lead you through a structured conversation
   - You'll make decisions, learn concepts, and implement features step-by-step
3. **Reference `../SETUP-AUTHENTICATION.md`** - Detailed authentication setup (if adding real auth as optional feature)

This README explains **WHY** and **WHAT**. Claude uses TODO.md to guide **HOW**.

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

## Core Features (Minimal Viable Product)

You'll start by implementing these minimal features to get a working app quickly:

### 1. Simple User Login (Dev Mode)
- Basic session-based user switching (pick from a list of users)
- No password, OAuth, or email required initially
- Focus on learning core features first
- Can upgrade to real authentication later as an optional feature

**What you learn**: Session management, Flask routing, basic authentication concepts

---

### 2. Post Creation & Display
- Users can create short text posts (280 chars max)
- Posts include timestamp and author
- Posts display on global feed and user profiles
- Validation prevents empty/too-long posts

**What you learn**: Forms, POST requests, validation, database writes, flash messages

---

### 3. Global Feed
- Homepage shows recent posts from all users
- Sorted by timestamp (newest first)
- Limited to reasonable number (50 posts)
- Anyone can browse (no login required for viewing)

**What you learn**: Database queries, sorting, template rendering

---

### 4. User Profiles
- View any user's profile page
- See username, join date, post count
- List all posts by that user

**What you learn**: URL parameters, database queries, template rendering, user data aggregation

---

## Understanding the Data Model

### Core Entities (Minimal Features)

**User** - Represents a person using the app
- `id` (primary key)
- `username` (unique)
- `display_name`
- `created_at`

**Post** - A single message/status update
- `id` (primary key)
- `user_id` (foreign key → users.id)
- `content` (text, max 280 characters)
- `created_at`

### Database Relationships (Minimal)

```
User
  └─→ Posts (one-to-many): user.posts

Post
  └─→ User (many-to-one): post.user
```

**Why this matters**: Understanding relationships helps you write efficient queries and avoid N+1 problems.

---

## Optional Extensions (Choose at least ONE)

After implementing the minimal features, you must choose at least ONE additional feature to implement. Here are some options:

- 🔐 **Real Authentication** - Replace dev mode with Google OAuth or email magic links (see `SETUP-AUTHENTICATION.md`)
- 👥 **Following System** - Follow/unfollow users, personalized feed showing only posts from followed users
- ❤️ **Likes** - Like/unlike posts, show like counts
- 📷 **Image Uploads** - Attach images to posts (file handling, validation)
- 💬 **Comments** - Add comments to posts (nested content)
- 🔍 **Search** - Find users or posts by keyword (full-text search)
- 👤 **Profile Editing** - Edit display name, add bio and avatar
- 🎨 **Better UI** - Polish with CSS frameworks (responsive design)
- 🧪 **Tests** - pytest for routes (testing patterns)
- 💡 **Your idea** - Any other feature you propose

**Note**: When you choose a feature in Part 5 of the implementation, Claude will explain what it involves (complexity, setup requirements, time estimate) before you commit. You'll then describe the architecture (objects, actions, views) before Claude implements it.

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
- Prevents SQL injection
- Type-safe queries

**Why ORM**: More maintainable, safer than raw SQL, better for prototyping

---

### SQLite
- File-based database (no setup required)
- Single `.db` file in `shared/database/` directory
- Great for prototyping and learning
- Shared between Phase 1 and Phase 2

**Limitation**: Not ideal for production at scale (concurrent writes), but perfect for class projects

---

### Jinja2
- Template engine for generating HTML
- Embed Python-like code in HTML:
  - `{{ variable }}` - Output value
  - `{% if condition %}` - Conditionals
  - `{% for item in items %}` - Loops
- Auto-escapes HTML (prevents XSS)

**Pattern**: Controller (Flask route) → Model (database query) → View (Jinja2 template)

This is the classic **MVC (Model-View-Controller)** pattern!

---

### Flask Sessions
- HTTP is stateless - server doesn't remember who you are between requests
- **Sessions** solve this by storing data (like user ID) across requests
- Flask stores session data in a signed cookie on the user's browser
- **SECRET_KEY** is used to sign the cookie so users can't tamper with it

**How it works**:
1. User logs in (dev mode or real auth)
2. Server stores `user_id` in session: `session['user_id'] = user.id`
3. Browser sends session cookie with every request
4. Server reads user_id from session: `user_id = session.get('user_id')`
5. Server can load that User from the database

**Both dev mode and real authentication use sessions!** The difference is just HOW users prove their identity initially (pick from list vs OAuth/email).

---

## Project Structure

```
phase1/
├── app.py                 # Main Flask application entry point
├── database.py            # Flask app and db initialization
├── models.py              # Database models (User, Post)
├── routes.py              # Route handlers (views)
├── auth.py                # Authentication logic (if using real auth)
├── .env                   # Environment variables (SECRET_KEY, etc.)
├── dev_scripts/           # Helper scripts for database setup
│   ├── init_database.py
│   └── create_test_data.py
├── static/
│   ├── css/
│   │   └── style.css     # Custom styling (optional)
│   └── uploads/          # User-uploaded images (if implemented)
└── templates/
    ├── base.html         # Base template (navigation, structure)
    ├── login.html        # Login page (dev mode or real auth)
    ├── index.html        # Homepage/global feed
    └── profile.html      # User profile

shared/
├── database/
│   └── app.db            # SQLite database (shared with Phase 2)
├── static/
│   └── style.css         # Shared stylesheet
└── invites.txt           # Allowed emails (if using real auth with file-based invites)
```

---

## Time to Start Writing Code!

**You're now ready to begin implementation.** You've learned:
- What a monolithic architecture is and why it's good for prototyping
- The minimal features you'll build (login, posts, feed, profiles)
- The optional features you can add (and you'll choose one later)
- The technology stack (Flask, SQLAlchemy, SQLite, Jinja2, sessions)
- How your files will be organized

**Next step**: Tell Claude to guide you through the implementation:

> **"Read TODO-phase1.md and guide me through Phase 1. Follow the conversational scripts."**

Claude will lead you through a structured conversation where you'll:
1. Make quick design decisions (app name, colors)
2. Set up your environment
3. Learn the objects/actions/views framework
4. Implement minimal features step-by-step
5. Choose and design an additional feature (you'll describe it before Claude codes it!)
6. Get a code review and learn best practices
7. Optionally add more features if time permits

**The rest of this document is reference material** - come back to it when you want to understand:
- Common patterns (flash messages, redirects, etc.)
- Security concepts (why they matter)
- Troubleshooting common issues
- Architectural trade-offs

**Don't try to memorize everything now** - you'll learn by doing with Claude's guidance!

---

## Common Patterns and Concepts

**Note**: This section is reference material. You'll encounter these patterns during implementation - refer back here when you want deeper understanding.

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
    post = Post(content=request.form['content'], user_id=current_user_id)
    db.session.add(post)
    db.session.commit()

    # Redirect to prevent duplicate submission on refresh
    return redirect(url_for('index'))
```

**Why**: Prevents form resubmission when user refreshes page (Post-Redirect-Get pattern)

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

**Important**: Always commit after making changes, or they'll be lost!

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

**Benefit**: Change navigation once in `base.html`, affects all pages

---

### Declarative Checking of Authentication

Your app needs to check if users are logged in and protect certain routes (like creating posts). You'll implement this in two stages:

#### Initial Implementation: Manual Session Checks
```python
@app.route('/posts', methods=['POST'])
def create_post():
    if 'user_id' not in session:
        return redirect(url_for('dev_login'))

    user_id = session['user_id']
    # ... create post
```

**Characteristics**:
- Simple and explicit - you can see exactly what's happening
- Must repeat the check in every protected route
- Easy to understand for learning

#### Refactored Implementation: Decorators (Introduced in Code Review)
```python
from flask_login import login_required, current_user

@app.route('/posts', methods=['POST'])
@login_required
def create_post():
    user_id = current_user.id
    # ... create post
```

**Characteristics**:
- "Declarative" - the `@login_required` decorator declares this route's requirement
- Less repetition - the check is in one place
- Easier to see at a glance which routes require authentication
- Follows Flask conventions and best practices

**Your journey**: You'll start with manual checks (understand the mechanics), then during code review, Claude will introduce decorators as a refactoring opportunity. Both approaches work - decorators are just cleaner code organization using Python's decorator pattern.

**What's a decorator?** A function that wraps another function to add behavior. `@login_required` wraps your route function and checks authentication before calling it. You'll see this pattern throughout Flask (`@app.route`, `@login_required`, etc.).

---

## Security Considerations

Even as a prototype, avoid basic security issues:

### SECRET_KEY
**Most important!** Used to sign session cookies and tokens.

- ❌ NEVER use: "change-this-secret-key", "mysecretkey123", any memorizable value
- ✅ Generate: `uv run python -c "import secrets; print(secrets.token_hex(32))"`
- ✅ Store in `.env` file (never commit to git)
- ✅ Minimum 64 hex characters (32 bytes)

**If compromised**: Attackers can impersonate any user!

---

### SQL Injection Prevention
- ✅ Always use SQLAlchemy ORM
- ❌ Never concatenate user input into SQL strings
- ❌ Never use `db.session.execute(f"SELECT * FROM users WHERE username = '{username}'")`
- ✅ Use `User.query.filter_by(username=username).first()`

**Why**: Prevents attackers from injecting malicious SQL code

---

### XSS Prevention
- ✅ Jinja2 auto-escapes HTML by default (good!)
- ❌ Don't use `|safe` filter on user-generated content
- ✅ User input displayed as text, not executed as HTML/JS

**Why**: Prevents attackers from injecting malicious scripts into your app

---

### File Upload Security (if implementing image uploads)
- ✅ Validate file extensions (whitelist: .jpg, .png, .gif only)
- ✅ Limit file sizes (use Flask's `MAX_CONTENT_LENGTH`)
- ✅ Sanitize filenames (use `werkzeug.utils.secure_filename`)
- ❌ Don't trust user-supplied filenames directly

**Why**: Prevents attackers from uploading malicious files

---

### Environment Variables
- ✅ `.env` in `.gitignore`
- ❌ Never commit credentials to git
- ✅ Use `.env.example` as template (with placeholder values)

**Why**: Keeps secrets out of version control

---

## Common Issues and Solutions

### "No such table" errors
**Cause**: Database not initialized
**Solution**: Run `uv run python dev_scripts/init_database.py`

### Routes return 404 even though they're defined
**Cause**: Circular import issue between `app.py` and `routes.py`
**Solution**: Use separate `database.py` module to break circular dependency (see CLAUDE.md)

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

### Shared stylesheet not loading
**Cause**: Need route to serve shared static files
**Solution**: Configure Flask to serve from `shared/static/` directory (see CLAUDE.md)

---

## Checkpoint: Before Moving to Phase 2

Before starting Phase 2, you should have:

- [ ] **Working dev-mode login** - Can switch between users
- [ ] **Post creation** - Can create and view posts
- [ ] **Global feed** - Shows recent posts from all users
- [ ] **User profiles** - Can view any user's profile
- [ ] **At least one additional feature** - Following, real auth, likes, images, comments, search, or your own idea
- [ ] **Basic UI** - App looks presentable and usable
- [ ] **No critical bugs** - App runs without errors
- [ ] **Code committed** - Saved to git repository
- [ ] **DECISIONS-MADE-phase1.md** - Documents your architectural choices
- [ ] **Understanding** - Can explain the objects/actions/views pattern and how your app works

---

## Learning Objectives

By completing Phase 1, you should understand:

### Technical Skills
- ✅ Flask routing and request handling
- ✅ SQLAlchemy ORM and relationships
- ✅ Session-based authentication (at least dev mode)
- ✅ Server-side template rendering (Jinja2)
- ✅ Form handling and validation
- ✅ Database queries and filtering
- ✅ The Post-Redirect-Get pattern

### Architectural Concepts
- ✅ Monolithic architecture pros/cons
- ✅ Server-side rendering pattern
- ✅ MVC (Model-View-Controller) pattern
- ✅ Stateful authentication (sessions)
- ✅ Data modeling and relationships
- ✅ Why tight coupling can be both good (simplicity) and bad (flexibility)

### Security Awareness
- ✅ SECRET_KEY importance
- ✅ SQL injection prevention (ORM benefits)
- ✅ XSS prevention (auto-escaping)
- ✅ Environment variable security
- ✅ File upload security (if implemented)

---

## Reflection Questions

Before moving to Phase 2, consider:

1. **What architectural decisions did you make?**
   - Which optional feature did you add and why?
   - If you added real auth: Which method (OAuth/email) and why?
   - If you added following: How did the many-to-many relationship work?
   - What was easier than expected? What was harder?

2. **How did the objects/actions/views framework help?**
   - Did thinking in these terms make planning easier?
   - Could you describe your additional feature using this framework?
   - Would this approach work for other web applications?

3. **What would be hard to change later?**
   - Database schema (requires migration or recreation)
   - Authentication method (requires code rewrite throughout)
   - Template structure (requires refactoring many files)
   - Why is coupling a problem for making changes?

4. **What limitations does this monolithic architecture have?**
   - Must reload page for updates (no real-time, no smooth UX)
   - Can't easily support mobile app (HTML pages, not JSON API)
   - Tight coupling of frontend and backend (change one, may need to change the other)
   - Server renders every page (more server work, slower for users far away)

5. **How did you use AI (Claude) effectively?**
   - What prompting strategies worked well?
   - When did you need to ask for clarification or explanations?
   - What concepts did you learn by asking "why"?
   - What did Claude explain that surprised you?

**These limitations motivate Phase 2!** You'll address them with a separated REST API architecture.

---

## Next Steps

Once Phase 1 is complete:

1. **Test thoroughly** - Verify all features work end-to-end
2. **Commit your code** - Save to git with meaningful commit messages
3. **Take screenshots** - For portfolio/documentation
4. **Review your learning** - What architectural concepts did you understand?
5. **Move to Phase 2** - Read `ARCHITECTURE-GUIDE-phase2.md`, then tell Claude: "Read TODO-phase2.md and guide me through Phase 2"

**Phase 2 Preview**: You'll build similar features with:
- FastAPI backend (REST API returning JSON)
- React/Vue frontend (JavaScript, client-side rendering)
- JWT authentication (stateless tokens)
- Separated architecture (frontend and backend independent)

Then you'll **compare** and understand the trade-offs!

---

## Resources

**Flask Documentation**: https://flask.palletsprojects.com/
**SQLAlchemy**: https://docs.sqlalchemy.org/
**Jinja2**: https://jinja.palletsprojects.com/
**Flask-Login**: https://flask-login.readthedocs.io/ (if using real auth)

**For implementation:**
- Tell Claude: "Read TODO-phase1.md and guide me through Phase 1"
  - Claude will follow this guide (you don't need to read it yourself)
  - Includes structured conversations for learning core concepts
- `SETUP-AUTHENTICATION.md` - Authentication setup details (if adding real auth as optional feature)
- `deployment-guide.md` - PythonAnywhere deployment

**Getting Help**:
- Paste errors into Claude with context
- Ask Claude to explain concepts you don't understand
- Office hours
- Piazza

---

**Remember**: The goal is to understand architectural patterns and make informed decisions, not to write perfect code. Use AI to move quickly, but take time to understand the WHY behind the WHAT!
