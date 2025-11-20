# Phase 2 REST API + Frontend - Implementation TODO

**Goal**: Rebuild with separated architecture (2-2.5 hours) to understand REST API design and client-server separation.

## 🤖 For Claude/AI Assistants

This file is designed to guide you through helping a student build a REST API (FastAPI) + JavaScript frontend application. Here's how to use it:

1. **Ask the student to make key decisions first** (Part 1 - marked with 🎯)
   - Don't proceed until they've made these choices
   - These are architectural decisions the student should understand
   - Emphasize that **Phase 2 does NOT require feature parity with Phase 1**
   - Focus on 3-4 core features with clean REST API design

2. **Guide them through tasks sequentially** (Parts 2-8)
   - Read each section before implementing
   - Explain what you're about to do and why
   - Generate code only after the student understands the task
   - Help them reference Phase 1 code for logic they can adapt

3. **Encourage understanding, not just generation**
   - Ask if they want explanations of REST principles
   - Explain JWT vs session-based auth differences
   - Suggest testing after each feature (use FastAPI's /docs)
   - Help debug errors with explanations

4. **File locations matter:**
   - Backend code goes in `phase2/backend/` directory
   - Frontend code goes in `phase2/frontend/src/` directory
   - Can reuse database: `shared/database/app.db` (recommended to see Phase 1 data)
   - Can reference Phase 1 code: `phase1/` directory
   - Documentation is at top level (`README.md`, `SETUP-AUTHENTICATION.md`, etc.)

5. **IMPORTANT: Record decisions**
   - In Part 1, update `phase2/DECISIONS-MADE-phase2.md` as student makes each decision
   - This documents their architectural choices
   - Useful if they start a new Claude conversation
   - Will be submitted as part of assignment

---

## For Students

**How to use this file with Claude:**
1. Tell Claude: "Read TODO-phase2.md and guide me through Phase 2. I can reference my Phase 1 code. Ask me for decisions marked with 🎯."
2. Claude will ask you questions and help implement based on your choices
3. Check off items as you complete them
4. Test frequently using FastAPI's interactive docs at `/docs`!

**Reminder**: Phase 2 is about architecture, not feature parity. Pick 3-4 core features and implement them well.

---

## Prerequisites

- [ ] Completed Phase 1 (or understand the features from `phase1/` code)
- [ ] Read `ARCHITECTURE-GUIDE-phase2.md` to understand REST architecture
- [ ] Review `SETUP-AUTHENTICATION.md` JWT section for Phase 2 auth
- [ ] Have Node.js 18+ and npm installed (for frontend)

---

## Part 0: Shared Models Migration Check (IMPORTANT!)

**🤖 Claude: Before starting Phase 2, check if the student has migrated to shared models.**

**🤖 Claude: Do the following checks:**

1. Check if `shared/models.py` exists
2. Check if `shared/database.py` exists
3. Check if `phase1/models.py` still exists (indicates they haven't migrated)

**🤖 Claude: Based on the checks, say ONE of the following:**

### If shared models DON'T exist (typical case):

> **Before we start Phase 2, I recommend migrating to shared models.** This will:
> - Eliminate duplicate code between Phase 1 and Phase 2
> - Ensure both phases use identical database schema
> - Make maintenance much easier
> - Teach better code organization principles
>
> **The migration takes 15-20 minutes** and involves:
> 1. Creating a `shared/` Python package with your models
> 2. Updating Phase 1 to import from `shared/` instead of local `models.py`
> 3. Testing that Phase 1 still works
> 4. (Then Phase 2 will automatically use the same models)
>
> **Would you like to do the shared migration now, or skip it?**
>
> - **Option A**: Do the migration now (recommended) - I'll guide you through TODO-SHARED-MIGRATION.md
> - **Option B**: Skip for now - Phase 2 will have its own models (you'll need to keep them in sync manually)

🛑 **Wait for student's choice**

**🤖 Claude: If they choose Option A:**
> Great choice! Let's walk through the shared migration. I'll read TODO-SHARED-MIGRATION.md and guide you through it step by step.

**Then follow TODO-SHARED-MIGRATION.md completely before returning to this file.**

**🤖 Claude: If they choose Option B:**
> Understood. We'll proceed with Phase 2 having its own separate models. Just be aware that if you change your database schema, you'll need to update models in both `phase1/models.py` and `phase2/backend/models.py`.

**Then proceed to Part 1 below.**

### If shared models ALREADY exist:

> **Great! I can see you've already migrated to shared models.** Your `shared/models.py` and `shared/database.py` files are ready.
>
> This means:
> - ✅ Phase 2 will automatically use the same models as Phase 1
> - ✅ Both phases share the same database schema
> - ✅ No need to keep multiple model files in sync
>
> Let's proceed with Phase 2!

**Then proceed to Part 1 below.**

### If student already started Phase 2 without migration:

**🤖 Claude: If you see both `phase1/models.py` AND `phase2/backend/models.py` exist, say:**

> **I notice you've already started Phase 2 with separate models.** You have two options:
>
> **Option A**: Migrate both phases to shared models now (recommended)
> - Takes 20-25 minutes
> - Eliminates code duplication
> - Prevents schema drift between phases
> - See TODO-SHARED-MIGRATION.md Part 5 for migration with existing Phase 2
>
> **Option B**: Keep separate models (not recommended)
> - Faster to continue with current setup
> - But you'll need to manually keep both model files in sync
> - Risk of schema mismatches between phases
>
> Which would you prefer?

🛑 **Wait for student's choice, then proceed accordingly.**

---

- [ ] Shared models migration check completed
- [ ] Decision made: migrated OR proceeding with separate models

---

## Part 1: Key Decisions

🤖 **Claude: Follow the "Multiple Related Decisions Flow" pattern from CLAUDE.md. Summary:**

1. **First, present an overview** of the five decisions without going into options yet:
   - Frontend Framework - React vs Vue
   - Authentication Method - Development mode vs Production JWT
   - Database Strategy - Reuse Phase 1 database vs create new one
   - Core Features to Implement - Which 3-4 features to focus on (emphasize Phase 2 does NOT need feature parity)
   - Access Control Model - Fully private vs public reading
   - Ask if they have questions about what these decisions involve

2. **Then present each decision one by one:**
   - Show all options with advantages/disadvantages for that specific decision
   - Wait for their choice
   - Immediately update `phase2/DECISIONS-MADE-phase2.md` with their choice and reasoning
   - Move to next decision

3. **After all decisions are made:**
   - Summarize their choices
   - Ask if they want to revisit any decisions before proceeding
   - Verify `phase2/DECISIONS-MADE-phase2.md` is complete

**Do not proceed with implementation until all decisions are finalized.**

---

### 🎯 Decision 1: Frontend Framework

**Choose ONE**:

- [ ] **React** (More popular, larger ecosystem)
  - Component-based with hooks
  - Larger community and job market
  - More resources and tutorials

- [ ] **Vue** (Gentler learning curve)
  - Component-based with composition API
  - Simpler syntax, easier to learn
  - Good for rapid prototyping

---

### 🎯 Decision 2: Authentication Method

**Choose ONE**:

- [ ] **Development Mode** (Fastest - 10 minutes)
  - Username as "token" (no real JWT)
  - Easy to test API endpoints
  - ⚠️ ONLY for local development
  - Can upgrade to real JWT later

- [ ] **Production JWT** (Recommended for portfolio - 20-30 minutes)
  - Real JWT tokens signed with SECRET_KEY
  - Stateless authentication
  - Production-ready
  - Can reuse Phase 1's Google OAuth or Email Magic Links for initial login

---

### 🎯 Decision 3: Database Strategy

**Choose ONE**:

- [ ] **Reuse Phase 1 Database** (Recommended)
  - Path: `shared/database/app.db`
  - See your Phase 1 data in the API!
  - Can test with existing users/posts
  - Good for understanding data persistence

- [ ] **Create New Phase 2 Database**
  - Fresh start with clean data
  - Path: `phase2/backend/phase2.db`
  - Useful if Phase 1 database has issues

---

### 🎯 Decision 4: Core Features to Implement

**Choose 3-4 features** from your Phase 1 app (Phase 2 does NOT need full feature parity):

**Recommended minimum set:**
- [ ] User authentication (login/logout)
- [ ] View posts (list and individual)
- [ ] Create new post
- [ ] User profiles

**Additional features (choose 0-2):**
- [ ] Follow/unfollow users
- [ ] Personalized feed (your posts + followed users)
- [ ] Edit/delete posts
- [ ] Search functionality
- [ ] Image uploads

---

### 🎯 Decision 5: Access Control Model

**Should match or adapt your Phase 1 decision:**

- [ ] **Fully Private** - All API endpoints require authentication
- [ ] **Public Reading, Private Writing** - GET endpoints public, POST/PUT/DELETE require auth

---

- [ ] All five decisions recorded in `phase2/DECISIONS-MADE-phase2.md`

---

## Part 1.5: Visual Design - Reuse from Phase 1

🤖 **Claude: Phase 2 should use the same visual design (app name, colors, logo) from Phase 1 for consistency.**

**Note for students**: Your Phase 1 visual design is in:
- CSS file: `shared/static/style.css`
- Design choices documented in: `phase1/DECISIONS-MADE-phase1.md`

**For Phase 2 frontend**:
- Import the same `shared/static/style.css` file
- Use the same app name and logo
- Maintain visual consistency between architectures

🤖 **Claude: When building the Phase 2 frontend, reference `shared/static/style.css` and the app name from Phase 1. No need to create new styles - the shared CSS already works for both phases!**

- [ ] Confirmed visual design from Phase 1 will be reused
- [ ] `shared/static/style.css` will be imported in Phase 2 frontend

---

## Part 2: Backend Setup (FastAPI)

### Task 2.1: Install Backend Dependencies

```bash
# Navigate to the backend directory and sync dependencies:
cd phase2/backend
uv sync
```

**Note**: `uv` automatically manages the virtual environment - no need to manually create or activate it.

- [ ] Dependencies installed
- [ ] No installation errors

---

### Task 2.2: Configure Backend Environment

```bash
# Copy example environment file:
cp .env.example .env

# Generate SECRET_KEY (IMPORTANT - see SETUP-AUTHENTICATION.md):
uv run python -c "import secrets; print(secrets.token_hex(32))"

# Edit .env and paste the generated key:
# - Replace SECRET_KEY value with output from above command
# - Set DATABASE_URL based on your Decision 3
# - Configure CORS_ORIGINS for your frontend (default: http://localhost:5173)
```

🤖 **Claude: Help the student edit their `.env` file based on their decisions. Emphasize that SECRET_KEY MUST be replaced with a real random value.**

**Key environment variables to set:**
- `SECRET_KEY` - Generated random value (64 hex chars)
- `DATABASE_URL` - Path to database (shared or new)
- `CORS_ORIGINS` - Frontend URL (default Vite: http://localhost:5173)
- `ACCESS_TOKEN_EXPIRE_MINUTES` - JWT expiration (default: 30)

- [ ] `.env` file created from template
- [ ] SECRET_KEY generated and set (64+ hex characters, NOT the example value!)
- [ ] DATABASE_URL set correctly based on Decision 3
- [ ] CORS_ORIGINS includes frontend dev server URL

---

## Part 3: Backend Implementation - Database Models

🤖 **Claude: Help the student create `phase2/backend/models.py`. They can adapt models from `phase1/models.py` but need to ensure they work with SQLAlchemy for FastAPI (similar but slightly different from Flask-SQLAlchemy). If they're reusing the Phase 1 database, the models should match the existing schema.**

### Task 3.1: Create Database Models

**Create `phase2/backend/models.py`** with:

**At minimum (based on Decision 4 - Core Features):**
- User model (id, email, username, display_name, created_at, etc.)
- Post model (if implementing posts)
- Follow model (if implementing follow system)

**Reference**: Look at `phase1/models.py` for schema but adapt to pure SQLAlchemy (not Flask-SQLAlchemy)

🤖 **Claude: Key differences from Phase 1:**
- Use `from sqlalchemy import Column, Integer, String, etc.` not Flask-SQLAlchemy shortcuts
- Use `declarative_base()` not `db.Model`
- Relationships use `relationship()` from sqlalchemy.orm

- [ ] models.py created with User model
- [ ] Additional models created based on features chosen
- [ ] If reusing database: verified models match Phase 1 schema
- [ ] If new database: designed appropriate schema

🤖 **Claude: After creating models, say:**

> "I've created the database models for Phase 2. Before we continue, let's review an important architectural difference from Phase 1.
>
> **Compare these two files:**
> - Phase 1: `phase1/models.py.backup` (or `shared/models.py` if you migrated) - Flask-SQLAlchemy
> - Phase 2: `phase2/backend/models.py` - Plain SQLAlchemy
>
> Can you explain:
> 1. What's different about how we import Column types? (Hint: look at the import statements at the top)
> 2. What does `Base` replace from Phase 1? (Phase 1 used `db.Model`)
> 3. Why do we need `__tablename__ = 'user'` explicitly now?
>
> Take a minute to open both files side by side and let me know your thoughts."

🛑 **STOP: Wait for student to review. Answer questions. Confirm understanding of SQLAlchemy differences.**

- [ ] Student reviewed model differences
- [ ] Student understands Flask-SQLAlchemy vs plain SQLAlchemy

---

### Task 3.2: Create Database Connection Module

**Create `phase2/backend/database.py`** to handle database connections:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
import os
from dotenv import load_dotenv

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def get_db():
    """Dependency for FastAPI routes to get database session"""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

🤖 **Claude: Explain that `get_db()` is a FastAPI dependency that ensures database connections are properly closed.**

- [ ] database.py created
- [ ] Database connection tested

---

### Task 3.3: Initialize Database (if new database)

**Only if you chose "Create New Phase 2 Database" in Decision 3:**

🤖 **Claude: Create a dev script to initialize the Phase 2 database:**

**Create `dev_scripts/init_database_phase2.py` with:**
```python
"""Initialize Phase 2 database with all tables."""
from models import Base
from database import engine

Base.metadata.create_all(bind=engine)
print("✅ Phase 2 database initialized successfully!")
print(f"Tables created: {list(Base.metadata.tables.keys())}")
```

**Then run it:**
```bash
uv run python dev_scripts/init_database_phase2.py
```

**If reusing Phase 1 database**: Skip this step - tables already exist!

- [ ] Database initialized (if needed)
- [ ] Verified database file exists at configured path
- [ ] Script saved in dev_scripts/ for future use

---

## Part 4: Backend Implementation - Authentication

🤖 **Claude: Help the student create authentication based on their Decision 2. See SETUP-AUTHENTICATION.md for JWT details.**

### Task 4.1: Create Authentication Module

**Create `phase2/backend/auth.py`** with:

**For Development Mode:**
- Simple function that accepts username and returns it as "token"
- Dependency function for protected routes that extracts username from header

**For Production JWT:**
- `create_access_token()` function using python-jose
- `verify_token()` function to validate JWT
- `get_current_user()` dependency for protected routes
- `/token` endpoint for login (can reuse Phase 1 auth flow, then issue JWT)

🤖 **Claude: For production JWT, explain the flow:**
1. User provides credentials (Google OAuth callback, email magic link, or username/password)
2. Backend verifies credentials
3. Backend creates JWT token with user info
4. Frontend stores token (localStorage)
5. Frontend sends token in `Authorization: Bearer <token>` header
6. Backend validates token on each request

**Reference**: See SETUP-AUTHENTICATION.md Phase 2 section for JWT implementation details

- [ ] auth.py created
- [ ] Token creation/verification implemented based on Decision 2
- [ ] `get_current_user()` dependency created for protected routes

🤖 **Claude: After creating auth.py, say:**

> "I've created the authentication module. This is fundamentally different from Phase 1's session-based authentication. Let's review how authentication works in Phase 2.
>
> Open `phase2/backend/auth.py` and examine the authentication flow.

**🤖 Claude: Then ask questions based on their choice:**

**For Development Mode:**
> Can you explain:
> 1. How does the frontend send the "token" to the backend? (Hint: look for where the Authorization header is checked)
> 2. What does the `get_current_user()` dependency do when a route uses `Depends(get_current_user)`?
> 3. Why is this approach NOT suitable for production deployment?
>
> Take a look at the code and let me know your thoughts."

**For Production JWT:**
> Can you explain:
> 1. What information is encoded inside a JWT token? (Look at the `create_access_token()` function)
> 2. How is this different from Phase 1's session cookies stored on the server?
> 3. Why is JWT considered "stateless" authentication?
> 4. What happens when a token expires?
>
> Compare this with Phase 1's session approach - in Phase 1, the server stored session data. Where is the user info stored in JWT-based auth?"

🛑 **STOP: Wait for student to review. Answer questions. Explain JWT vs sessions concept if needed.**

- [ ] Student reviewed authentication code
- [ ] Student understands token-based auth vs session-based auth
- [ ] Student understands stateless vs stateful authentication

---

### Task 4.2: Create Authentication Endpoints

**In `phase2/backend/main.py`**, create authentication routes:

**Development Mode:**
- `POST /dev-login` - Accepts username, returns fake "token"

**Production JWT:**
- `POST /token` - Accepts credentials, returns JWT token
- Optionally: `POST /refresh` - Refresh expired tokens

🤖 **Claude: If student chose to reuse Google OAuth or Email Magic Links from Phase 1, help them create endpoints that:**
1. Accept the OAuth code or magic link token
2. Verify it (reuse Phase 1 logic)
3. Issue a JWT token for the verified user

- [ ] Login endpoint created
- [ ] Endpoint tested with FastAPI docs (`/docs`)

---

## Part 5: Backend Implementation - Core API Endpoints

🤖 **Claude: Help the student create REST API endpoints for the features they chose in Decision 4. Follow REST conventions (resources as nouns, HTTP methods for actions). Use FastAPI's dependency injection for authentication and database sessions.**

### Task 5.1: Create Main FastAPI App

**Create `phase2/backend/main.py`** with:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import os
from dotenv import load_dotenv

load_dotenv()

app = FastAPI(title="Social Media API", version="1.0.0")

# CORS configuration for frontend
origins = os.getenv("CORS_ORIGINS", "http://localhost:5173").split(",")
app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
def read_root():
    return {"message": "Social Media API", "docs": "/docs"}
```

- [ ] main.py created with FastAPI app
- [ ] CORS middleware configured
- [ ] Test server runs: `uv run uvicorn main:app --reload`
- [ ] Visit `http://localhost:8000/docs` to see API documentation

---

### Task 5.2: Implement User Endpoints

**Based on your core features, create user-related endpoints:**

**Minimum endpoints:**
- `GET /users/me` - Get current user info (protected)
- `GET /users/{user_id}` - Get user profile (public if Decision 5 = public reading)

**Example structure:**

```python
from fastapi import Depends, HTTPException
from sqlalchemy.orm import Session
from database import get_db
from auth import get_current_user

@app.get("/users/me")
def get_current_user_profile(
    current_user: User = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    return current_user

@app.get("/users/{user_id}")
def get_user_profile(
    user_id: int,
    db: Session = Depends(get_db)
    # Add current_user dependency if Decision 5 = fully private
):
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

🤖 **Claude: Help student create Pydantic models for request/response validation. Explain the difference between SQLAlchemy models (database) and Pydantic models (API validation).**

- [ ] User endpoints implemented
- [ ] Pydantic schemas created for request/response
- [ ] Tested in `/docs` interface

🤖 **Claude: After creating user endpoints, say:**

> "Let's pause here and review the user endpoints we just created. This demonstrates a key architectural difference between Phase 1 and Phase 2.
>
> **Compare these two implementations:**
> - Phase 2: Open `phase2/backend/main.py` and find the `GET /users/{user_id}` endpoint
> - Phase 1: Open `phase1/routes.py` (or `phase1/app.py`) and find `GET /profile/<username>` route
>
> Can you explain:
> 1. What does Phase 1's route return? (Hint: look for `render_template`)
> 2. What does Phase 2's endpoint return? (Hint: look at the return statement - is it HTML or something else?)
> 3. Who is responsible for rendering the HTML in each architecture?
> 4. What does `Depends(get_db)` do in the Phase 2 endpoint? Why do we need it?
>
> Understanding this difference is crucial to understanding REST architecture. Take a minute to compare both files."

🛑 **STOP: Wait for student to review. Discuss server-side rendering vs client-side rendering. Explain dependency injection pattern.**

- [ ] Student compared Phase 1 route vs Phase 2 endpoint
- [ ] Student understands server-side rendering vs JSON API responses
- [ ] Student understands FastAPI dependency injection

---

### Task 5.3: Implement Post Endpoints

**If "posts" is in your core features (Decision 4):**

**Minimum endpoints:**
- `GET /posts` - List posts (public if Decision 5 = public reading)
- `GET /posts/{post_id}` - Get single post
- `POST /posts` - Create new post (protected)

**Optional endpoints:**
- `PUT /posts/{post_id}` - Update post (protected, owner only)
- `DELETE /posts/{post_id}` - Delete post (protected, owner only)

**Example structure:**

```python
from pydantic import BaseModel

class PostCreate(BaseModel):
    content: str

class PostResponse(BaseModel):
    id: int
    content: str
    user_id: int
    created_at: datetime

    class Config:
        from_attributes = True

@app.get("/posts", response_model=list[PostResponse])
def get_posts(
    db: Session = Depends(get_db),
    skip: int = 0,
    limit: int = 20
    # Add current_user dependency if Decision 5 = fully private
):
    posts = db.query(Post).offset(skip).limit(limit).all()
    return posts

@app.post("/posts", response_model=PostResponse, status_code=201)
def create_post(
    post_data: PostCreate,
    current_user: User = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    post = Post(content=post_data.content, user_id=current_user.id)
    db.add(post)
    db.commit()
    db.refresh(post)
    return post
```

🤖 **Claude: Reference `phase1/app.py` for the business logic (e.g., character limits, validation). The logic is the same, just the API interface is different.**

- [ ] Post endpoints implemented
- [ ] Request/response Pydantic models created
- [ ] Create post tested in `/docs`
- [ ] List posts tested

🤖 **Claude: After creating post endpoints, say:**

> "I've created the post endpoints. Let's pause here - I'd like you to review the 'create post' endpoint, just like we did in Phase 1.
>
> Open `phase2/backend/main.py` and find the function decorated with `@app.post("/posts", ...)`.
>
> Can you explain:
> 1. How does it know which user is creating the post? (Compare this to Phase 1's `session['user_id']`)
> 2. What validation is happening? (Look at the `PostCreate` Pydantic model)
> 3. What happens after the post is created - what does the endpoint return?
>
> Take a minute to read that function and let me know your thoughts."

🛑 **STOP: Wait for student to review. Answer questions.**

🤖 **Claude: After student answers question 1 (about getting current user), ask:**

> "You mentioned `Depends(get_current_user)`. Do you understand what `Depends()` does in FastAPI? It appears in several places in this code."

🛑 **STOP: Wait for response.**

**If student says no or seems uncertain, explain:**

> "`Depends()` is FastAPI's **dependency injection** system - one of the most important FastAPI concepts.
>
> ```python
> def create_post(
>     post_data: PostCreate,
>     current_user: User = Depends(get_current_user),
>     db: Session = Depends(get_db)
> ):
> ```
>
> **How it works:**
> 1. Before your function runs, FastAPI calls the function inside `Depends()`
> 2. The result becomes the parameter value
> 3. If that function fails (raises an exception), your endpoint never runs
>
> **In this example:**
> - `Depends(get_current_user)` → checks Authorization header, returns User (or raises 401)
> - `Depends(get_db)` → creates a database session, automatically closes it after
>
> **Compare to Phase 1:**
> - Phase 1: You manually checked `if 'user_id' not in session` in each route
> - Phase 2: `Depends(get_current_user)` does this automatically
>
> Does that make sense?"

**If student already understands Depends, acknowledge and move on.**

- [ ] Student reviewed create post endpoint
- [ ] Student understands `Depends()` dependency injection
- [ ] Student understands what the endpoint returns (JSON vs redirect)

---

🤖 **Claude: After confirming Depends understanding, follow up with validation questions:**

> "Good! Now let's look at the validation differences between Phase 1 and Phase 2.
>
> Compare with Phase 1's `POST /posts` route (in `phase1/routes.py`).
>
> Can you explain:
> 1. How does Phase 1 validate the form data? (Hint: look for manual `if not content.strip()` checks)
> 2. What happens in Phase 2 if the frontend sends invalid JSON (e.g., missing the `content` field)?
> 3. What's the benefit of Pydantic's automatic validation?
>
> Try testing this in the `/docs` interface - submit a POST request with missing or invalid data and see what happens!"

🛑 **STOP: Wait for student to review and test. Discuss automatic validation vs manual validation. Show how FastAPI/docs displays validation errors.**

- [ ] Student compared validation approaches
- [ ] Student understands Pydantic automatic validation
- [ ] Student tested validation in `/docs` interface

---

### Task 5.4: Implement Additional Feature Endpoints

**Based on your remaining core features from Decision 4:**

**If implementing follow system:**
- `POST /users/{user_id}/follow` - Follow a user (protected)
- `DELETE /users/{user_id}/follow` - Unfollow a user (protected)
- `GET /users/{user_id}/followers` - List followers
- `GET /users/{user_id}/following` - List following

**If implementing personalized feed:**
- `GET /feed` - Get posts from followed users + own posts (protected)

**If implementing search:**
- `GET /search/users?q={query}` - Search users
- `GET /search/posts?q={query}` - Search posts

🤖 **Claude: Help the student implement the endpoints for their chosen features. Keep the API design clean and RESTful. Remind them that 3-4 well-implemented features are better than 6 half-working ones.**

- [ ] Additional feature endpoints implemented
- [ ] All endpoints tested in `/docs`
- [ ] API responses are clean and well-structured

---

## Part 6: Frontend Setup

### Task 6.1: Create Frontend Project

🤖 **Claude: Help the student create a new Vite project with their chosen framework (Decision 1).**

**For React:**
```bash
cd phase2/frontend
npm create vite@latest . -- --template react
npm install
```

**For Vue:**
```bash
cd phase2/frontend
npm create vite@latest . -- --template vue
npm install
```

- [ ] Frontend project created
- [ ] Dependencies installed
- [ ] Dev server runs: `npm run dev`
- [ ] Can visit `http://localhost:5173`

---

### Task 6.2: Install Additional Frontend Dependencies

```bash
# For HTTP requests:
npm install axios

# Optional - for routing (if multi-page):
npm install react-router-dom  # React
# OR
npm install vue-router  # Vue

# Optional - for forms:
npm install react-hook-form  # React
```

- [ ] Axios installed
- [ ] Additional dependencies installed based on needs

---

### Task 6.3: Configure API Client

**Create `src/api.js` (or `api.ts` for TypeScript):**

```javascript
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:8000';

const api = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add token to requests if it exists
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default api;

// API functions
export const authAPI = {
  login: (credentials) => api.post('/token', credentials),
  // Or for dev mode: api.post('/dev-login', { username })
};

export const postsAPI = {
  list: (params) => api.get('/posts', { params }),
  get: (id) => api.get(`/posts/${id}`),
  create: (data) => api.post('/posts', data),
};

export const usersAPI = {
  me: () => api.get('/users/me'),
  get: (id) => api.get(`/users/${id}`),
};

// Add more API functions based on your endpoints
```

🤖 **Claude: Explain that the interceptor automatically adds the JWT token to every request.**

- [ ] API client configured
- [ ] Token interceptor set up
- [ ] API functions created for each backend endpoint

🤖 **Claude: After creating the API client, say:**

> "Let's review the API client configuration - this is a key pattern in modern web applications that centralize how your frontend talks to the backend.
>
> Open `phase2/frontend/src/api.js` and look at the axios interceptor section (the `api.interceptors.request.use(...)` part).
>
> Can you explain:
> 1. What does the interceptor do before every API request is sent?
> 2. Where did the token come from? (Hint: look for `localStorage.getItem('token')`)
> 3. How is this different from Phase 1's session cookies? (In Phase 1, cookies were sent automatically by the browser)
> 4. What happens if the token is expired or invalid when it reaches the backend?
>
> This pattern centralizes authentication - every API call automatically includes the token, so individual components don't need to worry about it!"

🛑 **STOP: Wait for student understanding. Explain interceptor pattern and token-based auth flow. Discuss localStorage vs cookies.**

- [ ] Student understands axios interceptor pattern
- [ ] Student understands localStorage for token storage
- [ ] Student understands Authorization header mechanism

---

## Part 7: Frontend Implementation

🤖 **Claude: Help the student create components for their core features. The implementation will be different for React vs Vue, but the concepts are the same. Reference Phase 1 templates for UI/UX inspiration, but implement in JavaScript with API calls.**

### Task 7.1: Create Authentication Components

**Components to create:**
- Login component/page
- Logout functionality
- Protected route wrapper (redirects to login if not authenticated)

**Example flow:**
1. User enters credentials
2. Call authAPI.login()
3. Store returned token in localStorage
4. Redirect to main app

🤖 **Claude: For React, use useState/useEffect and context for auth state. For Vue, use ref/reactive and composables or Pinia for state management.**

- [ ] Login component created
- [ ] Token storage implemented
- [ ] Auth state management set up
- [ ] Can successfully log in and receive token

---

### Task 7.2: Create Post Components

**If posts are in your core features:**

**Components to create:**
- PostList - Display list of posts
- PostItem - Individual post display
- CreatePost - Form to create new post

**Example flow:**
1. Component mounts
2. Call postsAPI.list()
3. Display posts
4. For create: call postsAPI.create(), refresh list

🤖 **Claude: Help student handle loading states, errors, and empty states gracefully.**

- [ ] Post list component created
- [ ] Create post component created
- [ ] Posts display correctly from API
- [ ] Can create new posts

🤖 **Claude: After creating post components, say:**

> "I've created the post components. Let's review the PostList component, just like we reviewed the homepage template in Phase 1.
>
> Open `phase2/frontend/src/components/PostList.jsx` (or `.vue` for Vue).
>
> Can you explain:
> 1. How does it know where to get posts from? (Hint: look for the `useEffect` hook and API call)
> 2. How does it iterate through posts? (Compare `posts.map()` to Phase 1's `{% for post in posts %}`)
> 3. What happens while waiting for the API response? (Hint: look for loading state)
>
> Take a minute to read the component and let me know your thoughts."

🛑 **STOP: Wait for student to review. Answer questions. Confirm understanding.**

- [ ] Student reviewed PostList component
- [ ] Student understands useEffect for data fetching
- [ ] Student understands how iteration works (map vs Jinja for loop)

---

🤖 **Claude: After student responds, compare architectures:**

> "Good! Now let's compare the two approaches directly.
>
> **Open both files side by side:**
> - Phase 1: `phase1/templates/index.html` (Jinja2 template)
> - Phase 2: `phase2/frontend/src/components/PostList.jsx`
>
> Can you explain:
> 1. **Data source**: Where does the post data come from in Phase 1? (server renders it into the HTML)
> 2. **Data source**: Where does the post data come from in Phase 2? (component fetches via API)
> 3. **Create post flow**: What happens when you create a new post in Phase 1? (Does the page refresh?)
> 4. **Create post flow**: What happens when you create a new post in Phase 2? (Does the page refresh, or does the component update?)
>
> This is the core difference between server-side rendering (SSR) and single-page applications (SPA)."

🛑 **STOP: Wait for student to compare. Discuss SSR vs SPA, page refresh vs state updates, user experience differences.**

- [ ] Student compared template rendering vs component rendering
- [ ] Student understands data fetching patterns (server-rendered vs API calls)
- [ ] Student understands SPA state updates vs full page reloads

---

### Task 7.3: Create User Profile Components

**Components to create:**
- UserProfile - Display user info and their posts
- CurrentUserProfile - Display logged-in user's profile

**Example flow:**
1. Get user_id from route params (or current user)
2. Call usersAPI.get(user_id)
3. Call postsAPI.list({ user_id })
4. Display combined data

- [ ] Profile component created
- [ ] User info displays correctly
- [ ] User's posts display correctly

---

### Task 7.4: Implement Additional Features

**Based on your remaining core features from Decision 4:**

**If implementing follow system:**
- Follow/Unfollow buttons
- Followers/Following lists
- Update UI optimistically

**If implementing feed:**
- Feed component that calls `/feed` endpoint
- Display posts from followed users

🤖 **Claude: Focus on making these features work cleanly with good UX. It's better to have 3 polished features than 6 buggy ones.**

- [ ] Additional features implemented
- [ ] All features tested end-to-end
- [ ] UI is clean and functional

---

## Part 8: Testing and Documentation

### Task 8.1: Test Complete Workflows

**Test these flows end-to-end:**

- [ ] User can log in
- [ ] User can view posts (test both logged in and logged out if public reading)
- [ ] User can create a post
- [ ] User can view profiles
- [ ] Additional features work as expected
- [ ] Error cases handled gracefully (network errors, validation errors, etc.)

🤖 **Claude: Help the student test each workflow and fix any bugs. Use browser dev tools to inspect network requests.**

---

### Task 8.2: Compare with Phase 1

🤖 **Claude: Before documenting the comparison, do a hands-on exercise:**

> "Before we document the architectural comparison, let's do a hands-on exercise to really see the differences in action.
>
> **Open two browser windows side by side:**
> - Window 1: Phase 1 app (http://localhost:5000 or 5001)
> - Window 2: Phase 2 app (http://localhost:5173)
>
> **Now test the same action in both windows:**
> 1. Create a new post in Phase 1 - watch what happens to the page
> 2. Open browser DevTools in Phase 2 (right-click → Inspect → Network tab)
> 3. Create a new post in Phase 2 - watch the Network tab
> 4. Like a post in Phase 2 (if you implemented likes) - watch the Network tab
>
> **Questions to consider:**
> - In Phase 1: What happened after you created the post? (Did the entire page reload?)
> - In Phase 2: What network requests did you see in the DevTools? (Look for POST /posts, GET /posts)
> - In Phase 2: Did the page reload when you liked a post?
> - Which approach felt faster/more responsive?
>
> Let me know what differences you notice in the user experience!"

🛑 **STOP: Wait for student observations. Discuss immediate feedback vs page reloads, network activity visibility, perceived performance.**

- [ ] Student tested both applications side-by-side
- [ ] Student observed network activity in DevTools
- [ ] Student noticed UX differences (page reloads vs smooth updates)

---

Now let's document your observations:

**Reflection questions** (add answers to DECISIONS-MADE.md or ARCHITECTURE.md):

1. What was easier in Phase 2 vs Phase 1?
2. What was harder in Phase 2 vs Phase 1?
3. How does JWT authentication compare to session-based?
4. How does client-side rendering compare to server-side?
5. What are the benefits of separated architecture?
6. What are the drawbacks of separated architecture?
7. When would you choose monolithic vs separated?

🤖 **Claude: Encourage the student to think critically about architectural trade-offs. There's no "right" answer - both approaches have merits.**

- [ ] Compared architectures
- [ ] Documented insights in ARCHITECTURE.md

---

### Task 8.3: Update Documentation

**Update `ARCHITECTURE.md`** with Phase 2 details:

- [ ] Document REST API endpoints (paths, methods, request/response formats)
- [ ] Document JWT authentication flow
- [ ] Document frontend architecture (components, state management)
- [ ] Document database schema (same as or different from Phase 1)
- [ ] Compare Phase 1 vs Phase 2 architectures
- [ ] Reflect on AI tool usage for Phase 2

**Update `DECISIONS-MADE.md`** with:

- [ ] Final implementation notes
- [ ] Any decisions changed during implementation
- [ ] Lessons learned

---

## Part 9: Optional Enhancements

**If you have extra time:**

- [ ] Add pagination to post lists
- [ ] Add image upload support (multipart/form-data)
- [ ] Add real-time updates (WebSockets)
- [ ] Improve error handling with custom error pages
- [ ] Add loading skeletons for better UX
- [ ] Deploy Phase 2 (backend to PythonAnywhere, frontend to Vercel/Netlify)

---

## Troubleshooting

### CORS Errors

**Symptom**: "No 'Access-Control-Allow-Origin' header" in browser console

**Fix**:
1. Check CORS middleware is configured in `main.py`
2. Verify frontend URL is in `CORS_ORIGINS` environment variable
3. Restart backend server after changing `.env`

### 401 Unauthorized

**Symptom**: All protected endpoints return 401

**Fix**:
1. Check token is stored: `localStorage.getItem('token')` in browser console
2. Verify token format in request headers (should be `Bearer <token>`)
3. Check token hasn't expired (default 30 min)
4. Verify SECRET_KEY is same as when token was created

### Database Errors

**Symptom**: "No such table" or "No such column"

**Fix**:
1. If reusing Phase 1 database: ensure models match Phase 1 schema exactly
2. If new database: ensure you ran database initialization
3. Check DATABASE_URL path is correct

### Frontend Can't Connect to Backend

**Symptom**: Network errors, "ERR_CONNECTION_REFUSED"

**Fix**:
1. Ensure backend is running: `uv run uvicorn main:app --reload`
2. Check API_BASE_URL in frontend matches backend address
3. Verify no firewall blocking localhost connections

---

## Success Criteria

You've successfully completed Phase 2 when:

- ✅ Backend API is running and documented at `/docs`
- ✅ Frontend connects to backend and displays data
- ✅ User can log in and get authenticated
- ✅ 3-4 core features work end-to-end
- ✅ Code is clean and well-organized
- ✅ DECISIONS-MADE.md documents your choices
- ✅ ARCHITECTURE.md is updated with Phase 2 details
- ✅ You can explain the architectural differences between Phase 1 and Phase 2

**Remember**: Phase 2 is about understanding REST architecture, not building a perfect production app. Focus on learning and clean implementation of core concepts.

---

## Next Steps

1. **Document learnings** in ARCHITECTURE.md
2. **Reflect on AI usage** - what worked, what didn't
3. **Optional**: Deploy Phase 2 (see deployment-guide.md)
4. **Optional**: Add more features if time permits

**Congratulations on completing both architectural approaches!** 🎉
