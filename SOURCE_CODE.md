# BuildMate — Complete Source Code Reference

> **Construction Management Platform for Australian Builders**
> Full-stack monorepo: Express/Prisma/SQLite backend + React/Vite/Tailwind frontend + React Native/Expo mobile app

## Overview

BuildMate is a construction management platform designed for Australian residential and commercial builders. It provides project management, financial tracking (AUD), scheduling with Gantt charts, inspection/defect management, tradie (subcontractor) compliance, document management, and reporting.

### Key Technical Details

| Component | Technology | Port |
|-----------|-----------|------|
| Server | Express 4 + Prisma ORM + SQLite | 3001 |
| Web | React 18 + Vite + Tailwind CSS | 5173 |
| Mobile | Expo SDK 52 + React Native + NativeWind | — |
| Auth | JWT Bearer tokens (bcrypt, 24h access / 7d refresh) | — |
| Database | SQLite (dev), PostgreSQL-ready (prod) | — |
| Testing | Vitest + Supertest (server), Vitest + Testing Library (web) | — |
| CI/CD | GitHub Actions → GHCR Docker images | — |

### Login Credentials (Seed Data)

- **Email:** `admin@buildmate.com`
- **Password:** `Password123!`

---

## Source Code Files

The complete source code is split across multiple documents for manageability:

| Document | Contents | Files |
|----------|----------|-------|
| [SOURCE_CODE_INFRA.md](SOURCE_CODE_INFRA.md) | Infrastructure & DevOps | .gitignore, docker-compose.yml, Dockerfiles, nginx.conf, CI/CD workflows |
| [SOURCE_CODE_SERVER.md](SOURCE_CODE_SERVER.md) | Server core, config, database | package.json, tsconfig, .env.example, Prisma schema, seed data, index.ts, config, types, middleware, libraries |
| [SOURCE_CODE_SERVER_ROUTES.md](SOURCE_CODE_SERVER_ROUTES.md) | API routes & tests | All 13 route files (auth, projects, transactions, invoices, budget, cashflow, tradies, tasks, inspections, documents, dashboard, reports, users) + 2 test files |
| [SOURCE_CODE_WEB.md](SOURCE_CODE_WEB.md) | Web frontend — config, core, API, state | package.json, tsconfig, vite.config, tailwind.config, index.html, main.tsx, App.tsx, types, utils, permissions, auth store, hooks, all 14 API client files |
| [SOURCE_CODE_WEB_COMPONENTS.md](SOURCE_CODE_WEB_COMPONENTS.md) | Web frontend — UI & layout components | 8 UI components (Badge, Button, Card, EmptyState, Input, LoadingSpinner, Modal, Table), 5 layout components (AppLayout, Sidebar, Header, BottomNav, MobileDrawer), GanttChart |
| [SOURCE_CODE_WEB_PAGES.md](SOURCE_CODE_WEB_PAGES.md) | Web frontend — all page components | All 27 page components + 8 test files |
| [SOURCE_CODE_MOBILE.md](SOURCE_CODE_MOBILE.md) | Mobile app — all files | Config, navigation, screens, components, hooks, API, tests |

---

## Project Structure

