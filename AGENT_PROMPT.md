# BuildMate — AI Agent Replication Prompt

**Use this prompt when starting a new AI agent session to continue or replicate the BuildMate project.**

Copy everything below the line into your AI agent's system prompt or initial message.

---

## PROMPT START

You are building **BuildMate**, a construction project management platform for Australian builders. This is a full-stack monorepo web application.

### Your Reference Documents

The project has 5 key reference documents. **Read them in this order before making any changes:**

1. **`PRODUCT_SPEC.md`** — The original product requirements. Defines all features, data models, API endpoints, UI/UX principles, and non-functional requirements. This is the source of truth for WHAT to build.

2. **`DESIGN_DOCUMENT.md`** (~2,200 lines) — Comprehensive technical design document. Contains:
   - Architecture, tech stack, project structure (Sections 1–4)
   - Database schema with 20 Prisma models, all enum values (Section 5)
   - Complete API endpoint reference with request/response formats (Section 6)
   - Frontend routing, state management, API client patterns (Section 7)
   - Authentication & security implementation (Section 8)
   - UI component specs and Gantt chart component (Sections 9–10)
   - Environment setup and demo data (Sections 11–12)
   - Known issues and workarounds (Section 13)
   - **UI/UX Design System** — exact colours, typography, spacing, component specs, chart designs, icon inventory, responsive breakpoints (Section 15)
   - **Complete Source File Reference** — all config files, Zod validation schemas, server route implementation details, frontend API layer patterns, exact dependency versions (Section 16)

3. **`TASKS.md`** (~570 lines) — Detailed task breakdown with 231 total tasks across 9 phases. Each task has a number, description, implementation detail, and status (✅/🔲/🔶). **Always update task status when completing work.**

4. **`SOURCE_CODE.md`** (~9,800 lines) — Verbatim source code for every file in the project. Use this to replicate files exactly. Contains all 50+ source files organised by: Web Frontend (config, types, store, hooks, API layer, UI components, layout components, GanttChart, pages) and Server Backend (entry, config, types, middleware, all 10 route files, Prisma schema).

5. **`server/prisma/schema.prisma`** — The database schema with 20 models. Read this directly for the latest version.

### Architecture Overview

```
Browser (React SPA, port 5173)
    ↓ Vite Proxy (/api → :3001)
Express REST API (port 3001)
    ↓ Prisma ORM
SQLite (server/prisma/dev.db)
```

- **Monorepo:** `server/` (Express + Prisma + SQLite) and `web/` (React + Vite + Tailwind)
- **Auth:** JWT Bearer tokens, stored in localStorage as `buildmate_token`
- **API pattern:** Flat routes (`/api/transactions?projectId=X`), not nested
- **State:** TanStack React Query (server state) + React Context (auth)
- **Styling:** Tailwind CSS with custom `primary`, `success`, `warning`, `danger` colour tokens

### Tech Stack (exact versions matter)

**Server:** Express 4.21, Prisma 6.3, TypeScript 5.7, bcryptjs 2.4, jsonwebtoken 9.0, zod 3.24, multer 1.4, helmet 8.0, morgan 1.10, cors 2.8, tsx 4.19

**Web:** React 18.3, Vite 5.4, TypeScript 5.6, Tailwind 3.4, React Router 6.27, TanStack React Query 5.59, Axios 1.7, Recharts 2.13, Lucide React 0.451, clsx + tailwind-merge, date-fns 4.1

### Current State (as of 30 March 2026)

- **Phase 1 (MVP) is ~90% complete.** 26 tasks done, 204 remaining across 9 phases.
- All server routes (10 files) are built and working
- All frontend pages (12 pages) are built with create forms
- All 8 UI components and 4 layout components are complete
- Interactive Gantt chart with zoom is working

### Known Issues (MUST maintain until resolved)

These are documented workarounds — do NOT break them when making changes:

| Area | Frontend Expects | Server Returns | Workaround |
|------|-----------------|----------------|------------|
| User name | `firstName`, `lastName` | `name` | `mapUser()` in `web/src/api/auth.ts` splits name |
| User role | lowercase `admin` | uppercase `ADMIN` | `mapUser()` lowercases |
| Auth token | `token` | `accessToken` | `auth.ts` maps `accessToken → token` |
| Task progress | `progress` | `percentComplete` | GanttChart uses `percentComplete` |
| Pagination | flat array | `{data: [], pagination: {}}` | Each API function: `Array.isArray(data) ? data : data.data ?? []` |

### Coding Conventions — FOLLOW EXACTLY

1. **Server route pattern:** Each route file creates `new PrismaClient()` at module level, applies `router.use(authenticate)`, defines Zod schemas inline, uses `validate(schema)` middleware.

2. **Frontend create form pattern:**
   ```typescript
   const [showCreate, setShowCreate] = useState(false);
   const [form, setForm] = useState(INITIAL_FORM);
   const createMutation = useMutation({
     mutationFn: (payload) => createEntity(payload),
     onSuccess: () => {
       queryClient.invalidateQueries({ queryKey: ['entity-key'] });
       setShowCreate(false);
       setForm(INITIAL_FORM);
     },
   });
   ```

3. **Page structure:** Back link → Title + action button → Content cards/tables. Use `space-y-6` vertical spacing.

4. **Component imports:** Always use `@/` path alias. Import from `@/components/ui/`, `@/api/`, `@/lib/utils`, `@/hooks/useAuth`.

5. **Styling:** Use `cn()` for conditional classes. Follow existing Tailwind patterns — no custom CSS. Use `text-sm` for body text, `text-xs` for labels, `text-xl font-bold` for headings.

6. **Tab filter pattern:** `flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1` with active: `bg-white text-gray-900 shadow-sm`.

7. **Select elements:** Style manually matching Input: `block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary`.

8. **Currency:** Always use `formatCurrency()` from `@/lib/utils` (AUD, no decimals).

9. **Dates:** Always use `formatDate()` from `@/lib/utils` (en-AU, 2-digit day, short month, numeric year).

10. **Error handling:** Server uses `AppError` class with status codes. Frontend shows error via `useState` error state or React Query error.

### Task Execution Rules

1. **Before starting any task:** Read the relevant existing source files to understand current implementation.
2. **Follow TASKS.md numbering:** Reference task numbers (e.g., "Implementing task 1.8.1").
3. **Update TASKS.md** when completing a task: Change 🔲 to ✅.
4. **Test after each change:** Ensure the server starts (`cd server && npm run dev`) and web compiles (`cd web && npm run dev`).
5. **One task at a time:** Complete and verify before moving to the next.
6. **No over-engineering:** Implement exactly what the task describes, nothing more.
7. **Maintain consistency:** Match existing code style, patterns, and naming.

### Priority Order for Remaining Work

If asked to "continue building" without a specific task, work in this order:

1. **Phase 1 remaining (1.8.x, 1.9.x, 1.10.x, 1.11.x)** — Complete the MVP
2. **Phase 2 (Financial Core)** — Transactions, invoices, budget enhancements
3. **Phase 3 (Planning & Tradies)** — Gantt interactivity, tradie management
4. **Phase 4 (Cash Flow & Reports)** — Forecasting, PDF reports
5. **Phase 5 (Inspections & Documents)** — Checklists, defect photos, versioning
6. **Phase 6 + 6B (Security & NFRs)** — RBAC, audit logging, accessibility
7. **Phase 7 (Production)** — PostgreSQL, Docker, CI/CD, testing
8. **Phase 8 (Mobile)** — React Native iPhone/iPad app

## PROMPT END
