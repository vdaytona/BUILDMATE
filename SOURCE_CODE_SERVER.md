# BuildMate — Server Core Source Code

## Table of Contents
- server/package.json
- server/tsconfig.json
- server/.env.example
- server/prisma/schema.prisma
- server/prisma/seed.ts
- server/src/index.ts
- server/src/config/index.ts
- server/src/types/index.ts
- server/src/middleware/auth.ts
- server/src/middleware/errorHandler.ts
- server/src/middleware/validate.ts
- server/src/lib/prisma.ts
- server/src/lib/sanitize.ts
- server/src/lib/auditLog.ts

---


## server/package.json

```json
{
  "name": "buildmate-server",
  "version": "1.0.0",
  "description": "BuildMate Construction Management API",
  "main": "dist/index.js",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:studio": "prisma studio"
  },
  "dependencies": {
    "@prisma/client": "^6.3.0",
    "bcryptjs": "^2.4.3",
    "cors": "^2.8.5",
    "dotenv": "^16.4.7",
    "express": "^4.21.2",
    "express-rate-limit": "^8.3.1",
    "helmet": "^8.0.0",
    "jsonwebtoken": "^9.0.2",
    "morgan": "^1.10.0",
    "multer": "^1.4.5-lts.1",
    "zod": "^3.24.1"
  },
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  },
  "devDependencies": {
    "@types/bcryptjs": "^2.4.6",
    "@types/cors": "^2.8.17",
    "@types/express": "^5.0.0",
    "@types/jsonwebtoken": "^9.0.7",
    "@types/morgan": "^1.9.9",
    "@types/multer": "^1.4.12",
    "@types/supertest": "^7.2.0",
    "@vitest/coverage-v8": "^4.1.2",
    "nodemon": "^3.1.9",
    "prisma": "^6.3.0",
    "supertest": "^7.2.2",
    "ts-node": "^10.9.2",
    "tsx": "^4.19.2",
    "typescript": "^5.7.3",
    "vitest": "^4.1.2"
  }
}

```

## server/tsconfig.json

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
  "exclude": ["node_modules", "dist", "src/**/*.test.ts", "src/__tests__"]
}

```

## server/.env.example

```bash
# ─── Database ────────────────────────────────────────
# SQLite (development)
DATABASE_URL="file:./dev.db"
# PostgreSQL (staging/production)
# DATABASE_URL="postgresql://user:password@localhost:5432/buildmate?schema=public"

# ─── Authentication ──────────────────────────────────
JWT_SECRET="your-secret-key-change-in-production"
JWT_REFRESH_SECRET="your-refresh-secret-change-in-production"

# ─── Server ──────────────────────────────────────────
PORT=3001
NODE_ENV="development"

# ─── CORS ────────────────────────────────────────────
CORS_ORIGIN="http://localhost:5173"

# ─── File Uploads ────────────────────────────────────
UPLOAD_DIR="./uploads"
MAX_FILE_SIZE_MB=50

