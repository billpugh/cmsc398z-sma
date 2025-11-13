# Frontend - Starter Template

This is a minimal React + Vite starter template for the frontend. You'll use AI tools to build out the full single-page application.

## Quick Start

1. **Navigate to frontend directory:**
   ```bash
   cd frontend
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Start development server:**
   ```bash
   npm run dev
   ```

4. **Visit:** `http://localhost:5173`

## Project Structure

Build toward this structure:

```
frontend/
├── package.json
├── vite.config.js       # Vite configuration
├── index.html           # HTML entry point
├── src/
│   ├── main.jsx         # React entry point
│   ├── App.jsx          # Main app component
│   ├── api.js           # API client functions
│   ├── components/
│   │   ├── Login.jsx
│   │   ├── Feed.jsx
│   │   ├── Profile.jsx
│   │   ├── CreatePost.jsx
│   │   └── Navbar.jsx
│   └── styles/
│       └── App.css      # Styling
└── public/
    └── (static assets)
```

## Suggested First Prompts

### 1. Basic React App Setup
```
Create a React application with:
- React Router for navigation (routes: /login, /feed, /profile/:username)
- Main App component with routing
- Navbar component with links
- Basic CSS styling (responsive design)
- Entry point in main.jsx
```

### 2. API Client Module
```
Create an API client module (api.js) with:
- Base URL configuration (use Vite env variable)
- Functions for each API endpoint:
  - login(email) - POST /api/auth/login
  - getPosts() - GET /api/posts/feed
  - createPost(content) - POST /api/posts
  - getProfile(username) - GET /api/users/{username}
- Automatically add Authorization header with JWT token from localStorage
- Handle errors (401, 404, network errors)
```

### 3. Authentication & Token Storage
```
Create authentication logic:
- Store JWT token in localStorage on login
- Retrieve token for API requests
- Clear token on logout
- Redirect to /login if no token (protected routes)
- Parse token to get current user info
```

### 4. Login Component
```
Create Login component:
- Form with email input
- Call API login endpoint
- Store JWT token in localStorage on success
- Redirect to /feed after login
- Show error message if login fails
- Show loading state while submitting
```

### 5. Feed Component
```
Create Feed component:
- Fetch posts from API on component mount
- Display posts with author, content, timestamp
- Show "Create Post" form at top (for logged-in users)
- Refresh feed after creating new post
- Show loading state while fetching
- Handle empty feed (show message)
```

### 6. Profile Component
```
Create Profile component:
- Get username from URL parameter (useParams hook)
- Fetch user data and posts from API
- Display user info (username, join date, post count)
- List all posts by user
- Show follow/unfollow button if not own profile
- Show loading state while fetching
```

### 7. CreatePost Component
```
Create CreatePost component:
- Text input/textarea for post content
- Character counter (e.g., 280 max)
- Submit button
- Call API to create post
- Clear form after successful post
- Show error if post fails
- Disable submit while posting
```

## Running the App

```bash
# Development mode (with hot reload)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Environment Variables

Create `.env` file for configuration:

```env
VITE_API_BASE_URL=http://localhost:8000
```

Access in code: `import.meta.env.VITE_API_BASE_URL`

For production, update to your deployed backend URL.

## Styling Tips

### Option 1: Simple CSS
Write custom CSS in `src/styles/App.css` - keep it simple and responsive.

### Option 2: Tailwind CSS
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Then ask AI to configure Tailwind and style components.

### Option 3: Component Library
```bash
# Material-UI
npm install @mui/material @emotion/react @emotion/styled

# Or Chakra UI
npm install @chakra-ui/react @emotion/react @emotion/styled framer-motion
```

Then ask AI to use the component library.

## Testing API Integration

1. **Start backend first:**
   ```bash
   cd ../backend
   uvicorn main:app --reload
   ```

2. **Then start frontend:**
   ```bash
   cd ../frontend
   npm run dev
   ```

3. **Check browser DevTools:**
   - Open Network tab
   - Watch API requests
   - Check for CORS errors, 401 errors, etc.

4. **Debugging tips:**
   - `console.log()` API responses
   - Check if token is stored: `localStorage.getItem('token')`
   - Verify API base URL is correct

## Common Issues

### API requests failing (CORS)
Make sure backend has CORS middleware configured for `http://localhost:5173`.

### Token not being sent
Check that API client adds `Authorization: Bearer ${token}` header.

### Routes not working (404 on refresh)
This is normal in development. For production, configure server to serve index.html for all routes.

### npm install fails
- Delete `node_modules` and `package-lock.json`
- Run `npm install` again
- Make sure Node.js version is 18+

## React Tips for Beginners

### State Management
Use `useState` hook for component state:
```javascript
const [posts, setPosts] = useState([])
```

### Side Effects (API Calls)
Use `useEffect` hook to fetch data on mount:
```javascript
useEffect(() => {
  // Fetch data here
}, []) // Empty array = run once on mount
```

### Routing
Use `useNavigate` to redirect programmatically:
```javascript
const navigate = useNavigate()
navigate('/feed')
```

### URL Parameters
Use `useParams` to get route parameters:
```javascript
const { username } = useParams()
```

## AI Prompting Tips

Be specific about what you want:

✅ "Create a Feed component that fetches posts from /api/posts/feed using useEffect, displays them in a list with author and timestamp, and shows a loading spinner while fetching"

❌ "Make a feed page"

Include context about your API structure:

✅ "My API returns posts as: { id, user_id, content, created_at, author: { username, display_name } }. Create a Post component to display this data."

## Need Help?

- React docs: https://react.dev/
- Vite docs: https://vitejs.dev/
- Check main [Phase 2 README](../README.md)
- Post on Piazza
