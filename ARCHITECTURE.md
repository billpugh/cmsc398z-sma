# Architecture Documentation

**Student Name:** [Your Name]
**Project:** Social Media Web Application
**Date:** [Date]

---

## Executive Summary

[Write 2-3 sentences summarizing your application architecture and key design decisions. Example: "Built a social media application twice - first as a Flask monolith with server-side rendering, then as a FastAPI REST API with React frontend. Chose Google OAuth for production-ready authentication and public reading/private writing access control to make the app portfolio-friendly."]

---

## Phase 1: Flask Monolithic Architecture

### Overview

**Implementation Approach:** [1-2 sentences describing your Phase 1 approach]

### Technology Stack

- **Framework:** Flask
- **Database:** SQLite (shared database in `shared/database/app.db`)
- **Authentication:** [Development Mode / Google OAuth / Email Magic Links]
- **Templating:** Jinja2
- **Key Libraries:** [e.g., Flask-Login, Authlib, SQLAlchemy]

### Architectural Decisions

Document your key decisions from `phase1/DECISIONS-MADE-phase1.md`:

**Authentication Method:** [Development Mode / Google OAuth / Email Magic Links]
**Why:** [Your reasoning]

**Access Control:** [Fully Private / Public Reading, Private Writing]
**Why:** [Your reasoning]

**Invite System:** [File-based / Database table]
**Why:** [Your reasoning]

### Database Schema

Document your actual database tables:

**Users Table:**
```
- id: INTEGER PRIMARY KEY
- email: TEXT UNIQUE NOT NULL
- username: TEXT UNIQUE NOT NULL
- display_name: TEXT
- created_at: TIMESTAMP
- [other fields you added]
```

**Posts Table:**
```
- id: INTEGER PRIMARY KEY
- user_id: INTEGER (foreign key → users.id)
- content: TEXT NOT NULL
- created_at: TIMESTAMP
- [other fields you added, e.g., image_path]
```

**Follows Table:**
```
- id: INTEGER PRIMARY KEY
- follower_id: INTEGER (foreign key → users.id)
- followee_id: INTEGER (foreign key → users.id)
- created_at: TIMESTAMP
- UNIQUE(follower_id, followee_id)
```

**[Other tables]:** [Document any additional tables like Likes, Comments, etc.]

### Features Implemented

- [x] User authentication
- [x] Create posts
- [x] View user profiles
- [x] Follow/unfollow users
- [x] Feed (personalized or global based on access control)
- [ ] [List any optional features you added: Image uploads, likes, comments, etc.]

### Architecture Pattern

```
Browser Request
   ↓
Flask Application
   ├── Routes (URL → Python function)
   ├── Business Logic
   ├── Jinja2 Templates (server-side rendering)
   └── Session Management (Flask-Login or dev mode)
   ↓
SQLite Database (shared/database/app.db)
```

**Key Characteristics:**
- Single Python application
- Server renders complete HTML pages
- Stateful (sessions/cookies for auth)
- Tight coupling between frontend and backend

---

## Phase 2: REST API + JavaScript Frontend Architecture

### Overview

**Implementation Approach:** [1-2 sentences describing your Phase 2 approach and architecture choice]

### Technology Stack

**Backend:**
- **Framework:** [FastAPI / Flask-RESTful / other]
- **Database:** [Shared SQLite from Phase 1 / New database]
- **Authentication:** JWT tokens
- **Key Libraries:** [e.g., python-jose, Authlib, SQLAlchemy]

**Frontend:**
- **Framework:** [React / Vue / Vanilla JS]
- **Build Tool:** [Vite / Create React App / other]
- **HTTP Client:** [fetch / axios]
- **Key Libraries:** [e.g., React Router, Vue Router]

### Architectural Decisions

Document your key decisions from `phase2/DECISIONS-MADE-phase2.md`:

**Frontend Framework:** [React / Vue / Vanilla JS]
**Why:** [Your reasoning]

**Database Strategy:** [Reused Phase 1 database / Created new database]
**Why:** [Your reasoning]

**Core Features Selected:** [List 3-4 features you focused on]
**Why:** [Your reasoning for this subset]

### REST API Design

Document your **actual implemented endpoints**:

#### Authentication Endpoints

