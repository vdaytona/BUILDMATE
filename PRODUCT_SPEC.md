# BuildMate — Construction Management Platform

## Product Specification Document

**Version:** 1.0
**Date:** 26 March 2026
**Status:** Draft

---

## 1. Executive Summary

BuildMate is a cloud-based construction management platform designed for residential and commercial builders in Australia. It provides a responsive web application (desktop, tablet, and mobile-friendly) and a native iPhone app, enabling builders to manage projects, finances, tradies, planning, inspections, and documents from the office or on-site.

---

## 2. Target Users

| Role | Description |
|------|-------------|
| **Builder / Director** | Business owner who oversees multiple projects, reviews financials, approves payments |
| **Site Supervisor** | On-site manager who tracks progress, logs inspections, manages tradies |
| **Project Manager** | Plans schedules, manages budgets, coordinates between trades |
| **Accounts / Admin** | Processes invoices, manages cash flow, handles tradie records |
| **Tradie (View Only)** | Subcontractor who views assigned tasks, submits timesheets/invoices |

---

## 3. Platform & Technology Stack

| Layer | Technology | Notes |
|-------|-----------|-------|
| **Web Frontend** | React 18 + TypeScript + Tailwind CSS | Responsive (mobile-first design) |
| **iPhone App** | React Native + TypeScript | Shared API layer and business logic |
| **Backend API** | Node.js + Express + TypeScript | RESTful API with JWT auth |
| **Database** | PostgreSQL 16 | Relational, ACID-compliant |
| **ORM** | Prisma | Type-safe database access |
| **File Storage** | AWS S3 / Azure Blob | Photos, documents, plans |
| **Auth** | JWT + bcrypt | Role-based access control (RBAC) |
| **Caching** | Redis | Session management, frequent queries |
| **Charts** | Recharts (web) / Victory Native (mobile) | Financial charts, Gantt |
| **Testing** | Jest + React Testing Library + Supertest | Unit, integration, E2E |
| **CI/CD** | GitHub Actions | Automated build, test, deploy |

---

## 4. Core Modules

### 4.1 Authentication & User Management

**Features:**
- Email/password registration and login
- JWT token-based authentication with refresh tokens
- Role-based access control (RBAC): Admin, Project Manager, Site Supervisor, Accounts, Tradie
- User profile management (name, phone, email, company)
- Password reset via email
- Session management and device tracking

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/auth/register | Register new user |
| POST | /api/auth/login | Login |
| POST | /api/auth/refresh | Refresh token |
| POST | /api/auth/forgot-password | Request password reset |
| POST | /api/auth/reset-password | Reset password |
| GET | /api/users/me | Get current user profile |
| PUT | /api/users/me | Update profile |
| GET | /api/users | List users (Admin only) |

---

### 4.2 Projects Module

**Features:**
- Create and manage construction projects
- Project dashboard with status overview (Not Started, In Progress, On Hold, Complete)
- Project details: address, building class (NCC), client info, contract value, dates
- Project team assignment (users and tradies)
- Project photo gallery
- Project notes and activity log

