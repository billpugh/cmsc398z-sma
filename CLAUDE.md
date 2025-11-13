# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a two-phase web development project for CMSC 398z (AI-Assisted Programming) at the University of Maryland. Students build a simplified social media application (similar to Instagram/Twitter) in two distinct architectural approaches:

- **Phase 1**: Flask monolithic application with server-side rendering
- **Phase 2**: REST API (FastAPI) + JavaScript frontend (React/Vue) with separated architecture

The project emphasizes understanding architectural patterns, REST API design, and effective use of AI coding tools for rapid prototyping and iteration.

## Project Structure

**Important**: Students work in the repository root directory. Claude Code can see all documentation and shared resources from this directory.

```
├── README.md                            # Main project overview and AI-guided workflow
├── TODO-phase1.md                       # Phase 1 implementation guide with Claude instructions
├── TODO-phase2.md                       # Phase 2 implementation guide with Claude instructions
├── ARCHITECTURE-GUIDE-phase1.md         # Phase 1 concepts (WHY/WHAT)
├── ARCHITECTURE-GUIDE-phase2.md         # Phase 2 concepts (WHY/WHAT)
├── SETUP-AUTHENTICATION.md              # Authentication setup (both phases)
├── ARCHITECTURE.md                      # Template for student documentation
├── deployment-guide.md                  # Deployment instructions
│
├── shared/                              # Shared resources between phases
│   ├── database/
│   │   └── app.db                       # SQLite database (created when Phase 1 runs)
│   ├── static/
│   │   └── style.css                    # Shared stylesheet for both phases
│   └── invites.txt                      # List of invited emails
│
├── phase1/                              # Phase 1: Flask monolithic app (student creates files here)
│   ├── .env.example                     # Environment template
│   ├── .gitignore
│   ├── pyproject.toml                   # Python dependencies
│   ├── DECISIONS-MADE-phase1.md         # Student's architectural decisions (updated in Part 1)
│   ├── app.py                           # STUDENT CREATES: Main Flask app
│   ├── models.py                        # STUDENT CREATES: Database models
│   ├── auth.py                          # STUDENT CREATES: Authentication (if needed)
│   ├── templates/                       # STUDENT CREATES: HTML templates
│   │   ├── base.html
│   │   ├── index.html
│   │   └── ...
│   └── static/                          # STUDENT CREATES: CSS, uploads
│       ├── css/style.css
│       └── uploads/
│
└── phase2/                              # Phase 2: REST API + Frontend (student creates files here)
    ├── DECISIONS-MADE-phase2.md         # Student's Phase 2 architectural decisions (updated in Part 1)
    ├── backend/                         # FastAPI backend
    │   ├── .env.example
    │   ├── .gitignore
    │   ├── pyproject.toml
    │   ├── main.py                      # STUDENT CREATES: FastAPI app
    │   ├── models.py                    # STUDENT CREATES: Database models
    │   ├── database.py                  # STUDENT CREATES: Database connection
    │   ├── auth.py                      # STUDENT CREATES: JWT authentication
    │   └── ...
    └── frontend/                        # React/Vue frontend
        ├── package.json
        ├── vite.config.js
        ├── index.html
        └── src/                         # STUDENT CREATES: Components
            ├── App.jsx/vue
            ├── api.js                   # API client
            ├── components/
            └── ...
```

**Key Design**: All documentation is at the top level so Claude can see it while students work in `phase1/` or `phase2/` subdirectories. The `shared/` directory contains resources used by both phases.

**IMPORTANT - Shared Resources:**
- `shared/database/app.db` - Database shared between phases (or Phase 2 can use its own)
- `shared/static/style.css` - Stylesheet shared between phases
- `shared/invites.txt` - Invite list (if using text file approach)

**CRITICAL:** Do NOT copy `shared/static/style.css` into `phase1/static/` or `phase2/frontend/`. It must remain in the shared directory so both phases can use the same styles. Configure Flask/FastAPI to serve it from the shared location.

## Core Features

### Phase 1 Minimal Features (Required)

1. **User Authentication**: Dev mode initially (can upgrade to real auth as optional feature)
   - Simple session-based login (pick from user list)
   - No password required
   - Can add Google OAuth or email magic links as optional feature

2. **Invite-only System**: Simple text file or database table
   - List of allowed email addresses
   - Check on user creation

3. **Posts**: Users can create text posts (max 280 chars)
4. **Global Feed**: View all recent posts from all users
5. **User Profiles**: View any user's profile showing their posts
6. **Homepage**: Displays the global feed

### Phase 1 Additional Features (Choose at least 1)

Students must implement at least ONE additional feature, articulating the design first:

