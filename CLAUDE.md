# Relay — CLAUDE.md

## Project Overview

**Relay** is an AI-powered founder CRM for deal memory and follow-up intelligence. It helps founders track warm relationships, infer when to follow up, and avoid letting deals go cold — without reading email bodies.

**Structure**: Monorepo
- `relay/backend/` — FastAPI Python API
- `relay/frontend/` — Next.js TypeScript web app
- `docker-compose.yml` — Orchestrates backend + frontend + PostgreSQL

---

## Development Commands

### Backend
```bash
cd relay/backend
pip install -r requirements.txt
alembic upgrade head          # Run DB migrations
uvicorn main:app --reload     # Dev server at http://localhost:8000
```

### Frontend
```bash
cd relay/frontend
npm install
npm run dev                   # Dev server at http://localhost:3000
npm run build
npm run lint
```

### Docker (full stack)
```bash
docker-compose up --build     # Build and start all services
docker-compose down
```

### Environment Setup
Copy `.env.example` to `.env` at the root and in `relay/backend/`. Required variables:
- `DATABASE_URL`, `SECRET_KEY`, `ENCRYPTION_KEY`
- `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_REDIRECT_URI`
- `NOTION_CLIENT_ID`, `NOTION_CLIENT_SECRET`
- `FRONTEND_URL`

---

## Tech Stack

### Backend
| Tech | Version | Purpose |
|------|---------|---------|
| FastAPI | 0.110 | Web framework, auto OpenAPI docs |
| SQLAlchemy | 2.0 | ORM |
| Alembic | 1.13 | DB migrations |
| PostgreSQL | 16 | Database |
| Pydantic | 2.7 | Request/response validation |
| python-jose / passlib | — | JWT + password hashing |
| cryptography (Fernet) | 42 | OAuth token encryption at rest |
| google-api-python-client | — | Gmail metadata + Calendar sync |
| notion-client | 2.2 | Notion database integration |

### Frontend
| Tech | Version | Purpose |
|------|---------|---------|
| Next.js | 14 | App Router, SSR/CSR |
| React | 18.3 | UI |
| TypeScript | 5.4 | Type safety |
| Tailwind CSS | 3.4 | Styling |
| Axios | 1.6 | HTTP client with JWT interceptor |
| Framer Motion | 11 | Animations |
| react-beautiful-dnd | — | Kanban drag-and-drop |
| date-fns | 3.6 | Date formatting |
| Heroicons | 2.1 | Icons |

---

## Architecture

### Backend Structure

```
backend/
├── main.py                   # FastAPI app, CORS, router registration
├── app/
│   ├── routers/              # API endpoint handlers
│   │   ├── auth.py           # Google OAuth flow, token exchange
│   │   ├── gmail.py          # Fetch email metadata (NO body)
│   │   ├── calendar.py       # Sync Google Calendar events
│   │   ├── notion.py         # Notion deal tracking integration
│   │   ├── deals.py          # CRUD for deals
│   │   ├── contacts.py       # Contact management
│   │   ├── today.py          # Today View: follow-ups, drafts, meetings
│   │   ├── pending.py        # AI-inferred actions awaiting approval
│   │   ├── integrations.py   # Manage connected accounts
│   │   ├── users.py          # User profile
│   │   └── demo.py           # Demo mode (no auth required)
│   ├── services/             # Business logic
│   │   ├── gmail_service.py          # Thread metadata fetching only
│   │   ├── calendar_service.py       # Calendar event sync
│   │   ├── notion_service.py         # Notion DB read/write
│   │   ├── intelligence_service.py   # Warmth scoring, deal momentum
│   │   └── demo_service.py           # Mock data for demo mode
│   ├── models.py             # SQLAlchemy ORM models
│   ├── schemas.py            # Pydantic schemas
│   ├── auth.py               # JWT creation/verification, get_current_user
│   ├── encryption.py         # Fernet token encryption utilities
│   ├── config.py             # Settings (env vars, JWT expiry)
│   └── database.py           # SQLAlchemy session management
└── alembic/versions/001_initial_schema.py
```

### Frontend Structure

```
frontend/src/
├── app/                      # Next.js App Router
│   ├── page.tsx              # Landing page (login / demo)
│   ├── onboarding/           # Post-OAuth onboarding flow
│   └── app/                  # Protected pages (require auth)
│       ├── today/            # Main dashboard
│       ├── pipeline/         # Kanban board
│       ├── pending/          # AI-inferred actions
│       ├── contacts/         # Contact directory + detail pages
│       ├── email-digest/     # Email activity summary
│       └── settings/         # Integrations + preferences
├── components/
│   ├── layout/               # Header, Sidebar, BottomNav
│   └── ui/                   # DealCard, KanbanBoard, KanbanCard,
│                             #   WarmthChip, InferredBadge, PendingItem,
│                             #   LoadingAnimation
├── context/
│   ├── AuthContext.tsx        # User auth state
│   └── AppContext.tsx         # Global app state
├── hooks/useApi.ts            # API request hook (loading/error states)
└── lib/api.ts                 # Axios instance + all API endpoint functions
```

### Database Schema

Tables managed by Alembic migrations:
- `users` — Founder profiles, Google OAuth IDs
- `integrations` — Encrypted OAuth tokens per provider
- `contacts` — Deal contacts (email, company, role, LinkedIn)
- `deals` — Deals with stage, warmth score, notes, confidence
- `emails` — Email metadata (subject, sender, date, thread_id — no body)
- `meetings` — Calendar event metadata
- `pending_actions` — AI-inferred actions awaiting user approval
- `activity_logs` — Audit trail

---

## Key Conventions & Rules

### Privacy (Critical)
- **NEVER fetch or store email bodies.** Gmail access uses `gmail.metadata` scope only.
  - Allowed fields: subject, sender, date, thread_id, reply latency, thread count
  - `gmail_service.py` must stay metadata-only
- **Google Calendar**: `calendar.readonly` scope — read only, never write
- **Notion writes**: Append-only. Never overwrite existing page content.

### Security
- All OAuth tokens (Google, Notion) must be encrypted at rest using Fernet via `encryption.py`.
- JWTs expire after 7 days (configured in `config.py`).
- Never expose raw tokens in API responses.

### AI Transparency
- All AI-inferred actions go into `pending_actions` table first.
- They require **explicit user approval** before being committed to the main DB.
- Never auto-apply AI suggestions without user confirmation.

### Deal Warmth System
Calculated in `intelligence_service.py`:
- **Hot** — recent activity, fast replies
- **Warm** — moderate engagement
- **Cooling** — slowing down
- **Cold** — prolonged silence
Based on: silence days, reply latency (hours), email thread count, days since last meeting.

Tailwind custom colors for warmth:
- `hot`: #EF4444, `warm`: #F59E0B, `cooling`: #3B82F6, `cold`: #6B7280

### Demo Mode
- Route: `/demo` (frontend), `/demo/*` (backend)
- No OAuth credentials required
- Data served from `demo_service.py` with mock founder "Not So Pareshan Founder" at "Demo Startup"
- Used for testing and showcasing without real credentials

### Frontend Patterns
- Use `useApi` hook for all API calls — handles loading/error state uniformly
- All API calls go through `lib/api.ts` Axios instance (JWT auto-attached via interceptor)
- Path alias `@/*` maps to `src/*`
- Minimum touch target: 44px (Tailwind `min-h-[44px]`)

---

## Updating This File

Update CLAUDE.md whenever:
- New routers, services, or components are added
- New environment variables are required
- Conventions or privacy rules change
- New integrations are added
- Database schema changes (new Alembic migration)
