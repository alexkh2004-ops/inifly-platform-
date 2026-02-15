# 🗄️ Database Schema - Inifly Platform

**Database:** PostgreSQL (Supabase)  
**Project ID:** `gjfjohbsvpehmzeozpxv`  
**Last Updated:** February 15, 2026  
**Status:** ✅ Created and Active

---

## 🎯 Overview

Simple, MVP-focused schema with 4 core tables:
- `ventures` - Projects/startups
- `ideas` - Strategic proposals within ventures
- `tasks` - Executable work items
- `ledger_entries` - Immutable contribution records

**Design Philosophy:**
- ✅ Simple and flat (no complex relations yet)
- ✅ Text-based user tracking (no auth yet)
- ✅ UUID primary keys
- ✅ Timestamps for audit trail
- ✅ Flexible for iteration

---

## 📊 Entity Relationship Diagram

```
┌─────────────┐
│  ventures   │  (The main project)
└──────┬──────┘
       │
       │ contains
       ▼
┌─────────────┐
│    ideas    │  (Strategy/direction)
└──────┬──────┘
       │
       │ breaks down to
       ▼
┌─────────────┐
│    tasks    │  (Execution)
└─────────────┘
       │
       │ records in
       ▼
┌──────────────────┐
│ ledger_entries   │  (Immutable points log)
└──────────────────┘
```

---

## 🗃️ Table Details

### 1. `ventures`

The top-level container - a project, startup, or collaborative effort.

```sql
CREATE TABLE ventures (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

**Fields:**
| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `id` | UUID | NO | Primary key |
| `name` | TEXT | NO | Venture name (e.g., "TaskMaster AI") |
| `description` | TEXT | YES | What this venture does |
| `created_at` | TIMESTAMP | YES | When created |

**Example:**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Inifly Platform",
  "description": "AI-orchestrated venture collaboration tool",
  "created_at": "2026-02-15T10:00:00Z"
}
```

**Notes:**
- No creator tracking yet (added in v2)
- No status field (all ventures are "active")
- No total_points field (calculated from ledger)

---

### 2. `ideas`

Strategic proposals - the "Head" part of the venture. Ideas get broken down into tasks.

```sql
CREATE TABLE ideas (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  venture_id UUID REFERENCES ventures(id),
  title TEXT NOT NULL,
  description TEXT,
  size TEXT, -- XS, S, M, L, XL
  strategist_name TEXT,
  is_approved BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

**Fields:**
| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `id` | UUID | NO | Primary key |
| `venture_id` | UUID | YES | Which venture this belongs to |
| `title` | TEXT | NO | Short description (e.g., "Build authentication system") |
| `description` | TEXT | YES | Detailed explanation |
| `size` | TEXT | YES | XS/S/M/L/XL (complexity) |
| `strategist_name` | TEXT | YES | Who proposed this (no auth yet) |
| `is_approved` | BOOLEAN | YES | Has this been validated? |
| `created_at` | TIMESTAMP | YES | When created |

**Size Options:**
- `XS` - Minor idea (10 pts)
- `S` - Small idea (30 pts)
- `M` - Medium initiative (100 pts)
- `L` - Large strategic plan (300 pts)
- `XL` - Major breakthrough (1000 pts)

**Example:**
```json
{
  "id": "abc123...",
  "venture_id": "550e8400...",
  "title": "Add OAuth authentication",
  "description": "Users should be able to login with Google and GitHub",
  "size": "L",
  "strategist_name": "Alex",
  "is_approved": true,
  "created_at": "2026-02-15T11:00:00Z"
}
```

**Notes:**
- Ideas can exist without tasks (just proposals)
- `is_approved` gates whether tasks can be created
- Points awarded when idea leads to successful tasks

---

### 3. `tasks`

Executable work items - the "Hands" part. Each task implements an idea.

```sql
CREATE TABLE tasks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  idea_id UUID REFERENCES ideas(id),
  title TEXT NOT NULL,
  executor_name TEXT,
  task_size TEXT, -- XS, S, M, L, XL
  status TEXT DEFAULT 'todo', -- todo, in_progress, done
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

**Fields:**
| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `id` | UUID | NO | Primary key |
| `idea_id` | UUID | YES | Which idea this implements |
| `title` | TEXT | NO | What needs to be done |
| `executor_name` | TEXT | YES | Who is doing this |
| `task_size` | TEXT | YES | XS/S/M/L/XL (effort) |
| `status` | TEXT | YES | todo, in_progress, done |
| `created_at` | TIMESTAMP | YES | When created |

