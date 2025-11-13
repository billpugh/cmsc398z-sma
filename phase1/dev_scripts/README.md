# Phase 1 Development Scripts

This directory contains utility scripts for database management and development tasks in Phase 1.

## Purpose

During development, you'll often need to run Python code to:
- Initialize or reset the database
- Add new tables when implementing features
- Create test data
- Fix data issues
- Perform one-time setup tasks

Instead of copying and pasting code from chat (which causes indentation errors), we create scripts in this directory. Each script has a clear, descriptive name and can be run easily.

## Common Scripts

You'll create scripts like these as you work through Phase 1:

### `init_database.py`
Initializes the database with all tables for the first time.

**When to run:** After creating your models, before running the app.

**Example:**
```bash
uv run python dev_scripts/init_database.py
```

### `add_follow_table.py` (or `add_like_table.py`, etc.)
Adds a new table to the existing database **without deleting your users and posts**.

**When to run:** After adding a new feature that needs a new table (Follow, Like, Comment).

**Why this is better:** Preserves your test data! You don't have to recreate users and posts.

**Example:**
```bash
uv run python dev_scripts/add_follow_table.py
```

### `reset_database.py`
Deletes the entire database and recreates it with all tables.

**When to run:** When you need to make structural changes to existing tables (changing column types, making columns required, etc.).

**Warning:** This deletes all your data! You'll need to create test users and posts again.

**Example:**
```bash
uv run python dev_scripts/reset_database.py
```

### `create_test_data.py`
Creates sample users, posts, and relationships for testing.

**When to run:** After resetting the database, or when you want a populated app for demos.

**Example:**
```bash
uv run python dev_scripts/create_test_data.py
```

## How Scripts Are Created

When you need to run multiline Python code, Claude will:

1. **Create a script** in this directory with a descriptive name
2. **Run it immediately** using `uv run python dev_scripts/script_name.py`
3. **Show you what happened** with clear success/error messages

You can re-run any script whenever needed!

## Benefits

✅ **No copy-paste errors** - Scripts are created correctly the first time
✅ **Repeatable** - Run the same script multiple times
✅ **Self-documenting** - Script names clearly show what they do
✅ **Version controlled** - Scripts are committed to git, showing your development history
✅ **Educational** - You can read the scripts to understand what's happening
✅ **Preserve data** - Migration scripts keep your test users/posts intact

## Running Scripts

Always run scripts from the `phase1/` directory using `uv run`:

```bash
# From phase1/ directory:
uv run python dev_scripts/init_database.py
uv run python dev_scripts/add_follow_table.py
uv run python dev_scripts/reset_database.py
```

## Important Notes

- Scripts in this directory are **part of your project** - commit them to git!
- Scripts are **educational tools** - read them to understand database operations
- Scripts use **your project's dependencies** - they import from your models, database, etc.
- In production, you'd use migration tools like Alembic, but these scripts teach the same concepts

## Examples from Class

As you work through Phase 1, this directory will fill up with scripts like:

```
dev_scripts/
├── README.md
├── init_database.py          # Created in Part 4
├── add_follow_table.py       # Created when adding follow feature
├── create_test_data.py       # Created for demo/testing
└── reset_database.py         # Created if structural changes needed
```

Each script documents a step in your development process!