```

## server/prisma/schema.prisma

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

// ──────────────────────────────────────────────
// Models
// ──────────────────────────────────────────────
// Note: SQLite does not support enums. All enum fields use String type.
// Valid values are documented in comments above each field.

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  password  String
  name      String
  phone     String?
  // Values: ADMIN, PROJECT_MANAGER, SITE_SUPERVISOR, ACCOUNTS, TRADIE
  role      String   @default("TRADIE")
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  projectsCreated    Project[]               @relation("ProjectCreatedBy")
  projectTeams       ProjectTeam[]
  transactionsCreated Transaction[]           @relation("TransactionCreatedBy")
  invoicesCreated    Invoice[]               @relation("InvoiceCreatedBy")
  variationsCreated  Variation[]             @relation("VariationCreatedBy")
  cashFlowEntries    CashFlowEntry[]         @relation("CashFlowCreatedBy")
  tradiesCreated     Tradie[]                @relation("TradieCreatedBy")
  documentsUploaded  TradieDocument[]        @relation("TradieDocUploadedBy")
  tasksCreated       Task[]                  @relation("TaskCreatedBy")
  tasksAssigned      Task[]                  @relation("TaskAssignedUser")
  delayLogs          DelayLog[]              @relation("DelayLogCreatedBy")
  inspectionsCreated Inspection[]            @relation("InspectionCreatedBy")
  defectsCreated     Defect[]                @relation("DefectCreatedBy")
  projectDocuments   Document[]              @relation("DocumentUploadedBy")
  auditLogs          AuditLog[]

  @@index([email])
}

model Project {
  id               String    @id @default(uuid())
  name             String
  address          String?
  suburb           String?
  state            String?
  postcode         String?
  buildingClass    String?
  constructionType String?
  clientName       String?
  clientEmail      String?
  clientPhone      String?
  contractValue    Decimal?
  estimatedCost    Decimal?
  startDate        DateTime?
  estimatedEndDate DateTime?
  actualEndDate    DateTime?
  // Values: NOT_STARTED, IN_PROGRESS, ON_HOLD, COMPLETE, CANCELLED
  status           String    @default("NOT_STARTED")
  notes            String?
  createdById      String
  createdAt        DateTime  @default(now())
  updatedAt        DateTime  @updatedAt

  createdBy              User                     @relation("ProjectCreatedBy", fields: [createdById], references: [id])
  team                   ProjectTeam[]
  transactions           Transaction[]
  invoices               Invoice[]
  budgetCategories       BudgetCategory[]
  variations             Variation[]
  cashFlowEntries        CashFlowEntry[]
  claimSchedules         ClaimSchedule[]
  tradieAssignments      TradieProjectAssignment[]
  tasks                  Task[]
  delayLogs              DelayLog[]
  inspections            Inspection[]
  defects                Defect[]
  documents              Document[]
  contingencyDrawdowns   ContingencyDrawdown[]

  contingencyAmount Decimal?

  @@index([status])
  @@index([createdById])
}

model ProjectTeam {
  id        String   @id @default(uuid())
  projectId String
  userId    String
  role      String
  createdAt DateTime @default(now())

  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  user    User    @relation(fields: [userId], references: [id])

  @@unique([projectId, userId])
  @@index([projectId])
  @@index([userId])
}

model Transaction {
  id            String   @id @default(uuid())
  projectId     String
  // Values: INCOME, EXPENSE
  type          String
  // Values: PROGRESS_CLAIM, SUBCONTRACTOR, MATERIALS, PLANT_HIRE, LABOUR, PERMIT_FEE, INSURANCE, OVERHEAD, OTHER
  category      String
  description   String
  amount        Decimal
  gstAmount     Decimal
  totalAmount   Decimal
  date          DateTime
  dueDate       DateTime?
  paidDate      DateTime?
  // Values: DRAFT, SUBMITTED, APPROVED, PAID, OVERDUE, CANCELLED
  status        String   @default("DRAFT")
  tradieId      String?
  invoiceNumber String?
  attachmentUrl String?
  notes         String?
  createdById   String
  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt

  project   Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  tradie    Tradie? @relation(fields: [tradieId], references: [id])
  createdBy User    @relation("TransactionCreatedBy", fields: [createdById], references: [id])

  @@index([projectId])
  @@index([tradieId])
  @@index([status])
  @@index([date])
}

model Invoice {
  id               String   @id @default(uuid())
  projectId        String
  invoiceNumber    String
  // Values: PROGRESS_CLAIM, TAX_INVOICE
  type             String
  toName           String
  toEmail          String?
  toAddress        String?
  subtotal         Decimal
  gstTotal         Decimal
  total            Decimal
  retentionPercent Decimal?
  retentionAmount  Decimal?
  amountDue        Decimal
  issueDate        DateTime
  dueDate          DateTime
  // Values: DRAFT, SENT, VIEWED, PAID, OVERDUE
  status           String   @default("DRAFT")
  paidDate         DateTime?
  paidAmount       Decimal?
  createdById      String
  createdAt        DateTime @default(now())
  updatedAt        DateTime @updatedAt

  project   Project           @relation(fields: [projectId], references: [id], onDelete: Cascade)
  createdBy User              @relation("InvoiceCreatedBy", fields: [createdById], references: [id])
  lineItems InvoiceLineItem[]

  @@index([projectId])
  @@index([status])
  @@index([invoiceNumber])
}

model InvoiceLineItem {
  id          String  @id @default(uuid())
  invoiceId   String
  description String
  quantity    Decimal
  unit        String?
  unitPrice   Decimal
  amount      Decimal
  costCode    String?

  invoice Invoice @relation(fields: [invoiceId], references: [id], onDelete: Cascade)

  @@index([invoiceId])
}

model BudgetCategory {
  id                 String   @id @default(uuid())
  projectId          String
  costCode           String
  name               String
  budgetAmount       Decimal
  revisedBudget      Decimal?
  forecastToComplete Decimal?
  sortOrder          Int      @default(0)
  parentId           String?
  createdAt          DateTime @default(now())
  updatedAt          DateTime @updatedAt

  project    Project          @relation(fields: [projectId], references: [id], onDelete: Cascade)
  parent     BudgetCategory?  @relation("BudgetCategoryTree", fields: [parentId], references: [id])
  children   BudgetCategory[] @relation("BudgetCategoryTree")
  variations Variation[]
  tasks      Task[]           @relation("TaskCostCode")

  @@unique([projectId, costCode])
  @@index([projectId])
  @@index([parentId])
}

model Variation {
  id              String    @id @default(uuid())
  projectId       String
  variationNumber Int
  title           String
  description     String?
  amount          Decimal
  // Values: DRAFT, SUBMITTED, APPROVED, REJECTED
  status          String    @default("DRAFT")
  categoryId      String?
  submittedDate   DateTime?
  approvedDate    DateTime?
  approvedBy      String?
  attachmentUrl   String?
  createdById     String
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt

  project   Project         @relation(fields: [projectId], references: [id], onDelete: Cascade)
  category  BudgetCategory? @relation(fields: [categoryId], references: [id])
  createdBy User            @relation("VariationCreatedBy", fields: [createdById], references: [id])

  @@unique([projectId, variationNumber])
  @@index([projectId])
  @@index([status])
}

model CashFlowEntry {
  id            String   @id @default(uuid())
  projectId     String
  month         DateTime
  // Values: FORECAST, ACTUAL
  type          String
  incomeAmount  Decimal
  expenseAmount Decimal
  notes         String?
  createdById   String
  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt

  project   Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  createdBy User    @relation("CashFlowCreatedBy", fields: [createdById], references: [id])

  @@index([projectId])
  @@index([month])
}

model ClaimSchedule {
  id              String    @id @default(uuid())
  projectId       String
  claimNumber     Int
  description     String
  scheduledDate   DateTime
  amount          Decimal
  percentComplete Decimal
  // Values: SCHEDULED, SUBMITTED, APPROVED, PAID
  status          String    @default("SCHEDULED")
  actualDate      DateTime?
  actualAmount    Decimal?
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt

  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)

  @@unique([projectId, claimNumber])
  @@index([projectId])
}

model Tradie {
  id                      String    @id @default(uuid())
  companyName             String
  contactName             String
  email                   String?
  phone                   String?
  abn                     String?
  // Values: BUILDER, CARPENTER, ELECTRICIAN, PLUMBER, PLASTERER, PAINTER, TILER, BRICKLAYER, CONCRETER, ROOFER, LANDSCAPER, DEMOLITION, EXCAVATION, STEEL_FIXER, CABINET_MAKER, GLAZIER, RENDERER, WATERPROOFER, INSULATION, FENCER, SCAFFOLDER, CLEANER, HVAC, FIRE_PROTECTION, SECURITY_SYSTEMS, FLOORING, STONEMASON, POOL_BUILDER, SURVEYOR, ENGINEER, ARCHITECT, DRAFTSPERSON, OTHER
  trade                   String
  licenceNumber           String?
  licenceExpiry           DateTime?
  insuranceProvider       String?
  insurancePolicyNumber   String?
  insuranceExpiry         DateTime?
  workersCompProvider     String?
  workersCompPolicyNumber String?
  workersCompExpiry       DateTime?
  rating                  Int?
  notes                   String?
  // Values: ACTIVE, INACTIVE, BLACKLISTED
  status                  String    @default("ACTIVE")
  createdById             String
  createdAt               DateTime  @default(now())
  updatedAt               DateTime  @updatedAt

  createdBy          User                     @relation("TradieCreatedBy", fields: [createdById], references: [id])
  documents          TradieDocument[]
  projectAssignments TradieProjectAssignment[]
  transactions       Transaction[]
  tasksAssigned      Task[]                   @relation("TaskAssignedTradie")
  defectsAssigned    Defect[]                 @relation("DefectAssignedTradie")

  @@index([trade])
  @@index([status])
  @@index([email])
}

model TradieDocument {
  id           String    @id @default(uuid())
  tradieId     String
  // Values: LICENCE, PUBLIC_LIABILITY, WORKERS_COMP, ABN_LOOKUP, SWMS, OTHER
  type         String
  name         String
  fileUrl      String
  expiryDate   DateTime?
  uploadedById String
  createdAt    DateTime  @default(now())

  tradie     Tradie @relation(fields: [tradieId], references: [id], onDelete: Cascade)
  uploadedBy User   @relation("TradieDocUploadedBy", fields: [uploadedById], references: [id])

  @@index([tradieId])
  @@index([expiryDate])
}

model TradieProjectAssignment {
  id                String    @id @default(uuid())
  tradieId          String
  projectId         String
  role              String?
  startDate         DateTime?
  endDate           DateTime?
  agreedRate        Decimal?
  // Values: HOURLY, DAILY, FIXED, SQM
  rateType          String?
  notes             String?
  performanceRating Int?
  createdAt         DateTime  @default(now())

  tradie  Tradie  @relation(fields: [tradieId], references: [id], onDelete: Cascade)
  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)

  @@unique([tradieId, projectId])
  @@index([tradieId])
  @@index([projectId])
}

model Task {
  id                String   @id @default(uuid())
  projectId         String
  name              String
  description       String?
  startDate         DateTime?
  endDate           DateTime?
  duration          Int?
  percentComplete   Int      @default(0)
  // Values: NOT_STARTED, IN_PROGRESS, COMPLETE, ON_HOLD, DELAYED
  status            String   @default("NOT_STARTED")
  // Values: LOW, MEDIUM, HIGH, CRITICAL
  priority          String   @default("MEDIUM")
  // Values: INITIATION, DESIGN, APPROVALS, SITE_PREP, BASE, FRAME, LOCK_UP, FIT_OFF, FIXING, COMPLETION, HANDOVER, DEFECT_LIABILITY
  stage             String?
  isMilestone       Boolean  @default(false)
  parentTaskId      String?
  assignedTradieId  String?
  assignedUserId    String?
  costCodeId        String?
  sortOrder         Int      @default(0)
  colour            String?
  baselineStartDate DateTime?
  baselineEndDate   DateTime?
  notes             String?
  createdById       String
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt

  project        Project         @relation(fields: [projectId], references: [id], onDelete: Cascade)
  parentTask     Task?           @relation("TaskTree", fields: [parentTaskId], references: [id])
  childTasks     Task[]          @relation("TaskTree")
  assignedTradie Tradie?         @relation("TaskAssignedTradie", fields: [assignedTradieId], references: [id])
  assignedUser   User?           @relation("TaskAssignedUser", fields: [assignedUserId], references: [id])
  costCode       BudgetCategory? @relation("TaskCostCode", fields: [costCodeId], references: [id])
  createdBy      User            @relation("TaskCreatedBy", fields: [createdById], references: [id])
  predecessors   TaskDependency[] @relation("TaskSuccessor")
  successors     TaskDependency[] @relation("TaskPredecessor")
  delayLogs      DelayLog[]

  @@index([projectId])
  @@index([status])
  @@index([parentTaskId])
  @@index([assignedTradieId])
  @@index([assignedUserId])
}

model TaskDependency {
  id            String @id @default(uuid())
  predecessorId String
  successorId   String
  // Values: FINISH_TO_START, START_TO_START, FINISH_TO_FINISH, START_TO_FINISH
  type          String @default("FINISH_TO_START")
  lagDays       Int    @default(0)

  predecessor Task @relation("TaskPredecessor", fields: [predecessorId], references: [id], onDelete: Cascade)
  successor   Task @relation("TaskSuccessor", fields: [successorId], references: [id], onDelete: Cascade)

  @@unique([predecessorId, successorId])
  @@index([predecessorId])
  @@index([successorId])
}

model DelayLog {
  id          String   @id @default(uuid())
  projectId   String
  taskId      String?
  // Values: WEATHER, MATERIAL_DELAY, TRADIE_NO_SHOW, INSPECTION_FAIL, CLIENT_CHANGE, PERMIT_DELAY, OTHER
  reason      String
  description String?
  delayDays   Int
  date        DateTime
  createdById String
  createdAt   DateTime @default(now())

  project   Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  task      Task?   @relation(fields: [taskId], references: [id])
  createdBy User    @relation("DelayLogCreatedBy", fields: [createdById], references: [id])

  @@index([projectId])
  @@index([taskId])
  @@index([date])
}

model Inspection {
  id            String    @id @default(uuid())
  projectId     String
  // Values: PRE_SLAB, FRAME, LOCK_UP, WATERPROOFING, FINAL, DEFECT_RECTIFICATION, SAFETY, CUSTOM
  type          String
  title         String
  scheduledDate DateTime
  completedDate DateTime?
  inspectorName String?
  // Values: SCHEDULED, IN_PROGRESS, COMPLETE, CANCELLED
  status        String    @default("SCHEDULED")
  // Values: PASS, FAIL, CONDITIONAL
  overallResult String?
  notes         String?
  createdById   String
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt

  project   Project          @relation(fields: [projectId], references: [id], onDelete: Cascade)
  createdBy User             @relation("InspectionCreatedBy", fields: [createdById], references: [id])
  items     InspectionItem[]
  defects   Defect[]

  @@index([projectId])
  @@index([status])
  @@index([scheduledDate])
}

model InspectionItem {
  id           String @id @default(uuid())
  inspectionId String
  category     String
  description  String
  // Values: PASS, FAIL, NA, NOT_INSPECTED
  result       String @default("NOT_INSPECTED")
  notes        String?
  // JSON string array, e.g. '["url1","url2"]'
  photoUrls    String @default("[]")
  sortOrder    Int    @default(0)

  inspection Inspection @relation(fields: [inspectionId], references: [id], onDelete: Cascade)

  @@index([inspectionId])
}

model Defect {
  id                     String    @id @default(uuid())
  projectId              String
  inspectionId           String?
  title                  String
  description            String?
  location               String?
  // Values: MINOR, MODERATE, MAJOR, CRITICAL
  severity               String
  // Values: OPEN, ASSIGNED, IN_PROGRESS, RECTIFIED, VERIFIED, CLOSED
  status                 String    @default("OPEN")
  assignedTradieId       String?
  dueDate                DateTime?
  rectifiedDate          DateTime?
  verifiedDate           DateTime?
  // JSON string arrays, e.g. '["url1","url2"]'
  photoUrls              String    @default("[]")
  rectificationPhotoUrls String    @default("[]")
  createdById            String
  createdAt              DateTime  @default(now())
  updatedAt              DateTime  @updatedAt

  project        Project     @relation(fields: [projectId], references: [id], onDelete: Cascade)
  inspection     Inspection? @relation(fields: [inspectionId], references: [id])
  assignedTradie Tradie?     @relation("DefectAssignedTradie", fields: [assignedTradieId], references: [id])
  createdBy      User        @relation("DefectCreatedBy", fields: [createdById], references: [id])

  @@index([projectId])
  @@index([inspectionId])
  @@index([status])
  @@index([assignedTradieId])
}

model Document {
  id           String   @id @default(uuid())
  projectId    String
  // Values: PLANS, CONTRACTS, PERMITS, SWMS, CERTIFICATES, INSURANCE, PHOTOS, REPORTS, CORRESPONDENCE, OTHER
  folder       String
  name         String
  description  String?
  fileUrl      String
  fileSize     Int
  mimeType     String?
  version      Int      @default(1)
  uploadedById String
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt

  project    Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  uploadedBy User    @relation("DocumentUploadedBy", fields: [uploadedById], references: [id])

  @@index([projectId])
  @@index([folder])
}

model ContingencyDrawdown {
  id          String   @id @default(uuid())
  projectId   String
  description String
  amount      Decimal
  date        DateTime
  approvedBy  String?
  createdAt   DateTime @default(now())

  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)

  @@index([projectId])
}

model AuditLog {
  id         String   @id @default(uuid())
  userId     String
  action     String   // CREATE, UPDATE, DELETE
  entityType String   // Project, Task, Transaction, etc.
  entityId   String
  changes    String?  // JSON string of changes
  createdAt  DateTime @default(now())

  user User @relation(fields: [userId], references: [id])

  @@index([entityType, entityId])
  @@index([userId])
  @@index([createdAt])
}

```