**Status Options:**
- `todo` - Not started
- `in_progress` - Being worked on
- `done` - Completed (triggers ledger entry)

**Example:**
```json
{
  "id": "def456...",
  "idea_id": "abc123...",
  "title": "Implement Google OAuth",
  "executor_name": "Sarah",
  "task_size": "M",
  "status": "done",
  "created_at": "2026-02-15T12:00:00Z"
}
```

**Notes:**
- Can be orphaned (no idea_id) for ad-hoc work
- Status change to "done" should trigger ledger entry
- Points based on task_size (100 for M)

---

### 4. `ledger_entries`

**The most critical table.** Immutable record of all contributions.

```sql
CREATE TABLE ledger_entries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  venture_id UUID REFERENCES ventures(id),
  contributor_name TEXT NOT NULL,
  source_type TEXT, -- 'idea', 'task', 'manual'
  source_id UUID, -- references ideas.id or tasks.id
  base_points INTEGER,
  multiplier NUMERIC DEFAULT 1.0,
  final_points INTEGER, -- base_points * multiplier
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

**Fields:**
| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `id` | UUID | NO | Primary key |
| `venture_id` | UUID | YES | Which venture |
| `contributor_name` | TEXT | NO | Who earned these points |
| `source_type` | TEXT | YES | 'idea', 'task', or 'manual' |
| `source_id` | UUID | YES | ID of the idea or task |
| `base_points` | INTEGER | YES | Raw points (before multiplier) |
| `multiplier` | NUMERIC | YES | Impact multiplier (1.0 - 5.0) |
| `final_points` | INTEGER | YES | base_points × multiplier |
| `created_at` | TIMESTAMP | YES | When recorded |

**Source Types:**
- `idea` - Points for strategic thinking
- `task` - Points for execution
- `manual` - Admin correction or special award

**Example:**
```json
{
  "id": "ghi789...",
  "venture_id": "550e8400...",
  "contributor_name": "Sarah",
  "source_type": "task",
  "source_id": "def456...",
  "base_points": 100,
  "multiplier": 1.5,
  "final_points": 150,
  "created_at": "2026-02-15T13:00:00Z"
}
```

**CRITICAL RULES:**
- ⚠️ **Immutable** - NO updates or deletes
- ✅ Corrections: Add new entry with negative points
- ✅ Multipliers: Applied at milestone completion
- ✅ Transparency: Everyone can see the full ledger

---

## 🔄 Relationships

### Venture → Ideas → Tasks → Ledger

```
Venture: "Inifly Platform"
  │
  ├─ Idea: "Build authentication" (L = 300 base pts)
  │   │
  │   ├─ Task: "Setup Supabase auth" (M = 100 pts)
  │   │   └─ Ledger: Sarah, 100 pts
  │   │
  │   └─ Task: "Design login UI" (S = 30 pts)
  │       └─ Ledger: Alex, 30 pts
  │
  └─ Idea: "Create database schema" (L = 300 base pts)
      │
      └─ Task: "Define tables" (M = 100 pts)
          └─ Ledger: Sarah, 100 pts
```

When milestone completes, multiplier applies:
- If auth was successful (multiplier 2.0):
  - Idea creator: 300 × 2.0 = 600 pts
  - Task executors: 100 × 2.0 = 200 pts each

---

## 📊 Key Queries

### Get total points per contributor in a venture

```sql
SELECT 
  contributor_name,
  SUM(final_points) as total_points,
  COUNT(*) as contributions,
  ROUND(
    (SUM(final_points)::NUMERIC / 
     (SELECT SUM(final_points) FROM ledger_entries WHERE venture_id = $1)) * 100,
    2
  ) as equity_percentage
FROM ledger_entries
WHERE venture_id = $1
GROUP BY contributor_name
ORDER BY total_points DESC;
```

### Get all contributions for a venture (timeline)

```sql
SELECT 
  le.contributor_name,
  le.source_type,
  COALESCE(t.title, i.title) as work_title,
  le.base_points,
  le.multiplier,
  le.final_points,
  le.created_at
