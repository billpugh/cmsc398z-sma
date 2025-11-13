# Phase 2: REST API + JavaScript Frontend

## Goal

Refactor your application into a modern separated architecture with a Python REST API backend and JavaScript frontend. The emphasis is on understanding architectural patterns, API design, and the separation of concerns between client and server.

## Expected Time: 2-2.5 hours

## Architecture Overview

In a separated REST architecture:

**Backend (FastAPI recommended):**
- Exposes RESTful API endpoints (JSON in/out)
- Handles business logic and data persistence
- Stateless - no server-side sessions
- Could serve multiple clients (web, mobile, etc.)

**Frontend (React/Vue recommended):**
- Single-page application (SPA) running in browser
- Makes HTTP requests to backend API
- Manages UI state and rendering
- Handles user interactions

**Benefits of this architecture:**
- Frontend and backend can be developed/deployed independently
- Multiple frontends can use the same API
- Easier to test (API contracts are clear)
- Scales better (can scale frontend/backend separately)
- Mobile apps can use the same backend

## Important: Reduced Feature Scope

**You do NOT need to implement all Phase 1 features in Phase 2.**

Focus on 3-4 core features with clean API design:

### Required Features (Phase 2 MVP)

1. **Authentication** (JWT token-based)
   - Login endpoint (returns JWT token)
   - Token validation on protected endpoints

2. **Posts** (choose 2-3 of these)
   - Create post
   - View posts (by user or feed)
   - Delete own post

3. **User Profiles**
   - Get user profile info
   - View user's posts

4. **Following** (optional for Phase 2)
   - Follow/unfollow user
   - Get following/followers list

The goal is quality over quantity - focus on learning REST principles and clean architecture.

## Technology Stack

### Backend: FastAPI (Recommended)

**Why FastAPI?**
- Automatic API documentation (Swagger UI)
- Type hints for better AI code generation
- Fast and modern
- Easy to test
- Similar to Flask (easy transition)

**Alternative:** Flask with Flask-RESTful (if you want minimal changes from Phase 1)

### Frontend: React or Vue (Recommended)

**React:**
- Most popular, best AI tool support
- Large ecosystem
- Component-based

**Vue:**
- Easier learning curve
- Good AI tool support
- Also component-based

**Alternative:** Vanilla JavaScript (simpler but less "professional")

### Database: Reuse Phase 1 SQLite

You can share the same database! Possibly extract models to a shared `models.py`.

## Project Structure

```
phase2/
├── backend/
│   ├── main.py              # FastAPI application
│   ├── models.py            # Database models (can reuse from Phase 1)
│   ├── auth.py              # JWT authentication
│   ├── routers/
│   │   ├── posts.py         # Post endpoints
│   │   ├── users.py         # User endpoints
│   │   └── follows.py       # Follow endpoints (optional)
│   ├── database.py          # Database connection
│   ├── schemas.py           # Pydantic schemas for request/response
│   ├── requirements.txt     # Or pyproject.toml
│   └── .env
├── frontend/
│   ├── package.json         # Node dependencies
│   ├── public/
│   │   └── index.html
│   └── src/
│       ├── App.jsx          # Main component (React)
│       ├── components/
│       │   ├── Feed.jsx
│       │   ├── Profile.jsx
│       │   ├── Login.jsx
│       │   └── Post.jsx
│       ├── api.js           # API client functions
│       └── main.jsx         # Entry point
└── shared/
    └── database.db          # Shared SQLite database (optional)
```

## REST API Design

### Key Principles

1. **Resources as nouns:** `/users`, `/posts`, not `/getUser` or `/createPost`
2. **HTTP methods for actions:**
   - GET: Retrieve data
   - POST: Create new resource
   - PUT/PATCH: Update resource
   - DELETE: Delete resource
3. **Stateless:** Each request contains all needed info (JWT token, not session cookies)
4. **JSON in/out:** Consistent data format
5. **Clear status codes:** 200 (OK), 201 (Created), 400 (Bad Request), 401 (Unauthorized), 404 (Not Found)

### Example API Endpoints

**Authentication Endpoints** (vary based on your auth method):

```
# For Email Magic Links:
POST   /api/auth/request-login      # Request magic link (email sent)
POST   /api/auth/verify             # Verify token from email, get JWT

# For Google OAuth:
GET    /api/auth/google/url         # Get Google OAuth authorization URL
POST   /api/auth/google             # Exchange Google code for JWT
```

**Core Endpoints** (all require authentication):