## server/prisma/seed.ts

```typescript
import { PrismaClient } from '@prisma/client';
import bcrypt from 'bcryptjs';

const prisma = new PrismaClient();

async function main() {
  // Clean existing data (order matters for foreign key constraints)
  await prisma.inspectionItem.deleteMany();
  await prisma.defect.deleteMany();
  await prisma.inspection.deleteMany();
  await prisma.delayLog.deleteMany();
  await prisma.taskDependency.deleteMany();
  await prisma.task.deleteMany();
  await prisma.cashFlowEntry.deleteMany();
  await prisma.variation.deleteMany();
  await prisma.budgetCategory.deleteMany();
  await prisma.claimSchedule.deleteMany();
  await prisma.invoiceLineItem.deleteMany();
  await prisma.invoice.deleteMany();
  await prisma.transaction.deleteMany();
  await prisma.tradieDocument.deleteMany();
  await prisma.tradieProjectAssignment.deleteMany();
  await prisma.document.deleteMany();
  await prisma.projectTeam.deleteMany();
  await prisma.tradie.deleteMany();
  await prisma.project.deleteMany();
  await prisma.user.deleteMany();

  console.log('🧹 Cleared existing data');

  // ──────────────────────────────────────────────
  // 1. Admin User
  // ──────────────────────────────────────────────
  const hashedPassword = await bcrypt.hash('Password123!', 10);

  const admin = await prisma.user.create({
    data: {
      email: 'admin@buildmate.com',
      password: hashedPassword,
      name: 'Admin User',
      phone: '0412 345 678',
      role: 'ADMIN',
    },
  });

  console.log('👤 Created admin user');

  // ──────────────────────────────────────────────
  // 2. Projects
  // ──────────────────────────────────────────────
  const project1 = await prisma.project.create({
    data: {
      name: 'Thornbury Duplex Development',
      address: '42 Station Street',
      suburb: 'Thornbury',
      state: 'VIC',
      postcode: '3071',
      clientName: 'David & Sarah Mitchell',
      clientEmail: 'mitchell.ds@email.com',
      clientPhone: '0413 222 333',
      contractValue: 1850000,
      startDate: new Date('2026-01-15'),
      estimatedEndDate: new Date('2026-09-30'),
      status: 'IN_PROGRESS',
      notes: 'Two-storey duplex on existing block. Heritage overlay applies to front facade.',
      createdById: admin.id,
    },
  });

  const project2 = await prisma.project.create({
    data: {
      name: 'Manly Beach House Renovation',
      address: '18 Bower Street',
      suburb: 'Manly',
      state: 'NSW',
      postcode: '2095',
      clientName: 'James & Emily Chen',
      clientEmail: 'jchen.property@email.com',
      clientPhone: '0422 876 543',
      contractValue: 720000,
      startDate: new Date('2026-03-01'),
      estimatedEndDate: new Date('2026-08-15'),
      status: 'NOT_STARTED',
      notes: 'Full renovation of 1960s beach house. Council DA approved with conditions.',
      createdById: admin.id,
    },
  });

  console.log('🏗️  Created 2 projects');

  // ──────────────────────────────────────────────
  // 3. Tradies
  // ──────────────────────────────────────────────
  const tradies = await Promise.all([
    prisma.tradie.create({
      data: {
        companyName: 'Spark Right Electrical',
        contactName: 'Mike Thompson',
        email: 'mike@sparkright.com.au',
        phone: '0411 456 789',
        abn: '51 824 753 166',
        trade: 'ELECTRICIAN',
        licenceNumber: 'EC12345',
        licenceExpiry: new Date('2027-06-30'),
        insuranceExpiry: new Date('2027-03-15'),
        workersCompExpiry: new Date('2027-01-31'),
        rating: 5,
        status: 'ACTIVE',
        createdById: admin.id,
      },
    }),
    prisma.tradie.create({
      data: {
        companyName: 'BluePipe Plumbing',
        contactName: 'Steve Papadopoulos',
        email: 'steve@bluepipe.com.au',
        phone: '0422 111 888',
        abn: '33 619 284 571',
        trade: 'PLUMBER',
        licenceNumber: 'PL44821',
        licenceExpiry: new Date('2027-09-30'),
        insuranceExpiry: new Date('2027-04-20'),
        workersCompExpiry: new Date('2027-02-28'),
        rating: 4,
        status: 'ACTIVE',
        createdById: admin.id,
      },
    }),
    prisma.tradie.create({
      data: {
        companyName: 'Ironbark Carpentry',
        contactName: 'Ben O\'Sullivan',
        email: 'ben@ironbarkcarpentry.com.au',
        phone: '0433 777 222',
        abn: '17 452 938 620',
        trade: 'CARPENTER',
        licenceNumber: 'CB-78512',
        licenceExpiry: new Date('2027-12-31'),
        insuranceExpiry: new Date('2027-06-30'),
        workersCompExpiry: new Date('2027-06-30'),
        rating: 5,
        status: 'ACTIVE',
        createdById: admin.id,
      },
    }),
    prisma.tradie.create({
      data: {
        companyName: 'TopCoat Rendering',
        contactName: 'Marco Rossi',
        email: 'marco@topcoat.com.au',
        phone: '0444 333 111',
        abn: '82 731 456 912',
        trade: 'RENDERER',
        licenceNumber: 'REN-2241',
        licenceExpiry: new Date('2027-08-31'),
        insuranceExpiry: new Date('2027-05-15'),
        workersCompExpiry: new Date('2027-03-31'),
        rating: 3,
        status: 'ACTIVE',
        createdById: admin.id,
      },
    }),
    prisma.tradie.create({
      data: {
        companyName: 'SolidBase Concreting',
        contactName: 'Tony Nguyen',
        email: 'tony@solidbase.com.au',
        phone: '0455 666 999',
        abn: '64 198 573 840',
        trade: 'CONCRETER',
        licenceNumber: 'CON-90234',
        licenceExpiry: new Date('2028-02-28'),
        insuranceExpiry: new Date('2027-07-31'),
        workersCompExpiry: new Date('2027-07-31'),
        rating: 4,
        status: 'ACTIVE',
        createdById: admin.id,
      },
    }),
  ]);

  console.log('🔧 Created 5 tradies');

  // ──────────────────────────────────────────────
  // 4. Budget Categories (12 total: 7 for project1, 5 for project2)
  // ──────────────────────────────────────────────
  const budgetCategories = await Promise.all([
    // Project 1 - Thornbury Duplex
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '1000', name: 'Preliminaries', budgetAmount: 85000, sortOrder: 1 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '2000', name: 'Demolition & Earthworks', budgetAmount: 120000, sortOrder: 2 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '3000', name: 'Concrete & Footings', budgetAmount: 195000, sortOrder: 3 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '4000', name: 'Structural Steel & Framing', budgetAmount: 280000, sortOrder: 4 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '5000', name: 'Electrical', budgetAmount: 145000, sortOrder: 5 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '6000', name: 'Plumbing & Drainage', budgetAmount: 165000, sortOrder: 6 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project1.id, costCode: '7000', name: 'External Render & Cladding', budgetAmount: 110000, sortOrder: 7 },
    }),
    // Project 2 - Manly Beach House
    prisma.budgetCategory.create({
      data: { projectId: project2.id, costCode: '1000', name: 'Site Establishment', budgetAmount: 35000, sortOrder: 1 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project2.id, costCode: '2000', name: 'Strip-Out & Demolition', budgetAmount: 55000, sortOrder: 2 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project2.id, costCode: '3000', name: 'Structural Alterations', budgetAmount: 140000, sortOrder: 3 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project2.id, costCode: '4000', name: 'Electrical Refit', budgetAmount: 85000, sortOrder: 4 },
    }),
    prisma.budgetCategory.create({
      data: { projectId: project2.id, costCode: '5000', name: 'Plumbing & Wet Areas', budgetAmount: 95000, sortOrder: 5 },
    }),
  ]);

  console.log('📊 Created 12 budget categories');

  // ──────────────────────────────────────────────
  // 5. Tasks (16 total: 10 for project1, 6 for project2)
  // ──────────────────────────────────────────────
  await Promise.all([
    // Project 1 - Thornbury Duplex
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Site establishment & fencing', description: 'Install temporary fencing, site shed, and amenities',
        startDate: new Date('2026-01-15'), endDate: new Date('2026-01-22'), duration: 5, percentComplete: 100,
        status: 'COMPLETE', priority: 'HIGH', isMilestone: false, sortOrder: 1, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Demolition of existing garage', description: 'Remove existing garage structure and dispose of waste',
        startDate: new Date('2026-01-23'), endDate: new Date('2026-01-30'), duration: 5, percentComplete: 100,
        status: 'COMPLETE', priority: 'MEDIUM', isMilestone: false, sortOrder: 2, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Excavation & site cut', description: 'Bulk excavation for footings and slab',
        startDate: new Date('2026-02-02'), endDate: new Date('2026-02-13'), duration: 10, percentComplete: 100,
        status: 'COMPLETE', priority: 'HIGH', isMilestone: false, assignedTradieId: tradies[4].id, sortOrder: 3, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Footings & slab pour', description: 'Form, reinforce, and pour concrete footings and slab',
        startDate: new Date('2026-02-16'), endDate: new Date('2026-03-06'), duration: 15, percentComplete: 75,
        status: 'IN_PROGRESS', priority: 'CRITICAL', isMilestone: false, assignedTradieId: tradies[4].id, sortOrder: 4,
        costCodeId: budgetCategories[2].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Slab inspection — milestone', description: 'Council pre-slab inspection must pass before proceeding',
        startDate: new Date('2026-03-09'), endDate: new Date('2026-03-09'), duration: 1, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'CRITICAL', isMilestone: true, sortOrder: 5, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Timber wall framing — Unit A', description: 'Erect wall frames for duplex Unit A',
        startDate: new Date('2026-03-10'), endDate: new Date('2026-04-03'), duration: 20, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'HIGH', isMilestone: false, assignedTradieId: tradies[2].id,
        sortOrder: 6, costCodeId: budgetCategories[3].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Rough-in electrical', description: 'First-fix electrical wiring and conduit',
        startDate: new Date('2026-04-06'), endDate: new Date('2026-04-17'), duration: 10, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'MEDIUM', isMilestone: false, assignedTradieId: tradies[0].id,
        sortOrder: 7, costCodeId: budgetCategories[4].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Rough-in plumbing', description: 'First-fix plumbing and drainage installation',
        startDate: new Date('2026-04-06'), endDate: new Date('2026-04-17'), duration: 10, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'MEDIUM', isMilestone: false, assignedTradieId: tradies[1].id,
        sortOrder: 8, costCodeId: budgetCategories[5].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'Frame inspection — milestone', description: 'Council frame stage inspection',
        startDate: new Date('2026-04-20'), endDate: new Date('2026-04-20'), duration: 1, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'HIGH', isMilestone: true, sortOrder: 9, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project1.id, name: 'External render & cladding', description: 'Apply render to external walls and install feature cladding',
        startDate: new Date('2026-05-04'), endDate: new Date('2026-05-22'), duration: 15, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'MEDIUM', isMilestone: false, assignedTradieId: tradies[3].id,
        sortOrder: 10, costCodeId: budgetCategories[6].id, createdById: admin.id,
      },
    }),
    // Project 2 - Manly Beach House
    prisma.task.create({
      data: {
        projectId: project2.id, name: 'Site setup & protection', description: 'Establish site access, protect neighbouring property',
        startDate: new Date('2026-03-01'), endDate: new Date('2026-03-04'), duration: 3, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'HIGH', isMilestone: false, sortOrder: 1, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project2.id, name: 'Internal strip-out', description: 'Remove existing kitchen, bathrooms, and internal walls',
        startDate: new Date('2026-03-05'), endDate: new Date('2026-03-16'), duration: 8, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'MEDIUM', isMilestone: false, sortOrder: 2, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project2.id, name: 'Structural steel beam installation', description: 'Install steel beams for open-plan living area',
        startDate: new Date('2026-03-17'), endDate: new Date('2026-03-27'), duration: 9, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'CRITICAL', isMilestone: false, sortOrder: 3,
        costCodeId: budgetCategories[9].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project2.id, name: 'Rewire entire house', description: 'Complete electrical refit to current standards',
        startDate: new Date('2026-03-30'), endDate: new Date('2026-04-10'), duration: 10, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'HIGH', isMilestone: false, assignedTradieId: tradies[0].id,
        sortOrder: 4, costCodeId: budgetCategories[10].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project2.id, name: 'New plumbing & wet area rough-in', description: 'Rerun plumbing for new bathroom and kitchen layouts',
        startDate: new Date('2026-03-30'), endDate: new Date('2026-04-10'), duration: 10, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'HIGH', isMilestone: false, assignedTradieId: tradies[1].id,
        sortOrder: 5, costCodeId: budgetCategories[11].id, createdById: admin.id,
      },
    }),
    prisma.task.create({
      data: {
        projectId: project2.id, name: 'Practical completion — milestone', description: 'Final walkthrough and handover',
        startDate: new Date('2026-08-14'), endDate: new Date('2026-08-14'), duration: 1, percentComplete: 0,
        status: 'NOT_STARTED', priority: 'CRITICAL', isMilestone: true, sortOrder: 6, createdById: admin.id,
      },
    }),
  ]);

  console.log('📋 Created 16 tasks');

  // ──────────────────────────────────────────────
  // 6. Transactions (8 total: 5 for project1, 3 for project2)
  // ──────────────────────────────────────────────
  await Promise.all([
    // Project 1
    prisma.transaction.create({
      data: {
        projectId: project1.id, type: 'INCOME', category: 'PROGRESS_CLAIM', description: 'Progress Claim #1 — Site establishment & demolition',
        amount: 185000, gstAmount: 18500, totalAmount: 203500,
        date: new Date('2026-01-31'), dueDate: new Date('2026-02-14'), paidDate: new Date('2026-02-10'),
        status: 'PAID', invoiceNumber: 'PC-001', createdById: admin.id,
      },
    }),
    prisma.transaction.create({
      data: {
        projectId: project1.id, type: 'EXPENSE', category: 'SUBCONTRACTOR', description: 'SolidBase Concreting — Excavation works',
        amount: 48000, gstAmount: 4800, totalAmount: 52800,
        date: new Date('2026-02-15'), dueDate: new Date('2026-03-15'),
        status: 'APPROVED', tradieId: tradies[4].id, invoiceNumber: 'SB-2026-041', createdById: admin.id,
      },
    }),
    prisma.transaction.create({
      data: {
        projectId: project1.id, type: 'EXPENSE', category: 'MATERIALS', description: 'Boral — Ready-mix concrete supply (N32 grade)',
        amount: 32500, gstAmount: 3250, totalAmount: 35750,
        date: new Date('2026-02-20'), dueDate: new Date('2026-03-20'),
        status: 'SUBMITTED', invoiceNumber: 'BOR-87234', createdById: admin.id,
      },
    }),
    prisma.transaction.create({
      data: {
        projectId: project1.id, type: 'EXPENSE', category: 'PERMIT_FEE', description: 'Darebin City Council — Building permit fees',
        amount: 12800, gstAmount: 0, totalAmount: 12800,
        date: new Date('2026-01-10'), paidDate: new Date('2026-01-10'),
        status: 'PAID', invoiceNumber: 'DCC-BP-2026-142', createdById: admin.id,
      },
    }),
    prisma.transaction.create({
      data: {
        projectId: project1.id, type: 'INCOME', category: 'PROGRESS_CLAIM', description: 'Progress Claim #2 — Footings & slab stage',
        amount: 277500, gstAmount: 27750, totalAmount: 305250,
        date: new Date('2026-03-15'), dueDate: new Date('2026-03-29'),
        status: 'SUBMITTED', invoiceNumber: 'PC-002', createdById: admin.id,
      },
    }),
    // Project 2
    prisma.transaction.create({
      data: {
        projectId: project2.id, type: 'INCOME', category: 'PROGRESS_CLAIM', description: 'Initial deposit — 10% contract value',
        amount: 72000, gstAmount: 7200, totalAmount: 79200,
        date: new Date('2026-02-15'), paidDate: new Date('2026-02-20'),
        status: 'PAID', invoiceNumber: 'MBH-001', createdById: admin.id,
      },
    }),
    prisma.transaction.create({
      data: {
        projectId: project2.id, type: 'EXPENSE', category: 'INSURANCE', description: 'Home Warranty Insurance — iCare policy',
        amount: 14500, gstAmount: 0, totalAmount: 14500,
        date: new Date('2026-02-25'), paidDate: new Date('2026-02-25'),
        status: 'PAID', invoiceNumber: 'HWI-2026-8821', createdById: admin.id,
      },
    }),
    prisma.transaction.create({
      data: {
        projectId: project2.id, type: 'EXPENSE', category: 'PLANT_HIRE', description: 'Kennards Hire — Mini excavator & skip bin (2 weeks)',
        amount: 4200, gstAmount: 420, totalAmount: 4620,
        date: new Date('2026-03-05'), dueDate: new Date('2026-04-04'),
        status: 'DRAFT', invoiceNumber: 'KH-INV-442891', createdById: admin.id,
      },
    }),
  ]);

  console.log('💰 Created 8 transactions');

  // ──────────────────────────────────────────────
  // 7. Cash Flow Entries (6 total: 4 for project1, 2 for project2)
  // ──────────────────────────────────────────────
  await Promise.all([
    // Project 1
    prisma.cashFlowEntry.create({
      data: {
        projectId: project1.id, month: new Date('2026-01-01'), type: 'ACTUAL',
        incomeAmount: 203500, expenseAmount: 12800, notes: 'PC1 received, permit fees paid',
        createdById: admin.id,
      },
    }),
    prisma.cashFlowEntry.create({
      data: {
        projectId: project1.id, month: new Date('2026-02-01'), type: 'ACTUAL',
        incomeAmount: 0, expenseAmount: 88550, notes: 'Excavation & concrete supply invoices',
        createdById: admin.id,
      },
    }),
    prisma.cashFlowEntry.create({
      data: {
        projectId: project1.id, month: new Date('2026-03-01'), type: 'FORECAST',
        incomeAmount: 305250, expenseAmount: 145000, notes: 'PC2 expected, framing costs forecast',
        createdById: admin.id,
      },
    }),
    prisma.cashFlowEntry.create({
      data: {
        projectId: project1.id, month: new Date('2026-04-01'), type: 'FORECAST',
        incomeAmount: 277500, expenseAmount: 195000, notes: 'PC3 forecast, electrical & plumbing rough-in',
        createdById: admin.id,
      },
    }),
    // Project 2
    prisma.cashFlowEntry.create({
      data: {
        projectId: project2.id, month: new Date('2026-02-01'), type: 'ACTUAL',
        incomeAmount: 79200, expenseAmount: 14500, notes: 'Deposit received, insurance paid',
        createdById: admin.id,
      },
    }),
    prisma.cashFlowEntry.create({
      data: {
        projectId: project2.id, month: new Date('2026-03-01'), type: 'FORECAST',
        incomeAmount: 0, expenseAmount: 95000, notes: 'Strip-out and structural works forecast',
        createdById: admin.id,
      },
    }),
  ]);

  console.log('📈 Created 6 cash flow entries');

  // ──────────────────────────────────────────────
  // 8. Inspections (3 total: 2 for project1, 1 for project2)
  // ──────────────────────────────────────────────
  await Promise.all([
    prisma.inspection.create({
      data: {
        projectId: project1.id, type: 'PRE_SLAB', title: 'Pre-Slab Inspection — Unit A & B',
        scheduledDate: new Date('2026-03-09'), inspectorName: 'Peter Wallace — BSA Inspector',
        status: 'SCHEDULED', notes: 'Both units to be inspected simultaneously. Ensure rebar ties and piering visible.',
        createdById: admin.id,
      },
    }),
    prisma.inspection.create({
      data: {
        projectId: project1.id, type: 'FRAME', title: 'Frame Stage Inspection',
        scheduledDate: new Date('2026-04-20'), inspectorName: 'Peter Wallace — BSA Inspector',
        status: 'SCHEDULED', notes: 'Includes bracing, tie-down, and wet area fall check.',
        createdById: admin.id,
      },
    }),
    prisma.inspection.create({
      data: {
        projectId: project2.id, type: 'SAFETY', title: 'Pre-Construction WHS Site Audit',
        scheduledDate: new Date('2026-02-27'), completedDate: new Date('2026-02-27'),
        inspectorName: 'Karen Liu — SafeWork NSW', status: 'COMPLETE', overallResult: 'PASS',
        notes: 'All signage, first aid, and fall protection verified. Site cleared to commence.',
        createdById: admin.id,
      },
    }),
  ]);

  console.log('🔍 Created 3 inspections');

  console.log('\n✅ Seed complete!');
  console.log('   Login: admin@buildmate.com / Password123!');
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });

```

