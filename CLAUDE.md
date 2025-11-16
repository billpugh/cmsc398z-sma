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
├── ARCHITECTURE.md                      # Technical documentation (Claude maintains)
├── REFLECTIONS.md                       # Student reflection (student writes at end)
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

**Documentation files**:
- **DECISIONS-MADE files** (`phase1/DECISIONS-MADE-phase1.md` and `phase2/DECISIONS-MADE-phase2.md`):
  - Updated by Claude as student makes each decision
  - Documents student's architectural choices with reasoning
  - Useful if student starts a new Claude conversation (provides context)
- **ARCHITECTURE.md** (root directory):
  - Technical reference maintained by Claude as features are implemented
  - Documents actual database schema, API endpoints, routes, features built
  - Think of it as the technical specification of what was actually built
- **REFLECTIONS.md** (root directory):
  - Student-written reflection filled out after both phases complete
  - Compares Phase 1 vs Phase 2 from student's perspective
  - Lessons learned, what worked well/poorly with AI tools
  - Main deliverable for demonstrating understanding

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

## Development Patterns

**See DEV-PATTERNS.md** for detailed guidance on:
- **The `dev_scripts/` directory pattern** - How to provide multiline Python code to students without copy-paste errors
- **Database migrations vs. recreation** - When to preserve student data vs. when to recreate the database

**Key principles:**
- Never ask students to copy-paste multiline code from chat
- Create scripts in `dev_scripts/` using the Write tool, then run with `uv run`
- Prefer database migrations over recreation to preserve student work
- Use migrations for additive changes (new tables, nullable columns)
- Only recreate database for structural changes (renaming columns, changing types)

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

## Troubleshooting

**See TROUBLESHOOTING.md** for detailed solutions to common issues.

When students encounter errors, use the Read tool to access relevant sections from TROUBLESHOOTING.md. The file covers:

**Phase 1 Issues:**
- Database file path errors (use absolute paths)
- "No such table" errors (db.create_all() + execute query)
- Session/login issues
- Template not found
- Stylesheet not loading
- Routes return 404 (circular import issue)

**Phase 2 Issues:**
- CORS errors
- 401 Unauthorized after login
- SQLite database locked
- Frontend can't connect to backend

## Deployment

Both phases should be deployed to **PythonAnywhere** (or alternative platforms):

- See `deployment-guide.md` for detailed instructions
- Phase 1: Single Flask app deployment
- Phase 2: Backend on PythonAnywhere, frontend can be on Vercel/Netlify or served from same host
- Update environment variables for production
- Update CORS settings for production domains

## Documentation Structure

This project uses three types of documentation with different purposes:

### 1. DECISIONS-MADE Files (Claude maintains during decisions)

**`phase1/DECISIONS-MADE-phase1.md`** - Updated by Claude as student makes choices:
- **Part 1**: Visual design choices (app name, color scheme)
- **Part 3**: Minimal features architecture (objects/actions/views)
- **Part 5**: Additional feature choice and design
- **Part 5 (if Real Authentication chosen)**: Invite system and access control decisions
- **Throughout**: Any architectural decisions made during implementation

**`phase2/DECISIONS-MADE-phase2.md`** - Updated by Claude in Phase 2 Part 1:
- Frontend framework choice and reasoning
- Authentication method choice and reasoning
- Database strategy (reuse vs new) and reasoning
- Core features selected (3-4) and reasoning
- Access control model and reasoning

**Purpose**: Provides context if student starts a new Claude conversation. You should reference these when resuming work.

### 2. ARCHITECTURE.md (Claude maintains during implementation)

**Location**: Root directory

**Updated by**: Claude, as features are implemented

**Contains**:
- What was actually built (not aspirational)
- Database schema (actual table definitions)
- Routes/API endpoints implemented
- Features completed
- Authentication & access control implementation details
- Security measures actually in place (and known limitations)
- Testing approach actually used
- Deployment configuration (if deployed)
- Technical decisions summary (references DECISIONS-MADE files for rationale)

**Purpose**: Technical reference document showing what the project actually contains. Think of it as the project's technical specification.

**Important**: Do NOT duplicate content from DECISIONS-MADE files. Reference them instead. Focus on documenting the actual implementation.

### 3. REFLECTIONS.md (Student writes at the end)

**Location**: Root directory

**Written by**: Student (after completing both phases)

**Contains**:
- Phase 1 experience (what went well, what was challenging)
- Phase 2 experience (what went well, what was challenging)
- Comparison of the two architectures from student's perspective
- What they preferred and why
- When to use each architecture
- Working with AI tools (what worked, what didn't, prompting strategies)
- Technical growth and learning
- Self-assessment
- Overall reflection

**Purpose**: Main deliverable demonstrating student's understanding of architectural trade-offs and reflection on the learning process.

**Important**: This is the student's voice, not Claude's. Claude should NOT write this file.

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
4. **Update documentation as you work**:
   - **DECISIONS-MADE files** (`phase1/DECISIONS-MADE-phase1.md`, `phase2/DECISIONS-MADE-phase2.md`):
     - Update as student makes each decision
     - Document their reasoning and choices
   - **ARCHITECTURE.md** (root):
     - Update after implementing each major feature
     - Document actual database schema after models are created
     - Document routes/endpoints after they're implemented
     - Keep it factual - what was actually built, not plans
   - **REFLECTIONS.md** (root):
     - Do NOT write this file - student writes it at the end
     - Remind student to fill it out after completing both phases
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
