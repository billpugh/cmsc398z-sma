# Week 11: Web Application Architecture Project

Build the same social media application twice - first as a Flask monolithic app, then as a REST API + JavaScript frontend - to understand architectural trade-offs.

## 🚀 Quick Start

### Prerequisites
- Python 3.9+ with `uv` (or `pip`)
- Node.js 18+ and npm (for Phase 2 frontend)
- Claude Code or another AI coding assistant

### Getting Started

1. **Open this repository in Claude Code (or your AI tool)**
2. **Start with Phase 1** - See instructions below

---

## Phase 1: Flask Monolithic Application

**Goal**: Build a working social media prototype in 1-1.5 hours with server-side rendering.

### Student Workflow

1. **Read the architecture guide** (15 minutes):
   ```
   Open and read: ARCHITECTURE-GUIDE-phase1.md
   ```
   This explains the WHY and WHAT you're building.

2. **Start working with Claude**:
   ```
   Tell Claude:
   "Read TODO-phase1.md and guide me through Phase 1"
   ```

3. **Claude will guide you through**:
   - Making key architectural decisions (authentication method, access control)
   - Setting up your environment
   - Creating database models
   - Implementing features
   - Testing and debugging

4. **Your files go in `phase1/`**:
   - `phase1/app.py` - Main Flask application
   - `phase1/models.py` - Database models
   - `phase1/auth.py` - Authentication logic
   - `phase1/templates/` - HTML templates
   - `phase1/static/` - CSS, uploads

5. **Shared resources**:
   - `shared/database/app.db` - SQLite database (auto-created)
   - `shared/invites.txt` - List of invited emails

### Key Files for Phase 1

| File | Purpose |
|------|---------|
| `TODO-phase1.md` | Step-by-step implementation checklist (work through with Claude) |
| `ARCHITECTURE-GUIDE-phase1.md` | Architectural concepts and learning objectives |
| `SETUP-AUTHENTICATION.md` | Detailed authentication setup guide |
| `phase1/.env.example` | Environment variables template |

---

## Phase 2: REST API + JavaScript Frontend

**Goal**: Rebuild with separated architecture (2-2.5 hours) to understand REST API design and client-server separation.

### Student Workflow

1. **Still in the repository root** (Claude context unchanged!)

2. **Read the Phase 2 architecture guide** (15 minutes):
   ```
   Open and read: ARCHITECTURE-GUIDE-phase2.md
   ```

3. **Start Phase 2 with Claude**:
   ```
   Tell Claude:
   "Read TODO-phase2.md and guide me through Phase 2"
   ```

4. **Your files go in `phase2/`**:
   - `phase2/backend/main.py` - FastAPI application
   - `phase2/backend/models.py` - Database models (can adapt from Phase 1)
   - `phase2/frontend/src/` - React/Vue components

5. **Shared resources** (same as Phase 1):
   - `shared/database/app.db` - Reuse same database to see your Phase 1 data in the API!
   - `shared/invites.txt` - Same invite list

### Key Files for Phase 2

| File | Purpose |
|------|---------|
| `TODO-phase2.md` | Step-by-step implementation checklist (work through with Claude) |
| `ARCHITECTURE-GUIDE-phase2.md` | REST architecture concepts |
| `SETUP-AUTHENTICATION.md` | JWT authentication for Phase 2 |
| `phase2/backend/.env.example` | Backend environment variables |
| `phase2/frontend/package.json` | Frontend dependencies |

---

## Project Structure

```
├── README.md                            # This file - project overview
├── TODO-phase1.md                       # Phase 1 implementation guide ⭐
├── TODO-phase2.md                       # Phase 2 implementation guide ⭐
├── ARCHITECTURE-GUIDE-phase1.md         # Phase 1 concepts
├── ARCHITECTURE-GUIDE-phase2.md         # Phase 2 concepts
├── SETUP-AUTHENTICATION.md              # Authentication setup (both phases)
├── deployment-guide.md                  # Deployment instructions
├── ARCHITECTURE.md                      # Template for your documentation
│
├── shared/                              # Shared resources
│   ├── database/
│   │   └── app.db                       # SQLite database (created when you run Phase 1)
│   └── invites.txt                      # List of invited emails
│
├── phase1/                              # Phase 1: Flask app
│   ├── .env.example                     # Environment template
│   ├── .gitignore
│   ├── pyproject.toml                   # Python dependencies
│   ├── app.py                           # YOU CREATE: Main Flask app
│   ├── models.py                        # YOU CREATE: Database models
│   ├── auth.py                          # YOU CREATE: Authentication
│   ├── templates/                       # YOU CREATE: HTML templates
│   │   ├── base.html
│   │   ├── index.html
│   │   └── ...
│   └── static/                          # YOU CREATE: CSS, uploads
│       ├── css/style.css
│       └── uploads/
│
└── phase2/                              # Phase 2: REST API + Frontend
    ├── backend/                         # FastAPI backend
    │   ├── .env.example
    │   ├── .gitignore
    │   ├── pyproject.toml
    │   ├── main.py                      # YOU CREATE: FastAPI app
    │   ├── models.py                    # YOU CREATE: Database models
    │   └── ...
    └── frontend/                        # React/Vue frontend
        ├── package.json
        ├── vite.config.js
        ├── index.html
        └── src/                         # YOU CREATE: Components
            └── ...
```

---

## How This Works with Claude

