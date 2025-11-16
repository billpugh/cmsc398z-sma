# TODO: Shared Models Migration (Optional)

**Purpose**: Migrate your Phase 1 application to use shared models and database files, preparing for Phase 2 development.

**When to use this**: After completing Phase 1, before or during Phase 2 development.

**Time required**: 15-20 minutes

**Why do this?**
- **DRY principle**: Don't Repeat Yourself - one set of models for both phases
- **Consistency**: Guaranteed identical database schema across phases
- **Better architecture**: Models independent of web framework (more portable)
- **Easier maintenance**: Change models once, both phases automatically updated

---

## 🤖 For Claude/AI Assistants

This guide helps students refactor their Phase 1 application to use shared models before starting Phase 2.

**Key principles**:
1. **Verify Phase 1 works first** - Don't migrate a broken app
2. **Incremental changes** - One step at a time with verification
3. **Preserve student data** - Don't lose their database
4. **Test after each major step** - Ensure Phase 1 still works
5. **Explain the changes** - Students should understand why each change is made

**Migration overview**:
1. Create shared package (`shared/__init__.py`, `shared/models.py`, `shared/database.py`)
2. Convert Phase 1 from Flask-SQLAlchemy to plain SQLAlchemy
3. Update imports in Phase 1
4. Move database file to shared location (if not already there)
5. Test Phase 1 works with new structure
6. (If Phase 2 exists) Update Phase 2 to use shared models

**Common issues**:
- Import errors: Usually need `sys.path` manipulation
- Session management: `db.session` becomes `get_db()` pattern
- Circular imports: Models must not import from app.py

---

## Part 0: Pre-Migration Check

**🤖 Claude: Say:**

> Before we migrate to shared models, let me verify your Phase 1 application is working correctly.
>
> This migration will:
> 1. Create a `shared/` package with your database models
> 2. Update Phase 1 to import models from `shared/` instead of having its own `models.py`
> 3. Move your database to `shared/database/` (if not already there)
> 4. Keep all your existing data intact
>
> **Benefits**:
> - Phase 2 will automatically use the same models as Phase 1
> - You'll never have to worry about keeping two model files in sync
> - Better code organization (models separate from application)
>
> Let me first check that Phase 1 is working.

**🤖 Claude: Do:**
1. Read `phase1/app.py` to understand current structure
2. Read `phase1/models.py` to see current models
3. Check if database exists and where it's located
4. Look for any custom models or relationships student added

**🤖 Claude: Ask student:**

> I can see your Phase 1 setup. Before we migrate, please:
> 1. Start your Phase 1 app (`cd phase1 && uv run flask run`)
> 2. Verify you can log in and see your posts
> 3. Confirm everything works as expected
>
> Let me know when you've verified Phase 1 is working, and we'll proceed with the migration.

🛑 **Wait for student confirmation before proceeding**

---

## Part 1: Create Shared Package Structure

**🤖 Claude: Say:**

> Great! Now let's create the shared package structure. This will be a proper Python package that both Phase 1 and Phase 2 can import from.
>
> I'll create three files in the `shared/` directory:
> 1. `__init__.py` - Makes it a Python package (can be empty)
> 2. `models.py` - Your database models (User, Post, Like, Follow, etc.)
> 3. `database.py` - Database configuration and utilities
>
> These will use **plain SQLAlchemy** instead of Flask-SQLAlchemy, which works with both Flask and FastAPI.

**🤖 Claude: Do:**

1. Create `shared/__init__.py` (empty file to make it a package)

2. Create `shared/models.py` by converting Phase 1's models to plain SQLAlchemy:
   - Change `from flask_sqlalchemy import SQLAlchemy` to `from sqlalchemy import Column, Integer, String, DateTime, ForeignKey, Text`
   - Change `db.Model` to `Base` (from declarative_base)
   - Keep all column definitions, relationships, and constraints identical
   - Include any custom models the student added

