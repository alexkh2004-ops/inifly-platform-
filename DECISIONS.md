# 🤔 Inifly Platform - Technical Decisions Log

All major technical decisions are documented here with reasoning.

**Purpose:** So future developers (human or AI) understand *why* we chose what we chose.

---

## Decision 001: Tech Stack - Frontend Framework
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **Next.js 14 (App Router)**

### Reasoning
- React-based (huge community, lots of resources)
- Server-side rendering (SSR) built-in (good for SEO)
- App Router is the modern approach
- Easy deployment to Vercel
- Great DX (developer experience)

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| **Remix** | Modern, great DX | Smaller community | Less learning resources |
| **SvelteKit** | Faster, smaller | Newer, less jobs | Harder to find help |
| **Astro** | Very fast | Content-focused | Not ideal for web apps |
| **Vite + React** | Simple | Manual SSR setup | More work, no framework |

### Trade-offs
- ✅ Pro: Fast development, lots of support
- ⚠️ Con: Next.js has a learning curve
- ⚠️ Con: App Router is new (some bugs/rough edges)

---

## Decision 002: Tech Stack - Styling
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **Tailwind CSS**

### Reasoning
- No CSS files to manage
- Fast iteration (change classes, see results)
- Responsive utilities built-in
- Great with Next.js/React
- Huge community (easy to find examples)

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| **CSS Modules** | Traditional, simple | More files | Slower to write |
| **Styled Components** | CSS-in-JS | Runtime overhead | Performance concerns |
| **Emotion** | Flexible | Complex setup | Overkill for MVP |
| **Plain CSS** | Simple | Messy at scale | Hard to maintain |

---

## Decision 003: Tech Stack - Backend & Database
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **Supabase (PostgreSQL + Auth + Real-time)**

### Reasoning
- **PostgreSQL:** Robust, relational database (perfect for ledgers)
- **Built-in Auth:** OAuth ready (Google, GitHub) - for later
- **Real-time subscriptions:** Live updates without polling
- **Generous free tier:** 50,000 monthly active users
- **Great DX:** Dashboard, SQL editor, logs
- **No backend code needed:** Just SQL + client library

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| **Firebase** | Popular | Expensive, NoSQL | Not great for relational data |
| **PlanetScale** | MySQL, serverless | No built-in auth | More setup needed |
| **Neon** | Serverless Postgres | Newer, less mature | Less features than Supabase |
| **Custom Backend** | Full control | Lots of work | Too slow for MVP |

---

## Decision 004: Authentication Strategy
**Date:** 2026-02-15  
**Status:** ✅ Decided (for later)  
**Decision:** **OAuth only (Google + GitHub), no email/password**

### Reasoning
- **Security:** No password storage, no breach risk
- **UX:** Users already have these accounts
- **Speed:** Supabase has built-in OAuth
- **Trust:** People trust Google/GitHub login

**Note:** Not implementing auth in MVP v0.1 - using text names for now!

---

## Decision 005: AI Integration Approach
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **BYOK (Bring Your Own Key) - Users provide their OpenAI API key**

### Reasoning
- **Zero cost for us:** Users pay OpenAI directly (~$20/month)
- **Scalability:** No infrastructure costs
- **Privacy:** User's API calls go directly to OpenAI
- **Flexibility:** Users can switch AI providers later
- **MVP-friendly:** Focus on core features, not billing

---

## Decision 006: Deployment Platform
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **Vercel**

### Reasoning
- **Made for Next.js:** Zero-config deployment
- **Free tier:** Generous for hobby projects
- **CI/CD built-in:** Push to GitHub = auto deploy
- **Great DX:** Preview deployments for PRs

---

## Decision 007: Project Structure
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **App Router structure with feature-based organization**

### Structure
```
src/
├── app/              # Next.js App Router
│   ├── (auth)/       # Auth-related pages (later)
│   ├── (dashboard)/  # Main app pages
│   └── api/          # API routes (if needed)
├── components/       # React components
│   ├── ui/           # Reusable UI (buttons, inputs)
│   └── features/     # Feature-specific components
├── lib/              # Utilities
│   ├── supabase.ts   # DB client
│   ├── ai.ts         # AI logic (later)
│   └── utils.ts      # Helpers
└── types/            # TypeScript types
```

---

## Decision 008: Database Schema Design (MVP v0.1)
**Date:** 2026-02-15  
**Status:** ✅ Decided & Implemented  
**Decided by:** alexkh2004-ops + Gemini (Google AI)

### Decision
**Simple 4-table schema with text-based user tracking (no auth yet)**

**Tables:**
1. `ventures` - Projects/startups
2. `ideas` - Strategic proposals
3. `tasks` - Execution work items
4. `ledger_entries` - Immutable contribution records

### Reasoning

#### Why text names instead of user accounts?
- ✅ **Speed:** Can start testing immediately without auth system
- ✅ **Simplicity:** No user registration, password resets, etc.
- ✅ **Focus:** Build core features first, add auth later
- ✅ **Testing:** Easy to manually create test data
- ✅ **Migration path:** Can add users table in v0.2 and backfill