### Claude Can See Everything

When you open this repository in Claude Code, Claude has access to:
- ✅ All documentation files (TODO, guides, setup instructions)
- ✅ Your Phase 1 code (when working on Phase 2)
- ✅ Shared resources (database, invites)
- ✅ Environment variable templates

### Claude-Guided Workflow

**Phase 1 Example:**
```
You: Read TODO-phase1.md and guide me through Phase 1.

Claude: I'll help you build a Flask social media app. I see from TODO-phase1.md
        that we need to make 3 key decisions first. Let's start:

        Decision 1: Authentication Method
        Which would you like:
        - Option A: Development Mode (5 min) - For local testing
        - Option B: Google OAuth (15-20 min) - For portfolio/production
        - Option C: Email Magic Links (30-45 min) - Advanced

You: Let's do Option A for now, I want to focus on features first.

Claude: Great choice! Development mode is perfect for learning. Now let's set up
        your environment. First, let's create a virtual environment in the phase1/
        directory...

[Claude guides you through each step of TODO-phase1.md]
```

### Student Makes Key Decisions

Items marked with 🎯 in the TODO files are decisions **you** should make:
- Authentication method (dev mode, Google OAuth, email magic links)
- Access control model (fully private vs. public reading)
- Invite system approach (file vs. database)

Claude asks you about these and implements based on your choices.

---

## Features You'll Build

### Phase 1 (All features):
- ✅ User authentication (your choice of method)
- ✅ Post creation and display (280 char limit)
- ✅ User profiles
- ✅ Following system
- ✅ Personalized feed
- ✅ Invite-only access control

### Phase 2 (Focus on 3-4 core features):
- ✅ REST API backend (FastAPI)
- ✅ JavaScript frontend (React or Vue)
- ✅ JWT authentication
- ✅ Subset of Phase 1 features with clean API design

**Important**: Phase 2 does NOT require feature parity with Phase 1. Focus on quality over quantity.

---

## Expected Timeline

- **Phase 1**: 1-1.5 hours (rapid prototyping)
- **Phase 2**: 2-2.5 hours (architectural refactor)
- **Documentation**: 30-45 minutes (fill out ARCHITECTURE.md)
- **Total**: 3-4 hours with AI assistance

---

## Documentation Requirements

As you work, you'll fill out `ARCHITECTURE.md` with:
1. Database schema documentation
2. REST API endpoint design (Phase 2)
3. Authentication flow explanation
4. Testing strategy
5. Security considerations
6. Phase 1 vs Phase 2 comparison
7. AI tool usage reflection

---

## Common Commands Quick Reference

### Phase 1 (Flask)

```bash
# Setup
cd phase1
uv sync

# Configure
cp .env.example .env
# Edit .env with your values

# Initialize database
uv run flask shell
>>> from app import db
>>> db.create_all()
>>> exit()

# Run
uv run flask run
# Visit: http://localhost:5000
```

### Phase 2 Backend (FastAPI)

```bash
# Setup
cd phase2/backend
uv sync

# Configure
cp .env.example .env
# Edit .env

# Run
uv run uvicorn main:app --reload
# API docs: http://localhost:8000/docs
```

### Phase 2 Frontend (React/Vue)

```bash
# Setup
cd phase2/frontend
npm install

# Run
npm run dev
# Visit: http://localhost:5173
```

---

## Learning Objectives

By completing both phases, you'll understand:

### Technical Skills
- Flask vs FastAPI
- Server-side rendering vs client-side rendering
- Session-based auth vs JWT tokens
- SQLAlchemy ORM
- REST API design
- JavaScript frontend frameworks

### Architectural Concepts
- Monolithic vs separated architectures
- Stateful vs stateless authentication
- API design principles
- Client-server communication
- Database reuse across architectures

### AI-Assisted Development
- Effective prompting
- Iterative development with AI
- When to let AI generate vs when to decide yourself
- Understanding vs just generating code

---

## Getting Help

### Documentation
1. **TODO files** - Step-by-step guides with prompts for Claude
2. **ARCHITECTURE-GUIDE files** - Conceptual understanding
3. **SETUP-AUTHENTICATION.md** - Detailed auth setup
4. **Claude/AI** - Ask questions, paste errors, request explanations

### Common Issues
- Check the TODO files - they include troubleshooting sections
- Ask Claude to explain concepts you don't understand
- Paste error messages to Claude with relevant code context

### Human Help
- Office hours
- Piazza
- Check with classmates

---

## Tips for Success

1. **Start with Phase 1** - Don't skip to Phase 2
2. **Make decisions yourself** (🎯 marked items) - Don't let AI choose
3. **Understand the code** - Ask Claude to explain anything unclear
4. **Test frequently** - After each feature, verify it works
5. **Commit to git** - Save progress regularly
6. **Reuse the database** - See your Phase 1 data in Phase 2 API
7. **Compare architectures** - Reflect on trade-offs as you build

---

## What Makes This Project Special

- **Same features, two architectures** - Direct comparison of approaches
- **AI-assisted but student-driven** - You make key decisions, AI implements
- **Practical learning** - Build real deployable apps, not toy examples
- **Portfolio-ready** - Choose authentication and access control for public deployment
- **Architectural insight** - Understand WHY different patterns exist

---

**Ready to start? Tell Claude:**

```
Read TODO-phase1.md and guide me through Phase 1
```

**Good luck! 🚀**