[Document based on YOUR chosen auth method - delete the section you didn't use]

**Email Magic Links:**
| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| POST | `/api/auth/request-login` | Request magic link | `{ "email": "user@example.com" }` | `{ "message": "Email sent" }` |
| POST | `/api/auth/verify` | Verify token from email | `{ "token": "..." }` | `{ "jwt": "...", "user": {...} }` |

**Google OAuth:**
| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| GET | `/api/auth/google/url` | Get Google OAuth URL | - | `{ "url": "https://accounts.google.com/..." }` |
| POST | `/api/auth/google` | Exchange Google code for JWT | `{ "code": "..." }` | `{ "jwt": "...", "user": {...} }` |

#### Core Endpoints

[Document only the endpoints you actually implemented]

| Method | Endpoint | Description | Auth Required | Request Body | Response |
|--------|----------|-------------|---------------|--------------|----------|
| POST | `/api/posts` | Create post | Yes | `{ "content": "..." }` | `{ "id": 1, "content": "...", ... }` |
| GET | `/api/posts/feed` | Get feed | Yes | - | `[{ "id": 1, ... }, ...]` |
| DELETE | `/api/posts/{id}` | Delete post | Yes | - | `{ "message": "deleted" }` |
| GET | `/api/users/{username}` | Get profile | Yes | - | `{ "username": "...", ... }` |
| GET | `/api/users/{username}/posts` | Get user's posts | Yes | - | `[{ "id": 1, ... }, ...]` |
| POST | `/api/users/{username}/follow` | Follow user | Yes | - | `{ "message": "followed" }` |
| DELETE | `/api/users/{username}/follow` | Unfollow user | Yes | - | `{ "message": "unfollowed" }` |

### Authentication Flow

[Describe YOUR actual implementation - delete the section you didn't use]

**Email Magic Links Flow:**
1. User enters email on login page
2. Frontend calls `POST /api/auth/request-login`
3. Backend checks invite list, generates temporary token, sends email
4. User clicks link, frontend calls `POST /api/auth/verify` with token
5. Backend validates token, returns JWT
6. Frontend stores JWT in localStorage
7. Frontend includes `Authorization: Bearer <jwt>` header on all requests
8. Backend validates JWT using dependency injection on protected routes

**Google OAuth Flow:**
1. User clicks "Sign in with Google"
2. Frontend fetches OAuth URL from `GET /api/auth/google/url`
3. User redirected to Google consent screen
4. Google redirects back with authorization code
5. Frontend calls `POST /api/auth/google` with code
6. Backend exchanges code with Google, checks invite list, returns JWT
7. Frontend stores JWT in localStorage
8. Frontend includes `Authorization: Bearer <jwt>` header on all requests
9. Backend validates JWT using dependency injection on protected routes

### Frontend Architecture

**Components:** [List your main components and what they do]
- `Login`: [description]
- `Feed`: [description]
- `Profile`: [description]
- [Other components]

**State Management:** [How you handle state - localStorage for JWT, component state, etc.]

**Routing:** [Your routes: `/login`, `/feed`, `/profile/:username`, etc.]

### Architecture Pattern

```
Browser (React/Vue SPA)
   ↓ HTTP requests (JSON)
FastAPI Backend
   ├── REST Endpoints (/api/*)
   ├── JWT Authentication (stateless)
   ├── Business Logic
   └── JSON Responses
   ↓
SQLite Database
```

**Key Characteristics:**
- Separated frontend and backend
- Client-side rendering (JavaScript)
- Stateless (JWT tokens, not sessions)
- Independent deployments
- JSON API (could support mobile apps)

---

## Comparison: Phase 1 vs Phase 2

| Aspect | Phase 1 (Monolith) | Phase 2 (Separated) |
|--------|-------------------|---------------------|
| **Architecture** | Single application | Separated client/server |
| **Rendering** | Server-side (Jinja2) | Client-side (React/Vue) |
| **Authentication** | Sessions/cookies | JWT tokens |
| **State Management** | Server maintains session | Stateless API |
| **API Format** | HTML responses | JSON responses |
| **Deployment** | Single deployment | Two separate deployments |
| **Clients** | Web browser only | Any client (web, mobile, etc.) |
| **Page Updates** | Full page reload | Dynamic updates (no reload) |
| **Development Speed** | [Your experience] | [Your experience] |
| **Debugging** | [Your experience] | [Your experience] |
| **Testing** | [Your experience] | [Your experience] |

### What Did You Learn from This Transition?

[Write 2-4 paragraphs reflecting on what you learned by building the same app twice. Consider:]
- What was easier/harder in each approach?
- When would you choose monolithic vs. separated architecture?
- How did the authentication differences affect development?
- What surprised you about the REST API approach?
- How did AI tools handle the different architectural patterns?

---

## Security Considerations

### What Did You Implement?

**Authentication & Authorization:**
- [How did you handle authentication? What makes it secure or insecure?]
- [How did you enforce the invite system?]
- [How do you prevent unauthorized access to endpoints?]

**Input Validation:**
- [How did you validate post content, email addresses, etc.?]
- [How did you prevent SQL injection? (e.g., using SQLAlchemy ORM)]
- [How did you prevent XSS? (e.g., Jinja2 auto-escaping)]

**Secrets Management:**
- [Where did you store SECRET_KEY, API keys, etc.?]
- [How did you ensure .env files aren't committed to git?]

### Known Limitations

[Be honest about what's NOT secure in your implementation:]
- Example: "JWT tokens don't expire in my implementation"
- Example: "No HTTPS in local development"
- Example: "No rate limiting on API endpoints"
- Example: "Development mode has no real authentication"

---

## Testing & Validation

[Even if you didn't write automated tests, describe how you validated your application worked:]

### How Did You Test?

**Phase 1:**
- [e.g., "Manually tested login flow with different users"]
- [e.g., "Created test posts and verified they appeared on profiles"]
- [e.g., "Tested follow/unfollow functionality"]

**Phase 2:**
- [e.g., "Used FastAPI's /docs interface to test endpoints"]
- [e.g., "Tested authentication flow with browser DevTools"]
- [e.g., "Verified JWT tokens were being sent correctly"]

### What Would You Test If You Had More Time?

- [e.g., "Write unit tests for database models"]
- [e.g., "Write API endpoint tests with pytest"]
- [e.g., "Write frontend component tests with Jest/Vitest"]
- [e.g., "Test error cases (401, 404, validation errors)"]

---

## Lessons Learned

### Using AI Coding Tools

**What worked well:**
- [Example: "AI was great at generating boilerplate code and database models"]
- [Example: "Claude helped debug error messages by explaining what they meant"]
- [List 2-3 things that worked well]

**What didn't work well:**
- [Example: "AI sometimes suggested outdated library versions"]
- [Example: "Had to be very specific about which auth method I chose"]
- [List 2-3 challenges]

**Best prompting strategies:**
- [Example: "Including the full error message and relevant code"]
- [Example: "Asking AI to explain concepts, not just generate code"]
- [List 2-3 strategies you learned]

### Web Development

[What did you learn about web development? Consider:]
- Understanding of client-server architecture
- How authentication works (sessions vs tokens)
- REST API design principles
- Frontend-backend communication
- Database relationships
- [2-4 bullet points about what you learned]

### Architecture

[What did you learn about software architecture? Consider:]
- Trade-offs between monolithic and separated architectures
- When to choose each approach
- How architecture affects development speed and flexibility
- The importance of planning before coding
- [2-4 bullet points about architectural insights]

---

## Deployment

**Where did you deploy?**
- Phase 1: [PythonAnywhere / other / not deployed]
- Phase 2: [PythonAnywhere / Vercel + PythonAnywhere / other / not deployed]

**Deployed URLs:**
- Phase 1: [URL or "not deployed"]
- Phase 2: [URL or "not deployed"]

**Deployment Challenges:**
[What issues did you encounter? How did you solve them? Or write "Not deployed" if you didn't deploy]

---

## Self-Assessment

Rate yourself on these aspects (1-5 scale, where 5 is excellent):

- **Phase 1 Implementation:** [ /5]
  _Brief justification:_ [e.g., "Implemented all core features and one optional feature (likes), works reliably"]

- **Phase 2 Implementation:** [ /5]
  _Brief justification:_ [e.g., "Built clean REST API with 4 core features, good separation of concerns"]

- **API Design Quality:** [ /5]
  _Brief justification:_ [e.g., "Followed REST principles, clear endpoint naming, proper HTTP methods"]

- **Code Organization:** [ /5]
  _Brief justification:_ [e.g., "Separated models, routes, and auth logic; could improve with more modular structure"]

- **Understanding of Architecture:** [ /5]
  _Brief justification:_ [e.g., "Understand trade-offs between approaches and can explain when to use each"]

- **Use of AI Tools:** [ /5]
  _Brief justification:_ [e.g., "Used Claude effectively to generate code and debug, learned to prompt with context"]

### Overall Reflection

**What are you most proud of?**
[Your answer - what did you accomplish that you're happy about?]

**What would you do differently next time?**
[Your answer - what would you change or improve?]

**Which phase did you prefer working on and why?**
[Your answer - Phase 1 or Phase 2, and what made it better for you?]

---

## Appendix

### Links

- **Code Repository:** [GitHub URL if applicable]
- **Deployed URLs:** [Listed above in Deployment section]

### Key Resources Used

[List any documentation, tutorials, or resources that were particularly helpful:]
- [Resource 1]
- [Resource 2]
- ...

### AI Interaction Example

[Optional: Include 1 example of an effective prompt and the result]

**Prompt:**
```
[An example prompt that worked particularly well]
```

**Result:**
[What the AI generated and whether it worked / what you learned from it]
