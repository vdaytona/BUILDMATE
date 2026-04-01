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

- [x] Task drag-and-drop reordering in Gantt chart (row reorder via GripVertical drag handle + POST `/tasks/reorder`)
- [x] Task dependency arrows in Gantt chart (SVG arrows, FS/SS/FF/SF types)
- [x] Critical path calculation (GET `/tasks/critical-path`, highlighted in Gantt)
- [x] Horizontal panning on Gantt timeline (mouse drag on empty space to scroll)
- [x] Gantt drag-to-reschedule fix (optimistic cache updates prevent snap-back; click vs drag resolved via mouseup-based detection — no `onClick` on bars)
- [x] Construction stage column on Planning page (12 stages: Initiation → Defect Liability)
- [x] Day-of-week labels in Gantt header (single letter M/T/W at default zoom, full Mon/Tue/Wed when zoomed in)
- [x] Document page fixes (category↔folder mapping, `uploadedBy` normalisation, null `mimeType` handling, `/uploads` Vite proxy, SQLite search fix)
- [x] Seed script for demo data (`prisma/seed.ts`)
- [x] Production deployment configuration (Docker, PostgreSQL)
- [ ] PDF report generation (inspections, invoices)
- [ ] Email notifications for expiring tradie licences
- [ ] Multi-project dashboard with portfolio view
- [ ] Photo capture for inspections (mobile)
- [ ] Role-based access control (currently all authenticated users have full access)
- [ ] Offline support with service worker

---

## 15. UI/UX Design System

This section provides the complete visual design specification so that any team can **exactly replicate** the look and feel of the BuildMate web application.

---

### 15.1 Design Philosophy

- **Clean, professional, and functional** — designed for tradies and builders who need quick, no-nonsense access to project data
- **Mobile-first responsive** — every page works on phone, tablet, and desktop
- **Consistent card-based layout** — content is grouped into white rounded cards on a light-gray background
- **Minimal decoration** — no gradients, no heavy shadows, no decorative images. Let data breathe.

---

### 15.2 Colour Palette

All colours are defined as **custom Tailwind colours** in `tailwind.config.js`:

| Token | Hex (DEFAULT) | Shades Defined | Usage |
|---|---|---|---|
| `primary` | `#1E40AF` | 50 `#EFF6FF`, 100 `#DBEAFE`, 200 `#BFDBFE`, 300 `#93C5FD`, 400 `#60A5FA`, 500 `#3B82F6`, 600 `#1E40AF`, 700 `#1E3A8A`, 800 `#1E3380`, 900 `#1E2D70` | Buttons, links, active nav, branding, focus rings, progress bars |
| `success` | `#059669` | 50 `#ECFDF5`, 100 `#D1FAE5`, 500 `#059669`, 600 `#047857` | Paid, complete, positive values, income amounts |
| `warning` | `#D97706` | 50 `#FFFBEB`, 100 `#FEF3C7`, 500 `#D97706`, 600 `#B45309` | Pending, on-hold, expiring, in-progress badges |
| `danger` | `#DC2626` | 50 `#FEF2F2`, 100 `#FEE2E2`, 500 `#DC2626`, 600 `#B91C1C` | Overdue, failed, over-budget, expense amounts, delete actions |

**Tailwind built-in colours also used:**

| Colour | Usage |
|---|---|
| `gray-50` (`#F9FAFB`) | Page background (`<body>`, `<main>`) |
| `gray-100` (`#F3F4F6`) | Tab bar background, secondary button hover |
| `gray-200` (`#E5E7EB`) | Card borders, table borders, dividers |
| `gray-300` (`#D1D5DB`) | Input borders, checkbox borders |
| `gray-400` (`#9CA3AF`) | Placeholder text, icons in empty states |
| `gray-500` (`#6B7280`) | Secondary text, labels, metadata |
| `gray-600` (`#4B5563`) | Body text (secondary) |
| `gray-700` (`#374151`) | Body text (primary in secondary buttons) |
| `gray-900` (`#111827`) | Headings, card titles, primary text |
| `blue-50` / `blue-600` | Scheduled/open badges, info highlights |
| `orange-50` / `orange-600` | High priority badges |
| `purple-600` | Milestone markers in Gantt chart |
| `emerald-500` / `emerald-100` | Gantt bar: COMPLETE status |
| `blue-500` / `blue-100` | Gantt bar: IN_PROGRESS status |
| `amber-500` / `amber-100` | Gantt bar: ON_HOLD status |
| `red-500` / `red-100` | Gantt bar: BLOCKED status |

**Theme colour (browser):** `#1E40AF` (set via `<meta name="theme-color">` in `index.html`)

---

### 15.3 Typography

Tailwind's default font stack is used (no custom fonts):

```
font-family: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
```

| Element | Tailwind Classes | Size | Weight |
|---|---|---|---|
| Page heading | `text-xl font-bold text-gray-900` | 20px | 700 (bold) |
| Card title | `text-sm font-semibold text-gray-900` | 14px | 600 (semibold) |
| Modal title | `text-lg font-semibold` | 18px | 600 |
| Body text | `text-sm text-gray-900` | 14px | 400 (normal) |
| Secondary text | `text-sm text-gray-500` | 14px | 400 |
| Small label | `text-xs text-gray-500` | 12px | 400 |
| Table header | `text-xs font-medium uppercase tracking-wider text-gray-500` | 12px | 500 + uppercase + tracking |
| Form label | `text-sm font-medium text-gray-700` | 14px | 500 |
| Button text | `text-sm font-medium` | 14px | 500 |
| Nav link | `text-sm font-medium` | 14px | 500 |
| Badge text | `text-xs font-medium capitalize` | 12px | 500 + capitalize |
| Stat value | `text-xl font-bold text-gray-900` or `text-2xl font-bold` | 20–24px | 700 |
| Chart tick label | `fontSize: 12` (Recharts prop) | 12px | 400 |
| Gantt task label | `text-xs font-medium text-gray-800` | 12px | 500 |
| Gantt week header | `text-[10px] font-medium text-gray-500` | 10px | 500 |
| Branding "BuildMate" | `text-lg font-bold text-primary` | 18px | 700 |

**Antialiasing:** `antialiased` class on `<body>` for smoother rendering.

---

### 15.4 Spacing & Layout System

#### Global Layout