- **Following System**: Follow/unfollow users, personalized feed
- **Like System**: Like/unlike posts, show counts
- **Image Uploads**: Attach images to posts
- **Search**: Search users or posts by keyword
- **Real Authentication**: Replace dev mode with Google OAuth or email magic links
- **Profile Editing**: Edit display name, add bio/avatar
- **Comments**: Add comments to posts
- **Student's idea**: Any other feature they propose

### Phase 2 Core Features

**Important**: Phase 2 does NOT require feature parity with Phase 1. Students should focus on 3-4 core features with clean REST API design and modern frontend.

## Student Workflow

**Phase 1** (~2 hours in class):
1. Student tells Claude: "Read TODO-phase1.md and guide me through Phase 1. Follow the conversational scripts."
2. Claude follows a structured conversation with stopping points (🛑)
3. **Part 1** (5 min): Quick decisions (app name, colors only - no invite/access control yet)
4. **Part 2** (10-15 min): Environment setup
5. **Part 3** (10-15 min): **Feature planning conversation** - teach objects/actions/views framework
6. **Part 4** (30-40 min): Implement minimal features (login, posts, feed, profiles)
   - Student reviews code at key points (model, route, template)
   - Uses dev-mode authentication initially (no real auth setup)
7. **Part 5** (15-20 min): **Choose and plan additional feature** (pedagogical focus)
   - Claude explains feature complexity BEFORE student commits
   - If student chooses real authentication: Make invite/access control decisions at this point
   - Student describes objects/actions/views for their chosen feature
8. **Part 6** (15-20 min): Implement that feature incrementally
   - Use database migrations when possible to preserve student data
9. **Part 7** (10 min): **Code review** - Claude finds issues, student-led fixes
10. **Part 8** (optional): More features if time permits
11. **Part 9** (5 min): Wrap up and submission
12. Database stored in `shared/database/app.db`

**Key pedagogical elements**:
- Uses **dev-mode authentication** initially (no OAuth setup needed)
- **Invite/access control decisions postponed** until student adds real authentication
- **Minimal features first**: login, create post, global feed, view profile
- **Feature complexity explained** before student commits to implementation
- **Student articulates architecture**: Must describe objects/actions/views for additional feature before code generation
- **Understanding checks**: Student reviews and explains specific code sections
- **Database migrations preferred** to preserve student's test data
- **Code review as learning**: Issues found but not auto-fixed

**Phase 2**:
1. Student tells Claude: "Read TODO-phase2.md and guide me through Phase 2"
2. Claude asks for key decisions (frontend framework, auth method, features to implement)
3. Claude updates `phase2/DECISIONS-MADE-phase2.md` as decisions are made
4. Claude guides through backend and frontend implementation
5. Student can reference `phase1/` code and reuse `shared/database/app.db`

**DECISIONS-MADE files**:
- Template files pre-exist in `phase1/DECISIONS-MADE-phase1.md` and `phase2/DECISIONS-MADE-phase2.md`
- Updated by Claude in Part 1 as student makes each decision
- Documents student's architectural choices with reasoning
- Useful if student starts a new Claude conversation (provides context)
- Submitted as part of assignment

## Python Dependency Management with `uv`

⚠️ **CRITICAL**: This project uses `uv` for Python dependency management.

**ALWAYS use `uv` commands, NEVER use bare `python` commands:**

- ✅ `uv run flask run` - Run Flask with uv
- ✅ `uv run python app.py` - Run Python script with uv
- ✅ `uv run python -c "import secrets; print(secrets.token_hex(32))"` - Run Python one-liner
- ✅ `uv run flask shell` - Run Flask shell
- ✅ `uv run uvicorn main:app --reload` - Run FastAPI with uv
- ✅ `uv run pytest` - Run tests with uv
- ❌ `python app.py` - WRONG: won't use project dependencies
- ❌ `flask run` - WRONG: might not find dependencies
- ❌ `python -c "..."` - WRONG: won't use project environment

**Why `uv`?**
- Ensures correct Python version and dependencies are used
- No need to manually activate virtual environments
- Automatically manages project isolation
- Faster and more reliable than traditional venv + pip

**Common `uv` commands:**
```bash
uv sync                    # Install/sync project dependencies (do this first!)
uv add <package>           # Add a new dependency to pyproject.toml
uv run <command>           # Run any command with project dependencies
```

**IMPORTANT**: Do NOT use these deprecated commands:
- ❌ `uv venv` - Not needed, uv manages environments automatically
- ❌ `uv pip install` - Use `uv sync` or `uv add` instead
- ❌ `source .venv/bin/activate` - Not needed, use `uv run` instead

