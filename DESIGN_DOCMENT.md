# BuildMate — Construction Management Platform

## Design Document v1.0

**Date:** 30 March 2026
**Status:** Development / MVP
**Platform:** Full-stack web application

---

## Table of Contents

1. [Overview](#1-overview)
2. [Architecture](#2-architecture)
3. [Tech Stack](#3-tech-stack)
4. [Project Structure](#4-project-structure)
5. [Database Schema](#5-database-schema)
6. [Backend API](#6-backend-api)
7. [Frontend Application](#7-frontend-application)
8. [Authentication & Security](#8-authentication--security)
9. [UI Components](#9-ui-components)
10. [Gantt Chart Component](#10-gantt-chart-component)
11. [Environment Setup](#11-environment-setup)
12. [Demo Data Seeding](#12-demo-data-seeding)
13. [Known Issues & Workarounds](#13-known-issues--workarounds)
14. [Future Enhancements](#14-future-enhancements)

---

## 1. Overview

BuildMate is a construction project management platform built for Australian builders. It manages:

- **Projects** — create, track status, contract values, client details
- **Financial Tracking** — income/expense transactions with GST calculation
- **Budget Management** — cost codes, categories, budget vs actuals, variations
- **Cash Flow** — monthly forecast vs actual income/expenses with S-curve charts
- **Planning & Scheduling** — task management with an interactive Gantt chart (zoom in/out)
- **Inspections & Defects** — schedule inspections, log defects with severity tracking
- **Document Management** — upload/download project documents by folder category
- **Tradie Directory** — manage subcontractors with licence/insurance compliance tracking

---

## 2. Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Browser (SPA)                        │
│              React + TypeScript + Vite                  │
│                  Port 5173                              │
├─────────────────────────────────────────────────────────┤
│              Vite Dev Proxy (/api → :3001)              │
├─────────────────────────────────────────────────────────┤
│               Express REST API Server                   │
│            Node.js + TypeScript + tsx                   │
│                  Port 3001                              │
├─────────────────────────────────────────────────────────┤
│              Prisma ORM + SQLite                        │
│            server/prisma/dev.db                         │
└─────────────────────────────────────────────────────────┘
```

**Key architectural decisions:**
- Monorepo with `/server` (backend) and `/web` (frontend) directories
- SQLite for zero-config development (no external DB needed)
- Vite proxy eliminates CORS issues in development
- JWT-based stateless authentication
- All API routes are flat (e.g., `/api/transactions?projectId=X`), not nested

---

## 3. Tech Stack

### Backend (`/server`)

| Technology | Version | Purpose |
|---|---|---|
| Node.js | 24.x | Runtime |
| Express | 4.21 | HTTP framework |
| TypeScript | 5.7 | Type safety |
| Prisma | 6.3+ | ORM & database client |
| SQLite | - | Database (file-based) |
| tsx | 4.19 | TypeScript execution (dev) |
| bcryptjs | 2.4 | Password hashing |
| jsonwebtoken | 9.0 | JWT auth tokens |
| zod | 3.24 | Request validation |
| multer | 1.4 | File upload handling |
| helmet | 8.0 | Security headers |
| morgan | 1.10 | HTTP request logging |
| cors | 2.8 | Cross-origin configuration |
| dotenv | 16.4 | Environment variables |

### Frontend (`/web`)

| Technology | Version | Purpose |
|---|---|---|
| React | 18.3 | UI framework |
| TypeScript | 5.6 | Type safety |
| Vite | 5.4 | Build tool & dev server |
| React Router | 6.27 | Client-side routing |
| TanStack React Query | 5.59 | Server state management |
| Axios | 1.7 | HTTP client |
| Tailwind CSS | 3.4 | Utility-first styling |
| Recharts | 2.13 | Charts (bar, line) |
| Lucide React | 0.451 | Icon library |
| clsx + tailwind-merge | - | Conditional class names |
| date-fns | 4.1 | Date formatting |

---

## 4. Project Structure

```
buildmate/
├── server/                      # Backend API
│   ├── prisma/
│   │   ├── schema.prisma        # Database schema (20 models)
│   │   ├── migrations/          # Database migrations
│   │   └── dev.db               # SQLite database file
│   ├── src/
│   │   ├── index.ts             # Express app entry point
│   │   ├── config/
│   │   │   └── index.ts         # Environment config (port, JWT, CORS, upload)
│   │   ├── middleware/
│   │   │   ├── auth.ts          # JWT authenticate + role authorize middleware
│   │   │   ├── errorHandler.ts  # Global error handler
│   │   │   └── validate.ts      # Zod schema validation middleware
│   │   ├── routes/
│   │   │   ├── auth.ts          # POST /register, /login, /refresh; GET/PUT /me
│   │   │   ├── projects.ts      # CRUD /projects
│   │   │   ├── transactions.ts  # CRUD /transactions
│   │   │   ├── invoices.ts      # CRUD /invoices
│   │   │   ├── budget.ts        # /budget/categories, /budget/variations, /budget/summary
│   │   │   ├── cashflow.ts      # /cashflow/entries, /cashflow/claims, /cashflow/forecast
│   │   │   ├── tasks.ts         # CRUD /tasks + /tasks/:id/dependencies
│   │   │   ├── inspections.ts   # CRUD /inspections + /inspections/defects
│   │   │   ├── documents.ts     # /documents (list, upload, download, delete)
│   │   │   └── tradies.ts       # CRUD /tradies + /tradies/compliance + /tradies/documents
│   │   └── types/
│   │       └── index.ts         # AuthRequest, AuthUser, AppError types
│   ├── uploads/                 # Uploaded document files
│   ├── package.json
│   ├── tsconfig.json
│   └── .env                     # DATABASE_URL, JWT_SECRET, PORT, CORS_ORIGIN
│
├── web/                         # Frontend SPA
│   ├── src/
│   │   ├── main.tsx             # App entry: React Query + Router + AuthProvider
│   │   ├── App.tsx              # Route definitions + ProtectedRoute wrapper
│   │   ├── index.css            # Tailwind imports
│   │   ├── api/                 # API client layer
│   │   │   ├── client.ts        # Axios instance with auth interceptor
│   │   │   ├── auth.ts          # login, register, getMe, updateProfile
│   │   │   ├── projects.ts      # getProjects, getProject, createProject, etc.
│   │   │   ├── transactions.ts  # CRUD (projectId as query param)
│   │   │   ├── budget.ts        # categories, variations, summary
│   │   │   ├── cashflow.ts      # entries, claims
│   │   │   ├── tasks.ts         # CRUD + dependencies
│   │   │   ├── inspections.ts   # inspections + defects
│   │   │   ├── documents.ts     # list, upload, download, delete
│   │   │   └── tradies.ts       # CRUD
│   │   ├── store/
│   │   │   └── authStore.tsx    # AuthContext + AuthProvider (token + user state)
│   │   ├── hooks/
│   │   │   └── useAuth.ts       # useAuth() hook
│   │   ├── types/
│   │   │   └── index.ts         # TypeScript interfaces for all entities
│   │   ├── lib/
│   │   │   └── utils.ts         # cn(), formatCurrency(), formatDate() helpers
│   │   ├── components/
│   │   │   ├── ui/              # Reusable UI components
│   │   │   │   ├── Badge.tsx
│   │   │   │   ├── Button.tsx   # Variants: primary, secondary, danger, ghost
│   │   │   │   ├── Card.tsx
│   │   │   │   ├── EmptyState.tsx
│   │   │   │   ├── Input.tsx    # With label + error support
│   │   │   │   ├── LoadingSpinner.tsx
│   │   │   │   ├── Modal.tsx    # Overlay modal with title + close
│   │   │   │   └── Table.tsx
│   │   │   ├── layout/
│   │   │   │   ├── AppLayout.tsx    # Sidebar + Header + Outlet
│   │   │   │   ├── Sidebar.tsx      # Desktop navigation
│   │   │   │   ├── Header.tsx       # Top bar
│   │   │   │   └── BottomNav.tsx    # Mobile navigation
│   │   │   └── GanttChart.tsx       # Interactive Gantt chart component
│   │   └── pages/
│   │       ├── Login.tsx
│   │       ├── Dashboard.tsx
│   │       ├── projects/
│   │       │   ├── ProjectsPage.tsx      # List + create modal
│   │       │   └── ProjectDetailPage.tsx # Tabs → sub-pages
│   │       ├── financials/
│   │       │   └── FinancialsPage.tsx    # Transactions + create modal
│   │       ├── budget/
│   │       │   └── BudgetPage.tsx        # Categories + variations + chart
│   │       ├── cashflow/
│   │       │   └── CashFlowPage.tsx      # S-curve line chart
│   │       ├── planning/
│   │       │   └── PlanningPage.tsx       # Gantt + list view + zoom + create
│   │       ├── inspections/
│   │       │   └── InspectionsPage.tsx   # Inspections + defects + create modals
│   │       ├── documents/
│   │       │   └── DocumentsPage.tsx     # Upload, drag-drop, download, delete
│   │       ├── tradies/
│   │       │   └── TradiesPage.tsx       # Directory + compliance + create
│   │       └── reports/
│   │           └── ReportsPage.tsx
│   ├── vite.config.ts           # React plugin + path alias + API proxy
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   ├── tsconfig.json
│   └── package.json
│
└── DESIGN_DOCUMENT.md           # This file
```

---

## 5. Database Schema

### Entity Relationship Summary

The database has **20 models** defined in `server/prisma/schema.prisma`:

| Model | Description | Key Relationships |
|---|---|---|
| **User** | System users (builders, admins) | Creates projects, tasks, transactions |
| **Project** | Construction project | Has transactions, tasks, budget, inspections, documents |
| **ProjectTeam** | Users assigned to projects | User ↔ Project (many-to-many) |
| **Transaction** | Income/expense records | Belongs to Project, optional Tradie |
| **Invoice** | Tax invoices & progress claims | Belongs to Project, has LineItems |
| **InvoiceLineItem** | Line items on invoices | Belongs to Invoice |
| **BudgetCategory** | Cost code categories | Self-referencing tree (parent/children) |
| **Variation** | Budget change orders | Belongs to Project + optional BudgetCategory |
| **CashFlowEntry** | Monthly forecast/actual cashflow | Belongs to Project |
| **ClaimSchedule** | Progress claim schedule | Belongs to Project |
| **Tradie** | Subcontractor directory | Has documents, project assignments, transactions |
| **TradieDocument** | Licence/insurance documents | Belongs to Tradie |
| **TradieProjectAssignment** | Tradie ↔ Project assignments | Many-to-many with rates |
| **Task** | Project tasks/schedule items | Self-referencing tree, has dependencies |
| **TaskDependency** | Task predecessor/successor links | Predecessor ↔ Successor |
| **DelayLog** | Delay tracking entries | Belongs to Project + optional Task |
| **Inspection** | Building inspections | Belongs to Project, has items + defects |
| **InspectionItem** | Checklist items per inspection | Belongs to Inspection |
| **Defect** | Defect records | Belongs to Project + optional Inspection |
| **Document** | Project documents/files | Belongs to Project, stored in folders |

### Key Enum Values (stored as strings in SQLite)

**Project Status:** `NOT_STARTED`, `IN_PROGRESS`, `ON_HOLD`, `COMPLETE`, `CANCELLED`

**Transaction Type:** `INCOME`, `EXPENSE`

**Transaction Category:** `PROGRESS_CLAIM`, `SUBCONTRACTOR`, `MATERIALS`, `PLANT_HIRE`, `LABOUR`, `PERMIT_FEE`, `INSURANCE`, `OVERHEAD`, `OTHER`

**Transaction Status:** `DRAFT`, `SUBMITTED`, `APPROVED`, `PAID`, `OVERDUE`, `CANCELLED`

**Task Status:** `NOT_STARTED`, `IN_PROGRESS`, `COMPLETE`, `ON_HOLD`, `DELAYED`

**Task Priority:** `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`

**Inspection Type:** `PRE_SLAB`, `FRAME`, `LOCK_UP`, `WATERPROOFING`, `FINAL`, `DEFECT_RECTIFICATION`, `SAFETY`, `CUSTOM`

**Defect Severity:** `MINOR`, `MODERATE`, `MAJOR`, `CRITICAL`

**Document Folder:** `PLANS`, `CONTRACTS`, `PERMITS`, `SWMS`, `CERTIFICATES`, `INSURANCE`, `PHOTOS`, `REPORTS`, `CORRESPONDENCE`, `OTHER`

**Trade Types:** `BUILDER`, `CARPENTER`, `ELECTRICIAN`, `PLUMBER`, `PLASTERER`, `PAINTER`, `TILER`, `BRICKLAYER`, `CONCRETER`, `ROOFER`, `LANDSCAPER`, `DEMOLITION`, `EXCAVATION`, `STEEL_FIXER`, `CABINET_MAKER`, `GLAZIER`, `RENDERER`, `WATERPROOFER`, `INSULATION`, `FENCER`, `SCAFFOLDER`, `CLEANER`, `HVAC`, `FIRE_PROTECTION`, `SECURITY_SYSTEMS`, `FLOORING`, `STONEMASON`, `POOL_BUILDER`, `SURVEYOR`, `ENGINEER`, `ARCHITECT`, `DRAFTSPERSON`, `OTHER`

**Variation Status:** `DRAFT`, `SUBMITTED`, `APPROVED`, `REJECTED`

**User Role:** `ADMIN`, `PROJECT_MANAGER`, `SITE_SUPERVISOR`, `ACCOUNTS`, `TRADIE`

---

## 6. Backend API

### 6.1 Server Configuration

**Entry point:** `server/src/index.ts`

```typescript
// Middleware stack (in order):
1. helmet()              // Security headers
2. cors({ origin })      // CORS from config
3. morgan('dev')         // Request logging
4. express.json()        // JSON body parsing (10mb limit)
5. express.urlencoded()  // URL-encoded body parsing
6. Static: /uploads      // Serve uploaded files
```

**Environment variables (`.env`):**

```env
DATABASE_URL="file:./dev.db"
JWT_SECRET="your-secret-key"
JWT_REFRESH_SECRET="your-refresh-secret"
PORT=3001
CORS_ORIGIN="http://localhost:5173"
```

### 6.2 API Endpoints

All routes require JWT authentication (via `Authorization: Bearer <token>`) except login/register.

#### Authentication (`/api/auth`)

| Method | Path | Body | Response | Description |
|---|---|---|---|---|
| POST | `/auth/register` | `{email, password, name, phone?, role?}` | `{user, accessToken, refreshToken}` | Register new user |
| POST | `/auth/login` | `{email, password}` | `{user, accessToken, refreshToken}` | Login |
| POST | `/auth/refresh` | `{refreshToken}` | `{accessToken, refreshToken}` | Refresh tokens |
| GET | `/auth/me` | - | `User` | Get current user |
| PUT | `/auth/me` | `{name?, phone?, email?}` | `User` | Update profile |

#### Projects (`/api/projects`)

| Method | Path | Params/Body | Response | Description |
|---|---|---|---|---|
| GET | `/projects` | `?page, limit, search, status` | `{data: Project[], pagination}` | List projects (paginated) |
| POST | `/projects` | `{name, address?, clientName?, contractValue?, status?, ...}` | `Project` | Create project |
| GET | `/projects/:id` | - | `Project` with financial summary | Get single project |
| PUT | `/projects/:id` | Partial project fields | `Project` | Update project |
| DELETE | `/projects/:id` | - | `{message}` | Delete project |

#### Transactions (`/api/transactions`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/transactions` | `?projectId, type, status, category, page, limit` | `{data: Transaction[], pagination}` |
| POST | `/transactions` | `{projectId, type, category, description, amount, gstAmount, totalAmount, date, status?, ...}` | `Transaction` |
| GET | `/transactions/:id` | - | `Transaction` |
| PUT | `/transactions/:id` | Partial fields | `Transaction` |
| DELETE | `/transactions/:id` | - | `{message}` |

#### Budget (`/api/budget`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/budget/categories` | `?projectId` (required) | `BudgetCategory[]` |
| POST | `/budget/categories` | `{projectId, costCode, name, budgetAmount, ...}` | `BudgetCategory` |
| PUT | `/budget/categories/:id` | Partial fields | `BudgetCategory` |
| DELETE | `/budget/categories/:id` | - | `{message}` |
| GET | `/budget/variations` | `?projectId` (required) | `Variation[]` |
| POST | `/budget/variations` | `{projectId, variationNumber, title, amount, ...}` | `Variation` |
| PUT | `/budget/variations/:id` | Partial fields | `Variation` |
| DELETE | `/budget/variations/:id` | - | `{message}` |
| GET | `/budget/summary/:projectId` | - | `{categories, summary}` |

#### Cash Flow (`/api/cashflow`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/cashflow/entries` | `?projectId` (required), `?type` | `CashFlowEntry[]` |
| POST | `/cashflow/entries` | `{projectId, month, type, incomeAmount, expenseAmount, notes?}` | `CashFlowEntry` |
| PUT | `/cashflow/entries/:id` | Partial fields | `CashFlowEntry` |
| DELETE | `/cashflow/entries/:id` | - | `{message}` |
| GET | `/cashflow/claims` | `?projectId` (required) | `ClaimSchedule[]` |
| POST | `/cashflow/forecast/:projectId` | - | Generated forecast entries |

#### Tasks (`/api/tasks`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/tasks` | `?projectId, status` | `Task[]` |
| POST | `/tasks` | `{projectId, name, description?, startDate?, endDate?, status?, priority?, isMilestone?}` | `Task` |
| GET | `/tasks/:id` | - | `Task` with dependencies |
| PUT | `/tasks/:id` | Partial fields | `Task` |
| DELETE | `/tasks/:id` | - | `{message}` |
| PUT | `/tasks/:id/progress` | `{percentComplete}` | `Task` |

#### Inspections (`/api/inspections`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/inspections` | `?projectId, status, page, limit` | `{data: Inspection[], pagination}` |
| POST | `/inspections` | `{projectId, type, title, scheduledDate, inspectorName?, notes?, items?}` | `Inspection` |
| GET | `/inspections/:id` | - | `Inspection` with items + defects |
| PUT | `/inspections/:id` | Partial fields | `Inspection` |
| DELETE | `/inspections/:id` | - | `{message}` |
| GET | `/inspections/defects/list` | `?projectId, status, severity, page, limit` | `{data: Defect[], pagination}` |
| POST | `/inspections/defects` | `{projectId, title, severity, description?, location?, dueDate?}` | `Defect` |
| PUT | `/inspections/defects/:id` | Partial fields | `Defect` |
| DELETE | `/inspections/defects/:id` | - | `{message}` |

#### Documents (`/api/documents`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/documents` | `?projectId, folder, search, page, limit` | `{data: Document[], pagination}` |
| POST | `/documents/upload` | FormData: `file` + `{projectId, folder, name, description?}` | `Document` |
| GET | `/documents/:id` | - | `Document` |
| GET | `/documents/:id/download` | - | File download |
| PUT | `/documents/:id` | `{folder?, name?, description?}` | `Document` |
| DELETE | `/documents/:id` | - | `{message}` (also deletes file from disk) |

#### Tradies (`/api/tradies`)

| Method | Path | Params/Body | Response |
|---|---|---|---|
| GET | `/tradies` | `?search, trade, status, page, limit` | `{data: Tradie[], pagination}` |
| POST | `/tradies` | `{companyName, contactName, trade, email?, phone?, abn?, ...}` | `Tradie` |
| GET | `/tradies/:id` | - | `Tradie` with documents + assignments |
| PUT | `/tradies/:id` | Partial fields | `Tradie` |
| DELETE | `/tradies/:id` | - | Soft delete (sets status to INACTIVE) |
| GET | `/tradies/compliance` | `?days` | Expiring licences/insurance report |
| POST | `/tradies/documents` | `{tradieId, type, name, fileUrl, expiryDate?}` | `TradieDocument` |
| DELETE | `/tradies/documents/:id` | - | `{message}` |

### 6.3 Response Formats

**Paginated responses** (projects, transactions, inspections, documents, tradies):
```json
{
  "data": [...],
  "pagination": { "page": 1, "limit": 20, "total": 42, "pages": 3 }
}
```

**Flat array responses** (tasks, budget categories, budget variations, cashflow entries):
```json
[...]
```

### 6.4 Validation

All POST/PUT endpoints use **Zod schemas** validated via middleware. Validation errors return:
```json
{
  "error": "Validation failed",
  "details": [
    { "field": "name", "message": "Required" }
  ]
}
```

### 6.5 Error Handling

Custom `AppError` class with HTTP status codes. Global error handler returns:
```json
{
  "error": "Error message",
  "status": 404
}
```

---

## 7. Frontend Application

### 7.1 Routing

| Route | Component | Auth | Description |
|---|---|---|---|
| `/login` | `LoginPage` | No | Login form |
| `/` | `DashboardPage` | Yes | Project stats overview |
| `/projects` | `ProjectsPage` | Yes | Project grid + create |
| `/projects/:id` | `ProjectDetailPage` | Yes | Project detail with tab navigation |
| `/projects/:id/budget` | `BudgetPage` | Yes | Budget categories + chart + variations |
| `/projects/:id/cashflow` | `CashFlowPage` | Yes | Cash flow S-curve chart |
| `/projects/:id/planning` | `PlanningPage` | Yes | Gantt chart + task list |
| `/projects/:id/financials` | `FinancialsPage` | Yes | Transactions list |
| `/projects/:id/inspections` | `InspectionsPage` | Yes | Inspections + defects |
| `/projects/:id/documents` | `DocumentsPage` | Yes | Document upload/download |
| `/tradies` | `TradiesPage` | Yes | Tradie directory |
| `/reports` | `ReportsPage` | Yes | Reports (placeholder) |

### 7.2 State Management

- **Server state:** TanStack React Query with 5-minute stale time, 1 retry
- **Auth state:** React Context (`AuthProvider`) with `token` in localStorage
- **Local state:** `useState` for form data, filters, modals, view toggles

### 7.3 API Client Layer

**`src/api/client.ts`** — Axios instance:
- Base URL: `/api` (proxied to `:3001` by Vite)
- Request interceptor: Attaches `Authorization: Bearer <token>` from localStorage
- Response interceptor: On 401 (not login), clears localStorage token

**Important pattern — paginated response unwrapping:**
```typescript
// API returns { data: [], pagination: {} } but frontend expects []
const { data } = await client.get('/projects', { params });
return Array.isArray(data) ? data : data.data ?? [];
```

**Auth response mapping** (`src/api/auth.ts`):
The server returns `{ accessToken, user: { name, role: "ADMIN" } }` but the frontend expects `{ token, user: { firstName, lastName, role: "admin" } }`. The `mapUser()` function handles this translation.

### 7.4 Create Forms (Modal Pattern)

Every page with a "New/Add" button follows the same pattern:

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

// In JSX:
<Button onClick={() => setShowCreate(true)}>New</Button>
<Modal open={showCreate} onClose={() => setShowCreate(false)} title="Create">
  <form onSubmit={handleSubmit}>
    {/* form fields */}
    <Button type="submit" disabled={createMutation.isPending}>Save</Button>
  </form>
</Modal>
```

---

## 8. Authentication & Security

### 8.1 JWT Flow

1. **Login/Register** → Server returns `accessToken` (24h) + `refreshToken` (7d)
2. **Frontend** stores `accessToken` in `localStorage` as `buildmate_token`
3. **Every API request** attaches `Authorization: Bearer <token>` header via Axios interceptor
4. **Server middleware** (`authenticate`) verifies JWT, extracts `{ id, email, role }` into `req.user`
5. **On 401** response, frontend clears stored token (React auth context handles redirect to `/login`)

### 8.2 Password Security

- Passwords hashed with **bcryptjs**, 12 salt rounds
- Minimum 8 characters (validated by Zod)

### 8.3 Security Headers

- **Helmet** middleware applies standard security headers
- File uploads limited to **10MB** via Multer

---

## 9. UI Components

### Reusable Components (`src/components/ui/`)

| Component | Props | Description |
|---|---|---|
| **Button** | `variant` (primary/secondary/danger/ghost), `size` (sm/md/lg), `disabled` | Styled button with focus ring |
| **Input** | `label`, `error`, standard HTML input props | Form input with label + error display |
| **Modal** | `open`, `onClose`, `title`, `children` | Overlay modal, locks body scroll |
| **Card** | `title?`, `action?`, `children`, `className?` | Content card with optional header |
| **Badge** | `status` | Color-coded status pill |
| **Table** | `headers[]`, `children` (table rows) | Responsive table with header row |
| **EmptyState** | `icon`, `title`, `description`, `action?` | Placeholder for empty data states |
| **LoadingSpinner** | `size` (sm/md/lg), `className?` | Animated loading indicator |

### Layout Components

| Component | Description |
|---|---|
| **AppLayout** | Desktop sidebar + header + `<Outlet />` for page content |
| **Sidebar** | Navigation links: Dashboard, Projects, Tradies, Reports |
| **Header** | Top bar with mobile menu toggle + user info |
| **BottomNav** | Mobile bottom navigation bar |

---

## 10. Gantt Chart Component

**File:** `src/components/GanttChart.tsx`

### Props

```typescript
interface GanttChartProps {
  tasks: GanttTask[];  // Array of tasks with startDate, endDate, etc.
  dayWidth?: number;   // Pixels per day (controls zoom level, default: 28)
}
```

### Features

- **Timeline rendering:** Automatically calculates date range from task data with 1-week padding
- **Week headers:** Shows date labels for each week
- **Task bars:** Color-coded by status (green=complete, blue=in-progress, gray=not started, amber=on hold, red=blocked)
- **Progress fill:** Shows percentage completion within the task bar
- **Milestones:** Displayed as purple diamond markers
- **Today line:** Red vertical line marking current date
- **Weekend shading:** Light gray background for Saturday/Sunday columns
- **Hover tooltips:** Shows task name, date range, duration, and progress percentage
- **Fixed task labels:** Left panel stays fixed while timeline scrolls horizontally
- **Legend bar:** Shows color key for all statuses + milestone marker

### Zoom Control (in PlanningPage)

The zoom is controlled by the parent `PlanningPage`, which passes `dayWidth` prop:

```typescript
const ZOOM_LEVELS = [8, 14, 20, 28, 40, 56, 80]; // pixels per day
const [zoomIdx, setZoomIdx] = useState(3);         // default = 28px

// ZoomOut and ZoomIn buttons using lucide-react icons
<GanttChart tasks={tasks} dayWidth={ZOOM_LEVELS[zoomIdx]} />
```

**Zoom buttons** are placed in the page header next to the Gantt/List view toggle, using `ZoomIn` and `ZoomOut` icons from lucide-react.

---

## 11. Environment Setup

### Prerequisites

- Node.js 18+ (tested on 24.x)
- npm

### Installation

```bash
# Clone the repository
# Install backend dependencies
cd server
npm install
npx prisma generate
npx prisma migrate dev

# Install frontend dependencies
cd ../web
npm install
```

### Running Development Servers

**Terminal 1 — Backend:**
```bash
cd server
npm run dev
# → BuildMate API running on port 3001
```

**Terminal 2 — Frontend:**
```bash
cd web
npm run dev
# → Vite ready at http://localhost:5173
```

### Default Credentials

Register the first admin user via API or the UI Register link:
```bash
curl -X POST http://localhost:3001/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@buildmate.com","password":"BuildMate2026!","name":"Admin","role":"ADMIN"}'
```

- **Email:** `admin@buildmate.com`
- **Password:** `BuildMate2026!`

### Vite Proxy Configuration

`web/vite.config.ts`:
```typescript
export default defineConfig({
  plugins: [react()],
  resolve: { alias: { '@': path.resolve(__dirname, './src') } },
  server: {
    proxy: {
      '/api': { target: 'http://localhost:3001', changeOrigin: true },
    },
  },
});
```

---

## 12. Demo Data Seeding

To populate the app with realistic construction project data, run the following after registering a user and creating a project:

### Tasks (16 items — full house build schedule)

```
Site Preparation      | 01 Apr – 14 Apr | COMPLETE     | 100%
Foundation & Slab     | 10 Apr – 28 Apr | COMPLETE     | 100%
Frame Erection        | 28 Apr – 19 May | IN_PROGRESS  | 65%
Roof & Trusses        | 12 May – 26 May | IN_PROGRESS  | 30%
Rough-in Plumbing     | 19 May – 02 Jun | NOT_STARTED  | 0%
Rough-in Electrical   | 19 May – 02 Jun | NOT_STARTED  | 0%
Brickwork             | 26 May – 16 Jun | NOT_STARTED  | 0%
Lock-up Stage ◆       | 16 Jun          | MILESTONE    | 0%
Internal Linings      | 16 Jun – 07 Jul | NOT_STARTED  | 0%
Waterproofing         | 23 Jun – 30 Jun | NOT_STARTED  | 0%
Tiling & Flooring     | 07 Jul – 21 Jul | NOT_STARTED  | 0%
Kitchen & Cabinetry   | 14 Jul – 28 Jul | NOT_STARTED  | 0%
Painting              | 21 Jul – 04 Aug | NOT_STARTED  | 0%
Fix-out P&E           | 04 Aug – 18 Aug | NOT_STARTED  | 0%
Final Inspection ◆    | 18 Aug          | MILESTONE    | 0%
Handover ◆            | 25 Aug          | MILESTONE    | 0%
```

### Cash Flow (6 months)

```
Apr 2026 ACTUAL   | Income: $120,000 | Expense: $45,000
May 2026 ACTUAL   | Income: $0       | Expense: $72,000
Jun 2026 FORECAST | Income: $90,000  | Expense: $85,000
Jul 2026 FORECAST | Income: $90,000  | Expense: $78,000
Aug 2026 FORECAST | Income: $90,000  | Expense: $65,000
Sep 2026 FORECAST | Income: $60,000  | Expense: $55,000
```

### Transactions (8 items)

```
Client deposit             | INCOME  | $120,000 | PAID
Concrete & steel           | EXPENSE | $28,000  | PAID
Earthworks - site prep     | EXPENSE | $17,000  | PAID
Timber framing package     | EXPENSE | $42,000  | PAID
Frame erection labour      | EXPENSE | $18,000  | PAID
Roof trusses & tiles       | EXPENSE | $22,000  | APPROVED
Plumbing rough-in          | EXPENSE | $8,500   | DRAFT
Electrical rough-in        | EXPENSE | $7,500   | DRAFT
```

### Budget Categories (12 cost codes)

```
SIT-01 Site Works        | $35,000
FND-02 Foundation        | $55,000
FRM-03 Framing           | $65,000
ROF-04 Roofing           | $28,000
PLB-05 Plumbing          | $32,000
ELC-06 Electrical        | $28,000
BRK-07 Brickwork         | $40,000
INT-08 Internal Linings  | $25,000
TIL-09 Tiling & Flooring | $22,000
KIT-10 Kitchen & Cabinetry | $35,000
PNT-11 Painting          | $15,000
FIX-12 Fix-out & Finishing | $20,000
Total: $400,000
```

---

## 13. Known Issues & Workarounds

### 13.1 Frontend/Backend Field Name Mismatches

The frontend TypeScript types don't perfectly match the server responses:

| Area | Frontend Expects | Server Returns | Workaround |
|---|---|---|---|
| User | `firstName`, `lastName` | `name` | `mapUser()` in `auth.ts` splits name |
| User role | lowercase `admin` | uppercase `ADMIN` | `mapUser()` lowercases |
| Auth response | `token` | `accessToken` | `auth.ts` maps `accessToken → token` |
| Task progress | `progress` | `percentComplete` | GanttChart uses `percentComplete` |

### 13.2 Paginated Response Unwrapping

Some API endpoints return `{data: [], pagination: {}}` while the frontend expects flat arrays. Each API client function handles this with:
```typescript
return Array.isArray(data) ? data : data.data ?? [];
```

### 13.3 WSL/Windows Filesystem Performance

When running on WSL with code on `/mnt/c/` (Windows filesystem):
- `tsx watch` is very slow (~60 seconds startup)
- Vite HMR file watching may not trigger
- **Recommendation:** Copy project to native Linux filesystem (`~/buildmate`) for better performance

---

## 14. Future Enhancements

- [ ] Task drag-and-drop reordering in Gantt chart
- [ ] Task dependency arrows in Gantt chart
- [ ] Critical path calculation
- [ ] PDF report generation (inspections, invoices)
- [ ] Email notifications for expiring tradie licences
- [ ] Multi-project dashboard with portfolio view
- [ ] Photo capture for inspections (mobile)
- [ ] Role-based access control (currently all authenticated users have full access)
- [ ] Seed script for demo data (`prisma/seed.ts`)
- [ ] Production deployment configuration (Docker, PostgreSQL)
- [ ] Offline support with service worker
