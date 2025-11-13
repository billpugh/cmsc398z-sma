# Backend API - Starter Template

This is a minimal starter template for the FastAPI backend. You'll use AI tools to build out the full REST API.

## Quick Start

1. **Navigate to backend directory:**
   ```bash
   cd backend
   ```

2. **Install dependencies:**
   ```bash
   uv sync
   ```

   **Note:** `uv` automatically manages the virtual environment - no need to manually create or activate it.

3. **Configure environment:**
   ```bash
   cp .env.example .env
   ```

   **⚠️ IMPORTANT:** Generate a secure SECRET_KEY:
   ```bash
   uv run python -c "import secrets; print(secrets.token_hex(32))"
   ```
   Add it to your `.env` file.

   See [SETUP-AUTHENTICATION.md](../../../SETUP-AUTHENTICATION.md#phase-2-considerations-jwt-tokens) for Phase 2 authentication details.

4. **Start building!**

   Use AI tools to create your API structure.

## Running the API

```bash
# Development mode (auto-reload on changes)
uv run uvicorn main:app --reload

# Visit API docs at: http://localhost:8000/docs
```

## Suggested First Prompts

### 1. Basic FastAPI Structure
```
Create a FastAPI application with:
- CORS middleware configured for localhost:5173 and localhost:3000
- SQLAlchemy database setup with SQLite
- Pydantic settings for configuration from .env
- Health check endpoint at /api/health
- API router structure with /api prefix
```

### 2. Database Models
```
Create SQLAlchemy models for:
- User: id, email, username, display_name, created_at
- Post: id, user_id (FK), content, created_at
- Follow: id, follower_id (FK), followee_id (FK), created_at
Include proper relationships and indexes
```

### 3. Pydantic Schemas
```
Create Pydantic schemas for request/response validation:
- UserCreate, UserResponse
- PostCreate, PostResponse
- Token, TokenData (for JWT)
Include examples in schema docstrings
```

### 4. JWT Authentication

**Note:** For Phase 2, you can start with a simplified "development mode" JWT system (username as token) and upgrade to real JWT later. See [SETUP-AUTHENTICATION.md](../../../SETUP-AUTHENTICATION.md#phase-2-considerations-jwt-tokens) for both approaches.

**Development Mode (Fast Start):**
```
Create simplified authentication for development:
- POST /api/dev-login - accepts username, returns it as token
- Dependency function that extracts username from Bearer token
- Add warning comments that this is dev-only
- Easy to upgrade to real JWT later
```

**Production Mode (Real JWT):**
```
Implement JWT authentication:
- Function to create JWT token with expiration (SECRET_KEY required)
- Function to verify and decode JWT token
- Dependency function get_current_user() for protected routes
- Use python-jose for JWT handling
```

### 5. Auth Endpoints

**For Development Mode:**
```
Create auth router (/api/auth):
- POST /api/auth/dev-login - accepts username, returns as "token"
- No email verification needed in dev mode
- Can skip invite list in dev mode
```

**For Production Mode:**
```
Create authentication router (/api/auth):
- POST /api/auth/login - accepts email, returns JWT token
- Check email against invite list before allowing login
- Return token with user info
- Token expires after 30 minutes
```

### 6. Post Endpoints
```
Create posts router (/api/posts):
- POST /api/posts - Create new post (requires auth)
- GET /api/posts/feed - Get feed (requires auth, posts from followed users)
- DELETE /api/posts/{post_id} - Delete post (requires auth, own posts only)
All endpoints use Pydantic schemas for validation
```

### 7. User Endpoints
```
Create users router (/api/users):
- GET /api/users/{username} - Get user profile
- GET /api/users/{username}/posts - Get all posts by user
Include post count and follower/following counts in user response
```

## Project Structure

Build toward this structure:

```
backend/
├── main.py              # FastAPI app, CORS, routers
├── database.py          # Database connection and session
├── models.py            # SQLAlchemy models
├── schemas.py           # Pydantic schemas
├── auth.py              # JWT utilities and dependencies
├── config.py            # Settings from .env (optional)
├── routers/
│   ├── __init__.py
│   ├── auth.py          # /api/auth endpoints
│   ├── posts.py         # /api/posts endpoints
│   └── users.py         # /api/users endpoints
├── invites.txt          # List of allowed emails
├── pyproject.toml
└── .env
```

## Testing Your API

### Interactive API Docs (Swagger)

Visit `http://localhost:8000/docs` for interactive API documentation. You can test all endpoints directly in the browser!

### Example Requests

```bash
# Health check
curl http://localhost:8000/api/health

# Login (get token)
curl -X POST http://localhost:8000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@umd.edu"}'

# Create post (with token)
curl -X POST http://localhost:8000/api/posts \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"content": "Hello from API!"}'

# Get feed
curl http://localhost:8000/api/posts/feed \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Tips

- Start with one router at a time (auth → posts → users)
- Test each endpoint in `/docs` before moving on
- Use Pydantic models for automatic validation
- FastAPI generates OpenAPI docs automatically
- Check uvicorn terminal for error messages

## Common Issues

### Import errors
Make sure you're in the backend directory and have run `uv sync`.

### Database errors
Run Python shell to create tables:
```bash
uv run python -c "from database import engine; from models import Base; Base.metadata.create_all(bind=engine)"
```

### CORS errors
Update CORS_ORIGINS in .env to include your frontend URL.

## Need Help?

- FastAPI docs: https://fastapi.tiangolo.com/
- Check main [Phase 2 README](../README.md)
- Post on Piazza