## Providing Multiline Python Code to Students

**CRITICAL**: When students need to run multiline Python code, **NEVER** ask them to copy-paste code from chat. This causes indentation errors and frustration.

**Instead, use the dev_scripts approach:**

### The `dev_scripts/` Directory Pattern

**For any multiline Python code that students need to run:**

1. **Create the script in `dev_scripts/` directory:**
   - Use descriptive names: `init_database.py`, `create_test_users.py`, `reset_database.py`
   - Makes purpose clear and self-documenting
   - Keeps setup scripts separate from application code
   - No need to add to `.gitignore` - these are useful to commit

2. **Write the script with the Write tool:**
   ```python
   # dev_scripts/init_database.py
   """Initialize the database with all tables."""
   from database import app, db
   from models import User, Post, Follow

   with app.app_context():
       db.create_all()
       db.session.execute(db.text("SELECT 1"))
       db.session.commit()
       print("✅ Database initialized successfully!")
       print(f"Tables created: {list(db.metadata.tables.keys())}")
   ```

3. **Run it immediately using the Bash tool:**
   ```bash
   uv run python dev_scripts/init_database.py
   ```

4. **Show the student what happened:**
   - "✅ Database initialized! I've created `dev_scripts/init_database.py` if you need to run it again."

### When to Use dev_scripts

| Situation | Script Name | Why |
|-----------|-------------|-----|
| Database initialization | `init_database.py` | Complex, repeatable, important |
| Reset database | `reset_database.py` | Destructive, want clear name |
| Create test data | `create_test_data.py` | Repeatable, useful for development |
| Database migrations | `add_follow_table.py` | Important to keep record, preserves data |
| One-time data fixes | `fix_user_emails.py` | Document what was changed |

### When NOT to Use dev_scripts

**For simple one-liners, run directly:**
```bash
# Generate SECRET_KEY
uv run python -c "import secrets; print(secrets.token_hex(32))"

# Quick check
uv run python -c "from database import db; print(db)"
```

**Rule of thumb:** If it's more than 2 lines or will be run multiple times, create a dev_script.

### Interactive Python Sessions

**If students need to explore interactively (rare), use:**
```bash
uv run python -i dev_scripts/setup_context.py
```

This loads the script then drops into interactive mode.

### Benefits of This Approach

- ✅ **No copy-paste errors** - students don't manually copy code
- ✅ **Self-documenting** - clear script names show what was done
- ✅ **Repeatable** - students can re-run setup scripts
- ✅ **Version controlled** - scripts are committed, showing setup history
- ✅ **Educational** - students see the code and can modify it
- ✅ **Clean** - separate directory keeps app code uncluttered

## Database Migrations vs. Recreation

