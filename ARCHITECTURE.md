# Architecture Documentation

> **Note:** This document is maintained by Claude as students build their project. It serves as a technical reference documenting what was actually implemented.

**Student Name:** [Name]
**Project:** Social Media Web Application
**Last Updated:** [Date]

---

## Phase 1: Flask Monolithic Application

### Implementation Details

**App Name:** [Name chosen by student]
**Color Scheme:** [Scheme chosen by student]
**Status:** [Not started / In progress / Complete]

### Technology Stack

- **Framework:** Flask
- **Database:** SQLite (`shared/database/app.db`)
- **Authentication:** [Development Mode / Google OAuth / Email Magic Links]
- **Templating:** Jinja2
- **Key Libraries:** Flask-SQLAlchemy, [additional libraries based on auth choice]

### Database Schema

[Claude: Document the actual database tables after models are created]

**Users Table:**
```
- id: INTEGER PRIMARY KEY
- email: TEXT UNIQUE NOT NULL
- username: TEXT UNIQUE NOT NULL
- display_name: TEXT
- created_at: TIMESTAMP
[additional fields]
```

**Posts Table:**
```
- id: INTEGER PRIMARY KEY
- user_id: INTEGER (foreign key → users.id)
- content: TEXT NOT NULL
- created_at: TIMESTAMP
[additional fields]
```

**[Additional Tables]:**
[Claude: Document any additional tables like Follow, Like, Comment, etc.]

### Routes Implemented

[Claude: List all routes after implementation]

**Authentication:**
- `GET /dev-login` or `GET /login` - [Description]
- `POST /dev-login` or `POST /login` - [Description]
- `POST /logout` - [Description]

**Core Features:**
- `GET /` - [Description]
- `POST /posts` - [Description]
- `GET /profile/<username>` - [Description]

**Additional Features:**
[Claude: Document additional feature routes]

### Features Implemented

**Minimal Features:**
- [x] User authentication
- [x] Create posts
- [x] View global feed
- [x] View user profiles

**Additional Feature(s):**
- [ ] [Feature name and description]

### Authentication & Access Control

**Authentication Method:** [Detailed description of what was implemented]

