# BuildMate — Detailed Task Breakdown

**Version:** 1.0
**Date:** 30 March 2026
**Status:** Planning

This document lists every task required to complete all BuildMate requirements across all phases, including the iPad and iPhone native apps.

---

## Table of Contents

1. [Phase 1 — Foundation (MVP)](#phase-1--foundation-mvp)
2. [Phase 2 — Financial Core](#phase-2--financial-core)
3. [Phase 3 — Planning & Tradies Enhancements](#phase-3--planning--tradies-enhancements)
4. [Phase 4 — Cash Flow & Reports](#phase-4--cash-flow--reports)
5. [Phase 5 — Inspections & Documents Enhancements](#phase-5--inspections--documents-enhancements)
6. [Phase 6 — Security & Access Control](#phase-6--security--access-control)
6B. [Phase 6B — Non-Functional Requirements](#phase-6b--non-functional-requirements)
7. [Phase 7 — Production Readiness](#phase-7--production-readiness)
8. [Phase 8 — iPad & iPhone App](#phase-8--ipad--iphone-app)

**Legend:**
- ✅ = Complete
- 🔲 = To Do
- 🔶 = Partially done

---

## Phase 1 — Foundation (MVP)

> Goal: Core project scaffolding, authentication, project CRUD, basic dashboard, responsive layout.
> **Status: ~90% complete** — most items are built, some polish tasks remain.

### 1.1 Project Scaffolding ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.1.1 | Initialise monorepo structure | Create root with `server/` and `web/` directories, shared `.gitignore`, `docker-compose.yml` | ✅ |
| 1.1.2 | Server scaffolding | `npm init`, install Express, TypeScript, tsx, Prisma, bcryptjs, jsonwebtoken, zod, multer, helmet, morgan, cors, dotenv. Set up `tsconfig.json`, `package.json` scripts (`dev`, `build`, `start`, `prisma:generate`, `prisma:migrate`, `prisma:studio`). | ✅ |
| 1.1.3 | Web scaffolding | `npm create vite@latest` with React + TypeScript. Install react-router-dom, @tanstack/react-query, axios, tailwindcss, postcss, autoprefixer, lucide-react, recharts, clsx, tailwind-merge, date-fns. Configure `vite.config.ts` with path alias `@` → `src/` and API proxy `/api` → `http://localhost:3001`. | ✅ |
| 1.1.4 | Tailwind configuration | Create `tailwind.config.js` with custom colour tokens: `primary` (9 shades), `success` (4 shades), `warning` (4 shades), `danger` (4 shades). Set `content` to `['./index.html', './src/**/*.{js,ts,jsx,tsx}']`. | ✅ |
| 1.1.5 | Global CSS setup | `index.css` with `@tailwind base/components/utilities` + base layer `body { @apply min-h-screen }`. `index.html` with `bg-gray-50 text-gray-900 antialiased` on body, viewport meta with `maximum-scale=1.0, user-scalable=no`, theme-color `#1E40AF`. | ✅ |
| 1.1.6 | Utility functions | `src/lib/utils.ts` — `cn()` (clsx + tailwind-merge), `formatCurrency()` (en-AU, AUD, 0 decimals), `formatDate()` (en-AU, 2-digit day, short month, numeric year). | ✅ |

### 1.2 Database Schema ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.2.1 | Prisma schema | Define all 20 models in `server/prisma/schema.prisma` using SQLite provider. Models: User, Project, ProjectTeam, Transaction, Invoice, InvoiceLineItem, BudgetCategory, Variation, CashFlowEntry, ClaimSchedule, Tradie, TradieDocument, TradieProjectAssignment, Task, TaskDependency, DelayLog, Inspection, InspectionItem, Defect, Document. | ✅ |
| 1.2.2 | Initial migration | Run `npx prisma migrate dev --name init` to create SQLite database. | ✅ |
| 1.2.3 | Generate Prisma client | Run `npx prisma generate` to create type-safe client. | ✅ |

### 1.3 Authentication ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.3.1 | Auth middleware | `server/src/middleware/auth.ts` — `authenticate()` middleware to verify JWT from `Authorization: Bearer` header, extract `{ id, email, role }` into `req.user`. `authorize(...roles)` middleware for role checking. | ✅ |
| 1.3.2 | Auth routes | `server/src/routes/auth.ts` — POST `/register` (hash password with bcryptjs 12 rounds, return user + accessToken + refreshToken), POST `/login`, POST `/refresh`, GET `/me`, PUT `/me`. Zod validation for email, password (min 8 chars). | ✅ |
| 1.3.3 | Frontend AuthContext | `web/src/store/authStore.tsx` — AuthProvider with `token` (from localStorage `buildmate_token`), `user`, `isLoading`, `login()`, `logout()`. On mount, if token exists call `getMe()`. | ✅ |
| 1.3.4 | useAuth hook | `web/src/hooks/useAuth.ts` — wrapper around `useContext(AuthContext)`. | ✅ |
| 1.3.5 | API client | `web/src/api/client.ts` — Axios instance with baseURL `/api`, request interceptor to attach Bearer token, response interceptor to clear token on 401 (except login endpoint). | ✅ |
| 1.3.6 | Auth API functions | `web/src/api/auth.ts` — `login()`, `register()`, `getMe()`, `updateProfile()` with `mapUser()` to translate server `name` → `firstName/lastName` and `ADMIN` → `admin`. | ✅ |
| 1.3.7 | Login page | Full login page with centred card, logo, form, error handling, register link. | ✅ |
| 1.3.8 | ProtectedRoute | In `App.tsx` — wrapper that redirects to `/login` if no token, shows LoadingSpinner during auth check. | ✅ |

### 1.4 Projects CRUD ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.4.1 | Server routes | `server/src/routes/projects.ts` — GET (paginated, search, status filter), POST, GET /:id, PUT /:id, DELETE /:id. Include financial summary in GET /:id. | ✅ |
| 1.4.2 | API functions | `web/src/api/projects.ts` — `getProjects()`, `getProject()`, `createProject()`, `updateProject()`, `deleteProject()`. Handle paginated response unwrapping. | ✅ |
| 1.4.3 | Projects list page | Card grid layout with search bar, status tabs (All/Active/Complete/On Hold), project cards with name, address, badge, contract value, progress bar, dates, hover shadow. New Project modal with form. | ✅ |
| 1.4.4 | Project detail page | Tab navigation (Overview, Budget, Cash Flow, Planning, Financials, Inspections, Documents, Team). Overview tab with stat cards + quick action links. | ✅ |

### 1.5 Dashboard ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.5.1 | Dashboard page | Stats row (Active Projects, Total Budget, Outstanding Invoices, Expiring Licences), Recent Projects card (2-col span), Cash Position card, Upcoming Milestones card. | ✅ |

### 1.6 Responsive Layout ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.6.1 | AppLayout | `md:pl-64` content offset, sidebar + header + outlet + bottom nav. | ✅ |
| 1.6.2 | Sidebar | Fixed left sidebar, w-64, hidden below md. Logo + 4 nav links. | ✅ |
| 1.6.3 | Header | Sticky top, h-16, mobile menu button, notification bell, user avatar dropdown. | ✅ |
| 1.6.4 | BottomNav | Mobile bottom tab bar with 5 tabs, hidden on md+. | ✅ |

### 1.7 UI Component Library ✅

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.7.1 | Button | `forwardRef`, 4 variants (primary/secondary/danger/ghost), 3 sizes (sm/md/lg), focus rings, disabled state. | ✅ |
| 1.7.2 | Input | `forwardRef`, label, error state, focus ring. | ✅ |
| 1.7.3 | Modal | Overlay, body scroll lock, close button, title, max-w-lg. | ✅ |
| 1.7.4 | Card | Optional title + action header, responsive padding. | ✅ |
| 1.7.5 | Badge | 20+ status-to-colour mappings, rounded-full pill, capitalize. | ✅ |
| 1.7.6 | Table | Responsive overflow, header row, dividers. | ✅ |
| 1.7.7 | EmptyState | Icon + title + description + action slot. | ✅ |
| 1.7.8 | LoadingSpinner | Animated border spinner, 3 sizes. | ✅ |

### 1.8 Remaining Phase 1 Tasks

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.8.1 | Register page | Create `/register` route with registration form (name, email, password, confirm password). Link from Login page "Register" link currently points to `/login`. | 🔲 |
| 1.8.2 | Password reset flow | POST `/api/auth/forgot-password` (sends email), POST `/api/auth/reset-password` (with token). Frontend pages for forgot/reset password. | 🔲 |
| 1.8.3 | Profile page | `/profile` route displaying user info with edit form (name, email, phone, company). Connect to PUT `/auth/me`. | 🔲 |
| 1.8.4 | Mobile menu drawer | Hamburger menu button in Header should open a slide-out drawer with the same nav links as Sidebar on mobile. Currently the button has no handler. | 🔲 |
| 1.8.5 | "More" bottom nav tab | Bottom nav "More" tab currently links to `#more`. Should open a menu with Settings, Profile, Sign Out. | 🔲 |
| 1.8.6 | Seed script | Create `server/prisma/seed.ts` with demo data: 1 admin user, 2 projects, 16 tasks, 8 transactions, 12 budget categories, 6 cash flow entries, 3 inspections, 5 tradies, sample documents. Add `prisma.seed` config to `package.json`. | 🔲 |
| 1.8.7 | Error handling middleware improvements | Add rate limiting on auth endpoints (e.g., express-rate-limit). Return consistent error format across all endpoints. | 🔲 |

### 1.9 Dashboard — Functional Widgets

> The Dashboard page (1.5.1) was built with placeholder values for 3 of the 4 stats and all sub-sections. These tasks make every widget functional.

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.9.1 | Outstanding Invoices widget | Query total amount of invoices with status SENT or OVERDUE. Display as stat card value instead of "—". | 🔲 |
| 1.9.2 | Expiring Licences widget | Call `GET /tradies/compliance?days=30` and display count. Link card to compliance view. | 🔲 |
| 1.9.3 | Upcoming Milestones widget | Query tasks where `isMilestone = true` and `startDate` within next 7 days. Display in Milestones card with date + project name. Replace "No upcoming milestones" placeholder. | 🔲 |
| 1.9.4 | Overdue Tasks & Defects widget | Query tasks with status DELAYED and defects with status OPEN past dueDate. Display count + list in a new dashboard card. | 🔲 |
| 1.9.5 | Recent Activity feed | Create `GET /api/projects/:id/activity` endpoint (returns last 20 creates/updates across entities). Display in a dashboard card with icon + description + timestamp. Requires AuditLog infrastructure (see 6.4). | 🔲 |
| 1.9.6 | Cash Position — income/expense totals | Aggregate PAID transaction totals across all projects. Display actual income/expense values instead of "—". | 🔲 |

### 1.10 Web Polish & Missing Pages

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.10.1 | Settings page | Create `/settings` route and page. Include: notification preferences, company details, data export. Add "Settings" to Sidebar nav (with `Settings` lucide icon). | 🔲 |
| 1.10.2 | Project edit UI | Add "Edit" button on ProjectDetailPage header. Open modal pre-filled with project data (name, address, client, contract value, dates, status). PUT `/projects/:id`. | 🔲 |
| 1.10.3 | Project detail tabs cleanup | Current `ProjectDetailPage.tsx` mixes routed tab links (`<Link>`) and local-state tabs (`useState`). Refactor so Overview and Team are also separate routes (`/projects/:id/overview`, `/projects/:id/team`) for consistent behaviour and browser back-button support. | 🔲 |
| 1.10.4 | Admin user list | Create `/admin/users` route (Admin only). Table showing all users with name, email, role, created date. Inline role change dropdown. `GET /api/users` endpoint. | 🔲 |

### 1.11 Frontend/Backend Field Alignment

> These are documented known issues (DESIGN_DOCUMENT §13.1). They work via workarounds but create maintenance debt.

| # | Task | Detail | Status |
|---|------|--------|--------|
| 1.11.1 | Normalise User name fields | **Option A:** Change server to return `firstName`, `lastName` separate fields. **Option B:** Change frontend types to use single `name` field. Remove `mapUser()` split hack in `web/src/api/auth.ts`. Update all frontend references. | 🔲 |
| 1.11.2 | Normalise Task progress field | Align on one field name: either `progress` or `percentComplete`. Update Prisma schema OR frontend types + GanttChart component. Remove the dual-naming. | 🔲 |
| 1.11.3 | Normalise auth response field | Either server returns `token` (matching frontend) or frontend reads `accessToken` (matching server). Remove the mapping in `auth.ts`. | 🔲 |
| 1.11.4 | Normalise status enum casing | Decide on UPPERCASE (server) or lowercase (frontend). Apply consistently. Remove `.toLowerCase()` hacks. | 🔲 |

---

## Phase 2 — Financial Core

> Goal: Full financial tracking — transactions, invoices, budget management, variations.

### 2.1 Transactions Enhancements

| # | Task | Detail | Status |
|---|------|--------|--------|
| 2.1.1 | Transaction edit modal | On click of a table row, open Modal pre-filled with transaction data. PUT to `/transactions/:id`. | 🔲 |
| 2.1.2 | Transaction delete | Delete button per row with confirmation dialog. DELETE `/transactions/:id`. | 🔲 |
| 2.1.3 | Transaction search/filter | Add search by description, filter by category dropdown, filter by date range (from/to). | 🔲 |
| 2.1.4 | Transaction pagination | Display pagination controls (page numbers, prev/next) when results exceed limit. Use `pagination` from API response. | 🔲 |
| 2.1.5 | Tradie linking | When creating an expense transaction, allow selecting a Tradie from dropdown. Show tradie name in transaction table. | 🔲 |
| 2.1.6 | Bulk status update | Select multiple transactions via checkboxes, update status in bulk (e.g., mark as Paid). | 🔲 |
| 2.1.7 | Transaction CSV export | "Export" button to download filtered transactions as CSV. | 🔲 |

### 2.2 Invoice Management

| # | Task | Detail | Status |
|---|------|--------|--------|
| 2.2.1 | Invoice list page | Add "Invoices" tab to Financials page or separate route. Table with invoice number, amount, due date, status, badge. | 🔲 |
| 2.2.2 | Create invoice modal | Form with: type (Tax Invoice / Progress Claim), to (name, email, address), line items (dynamic rows with description, qty, unit, unit price, amount), subtotal, GST (10%), retention %, total, due date. Auto-generate invoice number. | 🔲 |
| 2.2.3 | Invoice line items | Dynamic add/remove line item rows. Units: m, m², hrs, ea, lot. Auto-calculate line amount = qty × unit price. Auto-sum subtotal. | 🔲 |
| 2.2.4 | Invoice detail view | Full invoice preview in a printable format. Show all line items, totals, company details, client details. | 🔲 |
| 2.2.5 | Invoice PDF generation | Generate PDF of invoice using a library (e.g., `@react-pdf/renderer` or server-side `puppeteer`/`pdfkit`). Download button. | 🔲 |
| 2.2.6 | Invoice email | POST `/invoices/:id/send` — send invoice PDF to client email via email service (e.g., Nodemailer + SMTP / SendGrid). | 🔲 |
| 2.2.7 | Invoice status tracking | Status lifecycle: Draft → Sent → Viewed → Paid / Overdue. Auto-mark overdue if past due date and not paid. | 🔲 |
| 2.2.8 | Invoice edit/delete | Edit modal for draft invoices. Delete with confirmation. Cannot edit sent/paid invoices. | 🔲 |
| 2.2.9 | Retention release tracking | Track retention held per invoice (% and amount). Add `retentionReleaseDate` field. Show retained/released amounts on project financial summary. Allow manual release with date. | 🔲 |
| 2.2.10 | GST / BAS-ready reporting | Generate BAS-period GST summary: total GST collected (income), total GST paid (expenses), net GST payable. Export as CSV or PDF for accountant. Date range filter by BAS quarter (Q1 Jul–Sep, Q2 Oct–Dec, Q3 Jan–Mar, Q4 Apr–Jun). | 🔲 |
| 2.2.11 | Dedicated financial summary endpoint | Create `GET /api/projects/:id/financial-summary` returning: totalIncome, totalExpenses, netPosition, outstandingInvoices, retentionHeld, budgetVariance. Currently this data is embedded in project GET /:id but PRODUCT_SPEC requires a dedicated endpoint. | 🔲 |

### 2.3 Budget Management Enhancements

| # | Task | Detail | Status |
|---|------|--------|--------|
| 2.3.1 | Budget category edit modal | Click category row to open edit modal. Update cost code, name, budget amount. | 🔲 |
| 2.3.2 | Budget category delete | Delete with confirmation. Prevent if transactions linked. | 🔲 |
| 2.3.3 | Sub-categories | Support parent-child hierarchy for budget categories (e.g., "03 Concrete" → "03.1 Footings", "03.2 Slab"). Indent child rows in table. Use `parentId` field. | 🔲 |
| 2.3.4 | Variation edit/delete | Edit existing variations (only if status is DRAFT). Delete with confirmation. | 🔲 |
| 2.3.5 | Variation → Budget linking | When a variation is APPROVED, auto-update the linked BudgetCategory's `revisedBudget`. | 🔲 |
| 2.3.6 | Committed spend tracking | Show committed amount (approved purchase orders not yet paid) separate from actual spend. | 🔲 |
| 2.3.7 | Cost-to-complete forecast | Auto-calculate `forecastFinal = actualSpent + forecastToComplete`. Allow manual override of `forecastToComplete`. | 🔲 |
| 2.3.8 | Budget alerts | Show warning banner when any category is >80% spent. Show danger when >100%. | 🔲 |
| 2.3.9 | Contingency tracking | Add contingency amount field to project. Track drawdowns. Show remaining contingency. | 🔲 |
| 2.3.10 | ContingencyDrawdown Prisma model | Add `ContingencyDrawdown` model to `schema.prisma`: `{ id, projectId, description, amount, date, approvedBy, createdAt }`. Run migration. | 🔲 |
| 2.3.11 | Contingency drawdown API | Create `POST /api/projects/:id/contingency/drawdown` and `GET /api/projects/:id/contingency` endpoints. Return contingency balance = project contingency amount − sum of drawdowns. | 🔲 |

---

## Phase 3 — Planning & Tradies Enhancements

> Goal: Full Gantt chart interactivity, task dependencies, tradie management polish.

### 3.1 Gantt Chart Enhancements

| # | Task | Detail | Status |
|---|------|--------|--------|
| 3.1.1 | Task dependency arrows | Draw SVG arrows between tasks based on `TaskDependency` records. Arrow types: FS (finish-to-start), SS, FF, SF. | 🔲 |
| 3.1.2 | Task drag-and-drop | Drag task bars horizontally to reschedule (update start/end dates). Use mouse events on Gantt bars. PUT `/tasks/:id` on drop. | 🔲 |
| 3.1.3 | Task resize | Drag right edge of task bar to change duration/end date. | 🔲 |
| 3.1.4 | Critical path highlighting | Calculate critical path (longest sequence of dependent tasks). Highlight critical path tasks with a distinct border or colour (e.g., red outline). API endpoint GET `/tasks/critical-path`. | 🔲 |
| 3.1.5 | Baseline schedule comparison | Store baseline start/end dates when baseline is set. Show baseline as a grey bar below actual bar in Gantt. | 🔲 |
| 3.1.6 | Task click to edit | Click a Gantt bar or task row to open edit modal. Update name, dates, progress, status, priority, assignee. | 🔲 |
| 3.1.7 | Task delete | Delete button in edit modal with confirmation. DELETE `/tasks/:id`. | 🔲 |
| 3.1.8 | Sub-tasks | Support parent-child task hierarchy using `parentId`. Indent subtasks in list view. Group in Gantt with expandable rows. | 🔲 |
| 3.1.9 | Task progress update inline | Click or drag progress fill within Gantt bar to update percentage. PUT `/tasks/:id/progress`. | 🔲 |
| 3.1.10 | Add dependency UI | In task edit modal, add dependency picker: select predecessor task + dependency type + lag days. POST `/tasks/:id/dependencies`. | 🔲 |
| 3.1.11 | Remove dependency | Delete dependency from task edit modal. DELETE `/task-dependencies/:id`. | 🔲 |
| 3.1.12 | Delay logging | "Log Delay" button on Planning page. Form: reason (Weather, Material Delay, etc.), description, delay days, affected task. POST `/projects/:id/delays`. | 🔲 |
| 3.1.13 | Calendar view | Alternative to Gantt — monthly calendar showing tasks and milestones. Toggle between Gantt / List / Calendar. | 🔲 |
| 3.1.14 | Milestones endpoint | Create `GET /api/projects/:id/milestones` — returns tasks where `isMilestone = true`, ordered by date. Used by dashboard widget (1.9.3) and mobile. | 🔲 |
| 3.1.15 | Resource allocation view | Dedicated view or panel showing who is assigned to which tasks and when. Visual resource utilisation (e.g., which tradies/users are overloaded). Linked to task assignment. | 🔲 |
| 3.1.16 | Task colour coding | Allow custom `colour` field per task (hex value). Render in Gantt chart using the custom colour instead of status-based colour. Colour picker in task edit modal. | 🔲 |

### 3.2 Projects — Content Features

> These PRODUCT_SPEC features (§4.2) are not yet built.

| # | Task | Detail | Status |
|---|------|--------|--------|
| 3.2.0a | Project photo gallery | Upload project-level photos (separate from document management). Grid/masonry view. Full-screen preview. Delete with confirmation. API: `GET /api/projects/:id/photos`, `POST /api/projects/:id/photos` (FormData), `DELETE /api/projects/:id/photos/:photoId`. May reuse Document model with `folder = 'PHOTOS'` or create a separate `ProjectPhoto` model. | 🔲 |
| 3.2.0b | Project notes | Add a notes panel/editor on the project Overview tab. Rich-text or plain-text. Auto-save or save button. The `notes` field already exists on the Project model but there is no UI to view/edit it. | 🔲 |
| 3.2.0c | Project activity log endpoint | Create `GET /api/projects/:id/activity` — returns chronological list of all actions on this project (creates, updates, status changes). Requires AuditLog table (see 6.4). Display on project detail page as a timeline. | 🔲 |

### 3.3 Tradie Enhancements

| # | Task | Detail | Status |
|---|------|--------|--------|
| 3.2.1 | Tradie detail page | Dedicated `/tradies/:id` route with full profile, document list, project assignments, payment history. | 🔲 |
| 3.2.2 | Tradie edit modal | Click tradie card to edit all fields. PUT `/tradies/:id`. | 🔲 |
| 3.2.3 | Tradie archive | Soft-delete (set status to INACTIVE). Show archive confirmation. Filter to hide inactive by default. | 🔲 |
| 3.2.4 | Tradie document upload | Upload licence, insurance, workers comp certificates. Show expiry date and status (Valid / Expiring / Expired). POST `/tradies/documents`. | 🔲 |
| 3.2.5 | Tradie document delete | Delete document with confirmation. DELETE `/tradies/documents/:id`. | 🔲 |
| 3.2.6 | Expiry notifications | Backend: cron job or scheduled check for documents expiring in next 30 days. Frontend: notification bell counter in Header, notification dropdown listing expiring items. | 🔲 |
| 3.2.7 | Tradie project assignment | Assign tradie to a project with role, rate, rate type (hourly/daily/fixed/sqm). POST `/projects/:id/tradies`. | 🔲 |
| 3.2.8 | Tradie performance rating | After project completion, allow rating (1-5 stars) per project assignment. Store in `TradieProjectAssignment.performanceRating`. | 🔲 |
| 3.2.9 | Tradie payment history | On tradie detail page, list all transactions linked to this tradie across projects. | 🔲 |
| 3.2.10 | Compliance dashboard | Dedicated view showing all tradies grouped by compliance status: Valid (green), Expiring within 30 days (amber), Expired (red). Sortable, filterable. GET `/tradies/compliance`. | 🔲 |

---

## Phase 4 — Cash Flow & Reports

> Goal: Cash flow forecasting, claim scheduling, report generation, PDF export.

### 4.1 Cash Flow Enhancements

| # | Task | Detail | Status |
|---|------|--------|--------|
| 4.1.1 | Create cash flow entry | "Add Entry" button on Cash Flow page. Form: month, income amount, expense amount, type (Forecast/Actual), notes. POST `/cashflow/entries`. | 🔲 |
| 4.1.2 | Edit cash flow entry | Click row to edit. PUT `/cashflow/entries/:id`. | 🔲 |
| 4.1.3 | Delete cash flow entry | Delete with confirmation. DELETE `/cashflow/entries/:id`. | 🔲 |
| 4.1.4 | Auto-generate forecast | Button: "Generate Forecast" — server calculates monthly forecasts from budget + task schedule. POST `/cashflow/forecast/:projectId`. | 🔲 |
| 4.1.5 | Forecast vs Actual toggle | Toggle to show/hide forecast entries vs actual entries on chart and table. | 🔲 |
| 4.1.6 | Claim schedule CRUD | Add claim, edit claim, delete claim. Form: claim number, description, amount, due date. Show status (Upcoming → Submitted → Approved → Paid). | 🔲 |
| 4.1.7 | Multi-project consolidated cash flow | `/reports/cashflow` or dashboard widget — aggregated cash flow across all active projects. GET `/cashflow/consolidated`. | 🔲 |
| 4.1.8 | Cash flow alerts | Show warning banner when cumulative position goes negative in any future month. | 🔲 |

### 4.2 Reports

| # | Task | Detail | Status |
|---|------|--------|--------|
| 4.2.1 | Budget Summary report | Table/chart showing all projects with budget, spent, variance. Drill-down to project budget detail. | 🔲 |
| 4.2.2 | Cash Flow report | Multi-project cash flow chart (S-curve) with date range filter. | 🔲 |
| 4.2.3 | Tradie Compliance report | List of all tradies with licence/insurance status, expiry dates, colour-coded. Filterable by status. | 🔲 |
| 4.2.4 | Project Status report | All projects with status, progress %, contract value, key dates. Pie chart of status distribution. | 🔲 |
| 4.2.5 | PDF report generation | Install `@react-pdf/renderer` or server-side PDF library. Generate downloadable PDF for each report type. Include company header, date range, tables, charts. | 🔲 |
| 4.2.6 | Date range filter | Reports page date range picker (From/To) that filters data across all report types. Currently has UI but no backend integration. | 🔲 |
| 4.2.7 | Export to CSV | "Export CSV" button for each table-based report. | 🔲 |

---

## Phase 5 — Inspections & Documents Enhancements

> Goal: Full inspection checklists, defect photo support, document versioning, preview.

### 5.1 Inspections

| # | Task | Detail | Status |
|---|------|--------|--------|
| 5.1.1 | Inspection detail page | Click inspection row to see full detail: all checklist items with pass/fail status, linked defects, notes. GET `/inspections/:id`. | 🔲 |
| 5.1.2 | Inspection edit | Edit scheduled date, inspector, status, notes. PUT `/inspections/:id`. | 🔲 |
| 5.1.3 | Inspection delete | Delete with confirmation (also deletes items). DELETE `/inspections/:id`. | 🔲 |
| 5.1.4 | Checklist items | Add checklist items to an inspection: category, description, result (Pass/Fail/NA/Not Inspected), notes, photos. POST `/inspections/:id/items`. | 🔲 |
| 5.1.5 | Checklist item update | Toggle pass/fail per item with a tap/click. PUT `/inspection-items/:id`. | 🔲 |
| 5.1.6 | NCC standard checklists | Pre-built checklist templates by inspection type (Pre-Slab: 15 items, Frame: 20 items, etc.). Auto-populate when creating inspection. | 🔲 |
| 5.1.7 | Inspection PDF report | Generate printable PDF: inspection details, all items with results, defect summary, photos. | 🔲 |

### 5.2 Defects

| # | Task | Detail | Status |
|---|------|--------|--------|
| 5.2.1 | Defect detail page | Click defect row to see full detail: description, photos, status history, assigned tradie. | 🔲 |
| 5.2.2 | Defect edit | Edit title, description, severity, status, assigned tradie, due date. PUT `/inspections/defects/:id`. | 🔲 |
| 5.2.3 | Defect photo upload | Upload photos with defect. Show thumbnails in defect detail. POST `/defects/:id/photos`. Store in S3/uploads directory. | 🔲 |
| 5.2.4 | Defect rectification photos | Separate photo upload for rectification evidence. Timestamp and user recorded. | 🔲 |
| 5.2.5 | Defect status lifecycle | Status flow: Open → Assigned → In Progress → Rectified → Verified → Closed. Each transition logs timestamp. | 🔲 |
| 5.2.6 | Defect assignment to tradie | Dropdown to assign defect to a tradie. Tradie sees assigned defects on their view. | 🔲 |
| 5.2.7 | Defect filters | Filter by severity, status, assigned tradie, date range. | 🔲 |

### 5.3 Documents

| # | Task | Detail | Status |
|---|------|--------|--------|
| 5.3.1 | Document preview | In-browser preview for PDFs (iframe) and images (img tag). Preview modal on document click. | 🔲 |
| 5.3.2 | Document version control | Upload new version of existing document. POST `/documents/:id/version`. Show version history list. | 🔲 |
| 5.3.3 | Document metadata edit | Edit name, category/folder, description. PUT `/documents/:id`. | 🔲 |
| 5.3.4 | Document search | Search input to filter documents by name across all folders. | 🔲 |
| 5.3.5 | Mandatory document checklist | Per project, show a checklist of required documents (e.g., Building Permit, Engineers Certificate, Insurance). Mark as uploaded or missing. | 🔲 |
| 5.3.6 | Document sharing | Generate a shareable link for a document (time-limited). Or share via email to tradie. | 🔲 |

---

## Phase 6 — Security & Access Control

> Goal: Enforce role-based access, session management, audit logging.

| # | Task | Detail | Status |
|---|------|--------|--------|
| 6.1 | RBAC enforcement | Server: Check `req.user.role` against allowed roles for each endpoint. Admin: full access. Project Manager: CRUD on assigned projects. Site Supervisor: read + update on assigned projects. Accounts: financial endpoints only. Tradie: read-only on assigned projects/tasks. | 🔲 |
| 6.2 | Frontend route guards | Hide nav items and routes based on user role. Show/hide buttons (e.g., tradies can't create projects). | 🔲 |
| 6.3 | Project team management | Assign users to projects with roles. Only team members can access project data. UI: Team tab on project detail page — add/remove team members. | 🔲 |
| 6.4 | Audit logging | Log all data changes (create/update/delete) with user ID, timestamp, entity type, entity ID, action. Separate `AuditLog` table. Show activity log on project detail page. | 🔲 |
| 6.5 | Session management | Track active sessions per user. Allow user to view and revoke sessions. Device info (browser, OS). | 🔲 |
| 6.6 | Rate limiting | Install `express-rate-limit`. Limit auth endpoints to 5 requests/minute per IP. Limit API endpoints to 100 requests/minute per user. | 🔲 |
| 6.7 | Input sanitisation | Sanitise all string inputs to prevent XSS. Use a library like `dompurify` or server-side sanitisation. | 🔲 |
| 6.8 | CSRF protection | Add CSRF tokens for state-changing requests if cookie-based auth is added. | 🔲 |
| 6.9 | File upload size enforcement | PRODUCT_SPEC says 50MB per file; current Multer config is 10MB. Decide on correct limit and enforce consistently across document upload and photo upload endpoints. Validate on frontend too (show error before upload starts). | 🔲 |

---

## Phase 6B — Non-Functional Requirements

> From PRODUCT_SPEC §7. These are explicit requirements that need dedicated tasks.

| # | Task | Detail | Status |
|---|------|--------|--------|
| 6B.1 | WCAG 2.1 AA accessibility audit | Audit all web pages for WCAG 2.1 AA compliance. Fix: keyboard navigation, focus indicators, ARIA labels on interactive elements, colour contrast ratios (4.5:1 min for text), form labels, alt text. Test with screen reader (VoiceOver/NVDA). | 🔲 |
| 6B.2 | Cross-browser testing | Test on Chrome, Safari, Firefox, Edge (latest 2 versions). Fix any rendering/functional issues. Document known browser-specific behaviours. | 🔲 |
| 6B.3 | Data retention policy (7 years) | Implement soft-delete for financial records. Add `deletedAt` field or archive mechanism. Ensure financial transactions, invoices, and budget data cannot be permanently deleted within 7 years. Document retention policy. | 🔲 |
| 6B.4 | Load/performance testing | Run load tests with 500 concurrent users. Tools: k6, Artillery, or Apache JMeter. Test all critical endpoints. Ensure API response time < 200ms for standard queries. Identify and fix bottlenecks. | 🔲 |
| 6B.5 | Uptime/SLO configuration | Configure health checks, auto-restart on crash (PM2 or Docker restart policy). Set up uptime monitoring (e.g., Uptime Robot, Pingdom). Target 99.5% availability. | 🔲 |
| 6B.6 | Web offline support (service worker) | Add service worker for web app. Cache static assets and API responses for offline viewing. Show "Offline" banner. Sync queued changes when back online. Use Workbox or Vite PWA plugin. | 🔲 |

---

## Phase 7 — Production Readiness

> Goal: Docker deployment, PostgreSQL migration, CI/CD, testing, monitoring.

### 7.1 Database Migration

| # | Task | Detail | Status |
|---|------|--------|--------|
| 7.1.1 | PostgreSQL migration | Update `schema.prisma` datasource to `postgresql`. Update enum handling (PostgreSQL native enums vs string). Update `docker-compose.yml` to include PostgreSQL service. | 🔲 |
| 7.1.2 | Environment configuration | Separate `.env.development`, `.env.staging`, `.env.production`. Different `DATABASE_URL`, `JWT_SECRET`, `CORS_ORIGIN` per env. | 🔲 |
| 7.1.3 | Data migration script | Script to migrate existing SQLite data to PostgreSQL (if needed). | 🔲 |

### 7.2 Docker & Deployment

| # | Task | Detail | Status |
|---|------|--------|--------|
| 7.2.1 | Server Dockerfile | Multi-stage build: install → build TypeScript → production image with `node dist/index.js`. Include Prisma client generation. | 🔲 |
| 7.2.2 | Web Dockerfile | Multi-stage: install → build Vite → serve with nginx or similar. | 🔲 |
| 7.2.3 | docker-compose production | PostgreSQL, server, web (nginx), Redis (for caching). Volumes for uploads and database. | 🔲 |
| 7.2.4 | File storage migration | Move from local `uploads/` directory to AWS S3 or Azure Blob Storage. Update document upload/download routes. Pre-signed URLs for secure access. | 🔲 |
| 7.2.5 | Redis caching | Install `ioredis`. Cache frequently accessed data: project list, user sessions. Cache invalidation on mutations. | 🔲 |
| 7.2.6 | HTTPS/TLS | Configure SSL certificates. Force HTTPS redirect. | 🔲 |

### 7.3 Testing

| # | Task | Detail | Status |
|---|------|--------|--------|
| 7.3.1 | Server unit tests | Jest tests for each route handler. Mock Prisma client. Test validation (Zod schemas), auth middleware, error handling. Aim for >80% coverage. | 🔲 |
| 7.3.2 | Server integration tests | Supertest tests against running Express app with test database. Test full request/response cycle for all endpoints. | 🔲 |
| 7.3.3 | Frontend unit tests | React Testing Library tests for each UI component (Button, Input, Modal, Card, Badge, Table, EmptyState, LoadingSpinner). | 🔲 |
| 7.3.4 | Frontend page tests | Test each page component: mock API calls, verify rendering, test user interactions (form submit, filter toggle, modal open/close). | 🔲 |
| 7.3.5 | E2E tests | Playwright or Cypress tests for critical flows: login → create project → add transaction → view budget → view Gantt. | 🔲 |

### 7.4 CI/CD

| # | Task | Detail | Status |
|---|------|--------|--------|
| 7.4.1 | GitHub Actions — lint & type-check | Workflow: on push/PR, run `tsc --noEmit` for server + web. Add ESLint config and run lint. | 🔲 |
| 7.4.2 | GitHub Actions — test | Workflow: run server tests, web tests. Upload coverage reports. | 🔲 |
| 7.4.3 | GitHub Actions — build & deploy | Workflow: build Docker images, push to registry, deploy to staging/production. | 🔲 |

### 7.5 Monitoring & Logging

| # | Task | Detail | Status |
|---|------|--------|--------|
| 7.5.1 | Structured logging | Replace `morgan` with structured JSON logging (e.g., `winston` or `pino`). Log request ID, user ID, response time. | 🔲 |
| 7.5.2 | Health check endpoint | GET `/api/health` — currently returns `{ status: "ok", timestamp }`. Enhance to return `{ status, version, uptime, db: "connected"/"error" }`. Add DB connectivity check via `prisma.$queryRaw`. | 🔶 |
| 7.5.3 | Error tracking | Integrate Sentry or similar for frontend and backend error tracking. | 🔲 |
| 7.5.4 | Performance monitoring | Add request duration tracking. Alert if API response time exceeds 200ms. | 🔲 |

---

## Phase 8 — iPad & iPhone App

> Goal: Native iOS app using React Native, sharing API layer with the web app. Optimised for both iPad and iPhone.

### 8.1 Project Setup

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.1.1 | Initialise React Native project | `npx react-native init BuildMate --template react-native-template-typescript` (or use Expo: `npx create-expo-app BuildMate -t blank-typescript`). Create `mobile/` directory in monorepo. | 🔲 |
| 8.1.2 | Choose framework: Expo vs bare | **Recommended: Expo** (managed workflow) for faster development, OTA updates, push notifications. If camera/file access needs custom native modules, use Expo dev client. | 🔲 |
| 8.1.3 | Configure app.json / app.config.ts | Set `name: "BuildMate"`, `slug: "buildmate"`, `ios.bundleIdentifier: "com.buildmate.app"`, `ios.supportsTablet: true`, `icon`, `splash`, `scheme: "buildmate"`. | 🔲 |
| 8.1.4 | Install core dependencies | `@react-navigation/native`, `@react-navigation/bottom-tabs`, `@react-navigation/native-stack`, `react-native-screens`, `react-native-safe-area-context`, `@tanstack/react-query`, `axios`, `@react-native-async-storage/async-storage`, `date-fns`, `react-native-vector-icons` or `expo-vector-icons`. | 🔲 |
| 8.1.5 | Install UI dependencies | `react-native-paper` or `nativewind` (Tailwind for RN) for consistent styling with web. `react-native-svg` for charts. `victory-native` for charts (or `react-native-gifted-charts`). `react-native-reanimated` for animations. | 🔲 |
| 8.1.6 | Project structure | Create folder structure under `mobile/src/`: `api/`, `components/`, `screens/`, `navigation/`, `hooks/`, `store/`, `types/`, `utils/`, `theme/`. | 🔲 |

### 8.2 Shared Code & API Layer

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.2.1 | Shared TypeScript types | Copy or symlink `web/src/types/index.ts` to `mobile/src/types/index.ts`. Keep interfaces identical. Consider extracting to a `shared/` package in monorepo. | 🔲 |
| 8.2.2 | API client (mobile) | Create `mobile/src/api/client.ts` — Axios instance with `baseURL` pointing to server URL (e.g., `https://api.buildmate.com.au`). Request interceptor reads token from `AsyncStorage`. Response interceptor handles 401. | 🔲 |
| 8.2.3 | API functions | Replicate all API functions from `web/src/api/*.ts` in `mobile/src/api/*.ts`. Same function signatures, same response mapping. | 🔲 |
| 8.2.4 | Auth store (mobile) | React Context or Zustand store. Token stored in `AsyncStorage` (key: `buildmate_token`). Same `login()`, `logout()`, `getMe()` pattern. | 🔲 |
| 8.2.5 | Shared utility functions | `formatCurrency()` (en-AU, AUD), `formatDate()` (en-AU), `cn()` equivalent if using NativeWind. | 🔲 |

### 8.3 Theme & Design System (Mobile)

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.3.1 | Colour theme | Define theme object matching web palette: `primary: '#1E40AF'`, `success: '#059669'`, `warning: '#D97706'`, `danger: '#DC2626'`, grays. Use React Navigation `DefaultTheme` override. | 🔲 |
| 8.3.2 | Typography | Use system font (San Francisco on iOS). Define text variants matching web: `heading` (20px bold), `subheading` (18px semibold), `body` (14px regular), `caption` (12px regular), `label` (12px medium uppercase). | 🔲 |
| 8.3.3 | Spacing constants | Define spacing scale matching Tailwind: `xs: 4`, `sm: 8`, `md: 16`, `lg: 24`, `xl: 32`. Use consistently across all screens. | 🔲 |
| 8.3.4 | iPad-specific layout | Detect `Platform.isPad` or use `useWindowDimensions()` width breakpoint (>768px). iPad shows: split-view navigation (sidebar + content), multi-column grids, larger touch targets. | 🔲 |
| 8.3.5 | iPhone-specific layout | Bottom tab bar navigation, single-column layouts, pull-to-refresh, swipe-to-delete gestures. | 🔲 |

### 8.4 Navigation

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.4.1 | Auth navigation | Stack navigator: Login → Register → ForgotPassword. If token exists, skip to main. | 🔲 |
| 8.4.2 | iPhone tab navigation | Bottom tab bar (5 tabs): Home (Dashboard), Projects, Tradies, Reports, More. Use `@react-navigation/bottom-tabs`. Icons from `lucide-react-native` or `expo-vector-icons`. Active: primary colour, inactive: gray. | 🔲 |
| 8.4.3 | iPad split navigation | Use `@react-navigation/drawer` or custom sidebar. Left panel (w-64): same nav links as web sidebar. Right panel: screen content. Collapsible on portrait rotation. | 🔲 |
| 8.4.4 | Project detail tabs | Within project: top tab bar or segmented control for Overview, Budget, Cash Flow, Planning, Financials, Inspections, Documents, Team. Use `@react-navigation/material-top-tabs` or custom. | 🔲 |
| 8.4.5 | Stack navigators per tab | Each tab has its own stack for drill-down (e.g., Projects → ProjectDetail → Budget). Maintain independent back stacks. | 🔲 |
| 8.4.6 | Deep linking | Configure URL scheme `buildmate://` for deep links: `buildmate://projects/:id`, `buildmate://tradies/:id`. Set up in `app.json`. | 🔲 |

### 8.5 Core Screens

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.5.1 | Login screen | Logo (HardHat icon), email/password fields, sign-in button (primary), register link. Error banner. Match web styling. | 🔲 |
| 8.5.2 | Register screen | Name, email, password, confirm password fields. Company optional. | 🔲 |
| 8.5.3 | Dashboard screen | Stats cards (2×2 grid on iPhone, 4-column on iPad). Recent Projects list (FlatList). Cash Position card. Pull-to-refresh. | 🔲 |
| 8.5.4 | Projects list screen | Search bar (SearchBar component). Status filter (horizontal ScrollView pills). Project cards (FlatList). "New Project" FAB (floating action button) or header button. | 🔲 |
| 8.5.5 | Project create screen | Full-screen form or modal bottom sheet. Same fields as web: name, address, client name, contract value, dates. | 🔲 |
| 8.5.6 | Project detail screen | Header with name, address, status badge. Tab bar for sub-sections. Overview: stat cards + quick actions (2×4 grid on iPad, 2×2 on iPhone). | 🔲 |
| 8.5.7 | Financials screen | Summary cards (income/expense/net/outstanding). Type toggle (Income/Expense). Transaction list (FlatList). Add transaction (modal or new screen). | 🔲 |
| 8.5.8 | Budget screen | Summary cards. Budget categories table (horizontal scroll on iPhone, full table on iPad). Budget vs Actual chart (bar chart using Victory Native). Variations section. | 🔲 |
| 8.5.9 | Cash Flow screen | Cash position cards. S-curve chart (Victory Native LineChart). Monthly table. Claim schedule section. | 🔲 |
| 8.5.10 | Planning screen | **iPhone:** Task list view (FlatList) with status badges, progress bars. Add task button. **iPad:** Gantt chart (horizontal ScrollView with custom rendered bars) + task list. Zoom controls. | 🔲 |
| 8.5.11 | Inspections screen | Inspections list + defects list in separate sections (or tabs). Add inspection / add defect buttons. Status badges. | 🔲 |
| 8.5.12 | Documents screen | Category filter pills. Document list with file icons, size, date. Upload button (opens document picker). Download (open in system viewer). Delete with swipe. | 🔲 |
| 8.5.13 | Tradies list screen | Search bar. Trade filter pills (ScrollView). Tradie cards with name, company, trade badge, phone, email, rating stars, licence expiry warning. | 🔲 |
| 8.5.14 | Tradie detail screen | Full profile. Documents section. Project assignments. Payment history. Edit button. | 🔲 |
| 8.5.15 | Reports screen | Report cards (same 4 as web). Date range picker. Tap to view report detail. | 🔲 |
| 8.5.16 | Profile screen | User info display + edit. Sign out button. App version. | 🔲 |
| 8.5.17 | Settings screen | Notifications toggle. Theme preferences. Data sync status. About/version info. | 🔲 |

### 8.6 Mobile-Specific Features

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.6.1 | Camera integration | `expo-camera` or `expo-image-picker` for capturing photos. Used in: inspection checklists, defect logging, document upload. Compress images before upload (max 2MB). | 🔲 |
| 8.6.2 | Photo gallery for defects | Capture multiple photos per defect. Show horizontal scrollable thumbnail gallery. Full-screen preview on tap. | 🔲 |
| 8.6.3 | GPS location tagging | `expo-location` — auto-tag photos and inspections with GPS coordinates. Show location on defect detail. | 🔲 |
| 8.6.4 | Push notifications | `expo-notifications` — register device token with server. Server sends push for: overdue tasks, expiring licences, new defects assigned, invoice paid. | 🔲 |
| 8.6.5 | Server push infrastructure | New table: `DeviceToken { id, userId, token, platform, createdAt }`. API: POST `/devices/register`, DELETE `/devices/:id`. Server uses `expo-server-sdk` or APNs to send pushes. | 🔲 |
| 8.6.6 | Offline mode | `@tanstack/react-query` persistence with `AsyncStorage`. Cache project data, tasks, tradies locally. Show "Offline" banner. Queue mutations and sync when online. Use `NetInfo` to detect connectivity. | 🔲 |
| 8.6.7 | Pull-to-refresh | `RefreshControl` on all FlatList screens. Trigger `queryClient.invalidateQueries()`. | 🔲 |
| 8.6.8 | Swipe gestures | `react-native-gesture-handler` or `react-native-swipeable-list`. Swipe left on list items for quick actions (delete, archive). | 🔲 |
| 8.6.9 | Biometric authentication | `expo-local-authentication` — Face ID / Touch ID option for login. Store token securely in `expo-secure-store`. | 🔲 |
| 8.6.10 | Share sheet | Use `react-native-share` to share project reports, documents, invoices via email/messages/AirDrop. | 🔲 |
| 8.6.11 | Document picker | `expo-document-picker` for selecting files from device. Support PDF, images, spreadsheets. | 🔲 |
| 8.6.12 | Haptic feedback | `expo-haptics` — subtle haptic feedback on button presses, toggle switches, successful actions. | 🔲 |

### 8.7 iPad-Specific Enhancements

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.7.1 | Split view layout | Permanent sidebar (w-72) on landscape. Collapsible sidebar on portrait. Content area uses remaining width. Detect orientation with `useWindowDimensions()`. | 🔲 |
| 8.7.2 | Multi-column grids | iPad stat cards: 4 columns. Project cards: 3 columns. Tradie cards: 3 columns. Use `FlatList` with `numColumns` or `FlashList`. | 🔲 |
| 8.7.3 | Gantt chart (iPad) | Full Gantt chart component for iPad (similar to web GanttChart.tsx). Custom `ScrollView` with task bars rendered using `react-native-svg`. Pinch-to-zoom for day width. Today line, weekend shading, milestone diamonds. | 🔲 |
| 8.7.4 | Side-by-side views | On iPad landscape, support showing two panels (e.g., Gantt chart + task detail). Use `SplitView` pattern. | 🔲 |
| 8.7.5 | Drag and drop (iPad) | On iPad, allow drag-and-drop to reorder tasks in planning view. Use `react-native-gesture-handler`. | 🔲 |
| 8.7.6 | Keyboard shortcuts | Support hardware keyboard shortcuts on iPad: Cmd+N (new project), Cmd+F (search), Cmd+S (save). | 🔲 |
| 8.7.7 | Pointer/cursor support | On iPad with trackpad/mouse, show hover states similar to web. Use `onMouseEnter`/`onMouseLeave` (supported on iPad via Catalyst). | 🔲 |
| 8.7.8 | Multi-window support | Support iPad multi-window (Split View / Slide Over). Ensure layout adapts to various window sizes. | 🔲 |

### 8.8 Build & Distribution

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.8.1 | App icon | Create app icon: `1024×1024` with HardHat symbol on primary (#1E40AF) background. Generate all required sizes via Expo or asset generator. | 🔲 |
| 8.8.2 | Splash screen | Primary colour background (#1E40AF) with white BuildMate logo + HardHat icon. Configure in `app.json`. | 🔲 |
| 8.8.3 | iOS build configuration | Configure `ios.buildNumber`, `ios.infoPlist` (camera permission, location permission, photo library permission descriptions). Set deployment target to iOS 16.0+. | 🔲 |
| 8.8.4 | iPad support configuration | Ensure `ios.supportsTablet: true` in `app.json`. Test all screen sizes: iPad mini, iPad Air, iPad Pro 11", iPad Pro 12.9". | 🔲 |
| 8.8.5 | EAS Build setup | Configure `eas.json` with development, preview, and production build profiles. Set up EAS credentials (Apple Developer account). | 🔲 |
| 8.8.6 | TestFlight distribution | Build for TestFlight via `eas build --platform ios --profile production`. Upload to App Store Connect. Invite beta testers. | 🔲 |
| 8.8.7 | App Store submission | Prepare: screenshots (iPhone 6.7", iPad 12.9"), app description, keywords, privacy policy URL, support URL. Submit for review. | 🔲 |
| 8.8.8 | OTA updates | Configure `expo-updates` for over-the-air JavaScript bundle updates without App Store review. Set update channel per environment. | 🔲 |

### 8.9 Testing (Mobile)

| # | Task | Detail | Status |
|---|------|--------|--------|
| 8.9.1 | Component unit tests | Jest + React Native Testing Library for all shared components. | 🔲 |
| 8.9.2 | Screen tests | Test each screen renders correctly with mocked API data. | 🔲 |
| 8.9.3 | Navigation tests | Test navigation flows: auth → main → project detail → sub-screens. | 🔲 |
| 8.9.4 | iPad layout tests | Test layout adapts correctly to iPad dimensions. Verify sidebar, multi-column grids, Gantt chart render. | 🔲 |
| 8.9.5 | E2E tests (Detox) | Install Detox. Test critical flows on iPhone and iPad simulators: login → projects → create project → view budget. | 🔲 |
| 8.9.6 | Device testing matrix | Test on: iPhone 15, iPhone 15 Pro Max, iPhone SE 3, iPad mini, iPad Air, iPad Pro 11", iPad Pro 12.9". Both orientations. | 🔲 |

---

## Task Summary

| Phase | Total Tasks | Complete | Partial | Remaining |
|-------|-----------|----------|---------|-----------|
| Phase 1 — Foundation (MVP) | 49 | 26 | 0 | 23 |
| Phase 2 — Financial Core | 30 | 0 | 0 | 30 |
| Phase 3 — Planning, Projects & Tradies | 32 | 0 | 0 | 32 |
| Phase 4 — Cash Flow & Reports | 15 | 0 | 0 | 15 |
| Phase 5 — Inspections & Documents | 20 | 0 | 0 | 20 |
| Phase 6 — Security & Access Control | 9 | 0 | 0 | 9 |
| Phase 6B — Non-Functional Requirements | 6 | 0 | 0 | 6 |
| Phase 7 — Production Readiness | 16 | 0 | 1 | 15 |
| Phase 8 — iPad & iPhone App | 54 | 0 | 0 | 54 |
| **Total** | **231** | **26** | **1** | **204** |

---

## Recommended Build Order for iPad & iPhone App

1. **Setup** (8.1) → Project init, dependencies, structure
2. **Shared code** (8.2) → API client, types, auth store
3. **Theme** (8.3) → Colours, typography, spacing, responsive detection
4. **Navigation** (8.4) → Auth flow, tab bar (iPhone), sidebar (iPad)
5. **Auth screens** (8.5.1–8.5.2) → Login, register
6. **Dashboard** (8.5.3) → Stats, recent projects
7. **Projects** (8.5.4–8.5.6) → List, create, detail
8. **Financials + Budget** (8.5.7–8.5.8) → Transactions, budget
9. **Planning** (8.5.10) → Task list (iPhone), Gantt (iPad)
10. **Remaining screens** (8.5.9, 8.5.11–8.5.17) → Cash flow, inspections, documents, tradies, reports, profile, settings
11. **Mobile features** (8.6) → Camera, GPS, push, offline
12. **iPad enhancements** (8.7) → Split view, Gantt chart, multi-column
13. **Build & distribute** (8.8) → Icons, splash, TestFlight, App Store
14. **Testing** (8.9) → Unit, E2E, device matrix

---

*End of Task Breakdown*