#### Why separate ideas and tasks?
- ✅ **Philosophy:** Recognizes both "Head" (strategy) and "Hands" (execution)
- ✅ **Traceability:** See which tasks implement which ideas
- ✅ **Fairness:** Idea creators get credit when tasks succeed
- ✅ **Flexibility:** Ideas can exist without tasks (proposals)
- ✅ **Multipliers:** Apply to entire chain (idea → tasks)

#### Why immutable ledger?
- ✅ **Trust:** No one can retroactively change points
- ✅ **Transparency:** Complete audit trail
- ✅ **Corrections:** Add negative entries instead of editing
- ✅ **Legal:** Clear record for equity disputes
- ✅ **Blockchain-inspired:** Without the complexity

### Alternatives Considered

| Alternative | Pros | Cons | Why Not? |
|-------------|------|------|----------|
| **Full user auth from day 1** | Proper security | Months of work | Too slow for MVP |
| **Combined ideas+tasks table** | Simpler schema | Loses strategy/execution distinction | Against core philosophy |
| **Mutable points (allow updates)** | Easier to correct mistakes | No audit trail, trust issues | Defeats transparency goal |
| **NoSQL (Firebase)** | Flexible schema | Bad for relational queries | Equity calc needs joins |

### Schema Details

#### ventures
```sql
- id (UUID)
- name (TEXT)
- description (TEXT)
- created_at (TIMESTAMP)
```
**Notes:** No creator tracking, no status field (all active for now)

#### ideas
```sql
- id (UUID)
- venture_id (FK → ventures)
- title (TEXT)
- description (TEXT)
- size (TEXT: XS/S/M/L/XL)
- strategist_name (TEXT) ← no user FK!
- is_approved (BOOLEAN)
- created_at (TIMESTAMP)
```
**Notes:** `strategist_name` is just text, will migrate to user_id later

#### tasks
```sql
- id (UUID)
- idea_id (FK → ideas, nullable)
- title (TEXT)
- executor_name (TEXT) ← no user FK!
- task_size (TEXT: XS/S/M/L/XL)
- status (TEXT: todo/in_progress/done)
- created_at (TIMESTAMP)
```
**Notes:** Can be orphaned (no idea_id) for ad-hoc work

#### ledger_entries
```sql
- id (UUID)
- venture_id (FK → ventures)
- contributor_name (TEXT) ← no user FK!
- source_type (TEXT: idea/task/manual)
- source_id (UUID) ← references ideas or tasks
- base_points (INTEGER)
- multiplier (NUMERIC, default 1.0)
- final_points (INTEGER) ← base_points * multiplier
- created_at (TIMESTAMP)
```
**Notes:** **Immutable** - no updates/deletes allowed!

### Trade-offs

#### Pros ✅
- Fast to implement (done in 1 hour!)
- Easy to test manually
- No auth complexity
- Clear data model
- Can query equity instantly

#### Cons ⚠️
- No security (anyone can claim any name)
- Duplicate names possible
- No user profiles/avatars
- Can't link to external identity
- Will need migration later

### Migration Path (v0.2)

**When we add real auth:**

1. Create `users` table with Supabase Auth
2. Add `user_id` columns to all tables
3. Run migration script:
   ```sql
   -- Example: link text names to user IDs
   UPDATE ideas SET user_id = (
     SELECT id FROM users 
     WHERE full_name = ideas.strategist_name
   );
   ```
4. Make `user_id` required, drop text name columns
5. Add Row Level Security (RLS) policies

**Estimated effort:** 1-2 days

### Implementation Notes

**Created by:** Gemini AI + alexkh2004-ops  
**Tool:** Supabase Dashboard (manual table creation)  
**Date:** February 15, 2026, ~10:00 AM  
**Location:** https://supabase.com/dashboard/project/gjfjohbsvpehmzeozpxv

**What worked well:**
- Supabase UI is very intuitive
- Tables created in minutes
- Easy to see relationships

**What to improve:**
- Should have used migrations (SQL files)
- Need to export schema as SQL for version control
- Should add indexes for performance

### Future Considerations

**v0.2 (Next month):**
- Add `users` table
- Add `milestones` table  
- Foreign keys to users
- Row Level Security

**v0.3 (Later):**
- Add `contributors` table (permissions)
- Add `comments` table
- Add `attachments` table
- Add audit logs

### Documentation
Full schema documented in: [DATABASE_SCHEMA.md](./docs/DATABASE_SCHEMA.md)

---

## Template for Future Decisions

```markdown
## Decision XXX: [Title]
**Date:** YYYY-MM-DD  
**Status:** 🤔 Proposed / ✅ Decided / ❌ Rejected  
**Decision:** [What we chose]

### Reasoning
- Why this makes sense
- What problem it solves

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| Option A | ... | ... | ... |

### Trade-offs
- ✅ Pro: ...
- ⚠️ Con: ...

### Implementation Notes
- How to actually do it

### Future Consideration
- What we might change later
```

---

## 📝 How to Add a Decision

1. Copy the template above
2. Fill in all sections (don't skip!)
3. Assign next decision number (XXX)
4. Commit with message: `docs: add Decision XXX - [title]`

---

## 🔄 Revisiting Decisions

Decisions can change! If we need to revisit:
1. Don't delete the old decision
2. Add a new decision referencing the old one
3. Explain what changed and why

---

**Last updated:** February 15, 2026  
**Total Decisions:** 8