**Access Control Model:** [Fully Private / Public Reading, Private Writing]
- [Description of how it's enforced]

**Invite System:** [Text file / Database table / Not implemented]
- [Details of implementation]

### Key Implementation Notes

[Claude: Document any interesting implementation choices, workarounds, or patterns used]

---

## Phase 2: REST API + JavaScript Frontend

### Implementation Details

**Status:** [Not started / In progress / Complete]
**Frontend Framework:** [React / Vue / Other]
**Authentication:** [Development Mode / Production JWT]

### Technology Stack

**Backend:**
- **Framework:** FastAPI
- **Database:** [Shared SQLite from Phase 1 / New Phase 2 database]
- **Authentication:** JWT tokens (python-jose)
- **Key Libraries:** SQLAlchemy, python-jose, uvicorn

**Frontend:**
- **Framework:** [React / Vue]
- **Build Tool:** Vite
- **HTTP Client:** Axios
- **Key Libraries:** [React Router / Vue Router], [additional libraries]

### Database Strategy

[Claude: Document whether they reused Phase 1 database or created new one, and any schema differences]

**Database Location:** [Path]
**Reused Phase 1 Database:** [Yes / No]
**Schema Changes:** [None / Description of changes]

### REST API Endpoints

[Claude: Document all implemented API endpoints]

**Authentication:**
```
POST /token or /dev-login
Request: { ... }
Response: { "access_token": "...", "token_type": "bearer" }
```

**Users:**
```
GET /users/me
Auth: Required
Response: { "id": 1, "username": "...", ... }

GET /users/{user_id}
Auth: [Required / Public]
Response: { ... }
```

**Posts:**
```
GET /posts
Auth: [Required / Public]
Query Params: skip, limit
Response: [{ ... }, ...]

POST /posts
Auth: Required
Request: { "content": "..." }
Response: { "id": 1, "content": "...", ... }
```

**[Additional Features]:**
[Claude: Document any additional endpoints]

### Frontend Architecture

**Components Implemented:**
- [Component name] - [Description]
- [Component name] - [Description]

**State Management:**
[Description of how auth state, user data, etc. is managed]

**Routing:**
- `/login` - [Description]
- `/` - [Description]
- [Additional routes]

**API Integration:**
[Description of how frontend communicates with backend, token handling, etc.]

### Features Implemented (Phase 2)

**Core Features (3-4 selected):**
- [ ] [Feature name]
- [ ] [Feature name]
- [ ] [Feature name]

**Note:** Phase 2 does not require feature parity with Phase 1.

### Authentication Flow

[Claude: Document the actual JWT authentication flow implemented]

1. [Step-by-step description of how authentication works]
2. [Token storage approach]
3. [How token is sent to backend]
4. [How token is validated]

### Key Implementation Notes

[Claude: Document any interesting implementation choices, CORS configuration, error handling patterns, etc.]

---

## Security Considerations

### What Was Implemented

**Authentication:**
[Claude: Document actual security measures in place]

**Input Validation:**
[Claude: Document validation approaches used]

**Secrets Management:**
[Claude: Confirm .env usage, .gitignore configuration]

### Known Limitations

[Claude: Document known security limitations honestly]

Example areas to document:
- Token expiration settings (or lack thereof)
- Rate limiting (if not implemented)
- HTTPS in development
- Input sanitization gaps

---

## Technical Decisions Made

### Phase 1 Decisions

[Claude: Reference key decisions from DECISIONS-MADE-phase1.md without duplicating]

- **Authentication:** [Brief summary with reference to DECISIONS-MADE]
- **Additional Feature:** [Brief summary with reference to DECISIONS-MADE]
- **Access Control:** [Brief summary with reference to DECISIONS-MADE]

See `phase1/DECISIONS-MADE-phase1.md` for full decision rationale.

### Phase 2 Decisions

[Claude: Reference key decisions from DECISIONS-MADE-phase2.md without duplicating]

- **Frontend Framework:** [Brief summary with reference to DECISIONS-MADE]
- **Database Strategy:** [Brief summary with reference to DECISIONS-MADE]
- **Features Selected:** [Brief summary with reference to DECISIONS-MADE]

See `phase2/DECISIONS-MADE-phase2.md` for full decision rationale.

---

## Deployment

**Phase 1 Deployment:**
- Status: [Not deployed / Deployed]
- Location: [PythonAnywhere / Other / Local only]
- URL: [URL or "Not deployed"]

**Phase 2 Deployment:**
- Backend Status: [Not deployed / Deployed]
- Backend Location: [PythonAnywhere / Other / Local only]
- Frontend Status: [Not deployed / Deployed]
- Frontend Location: [PythonAnywhere / Vercel / Netlify / Other / Local only]
- URLs: [URLs or "Not deployed"]

### Deployment Notes

[Claude: Document any deployment-specific configuration, challenges encountered, etc.]

---

## Testing Approach

[Claude: Document what testing was actually done, not aspirational testing]

**Phase 1 Testing:**
- [How features were tested - manual testing, specific flows tested]

**Phase 2 Testing:**
- Backend: [FastAPI docs testing, curl commands, etc.]
- Frontend: [Browser testing, network tab inspection, etc.]
- Integration: [End-to-end flows tested]

---

## Development Environment

**Tools Used:**
- AI Assistant: Claude Code
- Python Version: [Version]
- Node.js Version (Phase 2): [Version]
- Browser: [Browser used for testing]
- Operating System: [OS]

**Development Challenges:**
[Claude: Note any environment-specific issues encountered and resolved]

---

## Project Statistics

**Phase 1:**
- Lines of Python code: [Approximate]
- Number of routes: [Count]
- Number of templates: [Count]
- Number of database models: [Count]

**Phase 2:**
- Backend lines of code: [Approximate]
- Number of API endpoints: [Count]
- Frontend lines of code: [Approximate]
- Number of components: [Count]

---

## Notes for Future Development

[Claude: Document any TODO items, known issues, or areas for improvement identified during development]

**Potential Improvements:**
- [Item]
- [Item]

**Known Issues:**
- [Issue]
- [Issue]

---

**Last Updated By:** Claude [Date]