```
┌──────────────────────────────────────────────────────┐
│ Sidebar (w-64, hidden on mobile)                     │
│ ┌──────────────────────────────────────────────────┐ │
│ │ Logo bar: h-16, px-6, border-b                   │ │
│ │ Nav items: px-3 py-4, gap-y-1                    │ │
│ └──────────────────────────────────────────────────┘ │
│                                                      │
│ Main area: md:pl-64                                  │
│ ┌──────────────────────────────────────────────────┐ │
│ │ Header: h-16, sticky top-0, px-4 sm:px-6        │ │
│ ├──────────────────────────────────────────────────┤ │
│ │ Content: p-4 sm:p-6, pb-20 (mobile) md:pb-6     │ │
│ └──────────────────────────────────────────────────┘ │
│                                                      │
│ Bottom Nav (mobile only): fixed bottom-0, md:hidden  │
└──────────────────────────────────────────────────────┘
```

#### Breakpoints (Tailwind defaults)

| Prefix | Min Width | Layout Behaviour |
|---|---|---|
| (none) | 0px | Single column, bottom nav visible, sidebar hidden |
| `sm` | 640px | Slightly wider padding (`p-6`), 2-column grids |
| `md` | 768px | Sidebar visible, bottom nav hidden, multi-column grids |
| `lg` | 1024px | 3–4 column grids |

#### Spacing Constants

| Use Case | Value |
|---|---|
| Page section vertical gap | `space-y-6` (24px) |
| Card grid gap | `gap-4` (16px) |
| Card internal padding | `p-4 sm:p-6` (16px / 24px) |
| Card header padding | `px-4 py-3 sm:px-6` |
| Form field vertical gap | `space-y-4` (16px) |
| Button icon gap | `gap-2` (8px) |
| Nav item gap | `space-y-1` (4px) |
| Nav item padding | `px-3 py-2.5` |
| Table cell padding | `px-4 py-3` |
| Modal padding | `p-6` |
| Modal max width | `max-w-lg` (512px) |

---

### 15.5 Component Design Specifications

#### Button (`components/ui/Button.tsx`)

| Property | Value |
|---|---|
| Base classes | `inline-flex items-center justify-center gap-2 rounded-lg font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none` |
| Border radius | `rounded-lg` (8px) |
| Focus | `focus:ring-2 focus:ring-offset-2` with variant colour |