**Data Model:**
```
Project {
  id: UUID
  name: string
  address: string
  suburb: string
  state: string (NSW, VIC, QLD, etc.)
  postcode: string
  buildingClass: string (Class 1a, 2, 3, etc.)
  constructionType: string (New Build, Renovation, Extension, Fitout)
  clientName: string
  clientEmail: string
  clientPhone: string
  contractValue: decimal
  estimatedCost: decimal
  startDate: date
  estimatedEndDate: date
  actualEndDate: date?
  status: enum (NOT_STARTED, IN_PROGRESS, ON_HOLD, COMPLETE, CANCELLED)
  notes: text
  createdBy: UUID (User)
  createdAt: timestamp
  updatedAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects | List projects (with filters) |
| POST | /api/projects | Create project |
| GET | /api/projects/:id | Get project details |
| PUT | /api/projects/:id | Update project |
| DELETE | /api/projects/:id | Archive project |
| GET | /api/projects/:id/team | Get project team |
| POST | /api/projects/:id/team | Add team member |
| GET | /api/projects/:id/activity | Get activity log |

---

### 4.3 Financial Records Module

**Features:**
- Track all financial transactions per project
- Income tracking (progress claims received, client payments)
- Expense tracking (subcontractor payments, materials, plant hire)
- Invoice management (create, send, track status)
- Retention tracking (% held, release dates)
- GST calculation and BAS-ready reporting
- Payment status tracking (Draft, Submitted, Approved, Paid, Overdue)

**Data Model:**
```
Transaction {
  id: UUID
  projectId: UUID
  type: enum (INCOME, EXPENSE)
  category: enum (PROGRESS_CLAIM, SUBCONTRACTOR, MATERIALS, PLANT_HIRE, 
                   LABOUR, PERMIT_FEE, INSURANCE, OVERHEAD, OTHER)
  description: string
  amount: decimal (ex GST)
  gstAmount: decimal
  totalAmount: decimal (inc GST)
  date: date
  dueDate: date?
  paidDate: date?
  status: enum (DRAFT, SUBMITTED, APPROVED, PAID, OVERDUE, CANCELLED)
  tradieId: UUID? (if subcontractor payment)
  invoiceNumber: string?
  attachmentUrl: string?
  notes: text?
  createdBy: UUID
  createdAt: timestamp
  updatedAt: timestamp
}

Invoice {
  id: UUID
  projectId: UUID
  invoiceNumber: string (auto-generated)
  type: enum (PROGRESS_CLAIM, TAX_INVOICE)
  toName: string
  toEmail: string
  toAddress: string
  lineItems: InvoiceLineItem[]
  subtotal: decimal
  gstTotal: decimal
  total: decimal
  retentionPercent: decimal?
  retentionAmount: decimal?
  amountDue: decimal
  issueDate: date
  dueDate: date
  status: enum (DRAFT, SENT, VIEWED, PAID, OVERDUE)
  paidDate: date?
  paidAmount: decimal?
  createdBy: UUID
  createdAt: timestamp
}