FROM ledger_entries le
LEFT JOIN tasks t ON le.source_id = t.id AND le.source_type = 'task'
LEFT JOIN ideas i ON le.source_id = i.id AND le.source_type = 'idea'
WHERE le.venture_id = $1
ORDER BY le.created_at DESC;
```

### Get ideas with their tasks

```sql
SELECT 
  i.title as idea_title,
  i.strategist_name,
  i.size as idea_size,
  json_agg(
    json_build_object(
      'title', t.title,
      'executor', t.executor_name,
      'size', t.task_size,
      'status', t.status
    )
  ) as tasks
FROM ideas i
LEFT JOIN tasks t ON t.idea_id = i.id
WHERE i.venture_id = $1
GROUP BY i.id, i.title, i.strategist_name, i.size
ORDER BY i.created_at DESC;
```

### Check equity distribution

```sql
-- Get real-time equity split
WITH totals AS (
  SELECT 
    venture_id,
    SUM(final_points) as total_points
  FROM ledger_entries
  WHERE venture_id = $1
  GROUP BY venture_id
)
SELECT 
  le.contributor_name,
  SUM(le.final_points) as user_points,
  t.total_points,
  ROUND(
    (SUM(le.final_points)::NUMERIC / t.total_points) * 100,
    2
  ) as equity_percent
FROM ledger_entries le
CROSS JOIN totals t
WHERE le.venture_id = $1
GROUP BY le.contributor_name, t.total_points
ORDER BY user_points DESC;
```

---

## 🚀 What's Missing (Future Versions)

### Not in MVP:
- ❌ User authentication (using text names for now)
- ❌ Milestones table (multipliers applied manually)
- ❌ Contributors/permissions table
- ❌ Audit logs (updated_at, updated_by)
- ❌ File attachments (evidence_url)
- ❌ Comments/discussions
- ❌ Notifications

### Coming in v0.2:
- ✅ Add `users` table
- ✅ Add `milestones` table
- ✅ Foreign keys to users instead of text names
- ✅ Row Level Security (RLS) policies
- ✅ Triggers for auto-calculations

---

## 🔧 Setup Instructions

### 1. Access the Database

**Supabase Dashboard:**
https://supabase.com/dashboard/project/gjfjohbsvpehmzeozpxv

**SQL Editor:**
https://supabase.com/dashboard/project/gjfjohbsvpehmzeozpxv/sql

### 2. Get Connection Info

```bash
# In your Supabase Dashboard:
# Settings → API → Project URL
NEXT_PUBLIC_SUPABASE_URL=https://gjfjohbsvpehmzeozpxv.supabase.co

# Settings → API → anon public key
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGc...
```

### 3. Test the Connection

```sql
-- Run in SQL Editor to verify tables exist
SELECT table_name 
FROM information_schema.tables 
WHERE table_schema = 'public';

-- Should return: ventures, ideas, tasks, ledger_entries
```

---

## 📝 Schema Migration History

| Date | Migration | Description |
|------|-----------|-------------|
| 2026-02-15 | Initial | Created 4 core tables (ventures, ideas, tasks, ledger_entries) |

**Note:** We're not using formal migrations yet. All changes made in Supabase Dashboard.

---

## 🎯 Design Decisions

### Why text names instead of user IDs?
- **MVP speed** - No auth system to build first
- **Flexibility** - Can work without signup
- **Easy testing** - Just type names manually
- **Migration path** - Can add users table later and backfill

### Why no foreign key constraints yet?
- **Simplicity** - Easier to iterate
- **Flexibility** - Can orphan records if needed
- **Speed** - No cascade complexity
- **Later** - Will add in v0.2 with proper users

### Why separate ideas and tasks?
- **Recognizes both strategy and execution**
- **Ideas can exist without tasks** (proposals)
- **Tasks can reference ideas** (traceability)
- **Multipliers apply to whole chain** (alignment)

---

## 🔐 Security Notes

### Current Status: ⚠️ OPEN
- **RLS not enabled yet** - anyone can read/write
- **No authentication** - open database
- **OK for MVP** - we're just testing

### Before Production:
1. Enable RLS on all tables
2. Add user authentication
3. Create proper policies
4. Audit all queries

---

## 📞 For AI Assistants

When working with this database:

1. **Check this file first** for current schema
2. **Use text names** (no user IDs yet)
3. **Never modify ledger_entries** after insert
4. **Calculate equity** from ledger dynamically
5. **Test queries** in Supabase SQL Editor first

**Common pitfall:** Don't try to use `users` table - it doesn't exist yet!

---

**Last Updated:** February 15, 2026  
**Schema Version:** 0.1.0  
**Status:** ✅ Active and working

---

*This is a living document. Update it whenever the schema changes!*