```
GET    /api/users/{username}          # Get user profile
GET    /api/users/{username}/posts    # Get user's posts

POST   /api/posts                     # Create new post
GET    /api/posts/feed                # Get feed
DELETE /api/posts/{post_id}           # Delete post

POST   /api/users/{username}/follow   # Follow user
DELETE /api/users/{username}/follow   # Unfollow user
GET    /api/users/{username}/following # Get following list
GET    /api/users/{username}/followers # Get followers list
```

**Notes:**
- All core endpoints require authentication (JWT token in Authorization header)
- Since this is invite-only, only authenticated users can view any content
- Authentication endpoints differ based on whether you chose Google OAuth or email magic links

### API Documentation

FastAPI automatically generates interactive API docs at `/docs` - use this to test your API!

## Getting Started with AI Tools

### Step 1: Backend Setup

```
Create a FastAPI application with:
- User authentication using JWT tokens
- SQLAlchemy models for User and Post (reuse from Phase 1 if possible)
- Pydantic schemas for request/response validation
- CORS configuration to allow frontend requests
- Database connection to SQLite
- Basic routes: /api/auth/login, /api/posts, /api/users/{username}
```

### Step 2: Authentication with JWT

Choose ONE authentication approach and implement it:

**Option A: Email Magic Links**
```
Implement email magic link authentication in FastAPI:
- POST /api/auth/request-login endpoint:
  - Accepts email, checks against invite list
  - Generates temporary token using python-jose
  - Sends email with login link containing token
- POST /api/auth/verify endpoint:
  - Validates temporary token from email
  - Creates/retrieves user from database
  - Generates long-lived JWT token
  - Returns JWT to frontend
- Dependency function to validate JWT token on protected routes
- Use python-jose for both temporary and JWT tokens
```

**Option B: Google OAuth**
```
Implement Google OAuth authentication in FastAPI:
- GET /api/auth/google/url endpoint:
  - Returns Google OAuth authorization URL
  - Frontend redirects user to this URL
- POST /api/auth/google endpoint:
  - Accepts authorization code from Google
  - Exchanges code for user info with Google API
  - Checks user email against invite list
  - Creates/updates user in database
  - Generates JWT token
  - Returns JWT to frontend
- Dependency function to validate JWT token on protected routes
- Use authlib for OAuth, python-jose for JWT
```

### Step 3: REST API Endpoints

Build endpoints one at a time:

```
Create REST API endpoints for posts:
- POST /api/posts - Create new post (requires auth)
  - Request: { "content": "text" }
  - Response: { "id": 1, "user_id": 1, "content": "text", "created_at": "..." }
- GET /api/posts/feed - Get feed (requires auth, returns posts from followed users)
- DELETE /api/posts/{post_id} - Delete own post (requires auth)
```

```
Create REST API endpoints for user profiles:
- GET /api/users/{username} - Get user info
  - Response: { "username": "...", "display_name": "...", "post_count": 10, ... }
- GET /api/users/{username}/posts - Get all posts by user
```

### Step 4: Frontend Setup

**For React:**
```
Create a React application with:
- Vite for build tool (or use npm create vite@latest)
- React Router for navigation
- Axios or fetch for API calls
- Components: Login, Feed, Profile, CreatePost
- Store JWT token in localStorage
- API client module that adds Authorization header to requests
```

**For Vue:**
```
Create a Vue application with:
- Vite for build tool
- Vue Router for navigation
- Axios or fetch for API calls
- Components: Login, Feed, Profile, CreatePost
- Store JWT token in localStorage
- API client module that adds Authorization header to requests
```

### Step 5: Connect Frontend to Backend

```
Create an API client module (api.js):
- Base URL configuration (http://localhost:8000)
- Helper functions for each endpoint
- Automatically add JWT token to Authorization header
- Handle errors (401, 404, etc.)

Example functions (auth functions vary by method):

For Email Magic Links:
- requestLogin(email) -> sends magic link email
- verifyToken(token) -> returns JWT and user data
- getPosts() -> returns array of posts
- createPost(content) -> creates post
- getProfile(username) -> returns user data

For Google OAuth:
- getGoogleAuthUrl() -> returns Google OAuth URL
- loginWithGoogle(code) -> exchanges code for JWT and user data
- getPosts() -> returns array of posts
- createPost(content) -> creates post
- getProfile(username) -> returns user data
```

### Step 6: Build UI Components

Work on components one at a time:

```
Create Login component (varies by auth method):

For Email Magic Links:
- Form to enter email
- Call POST /api/auth/request-login
- Show "Check your email" message
- Handle callback from email link (verify token)
- Store JWT in localStorage on success
- Redirect to feed

For Google OAuth:
- "Sign in with Google" button
- Fetch OAuth URL from GET /api/auth/google/url
- Redirect to Google consent screen
- Handle OAuth callback with authorization code
- Call POST /api/auth/google with code
- Store JWT in localStorage on success
- Redirect to feed
```