| Variant | Background | Text | Hover | Focus Ring |
|---|---|---|---|---|
| `primary` | `bg-primary` (#1E40AF) | `text-white` | `hover:bg-primary-700` | `focus:ring-primary-500` |
| `secondary` | `bg-gray-100` | `text-gray-700` | `hover:bg-gray-200` | `focus:ring-gray-400` |
| `danger` | `bg-danger` (#DC2626) | `text-white` | `hover:bg-danger-600` | `focus:ring-danger-500` |
| `ghost` | `bg-transparent` | `text-gray-700` | `hover:bg-gray-100` | `focus:ring-gray-400` |

| Size | Padding | Font |
|---|---|---|
| `sm` | `px-3 py-1.5` | `text-sm` (14px) |
| `md` (default) | `px-4 py-2` | `text-sm` (14px) |
| `lg` | `px-6 py-3` | `text-base` (16px) |

---

#### Input (`components/ui/Input.tsx`)

| Property | Value |
|---|---|
| Container | `w-full` |
| Label | `mb-1 block text-sm font-medium text-gray-700` |
| Input | `block w-full rounded-lg border px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400` |
| Border (normal) | `border-gray-300` |
| Border (error) | `border-danger` |
| Focus | `focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary` |
| Error text | `mt-1 text-xs text-danger` |

**Select elements** follow the same styling (manually applied):
```
block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary
```

**Textarea** uses the same classes as Input, with `rows={3}` default.

---

#### Card (`components/ui/Card.tsx`)

| Property | Value |
|---|---|
| Container | `rounded-xl border border-gray-200 bg-white shadow-sm` |
| Border radius | `rounded-xl` (12px) |
| Shadow | `shadow-sm` |
| Header (if title/action) | `flex items-center justify-between border-b border-gray-100 px-4 py-3 sm:px-6` |
| Title | `text-sm font-semibold text-gray-900` |
| Body | `p-4 sm:p-6` |

---

#### Modal (`components/ui/Modal.tsx`)

| Property | Value |
|---|---|
| Overlay | `fixed inset-0 z-50 bg-black/50` |
| Container | `relative z-10 w-full max-w-lg rounded-xl bg-white shadow-xl` |
| Header | `flex items-center justify-between border-b px-6 py-4` |
| Title | `text-lg font-semibold` |
| Close button | `rounded-lg p-1 hover:bg-gray-100`, X icon `h-5 w-5` |
| Body | `p-6` |
| Behaviour | Locks body scroll when open (`document.body.style.overflow = 'hidden'`) |

---

#### Badge (`components/ui/Badge.tsx`)

| Property | Value |
|---|---|
| Shape | `rounded-full px-2.5 py-0.5 text-xs font-medium capitalize` |
| Text transform | `capitalize`, underscores replaced with spaces |

| Status | Background | Text |
|---|---|---|
| active, paid, approved, passed, resolved | `bg-success-50` | `text-success-500` |
| complete, open, scheduled, submitted, sent, upcoming | `bg-blue-50` | `text-blue-600` |
| planning, closed, not_started, draft, low | `bg-gray-100` | `text-gray-600` |
| on_hold, pending, in_progress, conditional, medium | `bg-warning-50` | `text-warning-500` |
| cancelled, rejected, overdue, failed, critical, blocked | `bg-danger-50` | `text-danger-500` |
| high | `bg-orange-50` | `text-orange-600` |
| (fallback) | `bg-gray-100` | `text-gray-600` |

---

#### Table (`components/ui/Table.tsx`)

| Property | Value |
|---|---|
| Container | `overflow-x-auto rounded-lg border border-gray-200` |
| Table | `min-w-full divide-y divide-gray-200 text-sm` |
| Header row | `bg-gray-50` |
| Header cell | `whitespace-nowrap px-4 py-3 text-left text-xs font-medium uppercase tracking-wider text-gray-500` |
| Body | `divide-y divide-gray-100 bg-white` |
| Body cell (text) | `px-4 py-3 text-sm` |
| Body cell (number) | `whitespace-nowrap px-4 py-3 text-sm font-medium` |

---

#### EmptyState (`components/ui/EmptyState.tsx`)

| Property | Value |
|---|---|
| Container | `flex flex-col items-center justify-center py-12 text-center` |
| Icon | `mb-4 text-gray-400`, typically `h-12 w-12` |
| Title | `text-sm font-semibold text-gray-900` |
| Description | `mt-1 text-sm text-gray-500` |
| Action | `mt-4` (slot for a Button) |

---

#### LoadingSpinner (`components/ui/LoadingSpinner.tsx`)

| Property | Value |
|---|---|
| Container | `flex items-center justify-center` |
| Spinner | `animate-spin rounded-full border-2 border-gray-300 border-t-primary` |
| Size sm | `h-4 w-4` |
| Size md (default) | `h-8 w-8` |
| Size lg | `h-12 w-12` |

---

### 15.6 Layout Components

#### Sidebar (`components/layout/Sidebar.tsx`)

| Property | Value |
|---|---|
| Container | `hidden md:flex md:w-64 md:flex-col md:fixed md:inset-y-0 border-r border-gray-200 bg-white` |
| Logo bar | `h-16 px-6`, border-b, `HardHat` icon `h-7 w-7 text-primary` + `text-lg font-bold text-primary` |
| Nav area | `flex-1 space-y-1 px-3 py-4` |
| Nav item (inactive) | `flex items-center gap-3 rounded-lg px-3 py-2.5 text-sm font-medium text-gray-600 hover:bg-gray-50 hover:text-gray-900` |
| Nav item (active) | `bg-primary-50 text-primary` |
| Icons | `h-5 w-5` (lucide-react) |

**Navigation links:**

| Icon | Label | Route |
|---|---|---|
| `LayoutDashboard` | Dashboard | `/` |
| `FolderKanban` | Projects | `/projects` |
| `Users` | Tradies | `/tradies` |
| `BarChart3` | Reports | `/reports` |

---

#### Header (`components/layout/Header.tsx`)

| Property | Value |
|---|---|
| Container | `sticky top-0 z-30 flex h-16 items-center justify-between border-b border-gray-200 bg-white px-4 sm:px-6` |
| Mobile menu button | `rounded-lg p-2 hover:bg-gray-100 md:hidden`, `Menu` icon `h-5 w-5` |
| Title | `text-lg font-semibold text-gray-900` |
| Notification icon | `Bell h-5 w-5 text-gray-500`, `rounded-lg p-2 hover:bg-gray-100` |
| User avatar | `h-8 w-8 rounded-full bg-primary text-sm font-medium text-white` (initials) |
| User name | `hidden sm:block text-sm font-medium text-gray-700` |
| Dropdown menu | `absolute right-0 top-full mt-1 w-48 rounded-lg border border-gray-200 bg-white py-1 shadow-lg` |
| Dropdown item | `flex w-full items-center gap-2 px-4 py-2 text-sm text-gray-700 hover:bg-gray-50` |
| Sign out | `text-danger` colour |

---

#### BottomNav (`components/layout/BottomNav.tsx`)

| Property | Value |
|---|---|
| Container | `fixed bottom-0 left-0 right-0 z-40 border-t border-gray-200 bg-white md:hidden` |
| Tab item | `flex flex-1 flex-col items-center gap-0.5 py-2` |
| Tab label | `text-[10px] font-medium` |
| Tab icon | `h-5 w-5` |
| Active colour | `text-primary` |
| Inactive colour | `text-gray-500` |

**Tabs:**

| Icon | Label | Route |
|---|---|---|
| `LayoutDashboard` | Home | `/` |
| `FolderKanban` | Projects | `/projects` |
| `Users` | Tradies | `/tradies` |
| `BarChart3` | Reports | `/reports` |
| `MoreHorizontal` | More | `#more` |

---

### 15.7 Page-Level Design Patterns

#### Login Page

- Centred layout: `flex min-h-screen items-center justify-center bg-gray-50 p-4`
- Logo: `h-16 w-16 rounded-2xl bg-primary` with white `HardHat h-9 w-9`
- Brand: "BuildMate" `text-2xl font-bold text-gray-900`, subtitle `text-sm text-gray-500`
- Form card: `rounded-xl border border-gray-200 bg-white p-6 shadow-sm sm:p-8`
- Error banner: `rounded-lg bg-danger-50 px-4 py-3 text-sm text-danger`
- Register link: `font-medium text-primary hover:underline`

---

#### Dashboard Page

- **Stats row:** 4-column grid (`grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4`)
  - Each stat: Card with icon in `rounded-lg bg-gray-50 p-3` + label (`text-sm text-gray-500`) + value (`text-xl font-bold text-gray-900`)
  - Icon colours: primary, success, warning, danger
- **Content grid:** `grid-cols-1 lg:grid-cols-3 gap-6`
  - Recent Projects card (spans 2 cols): list of project rows with hover `hover:bg-gray-50`, showing name + address + badge + currency
  - Cash Position card: Total value `text-2xl font-bold`, income/expense `text-lg font-semibold`
- **Milestones card:** Full width below grid

---

#### Projects List Page

- **Header:** Title + "New Project" button (`Plus` icon, size `sm`)
- **Search bar:** Inline `Search` icon (`absolute left-3 top-1/2 h-4 w-4 text-gray-400`), input with `pl-10`
- **Status tabs:** `flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1`
  - Active tab: `bg-white text-gray-900 shadow-sm rounded-md`
  - Inactive tab: `text-gray-500 hover:text-gray-700`
  - Tabs: All, Active, Complete, On Hold
- **Project cards:** `grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4`
  - Card: `rounded-xl border border-gray-200 bg-white p-5 shadow-sm transition-shadow hover:shadow-md`
  - Name: `font-semibold text-gray-900`, address: `text-xs text-gray-500`
  - Contract value: `text-lg font-bold text-gray-900`
  - Progress bar: `h-2 rounded-full bg-gray-100` with `bg-primary` fill
  - Dates: `text-xs text-gray-500`

---

#### Project Detail Page

- **Back link:** `ArrowLeft h-4 w-4` + "Back to Projects", `text-sm text-gray-500 hover:text-gray-700`
- **Tab navigation:** Border-bottom tabs with `border-b border-gray-200`
  - Active: `border-primary text-primary` (border-b-2)
  - Inactive: `border-transparent text-gray-500 hover:text-gray-700`
  - Tabs: Overview, Budget, Cash Flow, Planning, Financials, Inspections, Documents, Team
- **Overview:** 4-column stat cards + Quick Actions grid (2×4 link cards with icon `h-6 w-6 text-primary`)

---

#### Tab Filter Pattern (used on Projects, Planning, Documents, Tradies)

```
Container: flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1
Active:    rounded-md bg-white text-gray-900 shadow-sm px-3–4 py-1.5 text-xs–sm font-medium
Inactive:  text-gray-500 hover:text-gray-700
```

---

#### View Toggle Pattern (used on Planning page)

```
Container: flex rounded-lg border border-gray-300 p-0.5
Active:    rounded-md bg-primary text-white px-2.5 py-1 text-xs font-medium
Inactive:  text-gray-500 hover:text-gray-700
```

---

#### Form Modal Pattern

All create/edit forms follow:
1. `<Modal>` wrapper with title
2. `<form className="space-y-4">`
3. `<Input>` components with labels
4. `<select>` elements matching Input styling
5. Grid layouts for related fields: `grid grid-cols-2 gap-4`
6. Footer: `flex justify-end gap-2 pt-2` with Cancel (secondary/ghost) + Submit (primary)
7. Submit button text changes to "Saving…" / "Creating…" when `isPending`
8. Submit button has `disabled` when pending or form incomplete

---

### 15.8 Chart Design (Recharts)

#### Budget vs Actual (BarChart)

| Property | Value |
|---|---|
| Container | Card titled "Budget vs Actual", chart in `h-72` div |
| Budget bars | `fill="#1E40AF"` (primary), `radius={[4, 4, 0, 0]}` |
| Spent bars | `fill="#DC2626"` (danger), `radius={[4, 4, 0, 0]}` |
| Grid | `strokeDasharray="3 3"` |
| Axis labels | `fontSize: 12` |
| Tooltip | Values formatted with `formatCurrency()` |
| Legend | Default Recharts legend |

#### S-Curve Cash Flow (LineChart)

| Property | Value |
|---|---|
| Container | Card titled "S-Curve: Planned vs Actual", chart in `h-72` div |
| Planned line | `stroke="#93C5FD"` (primary-300), `strokeWidth={2}`, `strokeDasharray="5 5"` |
| Actual line | `stroke="#1E40AF"` (primary), `strokeWidth={2}` |
| Grid | `strokeDasharray="3 3"` |
| Line type | `monotone` |

---

### 15.9 Gantt Chart Design

**File:** `components/GanttChart.tsx`

| Property | Value |
|---|---|
| Container | `rounded-xl border border-gray-200 bg-white overflow-hidden` |
| Row height | `36px` |
| Label column width | `200px` fixed |
| Default day width | `28px` (zoom level index 3) |
| Zoom levels | `[8, 14, 20, 28, 40, 56, 80]` pixels per day |

**Status bar colours:**

| Status | Bar Fill | Bar Background |
|---|---|---|
| COMPLETE | `bg-emerald-500` | `bg-emerald-100` |
| IN_PROGRESS | `bg-blue-500` | `bg-blue-100` |
| NOT_STARTED | `bg-gray-400` | `bg-gray-200` |
| ON_HOLD | `bg-amber-500` | `bg-amber-100` |
| BLOCKED | `bg-red-500` | `bg-red-100` |

**Visual elements:**

| Element | Style |
|---|---|
| Task bar | `rounded-md shadow-sm`, height `20px`, progress fill at `opacity: 0.85` |
| Milestone | `h-4 w-4 rotate-45 bg-purple-600 rounded-sm shadow-sm` diamond |
| Today line | `w-px bg-red-400` with label `bg-red-400 text-[9px] font-bold text-white` |
| Weekend shading | `bg-gray-50/60` |
| Grid lines | `border-r border-gray-100` at weekly intervals |
| Hover row | `bg-blue-50` (label) / `bg-blue-50/50` (timeline) |
| Tooltip | `rounded-lg bg-gray-900 px-3 py-2 text-xs text-white shadow-lg whitespace-nowrap` |

**Legend bar:** `flex flex-wrap items-center gap-3 border-b px-4 py-2 text-xs text-gray-500`
- Colour squares: `h-2.5 w-2.5 rounded-sm`
- Milestone indicator: CSS triangle border trick + "Milestone" label

---

### 15.10 Icon System

All icons use **Lucide React** (`lucide-react` v0.451).

| Context | Size | Additional Classes |
|---|---|---|
| Navigation icons | `h-5 w-5` | — |
| Button icons | `h-4 w-4` | — |
| Stat card icons | `h-6 w-6` | Colour class per semantic role |
| Empty state icons | `h-12 w-12` | `text-gray-400` |
| Document file icons | `h-8 w-8` | `text-gray-400` |
| Header icons | `h-5 w-5` | `text-gray-500` |
| Inline icons (phone/email) | `h-3.5 w-3.5` | — |
| Table inline icons | `h-3 w-3` | — |

**Icon inventory:**

| Module | Icons Used |
|---|---|
| Navigation | `LayoutDashboard`, `FolderKanban`, `Users`, `BarChart3`, `MoreHorizontal`, `HardHat` |
| Header | `Menu`, `Bell`, `LogOut`, `User` |
| Projects | `Plus`, `Search`, `ArrowLeft`, `ArrowRight` |
| Project Detail | `DollarSign`, `TrendingUp`, `CalendarDays`, `Receipt`, `ClipboardCheck`, `FileText`, `Users` |
| Planning | `ZoomIn`, `ZoomOut`, `BarChart3`, `List` |
| Documents | `Upload`, `FileText`, `FileImage`, `File`, `Download`, `Trash2` |
| Tradies | `Star`, `AlertTriangle`, `Phone`, `Mail` |
| Reports | `DollarSign`, `TrendingUp`, `Users`, `FolderKanban`, `Calendar`, `ArrowRight` |
| Dashboard | `FolderKanban`, `DollarSign`, `FileText`, `AlertTriangle`, `ArrowRight` |
| Modal | `X` |

---

### 15.11 Interactive States & Transitions

| Element | Normal | Hover | Active/Focus | Disabled |
|---|---|---|---|---|
| Primary button | `bg-primary text-white` | `bg-primary-700` | `ring-2 ring-primary-500 ring-offset-2` | `opacity-50 pointer-events-none` |
| Card link | `shadow-sm` | `shadow-md` | — | — |
| Nav link (desktop) | `text-gray-600` | `bg-gray-50 text-gray-900` | `bg-primary-50 text-primary` | — |
| Nav tab (bottom) | `text-gray-500` | — | `text-primary` | — |
| Table row (Gantt) | transparent | `bg-blue-50` | — | — |
| Project card | `border-gray-200 shadow-sm` | `shadow-md` | — | — |
| Input field | `border-gray-300` | — | `border-primary ring-1 ring-primary` | — |
| Dropdown item | `bg-white` | `bg-gray-50` | — | — |
| Tab (filter) | `text-gray-500` | `text-gray-700` | `bg-white text-gray-900 shadow-sm` | — |
| Zoom button | `border-gray-300 bg-white text-gray-600` | `bg-gray-100` | — | `opacity-30` |

**Transition:** All interactive elements use `transition-colors` (150ms ease default).

---

### 15.12 Progress Bar Pattern

Used in: Project cards, Budget categories, Task list, Project detail.

```
Container:  h-2 overflow-hidden rounded-full bg-gray-100
Fill:       h-full rounded-full bg-primary (or conditional colour)
            style={{ width: `${percentage}%` }}
```

**Budget progress conditional colours:**
- `> 100%`: `bg-danger`
- `> 80%`: `bg-warning`
- `≤ 80%`: `bg-success`

---

### 15.13 Alert / Banner Pattern

**Compliance warning (Tradies page):**
```
Container:  flex items-center gap-3 rounded-lg border border-warning bg-warning-50 px-4 py-3
Icon:       AlertTriangle h-5 w-5 text-warning
Text:       text-sm text-warning-600, with <strong> for count
```

**Login error:**
```
Container:  rounded-lg bg-danger-50 px-4 py-3 text-sm text-danger
```

---

### 15.14 Drag & Drop Zone (Documents)

```
Normal:     flex flex-col items-center justify-center rounded-xl border-2 border-dashed border-gray-300 p-8
Drag over:  border-primary bg-primary-50
Icon:       Upload h-8 w-8 text-gray-400, mb-2
Text:       text-sm text-gray-500
```

---

### 15.15 Currency & Date Formatting

**Currency** (Australian dollars):
```typescript
new Intl.NumberFormat('en-AU', {
  style: 'currency',
  currency: 'AUD',
  minimumFractionDigits: 0,
  maximumFractionDigits: 0,
}).format(amount);
// Output: "$120,000"
```

**Date:**
```typescript
new Date(date).toLocaleDateString('en-AU', {
  day: '2-digit',
  month: 'short',
  year: 'numeric',
});
// Output: "26 Mar 2026"
```

---

### 15.16 Utility Functions

**`cn()` — Conditional class merging:**
```typescript
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

Used everywhere for conditional and merged Tailwind classes. Dependencies: `clsx` + `tailwind-merge`.

---

### 15.17 Responsive Behaviour Summary

| Component | Mobile (< 768px) | Tablet/Desktop (≥ 768px) |
|---|---|---|
| Sidebar | Hidden | Fixed left, w-64 |
| Bottom Nav | Visible, fixed bottom | Hidden |
| Header menu button | Visible | Hidden |
| Main padding | `p-4 pb-20` | `p-6 pb-6` |
| Stats grid | 1 col (or 2 col at sm) | 4 columns |
| Project cards | 1 column | 2–3 columns |
| Tradie cards | 1 column | 2–3 columns |
| Form grids | Full width | 2–3 columns |
| Tab bars | Horizontally scrollable | Inline |
| User name (header) | Hidden | Visible |
| Report cards | 1 column | 2 columns |

---

## 16. Complete Source File Reference

This section documents every source file's exact content and configuration so the application can be **100% replicated** without access to the original repository. The design document above (Sections 1–15) describes _what_ and _why_; this section covers the remaining _exact how_ for configuration files, server source code, and frontend API layer.

---

### 16.1 Configuration Files

#### Root `.gitignore`

```gitignore
node_modules/
dist/
.env
*.log
uploads/*
!uploads/.gitkeep
.DS_Store
```

#### Server `.gitignore`

```gitignore
node_modules/
dist/
.env
uploads/*
!uploads/.gitkeep
```

#### Server `.env.example`

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/buildmate"
JWT_SECRET="your-secret-key-change-in-production"
JWT_REFRESH_SECRET="your-refresh-secret-change-in-production"
PORT=3001
CORS_ORIGIN="http://localhost:5173"
```

> **Note:** Development uses SQLite. The `.env` file actually contains `DATABASE_URL="file:./dev.db"`.

#### Server `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

#### Web `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "isolatedModules": true,
    "moduleDetection": "force",
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src"]
}
```

#### Web `postcss.config.js`

```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

#### `docker-compose.yml`

```yaml
version: '3.8'

services:
  db:
    image: postgres:16-alpine
    container_name: buildmate-db
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: buildmate
    ports:
      - '5432:5432'
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    container_name: buildmate-redis
    ports:
      - '6379:6379'

volumes:
  pgdata:
```

---

### 16.2 Server Source — Types & Configuration

#### `server/src/types/index.ts`

```typescript
import { Request } from 'express';

export interface AuthUser {
  id: string;
  email: string;
  role: string;
}

export interface AuthRequest extends Request {
  user?: AuthUser;
}

export interface PaginationQuery {
  page?: string;
  limit?: string;
  search?: string;
}

export class AppError extends Error {
  statusCode: number;
  isOperational: boolean;

  constructor(message: string, statusCode: number) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}
```

#### `server/src/config/index.ts`

```typescript
import dotenv from 'dotenv';

dotenv.config();

export const config = {
  port: parseInt(process.env.PORT || '3001', 10),
  corsOrigin: process.env.CORS_ORIGIN || 'http://localhost:5173',
  jwt: {
    secret: process.env.JWT_SECRET || 'dev-secret-change-me',
    refreshSecret: process.env.JWT_REFRESH_SECRET || 'dev-refresh-secret-change-me',
    expiresIn: '24h',
    refreshExpiresIn: '7d',
  },
  upload: {
    maxFileSize: 10 * 1024 * 1024, // 10MB
    destination: './uploads',
  },
};
```

---

### 16.3 Server Source — Middleware

#### `server/src/middleware/auth.ts` — JWT Authentication

- Extracts `Bearer <token>` from `Authorization` header
- Verifies with `config.jwt.secret`, extracts `{ id, email, role }` into `req.user`
- `authorize(...roles)` — checks `req.user.role` against allowed roles array
- Returns `AppError` 401 for missing/invalid token, 403 for insufficient permissions

#### `server/src/middleware/validate.ts` — Zod Validation

- Accepts `schema: ZodSchema` and `source: 'body' | 'query' | 'params'`
- On success: replaces `req[source]` with parsed data (strips extra fields)
- On failure: returns 400 with `{ error: "Validation failed", details: [{ field, message }] }`

#### `server/src/middleware/errorHandler.ts` — Global Error Handler

- `AppError` instances → `{ error: message }` with the error's `statusCode`
- Other errors → `console.error()` + 500 `{ error: "Internal server error" }`

---

### 16.4 Server Source — Zod Validation Schemas

Each route file defines its own Zod schemas. These are critical for replication:

#### Auth Schemas

```typescript
const registerSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(1),
  phone: z.string().optional(),
  role: z.enum(['ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR', 'ACCOUNTS', 'TRADIE']).optional(),
});

const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(1),
});

const updateProfileSchema = z.object({
  name: z.string().min(1).optional(),
  phone: z.string().optional(),
  email: z.string().email().optional(),
});
```

#### Project Schemas

```typescript
const createProjectSchema = z.object({
  name: z.string().min(1),
  address: z.string().optional(),
  suburb: z.string().optional(),
  state: z.string().optional(),
  postcode: z.string().optional(),
  buildingClass: z.string().optional(),
  constructionType: z.string().optional(),
  clientName: z.string().optional(),
  clientEmail: z.string().email().optional(),
  clientPhone: z.string().optional(),
  contractValue: z.number().optional(),
  estimatedCost: z.number().optional(),
  startDate: z.string().datetime().optional(),
  estimatedEndDate: z.string().datetime().optional(),
  status: z.enum(['NOT_STARTED', 'IN_PROGRESS', 'ON_HOLD', 'COMPLETE', 'CANCELLED']).optional(),
  notes: z.string().optional(),
});

const updateProjectSchema = createProjectSchema.partial().extend({
  actualEndDate: z.string().datetime().optional(),
});
```

#### Transaction Schemas

```typescript
const createTransactionSchema = z.object({
  projectId: z.string().uuid(),
  type: z.enum(['INCOME', 'EXPENSE']),
  category: z.enum(['PROGRESS_CLAIM', 'SUBCONTRACTOR', 'MATERIALS', 'PLANT_HIRE', 'LABOUR', 'PERMIT_FEE', 'INSURANCE', 'OVERHEAD', 'OTHER']),
  description: z.string().min(1),
  amount: z.number(),
  gstAmount: z.number(),
  totalAmount: z.number(),
  date: z.string().datetime(),
  dueDate: z.string().datetime().optional(),
  paidDate: z.string().datetime().optional(),
  status: z.enum(['DRAFT', 'SUBMITTED', 'APPROVED', 'PAID', 'OVERDUE', 'CANCELLED']).optional(),
  tradieId: z.string().uuid().optional(),
  invoiceNumber: z.string().optional(),
  attachmentUrl: z.string().optional(),
  notes: z.string().optional(),
});
```

#### Invoice Schemas

```typescript
const lineItemSchema = z.object({
  description: z.string().min(1),
  quantity: z.number(),
  unit: z.string().optional(),
  unitPrice: z.number(),
  amount: z.number(),
  costCode: z.string().optional(),
});

const createInvoiceSchema = z.object({
  projectId: z.string().uuid(),
  invoiceNumber: z.string().min(1),
  type: z.enum(['PROGRESS_CLAIM', 'TAX_INVOICE']),
  toName: z.string().min(1),
  toEmail: z.string().email().optional(),
  toAddress: z.string().optional(),
  subtotal: z.number(),
  gstTotal: z.number(),
  total: z.number(),
  retentionPercent: z.number().optional(),
  retentionAmount: z.number().optional(),
  amountDue: z.number(),
  issueDate: z.string().datetime(),
  dueDate: z.string().datetime(),
  status: z.enum(['DRAFT', 'SENT', 'VIEWED', 'PAID', 'OVERDUE']).optional(),
  lineItems: z.array(lineItemSchema).optional(),
});
```

#### Budget Schemas

```typescript
const createCategorySchema = z.object({
  projectId: z.string().uuid(),
  costCode: z.string().min(1),
  name: z.string().min(1),
  budgetAmount: z.number(),
  revisedBudget: z.number().optional(),
  forecastToComplete: z.number().optional(),
  sortOrder: z.number().int().optional(),
  parentId: z.string().uuid().optional(),
});

const createVariationSchema = z.object({
  projectId: z.string().uuid(),
  variationNumber: z.number().int(),
  title: z.string().min(1),
  description: z.string().optional(),
  amount: z.number(),
  status: z.enum(['DRAFT', 'SUBMITTED', 'APPROVED', 'REJECTED']).optional(),
  categoryId: z.string().uuid().optional(),
  submittedDate: z.string().datetime().optional(),
  approvedDate: z.string().datetime().optional(),
  approvedBy: z.string().optional(),
  attachmentUrl: z.string().optional(),
});
```

#### Cash Flow Schemas

```typescript
const createCashFlowSchema = z.object({
  projectId: z.string().uuid(),
  month: z.string().datetime(),
  type: z.enum(['FORECAST', 'ACTUAL']),
  incomeAmount: z.number(),
  expenseAmount: z.number(),
  notes: z.string().optional(),
});

const createClaimScheduleSchema = z.object({
  projectId: z.string().uuid(),
  claimNumber: z.number().int(),
  description: z.string().min(1),
  scheduledDate: z.string().datetime(),
  amount: z.number(),
  percentComplete: z.number(),
  status: z.enum(['SCHEDULED', 'SUBMITTED', 'APPROVED', 'PAID']).optional(),
  actualDate: z.string().datetime().optional(),
  actualAmount: z.number().optional(),
});
```

#### Task Schemas

```typescript
const createTaskSchema = z.object({
  projectId: z.string().uuid(),
  name: z.string().min(1),
  description: z.string().optional(),
  startDate: z.string().datetime().optional(),
  endDate: z.string().datetime().optional(),
  duration: z.number().int().optional(),
  percentComplete: z.number().int().min(0).max(100).optional(),
  status: z.enum(['NOT_STARTED', 'IN_PROGRESS', 'COMPLETE', 'ON_HOLD', 'DELAYED']).optional(),
  priority: z.enum(['LOW', 'MEDIUM', 'HIGH', 'CRITICAL']).optional(),
  isMilestone: z.boolean().optional(),
  parentTaskId: z.string().uuid().optional(),
  assignedTradieId: z.string().uuid().optional(),
  assignedUserId: z.string().uuid().optional(),
  costCodeId: z.string().uuid().optional(),
  sortOrder: z.number().int().optional(),
  colour: z.string().optional(),
  baselineStartDate: z.string().datetime().optional(),
  baselineEndDate: z.string().datetime().optional(),
  notes: z.string().optional(),
});

const createDependencySchema = z.object({
  predecessorId: z.string().uuid(),
  successorId: z.string().uuid(),
  type: z.enum(['FINISH_TO_START', 'START_TO_START', 'FINISH_TO_FINISH', 'START_TO_FINISH']).optional(),
  lagDays: z.number().int().optional(),
});

const createDelayLogSchema = z.object({
  projectId: z.string().uuid(),
  taskId: z.string().uuid().optional(),
  reason: z.enum(['WEATHER', 'MATERIAL_DELAY', 'TRADIE_NO_SHOW', 'INSPECTION_FAIL', 'CLIENT_CHANGE', 'PERMIT_DELAY', 'OTHER']),
  description: z.string().optional(),
  delayDays: z.number().int().min(1),
  date: z.string().datetime(),
});
```

#### Inspection & Defect Schemas

```typescript
const inspectionItemSchema = z.object({
  category: z.string().min(1),
  description: z.string().min(1),
  result: z.enum(['PASS', 'FAIL', 'NA', 'NOT_INSPECTED']).optional(),
  notes: z.string().optional(),
  photoUrls: z.array(z.string()).optional(),
  sortOrder: z.number().int().optional(),
});

const createInspectionSchema = z.object({
  projectId: z.string().uuid(),
  type: z.enum(['PRE_SLAB', 'FRAME', 'LOCK_UP', 'WATERPROOFING', 'FINAL', 'DEFECT_RECTIFICATION', 'SAFETY', 'CUSTOM']),
  title: z.string().min(1),
  scheduledDate: z.string().datetime(),
  completedDate: z.string().datetime().optional(),
  inspectorName: z.string().optional(),
  status: z.enum(['SCHEDULED', 'IN_PROGRESS', 'COMPLETE', 'CANCELLED']).optional(),
  overallResult: z.enum(['PASS', 'FAIL', 'CONDITIONAL']).optional(),
  notes: z.string().optional(),
  items: z.array(inspectionItemSchema).optional(),
});

const createDefectSchema = z.object({
  projectId: z.string().uuid(),
  inspectionId: z.string().uuid().optional(),
  title: z.string().min(1),
  description: z.string().optional(),
  location: z.string().optional(),
  severity: z.enum(['MINOR', 'MODERATE', 'MAJOR', 'CRITICAL']),
  status: z.enum(['OPEN', 'ASSIGNED', 'IN_PROGRESS', 'RECTIFIED', 'VERIFIED', 'CLOSED']).optional(),
  assignedTradieId: z.string().uuid().optional(),
  dueDate: z.string().datetime().optional(),
  rectifiedDate: z.string().datetime().optional(),
  verifiedDate: z.string().datetime().optional(),
  photoUrls: z.array(z.string()).optional(),
  rectificationPhotoUrls: z.array(z.string()).optional(),
});
```

#### Document Schemas

```typescript
const folderValues = ['PLANS', 'CONTRACTS', 'PERMITS', 'SWMS', 'CERTIFICATES',
  'INSURANCE', 'PHOTOS', 'REPORTS', 'CORRESPONDENCE', 'OTHER'] as const;

const createDocumentSchema = z.object({
  projectId: z.string().uuid(),
  folder: z.enum(folderValues),
  name: z.string().min(1),
  description: z.string().optional(),
});
```

#### Tradie Schemas

```typescript
const tradeValues = ['BUILDER', 'CARPENTER', 'ELECTRICIAN', 'PLUMBER', 'PLASTERER', 'PAINTER',
  'TILER', 'BRICKLAYER', 'CONCRETER', 'ROOFER', 'LANDSCAPER', 'DEMOLITION', 'EXCAVATION',
  'STEEL_FIXER', 'CABINET_MAKER', 'GLAZIER', 'RENDERER', 'WATERPROOFER', 'INSULATION',
  'FENCER', 'SCAFFOLDER', 'CLEANER', 'HVAC', 'FIRE_PROTECTION', 'SECURITY_SYSTEMS',
  'FLOORING', 'STONEMASON', 'POOL_BUILDER', 'SURVEYOR', 'ENGINEER', 'ARCHITECT',
  'DRAFTSPERSON', 'OTHER'] as const;

const createTradieSchema = z.object({
  companyName: z.string().min(1),
  contactName: z.string().min(1),
  email: z.string().email().optional(),
  phone: z.string().optional(),
  abn: z.string().optional(),
  trade: z.enum(tradeValues),
  licenceNumber: z.string().optional(),
  licenceExpiry: z.string().datetime().optional(),
  insuranceProvider: z.string().optional(),
  insurancePolicyNumber: z.string().optional(),
  insuranceExpiry: z.string().datetime().optional(),
  workersCompProvider: z.string().optional(),
  workersCompPolicyNumber: z.string().optional(),
  workersCompExpiry: z.string().datetime().optional(),
  rating: z.number().int().min(1).max(5).optional(),
  notes: z.string().optional(),
  status: z.enum(['ACTIVE', 'INACTIVE', 'BLACKLISTED']).optional(),
});

const createTradieDocumentSchema = z.object({
  tradieId: z.string().uuid(),
  type: z.enum(['LICENCE', 'PUBLIC_LIABILITY', 'WORKERS_COMP', 'ABN_LOOKUP', 'SWMS', 'OTHER']),
  name: z.string().min(1),
  fileUrl: z.string().min(1),
  expiryDate: z.string().datetime().optional(),
});
```

---

### 16.5 Server Route Implementation Details

Key implementation patterns not obvious from the API endpoint table:

#### Prisma Client Instantiation

Every route file creates its own `new PrismaClient()` instance at module level. All routes apply `router.use(authenticate)` at the top (except auth routes which selectively apply it).

#### JWT Token Generation (`auth.ts`)

```typescript
function generateTokens(user: { id: string; email: string; role: string }) {
  const accessToken = jwt.sign(
    { id: user.id, email: user.email, role: user.role },
    config.jwt.secret,
    { expiresIn: config.jwt.expiresIn }   // '24h'
  );
  const refreshToken = jwt.sign(
    { id: user.id },
    config.jwt.refreshSecret,
    { expiresIn: config.jwt.refreshExpiresIn }  // '7d'
  );
  return { accessToken, refreshToken };
}
```

- Password hashing: `bcrypt.hash(password, 12)` (12 salt rounds)
- Register returns: `{ user, accessToken, refreshToken }` (status 201)
- Login returns: `{ user: { id, email, name, phone, role }, accessToken, refreshToken }`
- User select fields exclude `password` in all responses

#### Pagination Pattern

Projects, transactions, inspections, documents, tradies use this pattern:

```typescript
const page = parseInt(req.query.page as string) || 1;
const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
const skip = (page - 1) * limit;

// ... findMany with skip/take ...

res.json({
  data: results,
  pagination: { page, limit, total, pages: Math.ceil(total / limit) },
});
```

Default limit: 20. Max limit: 100.

#### Flat Array Pattern

Tasks, budget categories, budget variations, cash flow entries return **flat arrays** (no pagination wrapper).

#### Date Handling

All datetime string inputs are converted to `new Date()` before Prisma create/update.

#### Project DELETE

Soft-delete: sets `status` to `'CANCELLED'` (not actual deletion).

#### Tradie DELETE

Soft-delete: sets `status` to `'INACTIVE'`.

#### Task Progress PATCH `/tasks/:id/progress`

Auto-sets status based on percentage:
- `0%` → `NOT_STARTED`
- `1-99%` → `IN_PROGRESS`
- `100%` → `COMPLETE`

#### Budget Summary `/budget/summary/:projectId`

Computes from database:
- `totalBudget`: sum of all category `budgetAmount`
- `totalRevised`: sum of `revisedBudget || budgetAmount`
- `totalVariations`: sum of APPROVED variation amounts
- `adjustedBudget`: `totalRevised + totalVariations`
- `totalSpent`: sum of APPROVED/PAID expense transaction `totalAmount`
- `totalForecast`: sum of `forecastToComplete`
- `variance`: `adjustedBudget - totalSpent - totalForecast`

#### Cash Flow Forecast Generation `/cashflow/forecast/:projectId`

1. Requires project start/end dates
2. Generates monthly entries from start to end
3. Income per month: from claim schedule if a claim falls in that month
4. Expense per month: `totalBudget / totalMonths` (evenly distributed)
5. Deletes all existing FORECAST entries first, then creates new ones

#### Document Upload (Multer)

```typescript
const storage = multer.diskStorage({
  destination: './uploads',   // auto-creates if missing
  filename: `${Date.now()}-${Math.round(Math.random() * 1e9)}${ext}`,
});

// Max file size: 10MB
// File URL stored as: /uploads/{filename}
// Upload endpoint: POST /documents/upload — expects FormData with 'file' field
```

#### Document DELETE

Deletes both the database record AND the physical file from disk.

#### Tradie Compliance `/tradies/compliance`

Returns tradies and documents expiring within `?days` (default 30):
- Checks `tradieDocument.expiryDate`
- Checks `tradie.licenceExpiry`, `insuranceExpiry`, `workersCompExpiry`

---

### 16.6 Frontend API Layer — Response Mapping Patterns

Each `web/src/api/*.ts` file handles the mismatch between paginated server responses and the frontend's expectation of flat arrays:

```typescript
// Pattern used in: projects.ts, transactions.ts, inspections.ts, documents.ts, tradies.ts
const { data } = await client.get('/endpoint', { params });
return Array.isArray(data) ? data : data.data ?? [];
```

**Document upload** uses `FormData` with `multipart/form-data` header:
```typescript
const formData = new FormData();
formData.append('file', file);
formData.append('folder', folder.toUpperCase());
formData.append('projectId', projectId);
formData.append('name', file.name);
```

**Auth response mapping** (`mapUser()` function):
- Server sends `name: "John Smith"` → frontend splits to `firstName: "John"`, `lastName: "Smith"`
- Server sends `role: "ADMIN"` → frontend converts to `role: "admin"`
- Server sends `accessToken` → frontend maps to `token`

---

### 16.7 Prisma Schema — Complete Field-Level Reference

The full Prisma schema is in `server/prisma/schema.prisma` (535 lines, 20 models). Key details not captured elsewhere:

- **Database provider:** `sqlite` (with `file:./dev.db` URL)
- **All IDs:** `String @id @default(uuid())`
- **All timestamps:** `createdAt DateTime @default(now())`, `updatedAt DateTime @updatedAt`
- **Enums stored as strings** (SQLite does not support native enums)
- **Cascade deletes:** Project → all child entities. Invoice → line items. Inspection → items. Tradie → documents + assignments. Task → dependencies.
- **Self-referencing trees:** BudgetCategory (parent/children), Task (parentTask/childTasks)
- **JSON-as-string fields:** `InspectionItem.photoUrls`, `Defect.photoUrls`, `Defect.rectificationPhotoUrls` — stored as `String @default("[]")`, parsed as JSON arrays
- **Unique constraints:** `ProjectTeam(projectId, userId)`, `BudgetCategory(projectId, costCode)`, `Variation(projectId, variationNumber)`, `CashFlowEntry(projectId, month)`, `TradieProjectAssignment(tradieId, projectId)`, `TaskDependency(predecessorId, successorId)`
- **Indexes:** Applied on all foreign keys and frequently queried fields (status, date, email, trade)

---

### 16.8 Exact Dependency Versions

#### Server `package.json`

```json
{
  "dependencies": {
    "@prisma/client": "^6.3.0",
    "bcryptjs": "^2.4.3",
    "cors": "^2.8.5",
    "dotenv": "^16.4.7",
    "express": "^4.21.2",
    "helmet": "^8.0.0",
    "jsonwebtoken": "^9.0.2",
    "morgan": "^1.10.0",
    "multer": "^1.4.5-lts.1",
    "zod": "^3.24.1"
  },
  "devDependencies": {
    "@types/bcryptjs": "^2.4.6",
    "@types/cors": "^2.8.17",
    "@types/express": "^5.0.0",
    "@types/jsonwebtoken": "^9.0.7",
    "@types/morgan": "^1.9.9",
    "@types/multer": "^1.4.12",
    "nodemon": "^3.1.9",
    "prisma": "^6.3.0",
    "ts-node": "^10.9.2",
    "tsx": "^4.19.2",
    "typescript": "^5.7.3"
  }
}
```

**Scripts:** `dev: "tsx watch src/index.ts"`, `build: "tsc"`, `start: "node dist/index.js"`, `prisma:generate`, `prisma:migrate`, `prisma:studio`

#### Web `package.json`

```json
{
  "dependencies": {
    "@tanstack/react-query": "^5.59.0",
    "axios": "^1.7.7",
    "clsx": "^2.1.1",
    "date-fns": "^4.1.0",
    "lucide-react": "^0.451.0",
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "react-router-dom": "^6.27.0",
    "recharts": "^2.13.0",
    "tailwind-merge": "^2.5.4"
  },
  "devDependencies": {
    "@types/react": "^18.3.11",
    "@types/react-dom": "^18.3.1",
    "@vitejs/plugin-react": "^4.3.2",
    "autoprefixer": "^10.4.20",
    "postcss": "^8.4.47",
    "tailwindcss": "^3.4.13",
    "typescript": "^5.6.3",
    "vite": "^5.4.9"
  }
}
```

**Scripts:** `dev: "vite"`, `build: "tsc && vite build"`, `preview: "vite preview"`
