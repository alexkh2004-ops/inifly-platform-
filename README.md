# 🌀 Inifly Platform

**AI-orchestrated collaborative venture management**

> A platform where teams build projects together, track contributions transparently, and earn equity based on real impact—not politics or promises.

---

## 🎯 What is Inifly?

Inifly is an **open-source project management tool** designed for collaborative ventures. It solves the problem of fair equity distribution by:

- 📊 **Transparent contribution tracking** - Every task, every line of code, every decision is recorded
- 🤖 **AI-assisted point allocation** - Smart suggestions for task complexity and impact
- 🧮 **Automatic equity calculation** - Your ownership % updates in real-time based on contributions
- 🚫 **No money handling (MVP)** - Focus on collaboration first, economics later

---

## 🏗️ Current Status

**Phase:** MVP Development  
**Version:** 0.1.0-alpha  
**Stage:** Foundation Setup

We're building this **in public**, using AI assistants (Claude, GPT, etc.) to help write code, make decisions, and document everything.

👉 **See [PROGRESS.md](./PROGRESS.md) for current status**

---

## 🛠️ Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | Next.js 14 | React with SSR, App Router |
| Styling | Tailwind CSS | Fast, utility-first |
| Backend | Supabase | PostgreSQL + Auth + Real-time |
| AI | OpenAI API | BYOK model (users bring their own key) |
| Hosting | Vercel | Free tier, instant deploys |

👉 **See [DECISIONS.md](./DECISIONS.md) for detailed reasoning**

---

## 📁 Project Structure
```
inifly-platform/
│
├── docs/               # Documentation
│   ├── MVP_SPEC.md    # What we're building
│   ├── DATABASE_SCHEMA.md
│   └── AI_PROMPTS.md
│
├── src/
│   ├── app/           # Next.js App Router pages
│   ├── components/    # React components
│   ├── lib/           # Utilities & helpers
│   └── ai/            # AI integration logic
│
├── supabase/
│   ├── migrations/    # Database schema changes
│   └── seed.sql       # Test data
│
├── PROGRESS.md        # 🔥 Read this to understand where we are
├── DECISIONS.md       # Why we made each technical choice
└── README.md          # You are here
```

---

## 🚀 Quick Start (Coming Soon)
```bash
# Clone the repo
git clone https://github.com/alexkh2004-ops/inifly-platform.git
cd inifly-platform

# Install dependencies
npm install

# Setup environment variables
cp .env.example .env.local
# Add your Supabase keys

# Run development server
npm run dev
```

🚧 **Note:** We're still setting up. This won't work yet!

---

## 🤝 How to Contribute

We're building this **with AI assistance** in a unique way:

### For Humans:
1. Read [PROGRESS.md](./PROGRESS.md) to see what we're working on
2. Check open [Issues](https://github.com/alexkh2004-ops/inifly-platform/issues)
3. Comment on what you'd like to help with
4. Submit PRs with clear descriptions

### For AI Assistants (Claude, GPT, etc.):
1. **Always read PROGRESS.md first** - it has current context
2. Check DECISIONS.md before suggesting architecture changes
3. When writing code, follow the patterns in existing files
4. Update PROGRESS.md when completing tasks

---

## 📖 Documentation

| Document | Purpose |
|----------|---------|
| [PROGRESS.md](./PROGRESS.md) | Current status, what's done, what's next |
| [DECISIONS.md](./DECISIONS.md) | Technical decisions and reasoning |
| [MVP_SPEC.md](./docs/MVP_SPEC.md) | Detailed feature specifications |
| [DATABASE_SCHEMA.md](./docs/DATABASE_SCHEMA.md) | Database structure |
| [GENESIS.md](./docs/GENESIS.md) | Original vision document |

---

## 🎯 MVP Goals

**What we're building first:**

- ✅ User authentication (Google/GitHub OAuth)
- ✅ Create and join ventures
- ✅ Task board (Kanban-style)
- ✅ Contribution ledger
- ✅ Automatic equity calculation
- ✅ AI task suggestions

**Not in MVP:**
- ❌ Money/payments
- ❌ Token marketplace
- ❌ Social feed
- ❌ Mobile app

---

## 📊 Project Principles

1. **Open Source First** - All code public, all decisions documented
2. **AI-Assisted Development** - Using AI tools to accelerate development
3. **Transparent Progress** - Anyone can see exactly where we are
4. **No Fake Complexity** - Simple solutions over clever ones
5. **Documentation > Code** - Explain why, not just what

---

## 📞 Contact & Community

- **GitHub:** [Issues](https://github.com/alexkh2004-ops/inifly-platform/issues) & [Discussions](https://github.com/alexkh2004-ops/inifly-platform/discussions)
- **Email:** [coming soon]
- **Discord:** [coming soon]

---

## 📜 License

[To be determined - likely MIT]

---

## 🙏 Acknowledgments

Built with help from:
- Claude (Anthropic) - Architecture and code assistance
- Gemini (Google) - Additional development support
- The open-source community

---

**"Your work is recorded. Your impact is multiplied. Your ownership is guaranteed."**

*Last updated: February 15, 2026*
