# Phase 2 Development Scripts

This directory contains utility scripts for database management and development tasks in Phase 2.

## Purpose

During Phase 2 development, you'll need to run Python code to:
- Initialize the database (whether new or reusing Phase 1's database)
- Add new tables when implementing features
- Create test data for API testing
- Perform database migrations
- Run one-time setup tasks

Instead of copying and pasting code from chat (which causes indentation errors), we create scripts in this directory. Each script has a clear, descriptive name and can be run easily.

## Phase 2 Database Options

You have two choices for Phase 2:

### Option A: Reuse Phase 1 Database (Recommended)
- Use the same `shared/database/app.db` from Phase 1
- Your users and posts from Phase 1 are already there!
- Can test your REST API immediately with existing data
- DATABASE_URL: `sqlite:///../../../shared/database/app.db`

### Option B: New Database for Phase 2
- Create a fresh database just for Phase 2
- Start with a clean slate
- DATABASE_URL: `sqlite:///./phase2.db`

Your scripts will work with whichever option you choose.

## Common Scripts

You'll create scripts like these as you work through Phase 2:

### `init_database.py`
Initializes the database with all tables for the first time (if using a new database).

**When to run:**
- If using a new Phase 2 database: After creating your models
- If reusing Phase 1 database: Usually not needed (tables already exist)

**Example:**
```bash
uv run python dev_scripts/init_database.py
```

### `add_table.py` (e.g., `add_notification_table.py`)
Adds a new table to the existing database **without deleting your data**.

**When to run:** When adding a new feature that needs a new table.

**Example:**
```bash
uv run python dev_scripts/add_notification_table.py
```

### `reset_database.py`
Deletes the entire database and recreates it with all tables.

**When to run:** When you need to make structural changes to existing tables.

**Warning:** This deletes all your data!

**Example:**
```bash
uv run python dev_scripts/reset_database.py
```

### `create_test_data.py`
Creates sample users, posts, and test data for API testing.

**When to run:** After initializing/resetting database, or when you need test data.

**Tip:** Include JWT tokens in the output so you can easily test authenticated endpoints!

**Example:**
```bash
uv run python dev_scripts/create_test_data.py
```

### `generate_tokens.py`
Generates JWT tokens for existing users (useful for API testing).

**When to run:** When you need to test authenticated API endpoints.

**Example:**
```bash
uv run python dev_scripts/generate_tokens.py
# Output: User tokens you can copy/paste into API requests
```

## Phase 2 Specific Considerations

### Testing REST API with Scripts
Phase 2 scripts are great for:
- Creating test users and getting their JWT tokens
- Populating the database with realistic data
- Resetting to a known state between tests

### Import Paths
Phase 2 scripts import from your backend code:

```python
# In phase2/dev_scripts/init_database.py
from database import app, db
from models import User, Post  # etc.
```

## Running Scripts

Always run scripts from the `phase2/backend/` directory using `uv run`:

```bash
# From phase2/backend/ directory:
uv run python dev_scripts/init_database.py
uv run python dev_scripts/create_test_data.py
uv run python dev_scripts/generate_tokens.py
```

## Benefits

✅ **No copy-paste errors** - Scripts are created correctly the first time
✅ **Repeatable** - Run the same script multiple times
✅ **API testing friendly** - Generate tokens, create test data easily
✅ **Self-documenting** - Script names clearly show what they do
✅ **Version controlled** - Scripts are committed to git
✅ **Educational** - Read scripts to understand database operations
✅ **Preserve data** - Migration scripts keep your data intact

## Important Notes

- Scripts in this directory are **part of your project** - commit them to git!
- Scripts are **educational tools** - read them to understand database operations
- Phase 2 scripts work with **FastAPI models and database**, not Flask
- In production, you'd use migration tools like Alembic, but these scripts teach the same concepts

## Example Directory Structure

As you work through Phase 2, this directory will fill up with scripts like:

```
dev_scripts/
├── README.md
├── init_database.py          # Created when setting up (if new DB)
├── create_test_data.py       # Created for API testing
├── generate_tokens.py        # Created for auth testing
├── add_notification_table.py # Created when adding features
└── reset_database.py         # Created if structural changes needed
```

Each script documents a step in your Phase 2 development process!

## Comparing to Phase 1

**Similarities:**
- Same purpose (database management, testing)
- Same benefits (repeatable, self-documenting)
- Similar scripts (init, add table, reset)

**Differences:**
- Phase 2 scripts import from FastAPI models (not Flask)
- Phase 2 scripts often generate JWT tokens for testing
- Phase 2 might reuse Phase 1's database (or create its own)
- Phase 2 scripts are useful for testing REST API endpoints

## Tips for API Testing

1. **Create test data with known credentials:**
   ```python
   # In create_test_data.py
   user = User(username="testuser", email="test@example.com")
   # Print the JWT token for this user
   ```

2. **Generate tokens for Postman/curl:**
   ```python
   # In generate_tokens.py
   print(f"Authorization: Bearer {token}")
   ```

3. **Reset to clean state between tests:**
   ```bash
   uv run python dev_scripts/reset_database.py
   uv run python dev_scripts/create_test_data.py
   ```

This makes API development much smoother!