3. Create `shared/database.py` with:
   ```python
   from sqlalchemy import create_engine
   from sqlalchemy.orm import sessionmaker, declarative_base
   import os
   from pathlib import Path

   # Get database URL from environment, default to shared location
   DATABASE_URL = os.getenv('DATABASE_URL', 'sqlite:///./shared/database/app.db')

   # Convert relative path to absolute if using SQLite
   if DATABASE_URL.startswith('sqlite:///'):
       db_path = DATABASE_URL.replace('sqlite:///', '')
       if not db_path.startswith('/'):
           # Relative path - make it relative to repo root
           repo_root = Path(__file__).parent.parent
           abs_path = repo_root / db_path
           DATABASE_URL = f'sqlite:///{abs_path}'

   # Create engine
   engine = create_engine(
       DATABASE_URL,
       connect_args={"check_same_thread": False} if DATABASE_URL.startswith('sqlite') else {}
   )

   # Create session factory
   SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

   # Create base class for models
   Base = declarative_base()

   def get_db():
       """Get database session (for dependency injection pattern)."""
       db = SessionLocal()
       try:
           yield db
       finally:
           db.close()

   def init_db():
       """Initialize database - create all tables."""
       # Import models to register them with Base
       from shared import models
       Base.metadata.create_all(bind=engine)
   ```

4. Show student the created files

**🤖 Claude: Say:**

> I've created the shared package with your models. The key changes:
>
> **Before (Flask-SQLAlchemy)**:
> ```python
> class User(db.Model):
>     id = db.Column(db.Integer, primary_key=True)
> ```
>
> **After (Plain SQLAlchemy)**:
> ```python
> class User(Base):
>     __tablename__ = 'user'
>     id = Column(Integer, primary_key=True)
> ```
>
> These are functionally identical - just different syntax. Plain SQLAlchemy works with any framework.

---

## Part 2: Update Phase 1 Application

**🤖 Claude: Say:**

> Now let's update Phase 1 to import from the shared package. This involves:
> 1. Adding the parent directory to Python's import path
> 2. Importing models from `shared.models`
> 3. Changing session management from `db.session` to the new pattern
>
> The app will work exactly the same - we're just reorganizing where the code lives.

**🤖 Claude: Do:**

1. Update `phase1/app.py`:

```python
# Add to the very top of app.py (after initial imports):
import sys
from pathlib import Path

# Add parent directory to path so we can import from shared/
sys.path.insert(0, str(Path(__file__).parent.parent))

# Now import from shared instead of local models
from shared.models import User, Post, Like, Follow
from shared.database import SessionLocal, init_db, engine

# Create Flask app
app = Flask(__name__)
# ... existing config ...

# Database session management for Flask
# Instead of db.session, we'll use get_db() pattern
from contextlib import contextmanager

@contextmanager
def get_db():
    """Get database session for request."""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# Or use Flask's g object for request-scoped sessions:
@app.before_request
def before_request():
    from flask import g
    g.db = SessionLocal()

@app.teardown_request
def teardown_request(exception):
    from flask import g
    db = g.pop('db', None)
    if db is not None:
        db.close()

# Then in routes, use: from flask import g; g.db.query(User)...
```

2. Update all routes in `phase1/app.py` to use the new session pattern:
   - Replace `db.session.query(...)` with `g.db.query(...)`
   - Replace `db.session.add(...)` with `g.db.add(...)`
   - Replace `db.session.commit()` with `g.db.commit()`
   - Replace `db.session.delete(...)` with `g.db.delete(...)`

3. If `phase1/auth.py` exists and uses models, update it:
   - Add same sys.path manipulation
   - Import from shared.models
   - Update session usage

4. Update `phase1/.env` if needed:
   - Ensure DATABASE_URL points to correct location
   - If database was in phase1/, it should now point to `sqlite:///../shared/database/app.db`

5. Remove or rename old `phase1/models.py`:
   ```bash
   mv phase1/models.py phase1/models.py.backup
   ```

**🤖 Claude: Say:**

> I've updated Phase 1 to use the shared models. The main changes:
>
> 1. **Import path setup**: Added `sys.path.insert(0, ...)` so Python can find the shared package
> 2. **Model imports**: Changed from `from models import User` to `from shared.models import User`
> 3. **Session management**: Changed from `db.session` to Flask's `g.db` (request-scoped)
>
> Your old `models.py` has been renamed to `models.py.backup` just in case.

---

## Part 3: Move/Verify Database Location

**🤖 Claude: Say:**

> Let's make sure the database is in the shared location so both phases can access it.

**🤖 Claude: Do:**