## server/src/index.ts

```typescript
import express, { Request, Response, NextFunction } from 'express';
import cors from 'cors';
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';
import path from 'path';
import prisma from './lib/prisma';
import { config } from './config';
import { errorHandler } from './middleware/errorHandler';
import { sanitize } from './lib/sanitize';

import authRoutes from './routes/auth';
import projectRoutes from './routes/projects';
import transactionRoutes from './routes/transactions';
import invoiceRoutes from './routes/invoices';
import budgetRoutes from './routes/budget';
import cashflowRoutes from './routes/cashflow';
import tradieRoutes from './routes/tradies';
import taskRoutes from './routes/tasks';
import inspectionRoutes from './routes/inspections';
import documentRoutes from './routes/documents';
import usersRoutes from './routes/users';
import dashboardRoutes from './routes/dashboard';
import reportRoutes from './routes/reports';

const app = express();
const startTime = Date.now();

// Middleware
app.use(helmet());
app.use(cors({ origin: config.corsOrigin, credentials: true }));

// Structured request logging
app.use((req: Request, res: Response, next: NextFunction) => {
  const start = Date.now();
  res.on('finish', () => {
    const duration = Date.now() - start;
    const log = {
      timestamp: new Date().toISOString(),
      method: req.method,
      url: req.originalUrl,
      status: res.statusCode,
      duration: `${duration}ms`,
      userAgent: req.get('user-agent'),
    };
    if (res.statusCode >= 400) {
      console.error(JSON.stringify(log));
    } else if (duration > 200) {
      console.warn(JSON.stringify({ ...log, warning: 'Slow response detected' }));
    } else {
      console.log(JSON.stringify(log));
    }
  });
  next();
});

app.use(express.json({ limit: '50mb' }));
app.use(express.urlencoded({ extended: true }));
app.use(sanitize);

// Static file serving for uploads
app.use('/uploads', express.static(path.join(__dirname, '..', 'uploads')));

// Rate limiting
const apiLimiter = rateLimit({
  windowMs: 60 * 1000,
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
  message: { error: 'Too many requests, please try again later' },
});
app.use('/api', apiLimiter);

// Health check with DB connectivity
app.get('/api/health', async (_req, res) => {
  let dbStatus: 'connected' | 'error' = 'error';
  try {
    await prisma.$queryRaw`SELECT 1`;
    dbStatus = 'connected';
  } catch {
    // db unreachable
  }

  const uptimeSeconds = Math.floor((Date.now() - startTime) / 1000);

  res.json({
    status: dbStatus === 'connected' ? 'ok' : 'degraded',
    version: '1.0.0',
    uptime: uptimeSeconds,
    db: dbStatus,
  });
});

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/projects', projectRoutes);
app.use('/api/transactions', transactionRoutes);
app.use('/api/invoices', invoiceRoutes);
app.use('/api/budget', budgetRoutes);
app.use('/api/cashflow', cashflowRoutes);
app.use('/api/tradies', tradieRoutes);
app.use('/api/tasks', taskRoutes);
app.use('/api/inspections', inspectionRoutes);
app.use('/api/documents', documentRoutes);
app.use('/api/users', usersRoutes);
app.use('/api/dashboard', dashboardRoutes);
app.use('/api/reports', reportRoutes);

// Global error handler
app.use(errorHandler);

app.listen(config.port, () => {
  console.log(`BuildMate API running on port ${config.port}`);
});

export default app;

```