```
Create Feed component:
- Fetch posts from /api/posts/feed on mount
- Display posts with author and timestamp
- Show "Create Post" form at top
- Handle post creation and refresh feed
```

```
Create Profile component:
- Get username from URL parameter
- Fetch user data and posts from API
- Display user info and posts
- Show follow/unfollow button if not own profile
```

## Testing Your API

### 1. Use FastAPI Docs (Swagger UI)

Visit `http://localhost:8000/docs` to see interactive API documentation. You can test all endpoints directly in the browser.

### 2. Use curl

```bash
# Login
curl -X POST http://localhost:8000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@umd.edu"}'

# Create post (with token)
curl -X POST http://localhost:8000/api/posts \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{"content": "Hello world!"}'

# Get feed
curl -X GET http://localhost:8000/api/posts/feed \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### 3. Test Frontend-Backend Integration

- Open browser DevTools (Network tab)
- Watch API requests as you use the app
- Check for errors (CORS, 401, 404, etc.)

## Common Issues and Solutions

### CORS Errors

Frontend can't access backend due to CORS policy.

**Solution:** Add CORS middleware to FastAPI:

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],  # Vite dev server
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Token Not Being Sent

Requests fail with 401 Unauthorized even after login.

**Solution:** Check that:
- Token is stored in localStorage
- API client adds `Authorization: Bearer <token>` header
- Token hasn't expired

### Database Locked (SQLite)

SQLite can't handle concurrent writes well.

**Solutions:**
- Use connection pooling with proper settings
- Consider PostgreSQL for production (but SQLite is fine for this project)
- Ensure database connections are properly closed

### Frontend Build Issues

React/Vue app won't start or build fails.

**Solutions:**
- Delete `node_modules` and run `npm install` again
- Check for syntax errors in JSX/Vue files
- Make sure Node.js version is recent (18+)

## Architecture Documentation

As you build Phase 2, document your decisions in `ARCHITECTURE.md`:

- What endpoints did you create and why?
- How does authentication work?
- What's your database schema?
- How would you test this?
- What security considerations did you address?
- How would you monitor this in production?

See the [ARCHITECTURE.md template](../ARCHITECTURE.md).

## Deployment

Both backend and frontend need to be deployed:

- **Backend:** Deploy to PythonAnywhere (see deployment guide)
- **Frontend:** Can be served as static files from PythonAnywhere, or use Vercel/Netlify

Update CORS settings and API base URL for production.

See [deployment-guide.md](../deployment-guide.md) for detailed instructions.

## Comparing Phase 1 vs Phase 2

| Aspect | Phase 1 (Flask Monolith) | Phase 2 (REST API) |
|--------|-------------------------|-------------------|
| Architecture | Single application | Separated client/server |
| Rendering | Server-side (Jinja2) | Client-side (JavaScript) |
| State | Server sessions | JWT tokens |
| API | HTML responses | JSON responses |
| Clients | Web browser only | Any client (web, mobile, etc.) |
| Deployment | Single deployment | Two deployments |
| Testing | Integration tests | Unit + API tests |
| Scaling | Scale entire app | Scale frontend/backend separately |

## Checkpoint: Before Deployment

Before deploying Phase 2, verify:

- [ ] Backend API endpoints work (test in `/docs`)
- [ ] Frontend can login and get JWT token
- [ ] Frontend can create and view posts
- [ ] Frontend can view profiles
- [ ] CORS is configured correctly
- [ ] Environment variables are set for production
- [ ] Database can be accessed by backend
- [ ] Architecture is documented in ARCHITECTURE.md

## Optional Extensions

If you finish early:

- Implement ALL Phase 1 features in REST architecture
- Add WebSocket support for real-time updates
- Create a mobile-optimized frontend
- Add comprehensive tests (pytest for backend, Jest for frontend)
- Implement API rate limiting
- Add Redis for caching
- Switch from SQLite to PostgreSQL
- Add API versioning (/api/v1/, /api/v2/)

## Resources

- [FastAPI Tutorial](https://fastapi.tiangolo.com/tutorial/)
- [React Tutorial](https://react.dev/learn)
- [Vue Tutorial](https://vuejs.org/tutorial/)
- [JWT Introduction](https://jwt.io/introduction)
- [REST API Best Practices](https://restfulapi.net/)

## Next Steps

Once Phase 2 is complete:
1. Deploy to PythonAnywhere
2. Complete ARCHITECTURE.md documentation
3. Write your AI interaction reflection
4. Submit everything!

Congratulations - you've built a modern web application with separated architecture!