**CRITICAL**: When students add features that require database changes (like following system, likes, comments), prefer database migrations over recreation to preserve their test data (users and posts they've already created).

### When to Migrate (Preferred)

**✅ Use migrations for additive changes:**
- Adding new tables (Follow, Like, Comment)
- Adding new columns with defaults or nullable columns
- Adding indexes
- Creating relationships that don't affect existing data

**Example: Adding Follow table after students have created users**

Create `dev_scripts/add_follow_table.py`:
```python
"""Add Follow table to existing database without deleting users/posts."""
from database import app, db
from models import Follow  # Import new model

with app.app_context():
    # Create only the new table, preserving existing data
    Follow.__table__.create(db.engine, checkfirst=True)
    db.session.commit()
    print("✅ Follow table added successfully!")
    print("✅ Your existing users and posts are preserved!")
    print(f"Tables in database: {list(db.metadata.tables.keys())}")
```

Then run: `uv run python dev_scripts/add_follow_table.py`

**Tell the student:**
> "✅ Follow table added! Your existing users and posts are still there. You can now start following users!"

### When to Recreate Database

**❌ Use recreation only for structural changes:**
- Removing required columns from existing tables
- Changing column types that require data transformation
- Renaming columns (SQLite doesn't support ALTER COLUMN RENAME)
- Changing primary/foreign key relationships
- Making nullable columns required (existing NULL values would break)

**Example: Structural change requiring recreation**

Create `dev_scripts/reset_database.py`:
```python
"""Reset database - deletes existing database and creates new one with all tables."""
from database import app, db
from models import User, Post, Follow  # Import ALL models
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
    db.session.execute(db.text("SELECT 1"))
    db.session.commit()
    print(f"✅ New database created with tables: {list(db.metadata.tables.keys())}")
```

**Tell the student:**
> "⚠️ This change requires recreating the database. You'll need to create your test users and posts again. Should we proceed?"

🛑 **Wait for confirmation before running**

### Implementation in TODO Workflow

**When students add features in Part 6:**

1. **Assess the database changes:**
   - "Let's think about what database changes this feature needs"
   - Explain if it's additive (new table) vs structural (modify existing)

2. **Choose migration strategy:**
   - **Additive changes**: Create `dev_scripts/add_[feature]_table.py`
   - **Structural changes**: Warn about data loss, create `dev_scripts/reset_database.py`

3. **Communicate what's preserved or lost:**
   - Migration: "✅ Your existing users and posts are preserved!"
   - Recreation: "⚠️ This change requires recreating the database. You'll need to create users again."

### Benefits of Migration Approach

- ✅ Students don't lose work when adding features
- ✅ Teaches realistic development workflow (migrations are industry standard)
- ✅ Reduces frustration from repeated user creation
- ✅ Faster iteration on features
- ✅ Better learning experience
- ✅ Still allows recreation when truly necessary

### Common Scenarios

| Feature | Strategy | Reason |
|---------|----------|--------|
| Adding Follow table | **Migrate** | New table, no changes to User/Post |
| Adding Like table | **Migrate** | New table, no changes to existing |
| Adding Comment table | **Migrate** | New table, no changes to existing |
| Adding User.bio column | **Migrate** | Nullable or has default value |
| Adding Post.image_path | **Migrate** | Nullable column |
| Making User.email required | **Recreate** | Existing users may have NULL emails |
| Changing Post.content length | **Recreate** | Existing posts may violate new constraint |
| Renaming User.username | **Recreate** | SQLite doesn't support column rename |
| Splitting User.display_name | **Recreate** | Requires data transformation |

## Common Development Commands

### Phase 1 (Flask)

```bash
# Setup (from repository root)
cd phase1

# Install dependencies
uv sync

# Install optional dependencies (choose based on auth method)
uv sync --extra google-oauth  # For Google OAuth
# OR
uv sync --extra email-auth    # For email magic links
# OR
uv sync --all-extras          # All optional features

# Initialize database (creates shared/database/app.db)
uv run flask shell
>>> from app import db
>>> db.create_all()
>>> exit()

# Run development server
uv run flask run

# Environment variables needed in .env:
# FLASK_APP=app.py
# FLASK_ENV=development
# SECRET_KEY=<generate with: uv run python -c "import secrets; print(secrets.token_hex(32))">
# DATABASE_URL=sqlite:///../shared/database/app.db  # Shared database
# (Plus auth-specific variables for Google OAuth or Email Magic Links)
# See SETUP-AUTHENTICATION.md for complete details
```

### Phase 2 Backend (FastAPI)

```bash
# Setup (from repository root)
cd phase2/backend

# Install dependencies
uv sync

# Run development server
uv run uvicorn main:app --reload

# Run with specific host/port
uv run uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Access interactive API documentation
# Visit: http://localhost:8000/docs (Swagger UI)
# Visit: http://localhost:8000/redoc (ReDoc)

# Run tests (if implemented)
uv run pytest

# Environment variables needed in .env:
# SECRET_KEY=<generate with: uv run python -c "import secrets; print(secrets.token_hex(32))">
# DATABASE_URL=sqlite:///../../../shared/database/app.db  # Reuse Phase 1 database (recommended)
# OR: DATABASE_URL=sqlite:///./phase2.db  # New database for Phase 2
# ALGORITHM=HS256
# ACCESS_TOKEN_EXPIRE_MINUTES=30
# CORS_ORIGINS=http://localhost:5173
# See SETUP-AUTHENTICATION.md for Phase 2 JWT details
```

### Phase 2 Frontend (React/Vue)

```bash
# Setup (from repository root)
cd phase2/frontend
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Architecture Patterns

### Phase 1: Monolithic Architecture

```
Browser
   ↓ HTTP Request
Flask App
   ├── Routes (URL handling)
   ├── Business Logic
   ├── Templates (Jinja2 server-side rendering)
   └── Session Management (Flask-Login)
   ↓
SQLite Database
```

**Key Characteristics**:
- Single Python application
- Server-side HTML rendering with Jinja2
- Session-based authentication (cookies)
- All logic in one codebase
- Simple deployment (single application)

### Phase 2: Separated Client-Server Architecture

```
Browser (React/Vue SPA)
   ↓ HTTP/JSON (AJAX requests)
FastAPI Backend
   ├── REST Endpoints (/api/*)
   ├── JWT Authentication (stateless)
   ├── Business Logic
   └── JSON Responses
   ↓
SQLite Database (can share with Phase 1)
```

**Key Characteristics**:
- Separated frontend and backend
- Client-side rendering (JavaScript)
- Token-based authentication (JWT)
- JSON API communication
- Independent deployments
- Supports multiple clients (web, mobile, etc.)

## Database Schema

Students design their own schema, but typical structure includes:

**Users Table**:
- `id` (primary key)
- `email` (unique)
- `username` (unique)
- `display_name`
- `created_at`

**Posts Table**:
- `id` (primary key)
- `user_id` (foreign key → users.id)
- `content` (text)
- `image_path` (optional)
- `created_at`

**Follows Table**:
- `id` (primary key)
- `follower_id` (foreign key → users.id)
- `followee_id` (foreign key → users.id)
- `created_at`
- UNIQUE constraint on (follower_id, followee_id)

**Invites Table** (or simple text file):
- List of allowed email addresses
- Can be `invites.txt` or database table

## REST API Design Principles

When working on Phase 2, follow these REST conventions:

- **Resources as nouns**: `/users`, `/posts` (not `/getUser`, `/createPost`)
- **HTTP methods for actions**:
  - GET: Retrieve data
  - POST: Create new resource
  - PUT/PATCH: Update resource
  - DELETE: Delete resource
- **Stateless**: Each request contains all needed info (JWT token in Authorization header)
- **JSON in/out**: Consistent data format
- **Standard HTTP status codes**:
  - 200 OK
  - 201 Created
  - 400 Bad Request
  - 401 Unauthorized
  - 404 Not Found
  - 500 Internal Server Error

## Authentication Approaches

**See SETUP-AUTHENTICATION.md for complete setup instructions.**

### Phase 1 (Flask)

**Three-tiered approach (students choose based on needs):**

1. **Development Mode (No Real Auth)**
   - Simple session with user_id
   - No password required
   - Fastest to implement (5 minutes)
   - ⚠️ NOT for public deployment
   - Easy migration path to real auth

2. **Google OAuth (Recommended)**
   - Uses Authlib library
   - No password management
   - Users log in with Google accounts
   - Setup: 15-20 minutes
   - Production-ready

3. **Email Magic Links (Advanced)**
   - Uses Flask-Mail + itsdangerous
   - Passwordless authentication
   - Requires email service setup (Gmail App Password or SendGrid)
   - Setup: 30-45 minutes
   - Production-ready with transactional email service

**All options use Flask-Login for session management (except dev mode).**

### Phase 2 (FastAPI)

**JWT (JSON Web Token) based authentication:**

- Stateless - no server-side sessions
- Token stored in frontend (localStorage)
- Token sent in `Authorization: Bearer <token>` header
- Token validated on each request using dependency injection

**Two-tiered approach:**

1. **Development Mode**
   - Username as "token" (no real JWT)
   - Fastest implementation
   - Easy to upgrade to real JWT

2. **Production Mode**
   - Real JWT tokens signed with SECRET_KEY
   - Token expiration (default 30 minutes)
   - Can reuse Phase 1 authentication flow (Google OAuth or Email Magic Links) to initially verify user, then issue JWT

**Initial authentication varies by student's choice:**
- **Google OAuth**: User authenticates with Google, backend exchanges OAuth code for user info, then issues JWT
- **Email Magic Links**: User requests login link via email, link contains temporary token that exchanges for JWT
- Once JWT is obtained, the flow is identical regardless of initial auth method

## Security Considerations

Even as prototypes, these applications should avoid basic security issues:

1. **SECRET_KEY Security**:
   - **CRITICAL**: Generate with `python -c "import secrets; print(secrets.token_hex(32))"`
   - Minimum 32 bytes (64 hex characters)
   - Never use predictable values like "change-this-to-a-random-secret-key"
   - Never commit to git (keep in `.env` which is in `.gitignore`)
   - If compromised: all sessions/tokens are vulnerable

2. **SQL Injection**: Always use SQLAlchemy ORM, never raw SQL with string concatenation

3. **XSS**: Jinja2 auto-escapes by default (don't use `|safe` on user content)

4. **Authentication**:
   - Development Mode: Only use locally, never deploy publicly
   - Use `werkzeug.security` for password hashing (if passwords are used)
   - JWT tokens should have reasonable expiration (30 minutes recommended)
   - Check invite list before allowing registration

5. **Email Configuration**:
   - **NEVER use actual Gmail password** - use App Passwords or transactional email service
   - Gmail App Passwords: Only for development/testing
   - Production: Use SendGrid, Mailgun, or similar service

6. **Environment Variables**: Never commit `.env` files to git

7. **File Uploads** (if implemented):
   - Validate file types
   - Limit file sizes
   - Sanitize filenames

8. **CORS**: Configure properly to allow only trusted origins

## Common Issues and Solutions

### Phase 1 Issues

**"Unable to open database file" or SQLite path errors**:
- **CRITICAL**: Always use absolute paths for SQLite databases, not relative paths in URIs
- ✅ **CORRECT approach** using `pathlib.Path`:
  ```python
  from pathlib import Path

  # Get absolute path to shared database directory
  DB_DIR = Path(__file__).parent.parent / "shared" / "database"
  DB_DIR.mkdir(parents=True, exist_ok=True)  # Ensure directory exists
  DB_PATH = DB_DIR / "app.db"

  # Use absolute path in SQLAlchemy URI
  app.config['SQLALCHEMY_DATABASE_URI'] = f'sqlite:///{DB_PATH}'
  ```
- ❌ **AVOID** relative paths in URIs like `sqlite:///../shared/database/app.db`
  - These depend on current working directory (ambiguous)
  - SQLAlchemy's URI parser may resolve them incorrectly
  - SQLite can create the DB file but NOT parent directories
- **Why absolute paths work better**:
  - Unambiguous - no dependency on where Python runs from
  - Explicit directory creation ensures parent dirs exist
  - Easier to debug (can print the path to verify location)

**"No such table" errors or database not being created**:
- **CRITICAL**: `db.create_all()` alone may not create the SQLite file - you must also execute a query or commit
- **CRITICAL**: Database initialization must happen AFTER routes.py is created to avoid import errors
  - `app.py` imports `routes`, so if routes.py doesn't exist, any script that imports from `database` or `app` will fail
  - **Correct order**: 1) Create models.py, 2) Create routes.py, 3) Create templates, 4) Initialize database
- ✅ **CORRECT approach** - use a dev script (see "Providing Multiline Python Code to Students" section):

  Create `dev_scripts/init_database.py`:
  ```python
  """Initialize the database with all tables."""
  from database import app, db
  from models import User, Post, Follow  # Import ALL models!

  with app.app_context():
      db.create_all()
      db.session.execute(db.text("SELECT 1"))  # Force SQLite to write file
      db.session.commit()
      print("✅ Database initialized successfully!")
      print(f"Tables created: {list(db.metadata.tables.keys())}")
  ```

  Then run: `uv run python dev_scripts/init_database.py`

- **Why this is needed**:
  - SQLite is lazy - it won't create the file until you actually write something
  - `db.create_all()` only creates the schema in memory
  - Must execute a query or commit to force file creation
  - Must import ALL models before `create_all()` so SQLAlchemy knows about them
- **Common mistakes**:
  - ❌ Forgetting to import all models (e.g., only importing User, Post but not Follow)
  - ❌ Not executing any query after `create_all()` (file won't be written)
  - ❌ Running `db.create_all()` from wrong directory (check `__file__` path)
- **To recreate database** (when adding new models):

  Create `dev_scripts/reset_database.py`:
  ```python
  """Reset database - deletes existing database and creates new one with all tables."""
  from database import app, db
  from models import User, Post, Follow  # Import ALL models
  import os

  with app.app_context():
      # Get the database path
      db_path = app.config['SQLALCHEMY_DATABASE_URI'].replace('sqlite:///', '')
      print(f"Database path: {db_path}")

      # Close any connections and delete old database
      db.session.remove()
      if os.path.exists(db_path):
          os.remove(db_path)
          print("✅ Old database deleted")

      # Create new database with all tables
      db.create_all()
      db.session.execute(db.text("SELECT 1"))
      db.session.commit()
      print(f"✅ New database created with tables: {list(db.metadata.tables.keys())}")
  ```

  Then run: `uv run python dev_scripts/reset_database.py`
- **Verify database was created**:
  - Check `shared/database/` directory for `app.db` file
  - If empty, the `db.session.execute()` + `commit()` steps were skipped
  - Verify all tables exist: `list(db.metadata.tables.keys())`

**Session/login issues**:
- Check `SECRET_KEY` is set in `.env` (64+ hex characters)
- Verify Flask-Login configuration (if using real auth)
- Ensure `@login_required` decorator is used on protected routes
- For dev mode: check session setup is correct

**Template not found**:
- Verify templates are in `phase1/templates/` folder
- Check spelling in `render_template()` calls

**Stylesheet (style.css) not loading**:
- **CRITICAL**: Do NOT copy `shared/static/style.css` to `phase1/static/` or `phase2/frontend/`
- The stylesheet must remain in `shared/static/` so both phases can use it
- **Solution**: Configure Flask to serve it from the shared directory:
  ```python
  # In database.py
  from pathlib import Path
  from flask import send_from_directory

  SHARED_STATIC = Path(__file__).parent.parent / "shared" / "static"

  @app.route('/shared/static/<path:filename>')
  def shared_static(filename):
      return send_from_directory(SHARED_STATIC, filename)
  ```
- **In templates**: Use `<link rel="stylesheet" href="/shared/static/style.css">`
- **DO NOT use**: `url_for('static', filename='style.css')` (this looks in phase1/static/)
- Verify the route is defined in `database.py` before importing routes

**Routes return 404 even though they're defined (Circular Import Issue)**:
- **CRITICAL**: This is the most common Flask architecture issue for beginners
- **Symptom**: Routes are defined with `@app.route()` decorators but return 404 when accessed
- **Root cause**: Circular import between `app.py` and `routes.py` prevents routes from registering
- **The Problem**:
  ```python
  # In app.py
  from flask import Flask
  app = Flask(__name__)
  import routes  # This imports routes.py

  # In routes.py
  from app import app  # This tries to import app.py
  @app.route('/')     # But app.py hasn't finished executing!
  def index():        # So this decorator runs on a partially-initialized app
      return "Hello"
  ```
  When Python encounters the circular dependency, it can't properly initialize both modules, and route decorators may run before the app object is fully ready.

- ✅ **SOLUTION**: Break the circular dependency with a separate database module
  ```python
  # Create database.py (NEW FILE)
  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy

  app = Flask(__name__)
  # ... configure app ...
  db = SQLAlchemy(app)

  # Update routes.py
  from database import app, db  # Import from database, not app
  @app.route('/')
  def index():
      return "Hello"

  # Update models.py
  from database import db  # Import from database, not app

  # Update app.py (main entry point)
  from database import app, db
  import routes  # Now this works - no circular dependency!

  if __name__ == '__main__':
      app.run(debug=True)
  ```

- **Why this works**:
  - `database.py` creates app and db (no imports from other project files)
  - `routes.py` imports from `database.py` (one-way dependency)
  - `models.py` imports from `database.py` (one-way dependency)
  - `app.py` imports from `database.py` and then `routes.py` (no circular dependency)
  - Import chain: `app.py` → `database.py` → nothing, and `app.py` → `routes.py` → `database.py` (already loaded)

- **How to debug**:
  ```python
  # Add this to app.py after importing routes
  print("Registered routes:")
  for rule in app.url_map.iter_rules():
      print(f"  {rule.rule} -> {rule.endpoint}")
  ```
  If you only see `/static/<path:filename>`, you have a circular import issue.

- **Alternative solutions** (less recommended for beginners):
  - Use Flask application factory pattern (more complex)
  - Import routes inside functions instead of at module level (harder to maintain)
  - Put all routes in app.py (doesn't scale well)

### Phase 2 Issues

**CORS Errors**:
- Add CORS middleware to FastAPI with correct origins
- For development, allow `http://localhost:5173` (Vite default)

**401 Unauthorized even after login**:
- Verify token is stored in localStorage
- Check Authorization header format: `Bearer <token>`
- Confirm token hasn't expired

**SQLite database locked**:
- SQLite has limited concurrent write support
- Ensure connections are properly closed
- Consider connection pooling settings

**Frontend can't connect to backend**:
- Check backend is running on expected port
- Verify API base URL in frontend matches backend address
- Check CORS configuration

## Deployment

Both phases should be deployed to **PythonAnywhere** (or alternative platforms):

- See `deployment-guide.md` for detailed instructions
- Phase 1: Single Flask app deployment
- Phase 2: Backend on PythonAnywhere, frontend can be on Vercel/Netlify or served from same host
- Update environment variables for production
- Update CORS settings for production domains

## Documentation Requirements

Students must create/complete these documentation files:

**`phase1/DECISIONS-MADE-phase1.md`** (updated throughout Phase 1):
- **Part 1**: Visual design choices (app name, color scheme)
- **Part 5**: Additional feature choice and design
- **Part 5 (if Real Authentication chosen)**: Invite system and access control decisions
- **Throughout**: Any additional architectural decisions made during implementation
- Template file pre-exists, Claude updates as student makes decisions
- Note: Invite/access control decisions only made if student adds real authentication

**`phase2/DECISIONS-MADE-phase2.md`** (updated in Phase 2, Part 1):
- Frontend framework choice and reasoning
- Authentication method choice and reasoning
- Database strategy (reuse vs new) and reasoning
- Core features selected (3-4) and reasoning
- Access control model and reasoning
- Any additional architectural decisions made during implementation
- Template file pre-exists, Claude updates as student makes decisions

**`ARCHITECTURE.md`** (completed throughout both phases):
1. Database schema documentation
2. REST API endpoint design (Phase 2)
3. Authentication flow explanation
4. Testing strategy (design even if not implemented)
5. Security considerations
6. Monitoring approach (design even if not implemented)
7. Phase 1 vs Phase 2 comparison
8. AI tool usage reflection

**Purpose of DECISIONS-MADE files**:
- Provides context if student starts a new Claude conversation
- Reference for student during implementation
- Submitted as part of assignment
- Demonstrates understanding of architectural trade-offs

## Timeline

- **Phase 1**: 1-1.5 hours (rapid prototyping)
- **Phase 2**: 2-2.5 hours (REST architecture)
- **Total**: 3-4 hours with AI assistance

## Key Pedagogical Points

When helping students:

1. **Focus on architecture understanding** over perfect implementation
2. **Phase 2 reduced scope is intentional** - quality over quantity
3. **Testing and security**: Students should design/document strategies, not necessarily implement comprehensive solutions
4. **AI tool usage**: This is a core learning objective - encourage effective prompting
5. **Iteration is expected**: Code doesn't need to be perfect, progression matters
6. **Comparison is key**: Understanding trade-offs between monolithic and separated architectures

## Working with TODO Files

**When students prompt you with TODO files**:

1. **Read the entire TODO file first** to understand the structure and workflow
2. **Follow the 🤖 For Claude/AI Assistants section** - these are instructions for you
3. **Follow the conversational scripts exactly** (Phase 1 uses this approach):
   - Scripts are marked with **🤖 Claude: Say exactly:** or **🤖 Claude: Say:**
   - These are designed for pedagogy, not just speed
   - **Stop at every 🛑 marker** and wait for student response
   - Don't generate code until student indicates understanding
   - This is a **teaching conversation**, not just code generation
4. **Update DECISIONS-MADE files** throughout:
   - Files already exist: `phase1/DECISIONS-MADE-phase1.md` and `phase2/DECISIONS-MADE-phase2.md`
   - Update each section as student makes decisions or completes design work
   - Document their reasoning and understanding
5. **Check understanding at key points**:
   - Phase 1: Student reviews Post model, create post route, homepage template
   - Ask student to explain concepts in their own words
   - Don't proceed until confusion is resolved
6. **For Phase 1 specifically**:
   - Part 3 is the **KEY pedagogical section** - teach objects/actions/views framework
   - Part 5.1 is **CRITICAL** - explain feature complexity BEFORE student commits
     - For Real Authentication: Explain external service setup, time requirements
     - For any feature: Explain what will be built, complexity level, time estimate
     - Let student compare features or change their choice
     - If Real Authentication chosen: Make invite/access control decisions before implementing
   - Part 5.2 requires **student to articulate the design** before you implement
   - Part 7 is **code review as learning** - find issues but don't auto-fix
7. **File locations**:
   - Phase 1 code: `phase1/` directory
   - Phase 2 backend: `phase2/backend/` directory
   - Phase 2 frontend: `phase2/frontend/src/` directory
   - Shared database: `shared/database/app.db`
   - Shared invites: `shared/invites.txt`
   - Shared styles: `shared/static/style.css`

**If student starts new conversation**:
- Ask them to show you their `DECISIONS-MADE-phase1.md` or `DECISIONS-MADE-phase2.md` file for context
- This will help you understand their architectural choices and progress
- Look for which Part they were on in the TODO
- Continue from where they left off

## Technologies Used

**Backend**:
- Flask 3.0+ (Phase 1)
- FastAPI 0.104+ (Phase 2)
- SQLAlchemy 2.0+
- SQLite database
- python-jose (JWT)
- Uvicorn (ASGI server for FastAPI)

**Frontend** (Phase 2):
- React or Vue (student choice)
- Vite (build tool)
- Axios or fetch (HTTP client)

**Authentication**:
- Flask-Login (Phase 1)
- Authlib (Google OAuth)
- itsdangerous (email magic links)
- python-jose (JWT for Phase 2)

## Testing

While comprehensive testing is optional, students should design test strategies:

**Backend Testing**:
- Unit tests for models and business logic
- API endpoint tests with `pytest` and `httpx`
- Test fixtures for database
- Authentication flow tests

**Frontend Testing**:
- Component tests with Jest/Vitest
- React Testing Library
- API client mocking
- Integration tests for user flows

## Additional Context

- This is Week 11 of CMSC 398z
- Students have Python/Java/C background but typically no web development experience
- Students have been using AI coding tools for 8-10 weeks prior
- The project validates that Flask prototypes can be built in "well under an hour" with AI tools (CMU experience)
- Architecture comparison is the primary learning objective