## server/src/config/index.ts

```typescript
import dotenv from 'dotenv';

dotenv.config();

export const config = {
  port: parseInt(process.env.PORT || '3001', 10),
  nodeEnv: process.env.NODE_ENV || 'development',
  corsOrigin: process.env.CORS_ORIGIN || 'http://localhost:5173',
  jwt: {
    secret: process.env.JWT_SECRET || 'dev-secret-change-me',
    refreshSecret: process.env.JWT_REFRESH_SECRET || 'dev-refresh-secret-change-me',
    expiresIn: '24h',
    refreshExpiresIn: '7d',
  },
  upload: {
    maxFileSize: parseInt(process.env.MAX_FILE_SIZE_MB || '50', 10) * 1024 * 1024,
    destination: process.env.UPLOAD_DIR || './uploads',
  },
};

```

## server/src/types/index.ts

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

## server/src/middleware/auth.ts

```typescript
import { Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';
import { config } from '../config';
import { AuthRequest, AuthUser, AppError } from '../types';

export function authenticate(req: AuthRequest, _res: Response, next: NextFunction): void {
  const authHeader = req.headers.authorization;

  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    next(new AppError('Authentication required', 401));
    return;
  }

  const token = authHeader.split(' ')[1];

  try {
    const decoded = jwt.verify(token, config.jwt.secret) as AuthUser;
    req.user = { id: decoded.id, email: decoded.email, role: decoded.role };
    next();
  } catch {
    next(new AppError('Invalid or expired token', 401));
  }
}

export function authorize(...roles: string[]) {
  return (req: AuthRequest, _res: Response, next: NextFunction): void => {
    if (!req.user) {
      next(new AppError('Authentication required', 401));
      return;
    }

    if (roles.length > 0 && !roles.includes(req.user.role)) {
      next(new AppError('Insufficient permissions', 403));
      return;
    }

    next();
  };
}

```

