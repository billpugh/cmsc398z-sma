# Troubleshooting Guide

This file contains solutions to common issues encountered during Phase 1 and Phase 2 development.

**For Claude Code**: Reference this file when students encounter errors. Use the Read tool to access specific sections as needed.

---

## Phase 1 Issues

### "Unable to open database file" or SQLite path errors

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

### "No such table" errors or database not being created

- **CRITICAL**: `db.create_all()` alone may not create the SQLite file - you must also execute a query or commit
- **CRITICAL**: Database initialization must happen AFTER routes.py is created to avoid import errors
  - `app.py` imports `routes`, so if routes.py doesn't exist, any script that imports from `database` or `app` will fail
  - **Correct order**: 1) Create models.py, 2) Create routes.py, 3) Create templates, 4) Initialize database
- ✅ **CORRECT approach** - use a dev script (see DEV-PATTERNS.md for details):

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

### Session/login issues

- Check `SECRET_KEY` is set in `.env` (64+ hex characters)
- Verify Flask-Login configuration (if using real auth)
- Ensure `@login_required` decorator is used on protected routes
- For dev mode: check session setup is correct

### Template not found

- Verify templates are in `phase1/templates/` folder
- Check spelling in `render_template()` calls

### Stylesheet (style.css) not loading

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

### Routes return 404 even though they're defined (Circular Import Issue)

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

---

## Phase 2 Issues

### CORS Errors

- Add CORS middleware to FastAPI with correct origins
- For development, allow `http://localhost:5173` (Vite default)

### 401 Unauthorized even after login

- Verify token is stored in localStorage
- Check Authorization header format: `Bearer <token>`
- Confirm token hasn't expired

### SQLite database locked

- SQLite has limited concurrent write support
- Ensure connections are properly closed
- Consider connection pooling settings

### Frontend can't connect to backend

- Check backend is running on expected port
- Verify API base URL in frontend matches backend address
- Check CORS configuration