```
buildmate/
├── .github/
│   └── workflows/
│       ├── ci.yml                    # CI — lint, type check, test
│       └── deploy.yml                # Build & deploy Docker images
├── server/
│   ├── prisma/
│   │   ├── schema.prisma             # 22 Prisma models (~570 lines)
│   │   └── seed.ts                   # Seed data (admin, 2 projects, 5 tradies, tasks, etc.)
│   ├── src/
│   │   ├── config/
│   │   │   └── index.ts              # Environment config (port, JWT, CORS, uploads)
│   │   ├── types/
│   │   │   └── index.ts              # AuthRequest, AuthUser, PaginationQuery, AppError
│   │   ├── middleware/
│   │   │   ├── auth.ts               # JWT authenticate + RBAC authorize
│   │   │   ├── errorHandler.ts       # Global error handler
│   │   │   └── validate.ts           # Zod schema validation
│   │   ├── lib/
│   │   │   ├── sanitize.ts           # HTML tag stripping middleware
│   │   │   └── auditLog.ts           # Audit log helper
│   │   ├── routes/
│   │   │   ├── auth.ts               # Register, login, refresh, profile
│   │   │   ├── projects.ts           # CRUD + team + financial summary + activity
│   │   │   ├── transactions.ts       # CRUD with 7-year retention policy
│   │   │   ├── invoices.ts           # CRUD + status transitions + retention
│   │   │   ├── budget.ts             # Categories + variations + summary + contingency
│   │   │   ├── cashflow.ts           # Entries + forecast generation + claim schedules
│   │   │   ├── tradies.ts            # CRUD + compliance + documents + assignments + payments
│   │   │   ├── tasks.ts              # CRUD + dependencies + critical path + delays + baseline
│   │   │   ├── inspections.ts        # CRUD + items + defects
│   │   │   ├── documents.ts          # Upload + download + versioning
│   │   │   ├── dashboard.ts          # Stats + recent activity
│   │   │   ├── reports.ts            # GST, budget, cashflow, status reports
│   │   │   └── users.ts              # List users + role management (admin)
│   │   ├── __tests__/
│   │   │   ├── auth.test.ts          # Auth route tests (register, login)
│   │   │   └── health.test.ts        # Health endpoint tests
│   │   └── index.ts                  # Express app entry point
│   ├── Dockerfile
│   ├── package.json
│   ├── tsconfig.json
│   └── .env.example
├── web/
│   ├── src/
│   │   ├── api/                      # 14 API client modules
│   │   ├── components/
│   │   │   ├── ui/                   # 8 reusable UI components
│   │   │   ├── layout/               # 5 layout components
│   │   │   └── GanttChart.tsx        # Interactive Gantt chart (539 lines)
│   │   ├── hooks/
│   │   │   └── useAuth.ts            # Auth hook
│   │   ├── lib/
│   │   │   ├── utils.ts              # formatCurrency, formatDate, cn()
│   │   │   └── permissions.ts        # Role-based permissions
│   │   ├── pages/                    # 27 page components
│   │   ├── store/
│   │   │   └── authStore.tsx         # Auth context + provider
│   │   ├── test/
│   │   │   └── setup.ts              # Vitest setup
│   │   ├── types/
│   │   │   └── index.ts              # Frontend TypeScript types
│   │   ├── App.tsx                   # Route definitions
│   │   ├── main.tsx                  # React entry point
│   │   └── index.css                 # Tailwind imports
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts
│   ├── vitest.config.ts
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   └── index.html
├── mobile/
│   ├── src/
│   │   ├── api/                      # 11 API client modules
│   │   ├── components/               # 7 shared components
│   │   ├── hooks/                    # 11 custom hooks
│   │   ├── navigation/               # 7 navigators
│   │   ├── screens/                  # 16+ screen components
│   │   ├── store/
│   │   │   └── authStore.tsx         # Auth context
│   │   ├── theme/
│   │   │   └── index.ts              # Theme constants
│   │   ├── types/
│   │   │   └── index.ts              # Mobile TypeScript types
│   │   └── utils/
│   │       └── index.ts              # Utility functions
│   ├── App.tsx                       # Expo entry point
│   ├── app.json
│   ├── babel.config.js
│   ├── eas.json
│   ├── jest.config.js
│   ├── package.json
│   ├── tsconfig.json
│   └── DEPLOYMENT.md
├── .gitignore
└── docker-compose.yml
```

---

## Database Schema Summary (22 Models)

| Model | Purpose | Key Relations |
|-------|---------|---------------|
| User | System users (5 roles) | Creates projects, tasks, transactions |
| Project | Construction projects | Has team, tasks, transactions, invoices, inspections |
| ProjectTeam | Project membership | Links User ↔ Project |
| Transaction | Income/expense records | Belongs to Project, optional Tradie |
| Invoice | Progress claims & tax invoices | Has LineItems, belongs to Project |
| InvoiceLineItem | Invoice line items | Belongs to Invoice |
| BudgetCategory | Cost code categories | Tree structure (parent/children), has Variations |
| Variation | Budget change orders | Links to BudgetCategory |
| CashFlowEntry | Monthly cash flow (actual/forecast) | Belongs to Project |
| ClaimSchedule | Progress claim schedule | Belongs to Project |
| Tradie | Subcontractors | Has documents, assignments, transactions |
| TradieDocument | Tradie compliance docs | Belongs to Tradie |
| TradieProjectAssignment | Tradie ↔ Project link | Performance rating |
| Task | Scheduled tasks | Tree structure, dependencies, delay logs |
| TaskDependency | Task relationships | FS, SS, FF, SF with lag days |
| DelayLog | Delay tracking | Links to Task and Project |
| Inspection | Site inspections (8 types) | Has Items and Defects |
| InspectionItem | Inspection checklist items | Pass/Fail/NA/Not Inspected |
| Defect | Defect tracking | Severity, status workflow, photos |
| Document | Project documents (10 folders) | Versioning support |
| ContingencyDrawdown | Contingency fund usage | Belongs to Project |
| AuditLog | Activity audit trail | Tracks all CRUD operations |