## server/src/middleware/errorHandler.ts

```typescript
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../types';

export function errorHandler(err: Error, _req: Request, res: Response, _next: NextFunction): void {
  if (err instanceof AppError) {
    res.status(err.statusCode).json({
      error: err.message,
    });
    return;
  }

  console.error('Unhandled error:', err);

  res.status(500).json({
    error: 'Internal server error',
  });
}

```

## server/src/middleware/validate.ts

```typescript
import { Request, Response, NextFunction } from 'express';
import { ZodSchema, ZodError } from 'zod';

export function validate(schema: ZodSchema, source: 'body' | 'query' | 'params' = 'body') {
  return (req: Request, res: Response, next: NextFunction): void => {
    try {
      const data = schema.parse(req[source]);
      req[source] = data;
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        res.status(400).json({
          error: 'Validation failed',
          details: error.errors.map((e) => ({
            field: e.path.join('.'),
            message: e.message,
          })),
        });
        return;
      }
      next(error);
    }
  };
}

```

## server/src/lib/prisma.ts

```typescript
import { PrismaClient, Prisma } from '@prisma/client';

const basePrisma = new PrismaClient();

// Convert Prisma Decimal objects to plain numbers in all query results.
// SQLite/Prisma returns Decimal fields as Decimal.js objects that serialise
// to strings in JSON — this extension walks every result and converts them
// to Number so the API always returns numeric values.
function convertDecimals(obj: unknown): unknown {
  if (obj === null || obj === undefined) return obj;
  if (obj instanceof Prisma.Decimal) return obj.toNumber();
  if (Array.isArray(obj)) return obj.map(convertDecimals);
  if (obj instanceof Date) return obj;
  if (typeof obj === 'object') {
    const result: Record<string, unknown> = {};
    for (const [key, value] of Object.entries(obj as Record<string, unknown>)) {
      result[key] = convertDecimals(value);
    }
    return result;
  }
  return obj;
}

const prisma = basePrisma.$extends({
  query: {
    $allOperations({ args, query }) {
      return query(args).then(convertDecimals);
    },
  },
});

export default prisma;

```