1. Check current database location (look at old DATABASE_URL in phase1/.env)

2. If database is in `phase1/` directory:
   ```bash
   # Create shared/database directory if it doesn't exist
   mkdir -p shared/database

   # Move database file
   mv phase1/app.db shared/database/app.db
   # OR if it was in a different location, move from there
   ```

3. If database is already in `shared/database/`, verify it exists:
   ```bash
   ls -lh shared/database/app.db
   ```

4. Update `phase1/.env` to point to shared database:
   ```
   DATABASE_URL=sqlite:///../shared/database/app.db
   ```

**🤖 Claude: Say:**

> Database is now located at `shared/database/app.db` and accessible to both phases.

---

## Part 4: Test Phase 1 with Shared Models

**🤖 Claude: Say:**

> Now let's test that Phase 1 still works with the shared models!
>
> Please:
> 1. Stop your Phase 1 server if it's running (Ctrl+C)
> 2. Start it again: `cd phase1 && uv run flask run`
> 3. Visit http://localhost:5000
> 4. Try these actions:
>    - Log in with your username
>    - View the home feed (you should see your existing posts)
>    - Create a new post
>    - View a profile
>    - Like a post (if you have likes feature)
>    - Follow someone (if you have follow feature)
>
> Everything should work exactly as before. Let me know if you see any errors!

🛑 **Wait for student to test**

**🤖 Claude: If student reports errors:**

Common issues and fixes:

1. **ImportError: No module named 'shared'**
   - Check sys.path.insert() is at the very top of app.py
   - Verify `shared/__init__.py` exists

2. **AttributeError: 'NoneType' object has no attribute 'query'**
   - Check Flask's g.db is being set up in before_request
   - Verify routes are using `g.db` not `db.session`

3. **Database file not found**
   - Check DATABASE_URL in .env is correct
   - Verify shared/database/app.db exists

4. **No such table errors**
   - May need to run init_db() if schema changed
   - Or copy database file to correct location

**🤖 Claude: When student confirms it works:**

> Excellent! Phase 1 is now using shared models. You can safely delete `phase1/models.py.backup` if you want.
>
> **What you've accomplished**:
> ✅ Created a shared Python package with your database models
> ✅ Converted from Flask-SQLAlchemy to plain SQLAlchemy
> ✅ Updated Phase 1 to import from shared package
> ✅ Centralized your database in shared/database/
> ✅ Verified everything still works
>
> **Benefits for Phase 2**:
> - Phase 2 will automatically use the same models
> - No risk of schema mismatches between phases
> - Better code organization and maintainability
>
> You're now ready to start Phase 2! When you begin, Phase 2 will automatically use these shared models.

**🤖 Claude: Update DECISIONS-MADE-phase2.md:**

If `phase2/DECISIONS-MADE-phase2.md` exists, update the "Shared Models Migration" section to:

```markdown
## Shared Models Migration
**Choice**: Migrated to Shared Models

**Implementation**:
- Shared models: `shared/models.py` (plain SQLAlchemy)
- Shared database config: `shared/database.py`
- Phase 1 imports from shared package
- Phase 2 imports from shared package (or will when created)

**Reasoning**:
- Eliminates code duplication between phases
- Ensures schema consistency (impossible for models to drift)
- Better code organization (models independent of framework)
- Single source of truth for database structure

**Date**: [current date]
```

---

## Part 5: Update Phase 2 (If Already Started)

**🤖 Claude: Say (only if student already has phase2/backend/):**

> I notice you've already started Phase 2. Let's update it to use the shared models as well.

**🤖 Claude: Do:**

1. Check if `phase2/backend/models.py` exists

2. If it exists, update `phase2/backend/main.py` (or wherever models are imported):

```python
# Add to top of main.py (after initial imports):
import sys
from pathlib import Path

# Add parent directory to path so we can import from shared/
sys.path.insert(0, str(Path(__file__).parent.parent.parent))

# Import from shared instead of local models
from shared.models import User, Post, Like, Follow
from shared.database import get_db, Base
```

3. Remove imports of local models.py

