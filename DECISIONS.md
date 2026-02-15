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

### Implementation Notes
- Using App Router (not Pages Router)
- TypeScript from the start
- React Server Components where applicable

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

### Trade-offs
- ✅ Pro: Very fast development
- ✅ Pro: Consistent design system
- ⚠️ Con: HTML looks messy with long class strings
- ⚠️ Con: Learning curve for Tailwind classes

---

## Decision 003: Tech Stack - Backend & Database
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **Supabase (PostgreSQL + Auth + Real-time)**

### Reasoning
- **PostgreSQL:** Robust, relational database (perfect for ledgers)
- **Built-in Auth:** OAuth ready (Google, GitHub)
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

### Trade-offs
- ✅ Pro: Fast setup, full-featured
- ✅ Pro: Free for MVP scale
- ⚠️ Con: Vendor lock-in (mitigated: PostgreSQL is portable)
- ⚠️ Con: Less control over infrastructure

### Implementation Notes
- Using Row Level Security (RLS) for permissions
- Database migrations in `/supabase/migrations`
- Supabase client library in `/src/lib/supabase.ts`

---

## Decision 004: Authentication Strategy
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **OAuth only (Google + GitHub), no email/password**

### Reasoning
- **Security:** No password storage, no breach risk
- **UX:** Users already have these accounts
- **Speed:** Supabase has built-in OAuth
- **Trust:** People trust Google/GitHub login
- **No email verification needed**

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| **Email/Password** | Traditional | Security risk, verification needed | Too much work |
| **Magic Links** | Passwordless | Email delivery issues | UX friction |
| **Phone OTP** | Secure | SMS costs, international issues | Not needed yet |

### Trade-offs
- ✅ Pro: Fast, secure, familiar UX
- ⚠️ Con: Users without Google/GitHub can't sign up
- ⚠️ Con: Requires OAuth app setup

### Implementation Notes
- Google OAuth: configured in Supabase dashboard
- GitHub OAuth: configured in Supabase dashboard
- Callback URL: `[your-domain]/auth/callback`

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

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| **Hosted AI** | Better UX | High costs, billing complexity | Not viable for MVP |
| **Free tier (limited)** | Simple UX | Unsustainable | Need revenue model first |
| **No AI** | Simplest | Defeats the purpose | AI is core to Inifly |

### Trade-offs
- ✅ Pro: Free for us, scalable
- ⚠️ Con: UX friction (users need API key)
- ⚠️ Con: Non-technical users may struggle

### Implementation Notes
- API key stored encrypted in Supabase (user table)
- AI calls made from client-side
- Settings page for key management
- Onboarding guide for getting OpenAI key

### Future Consideration
- After MVP: offer hosted AI for $10/month (we provide the key)

---

## Decision 006: Deployment Platform
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **Vercel**

### Reasoning
- **Made for Next.js:** Zero-config deployment
- **Free tier:** Generous for hobby projects
- **CI/CD built-in:** Push to GitHub = auto deploy
- **Edge functions:** Fast globally
- **Great DX:** Preview deployments for PRs

### Alternatives Considered
| Option | Pros | Cons | Why Not? |
|--------|------|------|----------|
| **Netlify** | Similar to Vercel | Less Next.js optimized | Vercel is better for Next |
| **Railway** | Simple, cheap | Less features | Overkill for static + API |
| **AWS/GCP** | Full control | Complex, expensive | Too much for MVP |
| **Self-hosted** | Free | DevOps work | Time sink |

### Trade-offs
- ✅ Pro: Easiest deployment
- ✅ Pro: Free for MVP scale
- ⚠️ Con: Costs rise with scale (but that's a good problem)

---

## Decision 007: Project Structure
**Date:** 2026-02-15  
**Status:** ✅ Decided  
**Decision:** **App Router structure with feature-based organization**

### Structure
```
src/
├── app/              # Next.js App Router
│   ├── (auth)/       # Auth-related pages
│   ├── (dashboard)/  # Main app pages
│   └── api/          # API routes
├── components/       # React components
│   ├── ui/           # Reusable UI (buttons, inputs)
│   └── features/     # Feature-specific components
├── lib/              # Utilities
│   ├── supabase.ts   # DB client
│   ├── ai.ts         # AI logic
│   └── utils.ts      # Helpers
└── types/            # TypeScript types
```

### Reasoning
- Clear separation of concerns
- Easy to find files
- Scales well as project grows

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

Example:
```markdown
## Decision 015: Change from BYOK to Hosted AI
**Date:** 2026-06-01  
**Status:** ✅ Decided  
**Supersedes:** Decision 005

**Why changed:** Users struggled with API key setup.
70% drop-off during onboarding.

**New decision:** We now provide hosted AI for $10/month...
```

---

*Last updated: February 15, 2026*