InvoiceLineItem {
  id: UUID
  invoiceId: UUID
  description: string
  quantity: decimal
  unit: string (m, m², hrs, ea, lot)
  unitPrice: decimal
  amount: decimal
  costCode: string?
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects/:id/transactions | List transactions |
| POST | /api/projects/:id/transactions | Create transaction |
| PUT | /api/transactions/:id | Update transaction |
| DELETE | /api/transactions/:id | Delete transaction |
| GET | /api/projects/:id/invoices | List invoices |
| POST | /api/projects/:id/invoices | Create invoice |
| GET | /api/invoices/:id | Get invoice detail |
| PUT | /api/invoices/:id | Update invoice |
| POST | /api/invoices/:id/send | Send invoice via email |
| GET | /api/projects/:id/financial-summary | Financial summary |

---

### 4.4 Budget Management Module

**Features:**
- Create project budget with cost codes / categories
- Set budget amounts per cost code
- Track actual spend vs budget per cost code
- Variation management (approved variations adjust budget)
- Contingency tracking (% of contract, draw-down tracking)
- Cost-to-complete forecasting
- Budget alerts (over budget, approaching limit)
- Budget vs Actual comparison charts

**Data Model:**
```
BudgetCategory {
  id: UUID
  projectId: UUID
  costCode: string (e.g., "01", "02", "03.1")
  name: string (e.g., "Preliminaries", "Excavation", "Concrete - Footings")
  budgetAmount: decimal
  revisedBudget: decimal (original + approved variations)
  actualSpent: decimal (calculated from transactions)
  committed: decimal (approved orders not yet paid)
  forecastToComplete: decimal
  forecastFinal: decimal (actual + forecast to complete)
  variance: decimal (revised budget - forecast final)
  sortOrder: integer
  parentId: UUID? (for sub-categories)
  createdAt: timestamp
  updatedAt: timestamp
}

Variation {
  id: UUID
  projectId: UUID
  variationNumber: integer (auto-increment per project)
  title: string
  description: text
  amount: decimal
  status: enum (DRAFT, SUBMITTED, APPROVED, REJECTED)
  categoryId: UUID? (BudgetCategory)
  submittedDate: date?
  approvedDate: date?
  approvedBy: string?
  attachmentUrl: string?
  createdBy: UUID
  createdAt: timestamp
}

ContingencyDrawdown {
  id: UUID
  projectId: UUID
  description: string
  amount: decimal
  date: date
  approvedBy: string
  createdAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects/:id/budget | Get full budget breakdown |
| POST | /api/projects/:id/budget | Create budget category |
| PUT | /api/budget/:id | Update budget category |
| DELETE | /api/budget/:id | Delete budget category |
| GET | /api/projects/:id/budget/summary | Budget summary with variance |
| GET | /api/projects/:id/variations | List variations |
| POST | /api/projects/:id/variations | Create variation |
| PUT | /api/variations/:id | Update variation |
| GET | /api/projects/:id/contingency | Contingency status |
| POST | /api/projects/:id/contingency/drawdown | Record drawdown |

---

### 4.5 Cash Flow Planning Module

**Features:**
- Monthly cash flow forecast table and chart
- Income forecast based on progress claim schedule
- Expenditure forecast based on budget and schedule
- Actual vs forecast comparison
- Cumulative cash position over time
- Multi-project consolidated cash flow view
- Cash flow alerts (negative cash position warning)
- S-curve chart (planned vs actual expenditure over time)

**Data Model:**
```
CashFlowEntry {
  id: UUID
  projectId: UUID
  month: date (first of month)
  type: enum (FORECAST, ACTUAL)
  incomeAmount: decimal
  expenseAmount: decimal
  netCashFlow: decimal (income - expense)
  cumulativePosition: decimal
  notes: text?
  createdBy: UUID
  createdAt: timestamp
  updatedAt: timestamp
}

ClaimSchedule {
  id: UUID
  projectId: UUID
  claimNumber: integer
  description: string (e.g., "Claim 1 - Slab Complete")
  scheduledDate: date
  amount: decimal
  percentComplete: decimal
  status: enum (SCHEDULED, SUBMITTED, APPROVED, PAID)
  actualDate: date?
  actualAmount: decimal?
  createdAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects/:id/cashflow | Get cash flow entries |
| POST | /api/projects/:id/cashflow | Create/update cash flow entry |
| GET | /api/projects/:id/cashflow/forecast | Auto-generated forecast |
| GET | /api/projects/:id/cashflow/chart | Chart data (S-curve) |
| GET | /api/cashflow/consolidated | Multi-project cash flow |
| GET | /api/projects/:id/claim-schedule | Get claim schedule |
| POST | /api/projects/:id/claim-schedule | Create claim milestone |
| PUT | /api/claim-schedule/:id | Update claim |

---

### 4.6 Tradie Records Module

**Features:**
- Subcontractor / tradesperson database
- Contact details, ABN, trade type
- Licence tracking with expiry alerts
- Insurance tracking (public liability, workers comp) with expiry alerts
- Performance ratings and notes per project
- Assignment to projects
- Payment history across projects
- Document storage (licence copies, insurance certificates)
- Compliance dashboard (expired/expiring documents)

**Data Model:**
```
Tradie {
  id: UUID
  companyName: string
  contactName: string
  email: string?
  phone: string
  abn: string
  trade: enum (CARPENTER, ELECTRICIAN, PLUMBER, CONCRETER, BRICKLAYER,
               PLASTERER, PAINTER, TILER, ROOFER, LANDSCAPER, DEMOLITION,
               EXCAVATION, STEEL_FIXER, SCAFFOLDER, WATERPROOFER, 
               GLAZIER, RENDERER, CABINET_MAKER, HVAC, FIRE_SERVICES, OTHER)
  licenceNumber: string?
  licenceExpiry: date?
  insuranceProvider: string?
  insurancePolicyNumber: string?
  insuranceExpiry: date?
  workersCompProvider: string?
  workersCompPolicyNumber: string?
  workersCompExpiry: date?
  rating: integer? (1-5)
  notes: text?
  status: enum (ACTIVE, INACTIVE, BLACKLISTED)
  createdBy: UUID
  createdAt: timestamp
  updatedAt: timestamp
}

TradieDocument {
  id: UUID
  tradieId: UUID
  type: enum (LICENCE, PUBLIC_LIABILITY, WORKERS_COMP, ABN_LOOKUP, SWMS, OTHER)
  name: string
  fileUrl: string
  expiryDate: date?
  uploadedBy: UUID
  createdAt: timestamp
}

TradieProjectAssignment {
  id: UUID
  tradieId: UUID
  projectId: UUID
  role: string
  startDate: date?
  endDate: date?
  agreedRate: decimal?
  rateType: enum (HOURLY, DAILY, FIXED, SQM)
  notes: text?
  performanceRating: integer? (1-5)
  createdAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/tradies | List tradies (with search/filter) |
| POST | /api/tradies | Create tradie |
| GET | /api/tradies/:id | Get tradie details |
| PUT | /api/tradies/:id | Update tradie |
| DELETE | /api/tradies/:id | Archive tradie |
| GET | /api/tradies/:id/documents | List documents |
| POST | /api/tradies/:id/documents | Upload document |
| GET | /api/tradies/:id/projects | Projects assigned to |
| GET | /api/tradies/:id/payments | Payment history |
| GET | /api/tradies/compliance | Compliance dashboard (expiring) |
| POST | /api/projects/:id/tradies | Assign tradie to project |

---

### 4.7 Planning & Scheduling Module

**Features:**
- Gantt chart view with interactive timeline
- Task creation with start/end dates, duration
- Task dependencies (Finish-to-Start, Start-to-Start)
- Milestone markers (slab pour, frame complete, lock-up, etc.)
- Resource allocation (assign tradies/teams to tasks)
- Critical path highlighting
- Progress tracking (% complete per task)
- Drag-and-drop rescheduling
- Calendar view option
- Baseline schedule comparison
- Weather delay tracking

**Data Model:**
```
Task {
  id: UUID
  projectId: UUID
  name: string
  description: text?
  startDate: date
  endDate: date
  duration: integer (days)
  percentComplete: integer (0-100)
  status: enum (NOT_STARTED, IN_PROGRESS, COMPLETE, ON_HOLD, DELAYED)
  priority: enum (LOW, MEDIUM, HIGH, CRITICAL)
  isMilestone: boolean
  parentTaskId: UUID? (for subtasks)
  assignedTradieId: UUID?
  assignedUserId: UUID?
  costCodeId: UUID? (link to budget)
  sortOrder: integer
  colour: string? (hex for Gantt display)
  baselineStartDate: date?
  baselineEndDate: date?
  notes: text?
  createdBy: UUID
  createdAt: timestamp
  updatedAt: timestamp
}

TaskDependency {
  id: UUID
  predecessorId: UUID (Task)
  successorId: UUID (Task)
  type: enum (FINISH_TO_START, START_TO_START, FINISH_TO_FINISH, START_TO_FINISH)
  lagDays: integer (default 0)
}

DelayLog {
  id: UUID
  projectId: UUID
  taskId: UUID?
  reason: enum (WEATHER, MATERIAL_DELAY, TRADIE_NO_SHOW, INSPECTION_FAIL, 
                CLIENT_CHANGE, PERMIT_DELAY, OTHER)
  description: text
  delayDays: integer
  date: date
  createdBy: UUID
  createdAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects/:id/tasks | List tasks (Gantt data) |
| POST | /api/projects/:id/tasks | Create task |
| PUT | /api/tasks/:id | Update task |
| DELETE | /api/tasks/:id | Delete task |
| PUT | /api/tasks/:id/progress | Update progress % |
| GET | /api/projects/:id/tasks/critical-path | Get critical path |
| POST | /api/tasks/:id/dependencies | Add dependency |
| DELETE | /api/task-dependencies/:id | Remove dependency |
| GET | /api/projects/:id/delays | List delays |
| POST | /api/projects/:id/delays | Log delay |
| GET | /api/projects/:id/milestones | List milestones |

---

### 4.8 Inspections & Defects Module

**Features:**
- Create inspection checklists (pre-slab, frame, lock-up, waterproofing, final)
- NCC compliance checklists (by building class)
- Log inspection results (pass/fail per item)
- Defect logging with photos and location markup
- Defect assignment to responsible tradie
- Defect status tracking (Open, In Progress, Rectified, Verified)
- Inspection reports (PDF generation)
- Photo capture with timestamps and GPS

**Data Model:**
```
Inspection {
  id: UUID
  projectId: UUID
  type: enum (PRE_SLAB, FRAME, LOCK_UP, WATERPROOFING, FINAL, 
              DEFECT_RECTIFICATION, SAFETY, CUSTOM)
  title: string
  scheduledDate: date
  completedDate: date?
  inspectorName: string
  status: enum (SCHEDULED, IN_PROGRESS, COMPLETE, CANCELLED)
  overallResult: enum (PASS, FAIL, CONDITIONAL)?
  notes: text?
  createdBy: UUID
  createdAt: timestamp
}

InspectionItem {
  id: UUID
  inspectionId: UUID
  category: string
  description: string
  result: enum (PASS, FAIL, NA, NOT_INSPECTED)?
  notes: text?
  photoUrls: string[] 
  sortOrder: integer
}

Defect {
  id: UUID
  projectId: UUID
  inspectionId: UUID?
  title: string
  description: text
  location: string (e.g., "Master bedroom - north wall")
  severity: enum (MINOR, MODERATE, MAJOR, CRITICAL)
  status: enum (OPEN, ASSIGNED, IN_PROGRESS, RECTIFIED, VERIFIED, CLOSED)
  assignedTradieId: UUID?
  dueDate: date?
  rectifiedDate: date?
  verifiedDate: date?
  photoUrls: string[]
  rectificationPhotoUrls: string[]
  createdBy: UUID
  createdAt: timestamp
  updatedAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects/:id/inspections | List inspections |
| POST | /api/projects/:id/inspections | Create inspection |
| GET | /api/inspections/:id | Get inspection with items |
| PUT | /api/inspections/:id | Update inspection |
| POST | /api/inspections/:id/items | Add checklist items |
| PUT | /api/inspection-items/:id | Update item result |
| GET | /api/projects/:id/defects | List defects |
| POST | /api/projects/:id/defects | Create defect |
| PUT | /api/defects/:id | Update defect |
| POST | /api/defects/:id/photos | Upload defect photo |

---

### 4.9 Documents Module

**Features:**
- Upload and organise project documents
- Folder structure (Plans, Contracts, Permits, SWMS, Certificates, Photos)
- Document version control
- Search across documents
- Preview support (PDF, images)
- Share documents with team/tradies
- Mandatory document checklist per project

**Data Model:**
```
Document {
  id: UUID
  projectId: UUID
  folder: enum (PLANS, CONTRACTS, PERMITS, SWMS, CERTIFICATES, 
                INSURANCE, PHOTOS, REPORTS, CORRESPONDENCE, OTHER)
  name: string
  description: text?
  fileUrl: string
  fileSize: integer (bytes)
  mimeType: string
  version: integer
  uploadedBy: UUID
  createdAt: timestamp
  updatedAt: timestamp
}
```

**API Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/projects/:id/documents | List documents |
| POST | /api/projects/:id/documents | Upload document |
| GET | /api/documents/:id | Get document metadata |
| GET | /api/documents/:id/download | Download file |
| PUT | /api/documents/:id | Update metadata |
| DELETE | /api/documents/:id | Delete document |
| POST | /api/documents/:id/version | Upload new version |

---

## 5. UI/UX Design Principles

### 5.1 Responsive Design (Mobile-First)
- **Mobile (< 768px):** Single column layout, bottom tab navigation, large touch targets (min 44px), swipe gestures
- **Tablet (768px - 1024px):** Two column layout where appropriate, sidebar navigation
- **Desktop (> 1024px):** Full layout with sidebar, multi-panel views, Gantt chart in full width

### 5.2 Navigation Structure

**Web (Desktop/Tablet):**
```
Sidebar Navigation:
├── Dashboard (home)
├── Projects
│   └── [Project Detail]
│       ├── Overview
│       ├── Budget
│       ├── Cash Flow
│       ├── Planning (Gantt)
│       ├── Financials
│       ├── Inspections
│       ├── Documents
│       └── Team
├── Tradies
├── Financial Reports
├── Settings
└── Profile
```

**Mobile (Phone):**
```
Bottom Tab Bar:
├── Home (Dashboard)
├── Projects
├── Tradies
├── Reports
└── More (Settings, Profile)
```

### 5.3 Dashboard
- Active project count and status cards
- Upcoming milestones (next 7 days)
- Overdue tasks and defects
- Cash position summary
- Expiring tradie documents (next 30 days)
- Recent activity feed

### 5.4 Colour Palette
| Colour | Hex | Usage |
|--------|-----|-------|
| Primary | #1E40AF | Buttons, links, active states |
| Success | #059669 | Pass, paid, on track |
| Warning | #D97706 | Approaching deadline, expiring |
| Danger | #DC2626 | Overdue, failed, over budget |
| Neutral | #374151 | Text, borders |
| Background | #F9FAFB | Page background |
| Card | #FFFFFF | Card backgrounds |

---

## 6. Database Schema Overview

### Entity Relationship Summary
```
User (1) ──── (M) Project (via team assignment)
Project (1) ──── (M) Transaction
Project (1) ──── (M) Invoice ──── (M) InvoiceLineItem
Project (1) ──── (M) BudgetCategory
Project (1) ──── (M) Variation
Project (1) ──── (M) CashFlowEntry
Project (1) ──── (M) ClaimSchedule
Project (1) ──── (M) Task ──── (M) TaskDependency
Project (1) ──── (M) DelayLog
Project (1) ──── (M) Inspection ──── (M) InspectionItem
Project (1) ──── (M) Defect
Project (1) ──── (M) Document
Tradie (1) ──── (M) TradieDocument
Tradie (M) ──── (M) Project (via TradieProjectAssignment)
```

---

## 7. Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| **Response time** | API < 200ms for standard queries |
| **Availability** | 99.5% uptime |
| **Max concurrent users** | 500 |
| **Data retention** | 7 years (financial records) |
| **File upload limit** | 50MB per file |
| **Supported browsers** | Chrome, Safari, Firefox, Edge (latest 2 versions) |
| **iOS support** | iOS 16+ |
| **Accessibility** | WCAG 2.1 AA compliance |
| **Security** | HTTPS, encrypted passwords, RBAC, input validation |

---

## 8. Project Structure

```
buildmate/
├── PRODUCT_SPEC.md
├── README.md
├── docker-compose.yml
│
├── server/                    # Backend API
│   ├── package.json
│   ├── tsconfig.json
│   ├── prisma/
│   │   └── schema.prisma     # Database schema
│   └── src/
│       ├── index.ts           # Entry point
│       ├── config/
│       │   └── index.ts       # Environment config
│       ├── middleware/
│       │   ├── auth.ts        # JWT authentication
│       │   ├── validate.ts    # Request validation
│       │   └── errorHandler.ts
│       ├── routes/
│       │   ├── auth.ts
│       │   ├── projects.ts
│       │   ├── transactions.ts
│       │   ├── invoices.ts
│       │   ├── budget.ts
│       │   ├── cashflow.ts
│       │   ├── tradies.ts
│       │   ├── tasks.ts
│       │   ├── inspections.ts
│       │   ├── defects.ts
│       │   └── documents.ts
│       ├── services/          # Business logic
│       ├── utils/
│       └── types/
│
├── web/                       # React Web App
│   ├── package.json
│   ├── tsconfig.json
│   ├── tailwind.config.js
│   ├── vite.config.ts
│   ├── index.html
│   └── src/
│       ├── main.tsx
│       ├── App.tsx
│       ├── api/               # API client
│       ├── components/        # Reusable UI components
│       │   ├── ui/            # Base components (Button, Input, Card, etc.)
│       │   ├── layout/        # Layout (Sidebar, Header, BottomNav)
│       │   ├── charts/        # Chart components
│       │   └── forms/         # Form components
│       ├── pages/             # Page components
│       │   ├── Dashboard.tsx
│       │   ├── Login.tsx
│       │   ├── projects/
│       │   ├── financials/
│       │   ├── budget/
│       │   ├── cashflow/
│       │   ├── tradies/
│       │   ├── planning/
│       │   ├── inspections/
│       │   └── documents/
│       ├── hooks/             # Custom React hooks
│       ├── store/             # State management
│       ├── types/             # TypeScript types
│       └── utils/
│
└── mobile/                    # React Native iPhone App
    ├── package.json
    ├── app.json
    └── src/
        ├── App.tsx
        ├── navigation/
        ├── screens/
        ├── components/
        ├── api/
        ├── hooks/
        ├── store/
        └── types/
```

---

## 9. Development Phases

### Phase 1 — Foundation (MVP)
- [x] Product specification
- [ ] Project scaffolding (server + web)
- [ ] Database schema (Prisma)
- [ ] Authentication (register, login, JWT)
- [ ] Projects CRUD
- [ ] Basic dashboard
- [ ] Responsive layout (mobile-friendly)

### Phase 2 — Financial Core
- [ ] Transactions (income/expense tracking)
- [ ] Invoice creation and management
- [ ] Budget setup and tracking
- [ ] Budget vs Actual charts
- [ ] Variation management

### Phase 3 — Planning & Tradies
- [ ] Tradie database with CRUD
- [ ] Licence/insurance tracking with alerts
- [ ] Task/schedule management
- [ ] Gantt chart view
- [ ] Task dependencies and milestones

### Phase 4 — Cash Flow & Reports
- [ ] Cash flow planning and forecasting
- [ ] Claim schedule management
- [ ] S-curve charts
- [ ] Financial reports dashboard
- [ ] PDF report generation

### Phase 5 — Inspections & Documents
- [ ] Inspection checklists
- [ ] Defect management with photos
- [ ] Document upload and organisation
- [ ] Document version control

### Phase 6 — Mobile App
- [ ] React Native project setup
- [ ] Core screens (Dashboard, Projects, Tradies)
- [ ] Photo capture for inspections/defects
- [ ] Push notifications
- [ ] Offline mode for on-site use

---

## 10. Getting Started

```bash
# Clone the repository
cd buildmate

# Install server dependencies
cd server && npm install

# Set up database
cp .env.example .env
npx prisma migrate dev

# Start server
npm run dev

# In another terminal - install web dependencies
cd ../web && npm install

# Start web app
npm run dev
```

---

*End of Product Specification*