## server/src/lib/sanitize.ts

```typescript
import { Request, Response, NextFunction } from 'express';

export function sanitizeString(str: string): string {
  return str.replace(/<[^>]*>/g, '').trim();
}

function sanitizeValue(value: unknown): unknown {
  if (typeof value === 'string') return sanitizeString(value);
  if (Array.isArray(value)) return value.map(sanitizeValue);
  if (value && typeof value === 'object') return sanitizeObject(value as Record<string, unknown>);
  return value;
}

function sanitizeObject(obj: Record<string, unknown>): Record<string, unknown> {
  const result: Record<string, unknown> = {};
  for (const [key, value] of Object.entries(obj)) {
    result[key] = sanitizeValue(value);
  }
  return result;
}

export function sanitize(req: Request, _res: Response, next: NextFunction): void {
  if (req.body && typeof req.body === 'object') {
    req.body = sanitizeObject(req.body);
  }
  next();
}

```

## server/src/lib/auditLog.ts

```typescript
import prisma from './prisma';

export async function logAudit(
  userId: string,
  action: string,
  entityType: string,
  entityId: string,
  changes?: Record<string, unknown>,
): Promise<void> {
  try {
    await prisma.auditLog.create({
      data: {
        userId,
        action,
        entityType,
        entityId,
        changes: changes ? JSON.stringify(changes) : null,
      },
    });
  } catch {
    // Don't let audit logging failures break the main request
    console.error('Audit log failed:', { userId, action, entityType, entityId });
  }
}

```
