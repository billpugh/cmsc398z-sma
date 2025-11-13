# Deployment Guide: PythonAnywhere

This guide covers deploying both Phase 1 (Flask monolith) and Phase 2 (REST API + frontend) to PythonAnywhere.

---

## Table of Contents

1. [Getting Started with PythonAnywhere](#getting-started-with-pythonanywhere)
2. [Phase 1: Deploying Flask App](#phase-1-deploying-flask-app)
3. [Phase 2: Deploying REST API + Frontend](#phase-2-deploying-rest-api--frontend)
4. [Common Issues & Troubleshooting](#common-issues--troubleshooting)

---

## Getting Started with PythonAnywhere

### 1. Create Account

1. Go to [https://www.pythonanywhere.com](https://www.pythonanywhere.com)
2. Sign up for a **free Beginner account**
3. Verify your email

### 2. Understand Free Account Limitations

- One web app at a time
- Your app will be at `yourusername.pythonanywhere.com`
- HTTPS is provided automatically
- SQLite databases work fine
- Scheduled tasks available
- Some external sites are blocked (but most APIs work)

### 3. Access Your Dashboard

After logging in, you'll see:
- **Dashboard**: Overview
- **Consoles**: Terminal access
- **Files**: File browser
- **Web**: Web app configuration
- **Databases**: Database management

---

## Phase 1: Deploying Flask App

### Step 1: Upload Your Code

**Option A: Git (Recommended)**

1. Open a **Bash console** from the PythonAnywhere dashboard
2. Clone your repository:
   ```bash
   git clone https://github.com/yourusername/your-repo.git
   cd your-repo/phase1
   ```

**Option B: Upload Files**

1. Use the **Files** tab to upload your files
2. Or use the **Upload** button to upload a .zip file

### Step 2: Set Up Virtual Environment

In the Bash console:

```bash
# Navigate to your project directory
cd ~/your-repo/phase1

# Install uv if not already installed
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.cargo/env

# Install dependencies (uv handles virtual environment automatically)
uv sync
```

### Step 3: Configure Environment Variables

1. Create a `.env` file in your project directory:
   ```bash
   nano .env
   ```

2. Add your configuration (update values for production):
   ```bash
   FLASK_APP=app.py
   SECRET_KEY=your-production-secret-key-generate-a-new-one
   DATABASE_URL=sqlite:////home/yourusername/your-repo/phase1/instance/app.db

   # Google OAuth (if using)
   GOOGLE_CLIENT_ID=your-client-id
   GOOGLE_CLIENT_SECRET=your-client-secret

   # Email (if using)
   MAIL_SERVER=smtp.gmail.com
   MAIL_PORT=587
   MAIL_USERNAME=your-email@gmail.com
   MAIL_PASSWORD=your-app-password
   ```

3. Save and exit (Ctrl+O, Enter, Ctrl+X)

### Step 4: Initialize Database

In the Bash console:

```bash
# Navigate to phase1 directory
cd ~/your-repo/phase1

# Initialize database using uv run
uv run python -c "from app import app, db; app.app_context().push(); db.create_all()"
```

### Step 5: Configure Web App

1. Go to **Web** tab in PythonAnywhere dashboard
2. Click **Add a new web app**
3. Choose **Manual configuration**
4. Select **Python 3.10**

**Configure these sections:**

#### Code Section

- **Source code:** `/home/yourusername/your-repo/phase1`
- **Working directory:** `/home/yourusername/your-repo/phase1`

#### Virtualenv Section

- **Virtualenv path:** `/home/yourusername/your-repo/phase1/.venv`

#### WSGI Configuration

1. Click on the WSGI configuration file link
2. Delete all the existing content
3. Replace with this configuration:

```python
import sys
import os
from dotenv import load_dotenv

# Add your project directory to the sys.path
project_home = '/home/yourusername/your-repo/phase1'
if project_home not in sys.path:
    sys.path = [project_home] + sys.path

# Load environment variables
load_dotenv(os.path.join(project_home, '.env'))

# Import your Flask app
from app import app as application  # If your Flask app variable is named 'app'
```

4. Save the file (Ctrl+S or Save button)

### Step 6: Configure Static Files (Optional)

If you have static files (CSS, JS, images):

In the **Web** tab, under **Static files**:
- **URL:** `/static/`
- **Directory:** `/home/yourusername/your-repo/phase1/static`

### Step 7: Update OAuth Redirect URIs (If Using Google OAuth)

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Select your project
3. Go to **APIs & Services** → **Credentials**
4. Edit your OAuth 2.0 Client ID
5. Add authorized redirect URIs:
   - `https://yourusername.pythonanywhere.com/auth/callback`
   - Or whatever your callback route is

### Step 8: Reload and Test

1. Click the **Reload** button (big green button on Web tab)
2. Visit `https://yourusername.pythonanywhere.com`
3. Test your app!

### Step 9: Check Error Logs

If something goes wrong:

1. Go to **Web** tab
2. Scroll down to **Log files**
3. Check:
   - **Error log**: Shows Python errors
   - **Server log**: Shows access logs

---

## Phase 2: Deploying REST API + Frontend

Phase 2 requires deploying both backend (FastAPI) and frontend (React).

### Strategy 1: Backend API + Serve Frontend as Static Files

This approach hosts both on PythonAnywhere.

#### Part A: Deploy Backend API

1. **Upload code** (similar to Phase 1)
   ```bash
   cd ~
   git clone https://github.com/yourusername/your-repo.git
   cd your-repo/phase2/backend
   ```

2. **Install dependencies**
   ```bash
   # Install uv if not already installed
   curl -LsSf https://astral.sh/uv/install.sh | sh
   source $HOME/.cargo/env

   # Sync dependencies (uv handles virtual environment automatically)
   uv sync
   ```

3. **Configure environment**
   ```bash
   nano .env
   ```

   Add:
   ```bash
   SECRET_KEY=your-production-secret-key
   DATABASE_URL=sqlite:////home/yourusername/your-repo/phase2/backend/social_media.db
   CORS_ORIGINS=https://yourusername.pythonanywhere.com
   ```

4. **Initialize database**
   ```bash
   uv run python -c "from database import engine; from models import Base; Base.metadata.create_all(bind=engine)"
   ```

5. **Configure Web App**

   Go to **Web** tab:
   - **Source code:** `/home/yourusername/your-repo/phase2/backend`
   - **Working directory:** `/home/yourusername/your-repo/phase2/backend`
   - **Virtualenv:** `/home/yourusername/your-repo/phase2/backend/.venv`

6. **WSGI Configuration** for FastAPI:

   ```python
   import sys
   import os
   from dotenv import load_dotenv

   # Add your project directory
   project_home = '/home/yourusername/your-repo/phase2/backend'
   if project_home not in sys.path:
       sys.path = [project_home] + sys.path

   # Load environment variables
   load_dotenv(os.path.join(project_home, '.env'))

   # Import FastAPI app
   from main import app as application
   ```

#### Part B: Build and Serve Frontend

1. **Build frontend locally** (on your computer):
   ```bash
   cd phase2/frontend
   npm run build
   # This creates a 'dist' folder
   ```

2. **Upload dist folder to PythonAnywhere**
   - Use the Files tab to upload
   - Or push to git and pull on PythonAnywhere

3. **Configure static files to serve React app**

   In the **Web** tab under **Static files**:
   - **URL:** `/`
   - **Directory:** `/home/yourusername/your-repo/phase2/frontend/dist`

4. **Configure API routes**

   In **Static files**, add:
   - **URL:** `/api`
   - **Directory:** (leave blank or remove - handled by WSGI)

5. **Update frontend API base URL**

   Before building, update your frontend code:
   ```javascript
   // In api.js or similar
   const API_BASE_URL = 'https://yourusername.pythonanywhere.com/api'
   ```

   Then rebuild:
   ```bash
   npm run build
   ```

6. **Handle React Router** (Single Page App routing)

   Create a `.htaccess` file in the `dist` folder (or configure in PythonAnywhere):
   - This ensures that all routes go to index.html
   - PythonAnywhere doesn't use .htaccess, so you may need to handle routing differently

   **Alternative:** Serve the React app from a route in your FastAPI app:

   ```python
   from fastapi.staticfiles import StaticFiles
   from fastapi.responses import FileResponse

   # In main.py
   app.mount("/static", StaticFiles(directory="frontend/dist/assets"), name="static")

   @app.get("/")
   async def read_root():
       return FileResponse("frontend/dist/index.html")

   @app.get("/{full_path:path}")
   async def catch_all(full_path: str):
       # Return index.html for all non-API routes
       if not full_path.startswith("api/"):
           return FileResponse("frontend/dist/index.html")
   ```

### Strategy 2: Split Deployment (Advanced)

Deploy backend on PythonAnywhere, frontend on Vercel/Netlify.

#### Backend on PythonAnywhere

Follow Part A above.

#### Frontend on Vercel

1. **Install Vercel CLI** (on your computer):
   ```bash
   npm install -g vercel
   ```

2. **Deploy:**
   ```bash
   cd phase2/frontend
   vercel
   ```

3. **Configure environment variables** in Vercel dashboard:
   ```
   VITE_API_BASE_URL=https://yourusername.pythonanywhere.com
   ```

4. **Update CORS** in backend `.env`:
   ```
   CORS_ORIGINS=https://your-vercel-app.vercel.app
   ```

#### Frontend on Netlify

1. **Install Netlify CLI**:
   ```bash
   npm install -g netlify-cli
   ```

2. **Deploy:**
   ```bash
   cd phase2/frontend
   netlify deploy --prod
   ```

3. Configure environment variables in Netlify dashboard.

---

## Common Issues & Troubleshooting

### Issue: "ImportError: No module named..."

**Solution:**
- Make sure virtual environment is set correctly in Web tab
- Reinstall dependencies: `uv sync`
- Check WSGI file has correct Python path

### Issue: "DatabaseError: unable to open database file"

**Solution:**
- Check DATABASE_URL path is absolute: `/home/yourusername/...`
- Ensure directory exists and is writable
- Initialize database with `db.create_all()`

### Issue: CORS errors when calling API

**Solution:**
- Update CORS_ORIGINS in backend `.env`
- Make sure CORS middleware is configured in FastAPI:
  ```python
  from fastapi.middleware.cors import CORSMiddleware

  app.add_middleware(
      CORSMiddleware,
      allow_origins=["https://yourusername.pythonanywhere.com"],
      allow_credentials=True,
      allow_methods=["*"],
      allow_headers=["*"],
  )
  ```

### Issue: 404 on page refresh (React Router)

**Solution:**
- Configure catch-all route in FastAPI (see strategy 1 above)
- Or use hash routing in React Router:
  ```javascript
  import { HashRouter } from 'react-router-dom'
  // Use HashRouter instead of BrowserRouter
  ```

### Issue: Static files (CSS/JS) not loading

**Solution:**
- Check static files mapping in Web tab
- Verify paths are absolute: `/home/yourusername/...`
- Check file permissions: `chmod 644 file.css`
- For React, ensure build output is in correct location

### Issue: Environment variables not loading

**Solution:**
- Install python-dotenv: `uv add python-dotenv` (then run `uv sync`)
- Load in WSGI file: `load_dotenv()`
- Or set environment variables in Web tab under "Environment variables" section (for sensitive data)

### Issue: "FastAPI doesn't work on PythonAnywhere"

**Note:** PythonAnywhere uses WSGI, not ASGI. FastAPI works but:
- No WebSocket support on free tier
- Use WSGI mode (which we configured above)
- For better async support, consider upgrading or using Flask-RESTful

### Issue: App works locally but not on PythonAnywhere

**Checklist:**
- [ ] All dependencies in requirements.txt / pyproject.toml
- [ ] Database path is absolute
- [ ] Environment variables are set
- [ ] WSGI file is configured correctly
- [ ] Virtual environment is correct
- [ ] Static files are mapped
- [ ] Check error logs!

### Issue: Need to update code after deployment

**Workflow:**
1. Push changes to git
2. Pull on PythonAnywhere:
   ```bash
   cd ~/your-repo
   git pull
   ```
3. If dependencies changed:
   ```bash
   uv sync
   ```
4. If frontend changed:
   - Rebuild locally: `npm run build`
   - Upload new dist folder
5. Click **Reload** button in Web tab

---

## Production Checklist

Before deploying, verify:

### Security
- [ ] Generated new SECRET_KEY for production
- [ ] Environment variables not committed to git
- [ ] HTTPS enabled (automatic on PythonAnywhere)
- [ ] CORS configured for your domain only
- [ ] Invite list restricts access

### Configuration
- [ ] Database path is absolute
- [ ] All environment variables set
- [ ] Frontend API URL points to production
- [ ] OAuth redirect URIs updated (if applicable)

### Testing
- [ ] Test all features on production site
- [ ] Test on mobile device
- [ ] Check error logs for issues
- [ ] Verify database is persisting data

### Documentation
- [ ] README has setup instructions
- [ ] Deployment URLs documented
- [ ] Known issues documented

---

## Monitoring Your Deployed App

### Check Logs Regularly

In PythonAnywhere Web tab:
- **Error log**: Python errors and exceptions
- **Server log**: HTTP requests and responses

### Useful Commands in Bash Console

```bash
# Check if app is running
curl https://yourusername.pythonanywhere.com/api/health

# View database
sqlite3 ~/path/to/your/database.db
sqlite> SELECT * FROM users;

# Check disk usage
du -sh ~/your-repo

# Tail error log in real-time
tail -f /var/log/yourusername.pythonanywhere.com.error.log
```

### Scheduled Tasks (Optional)

In **Tasks** tab, you can schedule maintenance tasks:
- Database backups
- Clear old sessions
- Send summary emails

---

## Getting Help

### PythonAnywhere Resources
- [PythonAnywhere Help](https://help.pythonanywhere.com/)
- [Forums](https://www.pythonanywhere.com/forums/)
- [Flask deployment guide](https://help.pythonanywhere.com/pages/Flask/)

### Debugging Steps
1. Check error logs first!
2. Try running your app in Bash console
3. Test database connection manually
4. Check all file paths are absolute
5. Verify virtual environment is correct

### Common Commands

```bash
# Navigate to your project directory
cd ~/your-repo/phase1

# Run Flask app manually (for debugging)
uv run python app.py

# Check Python version
uv run python --version

# Test database connection
uv run python -c "from app import db; print(db)"
```

---

## Next Steps After Deployment

1. Share your app URL: `https://yourusername.pythonanywhere.com`
2. Test with real users (classmates, friends)
3. Monitor error logs
4. Document any issues in ARCHITECTURE.md
5. Consider improvements based on user feedback

---

## Alternative Deployment Options

If PythonAnywhere doesn't work for you:

### Heroku (Free tier ended, but still an option)
- Good for both Flask and FastAPI
- PostgreSQL database included
- Easy deployment with git push

### Railway
- Free tier available
- Supports Python apps
- Easy deployment

### Render
- Free tier for web services
- Supports both Python and static sites
- Good for Phase 2 separated architecture

### Replit
- Free tier available
- Good for quick demos
- Built-in database

Each has different configuration - consult their documentation.

---

## Summary

**Phase 1 (Flask):**
1. Upload code to PythonAnywhere
2. Set up virtual environment
3. Configure WSGI file
4. Initialize database
5. Reload and test

**Phase 2 (REST API + Frontend):**
1. Deploy backend API with WSGI
2. Build frontend locally
3. Upload dist folder or deploy to Vercel/Netlify
4. Configure CORS
5. Test integration

Good luck with deployment! Remember to check error logs if anything goes wrong.
