<div align="center">

<img src="apps/web/public/logo.svg" width="88" alt="AI Software Engineer" />

# AI Software Engineer

**Build. Debug. Understand. Ship.**

An open-source AI software engineering workspace for building, debugging,
analyzing, testing and improving software projects.

[![License: MIT](https://img.shields.io/badge/License-MIT-4f8cff.svg)](LICENSE)
[![Node](https://img.shields.io/badge/node-%E2%89%A518-22d3ee.svg)](#installation)
[![Free](https://img.shields.io/badge/100%25-free-3fb950.svg)](#free-by-design)

**100% free to run locally · Open source · No credit card required ·
No mandatory paid API · No PostgreSQL required · No Docker required**

</div>

---

## ✨ What is this?

AI Software Engineer is a lightweight, self-hosted **AI coding workspace** — not a
chatbot wrapper. Log in, create a project, and get:

- a **file explorer** and a full **Monaco code editor** (the editor that powers VS Code),
- an **AI engineer panel** that understands the file you have open: explain, fix,
  refactor, optimize, generate tests, find bugs, document,
- a **debugging assistant** — paste an error, get likely cause → suggested fix → why → prevention,
- a **project analyzer** (languages, dependencies, code smells, quality score),
- a **static security scanner** (hard-coded secrets, `eval`, unsafe shells, SQL
  concatenation, …),
- a **sandboxed terminal** that operates on your project files,
- Git-flavoured tooling: simulated status, **AI commit messages**, **README
  generator**, project export/import — with real GitHub sync on the roadmap.

The killer feature: **it works with zero API keys.** With nothing configured, the
built-in **Demo AI engine** answers using local static-analysis heuristics, a
curated error knowledge base and code templates — so a fresh clone is fully
demonstrable. Add a `SARVAM_API_KEY` (or any OpenAI-compatible key) and the same
workspace upgrades to live AI, with automatic fallback to demo mode if the
provider is unreachable.

## 📸 Screenshots

> Add screenshots of the landing page, dashboard and workspace here
> (recommended: `docs/screenshots/`). The interface ships with a dark,
> developer-focused design system, Monaco editor and a three-pane IDE layout.

## 🧱 Architecture

```text
ai-software-engineer/
├── apps/
│   ├── web/                 React 18 + Vite + TypeScript (SPA)
│   └── api/                 Fastify 4 + TypeScript (REST API)
├── packages/
│   ├── shared/              Shared types & constants (@aise/shared)
│   └── database/            Prisma + SQLite client (@aise/database)
├── scripts/setup.mjs        One-command local setup
├── .env.example
└── LICENSE
```

```text
┌──────────────────────────────────────────────────────────┐
│                    Browser (React/Vite)                  │
├─────────────┬────────────────────────────┬───────────────┤
│  File        │       Monaco Editor        │  AI Engineer  │
│  Explorer    │       (code here)         │  Chat/Actions │
├─────────────┴────────────────────────────┴───────────────┤
│            Terminal / Output / Problems                  │
└───────────────────────┬──────────────────────────────────┘
                        │ REST (JWT)
                ┌───────┴────────┐
                │   Fastify API  │
                │  auth · files   │
                │  AI · analyzer  │
                └───┬────────┬───┘
                    │        │
             SQLite (Prisma)  └── AIProvider
             data/app.db        ├─ DemoProvider (local, free)
                                 ├─ SarvamProvider (optional)
                                 └─ OpenAIProvider (optional)
```

## 🛠 Tech stack

| Layer     | Choice                                            |
| --------- | ------------------------------------------------- |
| Frontend  | React 18, Vite 5, TypeScript, Monaco Editor        |
| Backend   | Node.js, Fastify 4, TypeScript                    |
| Database  | SQLite via Prisma (local file — no server needed)  |
| Auth      | Local email + password, bcrypt hashing, JWT        |
| Validation| Zod (every request body)                          |
| AI        | Provider abstraction: Demo / Sarvam / OpenAI-compatible |
| Tests     | Vitest (38 API tests: auth, projects, files, AI, security) |

## 🚀 Installation

**Prerequisites:** [Node.js ≥ 18.18](https://nodejs.org) (Node 20 LTS
recommended) and npm. Nothing else — no Docker, no PostgreSQL, no Redis.

```bash
git clone https://github.com/your-username/ai-software-engineer.git
cd ai-software-engineer
npm run setup      # install + .env + build packages + create SQLite DB + seed
npm run dev        # start API + web together
```

Then open **http://localhost:5173**.

A demo account is seeded automatically:

```text
email:    demo@example.com
password: demo1234!        (configurable via DEMO_PASSWORD)
```

It comes with four example projects (including one with deliberately planted
bugs and a hard-coded secret — try **Find Bugs** and **Security** on it).

### Running the parts separately

```bash
npm run dev:api     # Fastify API  → http://localhost:4000
npm run dev:web     # Vite web app → http://localhost:5173
```

### All commands

| Command              | What it does                                             |
| -------------------- | -------------------------------------------------------- |
| `npm run setup`      | Full local setup (install, env, build, db, seed)          |
| `npm run dev`        | Start API + web in watch mode (concurrently)             |
| `npm run db:setup`   | Create / migrate the local SQLite database               |
| `npm run db:seed`    | Seed the demo account and example projects              |
| `npm run build`      | Production builds for all packages and apps              |
| `npm run lint`       | TypeScript checks across the whole monorepo              |
| `npm run test`       | Run the API test suite (Vitest)                          |
| `npm start`          | Run the built API server                                 |

## ⚙️ Configuration

Copy `.env.example` to `.env` (or let `npm run setup` do it). Everything is
optional:

| Variable           | Default                          | Description                                      |
| ------------------ | -------------------------------- | ------------------------------------------------ |
| `PORT`             | `4000`                           | API port                                          |
| `WEB_ORIGIN`       | `http://localhost:5173`          | Allowed browser origin (CORS)                     |
| `DATABASE_URL`     | `file:../../../data/app.db`      | SQLite file — lands at `<repo>/data/app.db` (Prisma resolves sqlite paths relative to `packages/database/prisma/schema.prisma`) |
| `JWT_SECRET`       | random per run                   | Session signing key. Set it for persistent sessions; unset = auto-generated (with a warning) |
| `AI_PROVIDER`      | `auto`                           | `auto` \| `demo` \| `sarvam` \| `openai`          |
| `SARVAM_API_KEY`   | *(empty)*                        | Optional — enables Sarvam AI                       |
| `SARVAM_MODEL`     | `sarvam-m`                       | Sarvam model id                                   |
| `OPENAI_API_KEY`   | *(empty)*                        | Optional — any OpenAI-compatible endpoint         |
| `OPENAI_BASE_URL`  | `https://api.openai.com/v1`      | Point at OpenAI, Ollama, LM Studio, vLLM, …        |
| `OPENAI_MODEL`     | `gpt-4o-mini`                    | Model for the OpenAI-compatible provider           |
| `DEMO_PASSWORD`    | `demo1234!`                      | Password for the seeded demo account (local use)   |

## 🤖 Demo mode & AI providers

The AI layer is an `AIProvider` abstraction:

| Provider             | Needs a key? | Behaviour                                                       |
| -------------------- | ------------ | --------------------------------------------------------------- |
| `DemoProvider`       | ❌ never      | Local heuristics: static analysis, error knowledge base, code templates |
| `SarvamProvider`     | `SARVAM_API_KEY` | Sarvam AI chat completions (ai.sarvam.ai)                     |
| `OpenAIProvider`     | `OPENAI_API_KEY` | Any OpenAI-compatible API (OpenAI, Ollama, vLLM, …)            |

Selection logic:

- `AI_PROVIDER=demo` → always demo.
- `AI_PROVIDER=sarvam|openai` → that provider (falls back to demo if its key is missing).
- `AI_PROVIDER=auto` (default) → Sarvam/OpenAI if a key is set, otherwise demo.

**The app never crashes when an optional provider is unavailable.** If a live
provider errors (bad key, network, quota), every request falls back to the demo
engine and the response is clearly marked. API keys are read **server-side
only** — they are never sent to the browser.

To go live:

```env
AI_PROVIDER=sarvam        # or leave on auto
SARVAM_API_KEY=your-key
```

## 🔌 API documentation

Base URL: `http://localhost:4000` — all routes are JSON; auth routes issue a JWT
(`Authorization: Bearer <token>`). Requests are rate-limited (300/min globally,
20/min on auth routes) and validated with Zod.

| Method   | Route                              | Description                                   |
| -------- | ---------------------------------- | --------------------------------------------- |
| `GET`    | `/health`                          | Health check → `{"ok":true}`                  |
| `POST`   | `/api/auth/register`               | Register (name, email, password ≥ 8)          |
| `POST`   | `/api/auth/login`                  | Login → `{token, user}`                       |
| `GET`    | `/api/auth/me`                     | Current user                                  |
| `POST`   | `/api/auth/logout`                 | Logout (client discards token)                |
| `GET`    | `/api/projects`                    | List your projects                            |
| `POST`   | `/api/projects`                    | Create project (gets a README.md)             |
| `POST`   | `/api/projects/import`            | Import project from exported JSON             |
| `GET`    | `/api/projects/:id`                | Project details                               |
| `PATCH`  | `/api/projects/:id`                | Rename / update                              |
| `DELETE`| `/api/projects/:id`               | Delete (cascades files)                      |
| `GET`    | `/api/projects/:id/files`         | List files & folders                          |
| `GET/PUT/DELETE` | `/api/projects/:id/files/*` | Read / save (upsert) / delete a file          |
| `POST`   | `/api/projects/:id/files`         | Create file or folder                         |
| `PATCH`  | `/api/projects/:id/files/*`       | Rename (moves folder contents)                |
| `GET`    | `/api/projects/:id/git/status`    | Simulated git status                          |
| `POST`   | `/api/projects/:id/git/commit-message` | AI commit-message suggestion            |
| `GET`    | `/api/projects/:id/export`         | Export project as JSON                        |
| `POST`   | `/api/projects/:id/terminal`       | Run a sandboxed terminal command              |
| `GET`    | `/api/ai/status`                   | Active provider + mode                        |
| `POST`   | `/api/ai/chat`                     | Chat with file/selection context              |
| `POST`   | `/api/ai/action`                   | explain / fix / improve / refactor / optimize / generate-tests / find-bugs / document |
| `POST`   | `/api/ai/generate`                 | Generate code from a prompt                   |
| `POST`   | `/api/ai/debug`                    | Debugging assistant                           |
| `POST`   | `/api/ai/tests`                    | Generate tests for a file                     |
| `POST`   | `/api/ai/analyze`                  | Full project analysis report                  |
| `POST`   | `/api/ai/security`                 | Static security scan                          |
| `POST`   | `/api/ai/readme`                   | AI README generator                           |

## 🔒 Security

- **Password hashing** with bcrypt; JWT sessions signed server-side
- **Zod validation** on every request body; malformed input → structured 400s
- **Rate limiting** (global + stricter auth limits) and **CORS** restricted to the web origin
- **Safe file paths** — project files live in the database, all paths normalized
  and validated: no absolute paths, no `..` traversal, extension allow-list
- **Sandboxed terminal** — a virtual command interpreter over project files;
  no real shell, no package-script execution, no unrestricted command runs
- **Secrets never reach the browser** — API keys are read from env vars server-side only
- Errors return an **error id** for correlation; stack traces and secrets stay in server logs (auth headers redacted)

> The static security scanner is intentionally basic — a teaching and triage
> tool, not a replacement for professional security tooling or an audit.

## 🧪 Testing

```bash
npm run test
```

38 Vitest tests cover: health, auth (register/login/me/rejections), project CRUD
+ import, file operations (incl. path-traversal/absolute-path/blocked-extension
attempts), AI provider selection, chat/actions/analysis/security/debug/tests/
README/terminal behaviour, and provider fallback resilience.

`npm run lint` type-checks every package; `npm run build` must pass cleanly.

## 🗺 Future roadmap

- GitHub OAuth + real repository import/push (optional, off by default)
- Real git history per project (libgit2-style commits)
- Streaming AI responses + inline diff/apply of suggested fixes
- Monaco multi-cursor file search across projects
- Live test execution in an isolated sandbox
- Plugin system for custom analyzers
- Docker/Compose deployment guide (optional — never required)
- Possible hosted/cloud tier (explicitly **not** required by this repo — the
  local, free mode stays first-class)

## 🤝 Contributing

PRs welcome — keep changes small and tested:

1. Fork & branch from `main`
2. `npm run lint && npm run test && npm run build` must pass
3. Add tests for new behaviour

## 📄 License

[MIT](LICENSE) © 2026 Tanish Mehera

---

<div align="center">
<sub>Built with React · Fastify · Prisma · SQLite · Monaco — free and open source.</sub>
</div>