4. Update `phase2/backend/database.py` if it exists:
   - Change it to import and re-export from shared:
   ```python
   # phase2/backend/database.py can now just re-export from shared
   import sys
   from pathlib import Path
   sys.path.insert(0, str(Path(__file__).parent.parent.parent))

   from shared.database import get_db, Base, SessionLocal, engine, init_db

   # Re-export for convenience
   __all__ = ['get_db', 'Base', 'SessionLocal', 'engine', 'init_db']
   ```

5. Or delete `phase2/backend/database.py` and `phase2/backend/models.py` entirely, importing directly from shared

6. Update `phase2/backend/.env` to point to shared database:
   ```
   DATABASE_URL=sqlite:///../../shared/database/app.db
   ```

7. Backup old files:
   ```bash
   mv phase2/backend/models.py phase2/backend/models.py.backup
   mv phase2/backend/database.py phase2/backend/database.py.backup
   ```

**🤖 Claude: Say:**

> I've updated Phase 2 to use the shared models as well. Please test:
> 1. Stop Phase 2 backend if running
> 2. Start it: `cd phase2/backend && uv run uvicorn main:app --reload`
> 3. Start frontend: `cd phase2/frontend && npm run dev`
> 4. Test that everything works
>
> Both Phase 1 and Phase 2 now use the exact same models and database!

**🤖 Claude: Update DECISIONS-MADE-phase2.md:**

Update the "Shared Models Migration" section in `phase2/DECISIONS-MADE-phase2.md` to:

```markdown
## Shared Models Migration
**Choice**: Migrated to Shared Models

**Implementation**:
- Shared models: `shared/models.py` (plain SQLAlchemy)
- Shared database config: `shared/database.py`
- Phase 1 imports from shared package
- Phase 2 imports from shared package

**Reasoning**:
- Eliminates code duplication between phases
- Ensures schema consistency (impossible for models to drift)
- Better code organization (models independent of framework)
- Single source of truth for database structure
- Migrated after Phase 2 was already started

**Date**: [current date]
```

---

## Troubleshooting

### Import errors

**Problem**: `ImportError: No module named 'shared'`

**Solution**:
1. Verify `shared/__init__.py` exists (even if empty)
2. Check sys.path manipulation is at the very top of the file:
   ```python
   import sys
   from pathlib import Path
   sys.path.insert(0, str(Path(__file__).parent.parent))
   ```
3. For Phase 2, use `.parent.parent.parent` (one more level)

### Session errors

**Problem**: `AttributeError: 'NoneType' object has no attribute 'query'`

**Solution**: Verify Flask's before_request sets up g.db:
```python
@app.before_request
def before_request():
    from flask import g
    g.db = SessionLocal()
```

### Database not found

**Problem**: `sqlite3.OperationalError: unable to open database file`

**Solution**:
1. Check DATABASE_URL in .env points to correct location
2. Use absolute path or correct relative path
3. Verify database file actually exists at that location
4. Check shared/database/ directory exists

### Schema errors

**Problem**: `sqlite3.OperationalError: no such table: user`

**Solution**: Initialize the database:
```python
# In Flask shell or Python:
from shared.database import init_db
init_db()
```

### Circular import errors

**Problem**: `ImportError: cannot import name '...' from partially initialized module`

**Solution**:
- Don't import app from models
- Don't import models from database.py
- Keep dependencies one-way: app → models → database

---

## Rolling Back (If Needed)

If you need to revert the migration:

1. **Restore Phase 1**:
   ```bash
   mv phase1/models.py.backup phase1/models.py
   # Restore original app.py from git if you didn't back it up
   git checkout phase1/app.py
   ```

2. **Move database back**:
   ```bash
   mv shared/database/app.db phase1/app.db
   ```

3. **Update .env**:
   ```
   DATABASE_URL=sqlite:///./app.db
   ```

4. **Reinstall Flask-SQLAlchemy** if you removed it:
   ```bash
   cd phase1
   uv add flask-sqlalchemy
   ```

---

## Summary

After completing this migration:

- ✅ Models are in `shared/models.py` (plain SQLAlchemy)
- ✅ Database is in `shared/database/app.db`
- ✅ Phase 1 imports from shared package
- ✅ Phase 2 imports from shared package (or will when created)
- ✅ Both phases use identical schema
- ✅ No more keeping two model files in sync!

**Next step**: Proceed with Phase 2 development (TODO-phase2.md)
