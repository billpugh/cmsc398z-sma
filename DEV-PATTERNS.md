# Development Patterns Guide

This file documents key development patterns for working with students on this project.

**For Claude Code**: These patterns should be followed when helping students implement features.

---

## The `dev_scripts/` Directory Pattern

**CRITICAL**: When students need to run multiline Python code, **NEVER** ask them to copy-paste code from chat. This causes indentation errors and frustration.

**Instead, use the dev_scripts approach:**

### How to Use dev_scripts

**For any multiline Python code that students need to run:**

1. **Create the script in `dev_scripts/` directory:**
   - Use descriptive names: `init_database.py`, `create_test_users.py`, `reset_database.py`
   - Makes purpose clear and self-documenting
   - Keeps setup scripts separate from application code
   - No need to add to `.gitignore` - these are useful to commit

2. **Write the script with the Write tool:**
   ```python
   # dev_scripts/init_database.py
   """Initialize the database with all tables."""
   from database import app, db
   from models import User, Post, Follow

   with app.app_context():
       db.create_all()
       db.session.execute(db.text("SELECT 1"))
       db.session.commit()
       print("✅ Database initialized successfully!")
       print(f"Tables created: {list(db.metadata.tables.keys())}")
   ```

3. **Run it immediately using the Bash tool:**
   ```bash
   uv run python dev_scripts/init_database.py
   ```

4. **Show the student what happened:**
   - "✅ Database initialized! I've created `dev_scripts/init_database.py` if you need to run it again."

### When to Use dev_scripts

| Situation | Script Name | Why |
|-----------|-------------|-----|
| Database initialization | `init_database.py` | Complex, repeatable, important |
| Reset database | `reset_database.py` | Destructive, want clear name |
| Create test data | `create_test_data.py` | Repeatable, useful for development |
| Database migrations | `add_follow_table.py` | Important to keep record, preserves data |
| One-time data fixes | `fix_user_emails.py` | Document what was changed |

### When NOT to Use dev_scripts

**For simple one-liners, run directly:**
```bash
# Generate SECRET_KEY
uv run python -c "import secrets; print(secrets.token_hex(32))"

# Quick check
uv run python -c "from database import db; print(db)"
```

**Rule of thumb:** If it's more than 2 lines or will be run multiple times, create a dev_script.

### Interactive Python Sessions

**If students need to explore interactively (rare), use:**
```bash
uv run python -i dev_scripts/setup_context.py
```

This loads the script then drops into interactive mode.

### Benefits of This Approach

- ✅ **No copy-paste errors** - students don't manually copy code
- ✅ **Self-documenting** - clear script names show what was done
- ✅ **Repeatable** - students can re-run setup scripts
- ✅ **Version controlled** - scripts are committed, showing setup history
- ✅ **Educational** - students see the code and can modify it
- ✅ **Clean** - separate directory keeps app code uncluttered

---

## Database Migrations vs. Recreation

**CRITICAL**: When students add features that require database changes (like following system, likes, comments), prefer database migrations over recreation to preserve their test data (users and posts they've already created).

### When to Migrate (Preferred)

**✅ Use migrations for additive changes:**
- Adding new tables (Follow, Like, Comment)
- Adding new columns with defaults or nullable columns
- Adding indexes
- Creating relationships that don't affect existing data

**Example: Adding Follow table after students have created users**

Create `dev_scripts/add_follow_table.py`:
```python
"""Add Follow table to existing database without deleting users/posts."""
from database import app, db
from models import Follow  # Import new model

with app.app_context():
    # Create only the new table, preserving existing data
    Follow.__table__.create(db.engine, checkfirst=True)
    db.session.commit()
    print("✅ Follow table added successfully!")
    print("✅ Your existing users and posts are preserved!")
    print(f"Tables in database: {list(db.metadata.tables.keys())}")
```

Then run: `uv run python dev_scripts/add_follow_table.py`

**Tell the student:**
> "✅ Follow table added! Your existing users and posts are still there. You can now start following users!"

### When to Recreate Database

**❌ Use recreation only for structural changes:**
- Removing required columns from existing tables
- Changing column types that require data transformation
- Renaming columns (SQLite doesn't support ALTER COLUMN RENAME)
- Changing primary/foreign key relationships
- Making nullable columns required (existing NULL values would break)

**Example: Structural change requiring recreation**

Create `dev_scripts/reset_database.py`:
```python
"""Reset database - deletes existing database and creates new one with all tables."""
from database import app, db
from models import User, Post, Follow  # Import ALL models
import os

with app.app_context():
    # Get database path
    db_path = app.config['SQLALCHEMY_DATABASE_URI'].replace('sqlite:///', '')
    print(f"Database path: {db_path}")

    # Delete old database
    db.session.remove()
    if os.path.exists(db_path):
        os.remove(db_path)
        print("✅ Old database deleted")

    # Create new database with ALL tables
    db.create_all()
    db.session.execute(db.text("SELECT 1"))
    db.session.commit()
    print(f"✅ New database created with tables: {list(db.metadata.tables.keys())}")
```

**Tell the student:**
> "⚠️ This change requires recreating the database. You'll need to create your test users and posts again. Should we proceed?"

🛑 **Wait for confirmation before running**

### Implementation in TODO Workflow

**When students add features in Part 6:**

1. **Assess the database changes:**
   - "Let's think about what database changes this feature needs"
   - Explain if it's additive (new table) vs structural (modify existing)

2. **Choose migration strategy:**
   - **Additive changes**: Create `dev_scripts/add_[feature]_table.py`
   - **Structural changes**: Warn about data loss, create `dev_scripts/reset_database.py`

3. **Communicate what's preserved or lost:**
   - Migration: "✅ Your existing users and posts are preserved!"
   - Recreation: "⚠️ This change requires recreating the database. You'll need to create users again."

### Benefits of Migration Approach

- ✅ Students don't lose work when adding features
- ✅ Teaches realistic development workflow (migrations are industry standard)
- ✅ Reduces frustration from repeated user creation
- ✅ Faster iteration on features
- ✅ Better learning experience
- ✅ Still allows recreation when truly necessary

### Common Scenarios

| Feature | Strategy | Reason |
|---------|----------|--------|
| Adding Follow table | **Migrate** | New table, no changes to User/Post |
| Adding Like table | **Migrate** | New table, no changes to existing |
| Adding Comment table | **Migrate** | New table, no changes to existing |
| Adding User.bio column | **Migrate** | Nullable or has default value |
| Adding Post.image_path | **Migrate** | Nullable column |
| Making User.email required | **Recreate** | Existing users may have NULL emails |
| Changing Post.content length | **Recreate** | Existing posts may violate new constraint |
| Renaming User.username | **Recreate** | SQLite doesn't support column rename |
| Splitting User.display_name | **Recreate** | Requires data transformation |
