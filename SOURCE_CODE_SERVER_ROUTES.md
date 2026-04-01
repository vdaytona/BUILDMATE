# BuildMate — Server API Routes & Tests Source Code

## Table of Contents
- server/src/routes/auth.ts
- server/src/routes/projects.ts
- server/src/routes/transactions.ts
- server/src/routes/invoices.ts
- server/src/routes/budget.ts
- server/src/routes/cashflow.ts
- server/src/routes/tradies.ts
- server/src/routes/tasks.ts
- server/src/routes/inspections.ts
- server/src/routes/documents.ts
- server/src/routes/dashboard.ts
- server/src/routes/reports.ts
- server/src/routes/users.ts
- server/src/__tests__/auth.test.ts
- server/src/__tests__/health.test.ts

---


## server/src/routes/auth.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import bcrypt from 'bcryptjs';
import jwt from 'jsonwebtoken';
import rateLimit from 'express-rate-limit';
import { z } from 'zod';
import prisma from '../lib/prisma';
import { config } from '../config';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  standardHeaders: true,
  legacyHeaders: false,
  message: { error: 'Too many requests, please try again later' },
});

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

function splitName(name: string) {
  const [firstName = '', ...rest] = name.split(' ');
  return { firstName, lastName: rest.join(' ') || '' };
}

function normaliseUser(user: { name: string; role: string; [key: string]: unknown }) {
  return { ...user, ...splitName(user.name), role: user.role.toLowerCase() };
}

function generateTokens(user: { id: string; email: string; role: string }) {
  const accessToken = jwt.sign(
    { id: user.id, email: user.email, role: user.role },
    config.jwt.secret,
    { expiresIn: config.jwt.expiresIn }
  );
  const refreshToken = jwt.sign(
    { id: user.id },
    config.jwt.refreshSecret,
    { expiresIn: config.jwt.refreshExpiresIn }
  );
  return { accessToken, refreshToken };
}

// POST /auth/register
router.post('/register', authLimiter, validate(registerSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { email, password, name, phone, role } = req.body;

    const existing = await prisma.user.findUnique({ where: { email } });
    if (existing) {
      throw new AppError('Email already registered', 409);
    }

    const hashedPassword = await bcrypt.hash(password, 12);

    const user = await prisma.user.create({
      data: { email, password: hashedPassword, name, phone, role },
      select: { id: true, email: true, name: true, phone: true, role: true, createdAt: true },
    });

    const tokens = generateTokens({ id: user.id, email: user.email, role: user.role });

    res.status(201).json({ user: normaliseUser(user), token: tokens.accessToken, refreshToken: tokens.refreshToken });
  } catch (error) {
    next(error);
  }
});

// POST /auth/login
router.post('/login', authLimiter, validate(loginSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { email, password } = req.body;

    const user = await prisma.user.findUnique({ where: { email } });
    if (!user) {
      throw new AppError('Invalid email or password', 401);
    }

    const validPassword = await bcrypt.compare(password, user.password);
    if (!validPassword) {
      throw new AppError('Invalid email or password', 401);
    }

    const tokens = generateTokens({ id: user.id, email: user.email, role: user.role });

    res.json({
      user: normaliseUser({ id: user.id, email: user.email, name: user.name, phone: user.phone, role: user.role }),
      token: tokens.accessToken,
      refreshToken: tokens.refreshToken,
    });
  } catch (error) {
    next(error);
  }
});

// POST /auth/refresh
router.post('/refresh', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { refreshToken } = req.body;
    if (!refreshToken) {
      throw new AppError('Refresh token required', 400);
    }

    const decoded = jwt.verify(refreshToken, config.jwt.refreshSecret) as { id: string };
    const user = await prisma.user.findUnique({
      where: { id: decoded.id },
      select: { id: true, email: true, role: true },
    });

    if (!user) {
      throw new AppError('User not found', 404);
    }

    const tokens = generateTokens(user);
    res.json(tokens);
  } catch (error) {
    if (error instanceof jwt.JsonWebTokenError) {
      next(new AppError('Invalid refresh token', 401));
      return;
    }
    next(error);
  }
});

// GET /auth/me
router.get('/me', authenticate, async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const user = await prisma.user.findUnique({
      where: { id: req.user!.id },
      select: { id: true, email: true, name: true, phone: true, role: true, createdAt: true, updatedAt: true },
    });

    if (!user) {
      throw new AppError('User not found', 404);
    }

    res.json(normaliseUser(user));
  } catch (error) {
    next(error);
  }
});

// PUT /auth/me
router.put('/me', authenticate, validate(updateProfileSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const user = await prisma.user.update({
      where: { id: req.user!.id },
      data: req.body,
      select: { id: true, email: true, name: true, phone: true, role: true, createdAt: true, updatedAt: true },
    });

    res.json(normaliseUser(user));
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/projects.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import { authenticate, authorize } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';
import { logAudit } from '../lib/auditLog';

const router = Router();

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

router.use(authenticate);

// GET /projects
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const search = req.query.search as string;
    const status = req.query.status as string;
    const skip = (page - 1) * limit;

    const where: Prisma.ProjectWhereInput = {};

    if (search) {
      where.OR = [
        { name: { contains: search, mode: 'insensitive' } },
        { clientName: { contains: search, mode: 'insensitive' } },
        { address: { contains: search, mode: 'insensitive' } },
      ];
    }

    if (status) {
      where.status = status as Prisma.EnumProjectStatusFilter;
    }

    const [projects, total] = await Promise.all([
      prisma.project.findMany({
        where,
        skip,
        take: limit,
        orderBy: { updatedAt: 'desc' },
        include: {
          createdBy: { select: { id: true, name: true, email: true } },
          _count: { select: { transactions: true, tasks: true, inspections: true } },
        },
      }),
      prisma.project.count({ where }),
    ]);

    res.json({
      data: projects,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /projects (Admin, PM, Site Supervisor)
router.post('/', authorize('ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR'), validate(createProjectSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.startDate) data.startDate = new Date(data.startDate);
    if (data.estimatedEndDate) data.estimatedEndDate = new Date(data.estimatedEndDate);

    const project = await prisma.project.create({
      data: {
        ...data,
        createdById: req.user!.id,
      },
      include: {
        createdBy: { select: { id: true, name: true, email: true } },
      },
    });

    await logAudit(req.user!.id, 'CREATE', 'Project', project.id, { name: data.name });
    res.status(201).json(project);
  } catch (error) {
    next(error);
  }
});

// GET /projects/:id/financial-summary
router.get('/:id/financial-summary', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.params.id;

    const project = await prisma.project.findUnique({ where: { id: projectId } });
    if (!project) throw new AppError('Project not found', 404);

    const [incomeAgg, expenseAgg, invoiceAgg, budgetCategories] = await Promise.all([
      prisma.transaction.aggregate({
        where: { projectId, type: 'INCOME', status: 'PAID' },
        _sum: { totalAmount: true },
      }),
      prisma.transaction.aggregate({
        where: { projectId, type: 'EXPENSE', status: 'PAID' },
        _sum: { totalAmount: true },
      }),
      prisma.invoice.aggregate({
        where: { projectId, status: { in: ['SENT', 'OVERDUE'] } },
        _sum: { amountDue: true, retentionAmount: true },
      }),
      prisma.budgetCategory.findMany({
        where: { projectId },
        select: { budgetAmount: true },
      }),
    ]);

    const totalIncome = Number(incomeAgg._sum.totalAmount ?? 0);
    const totalExpenses = Number(expenseAgg._sum.totalAmount ?? 0);
    const totalBudget = budgetCategories.reduce((s, c) => s + Number(c.budgetAmount), 0);

    // Get actual spent from all transactions (not just PAID)
    const allExpenses = await prisma.transaction.aggregate({
      where: { projectId, type: 'EXPENSE' },
      _sum: { totalAmount: true },
    });

    res.json({
      totalIncome,
      totalExpenses,
      netPosition: totalIncome - totalExpenses,
      outstandingInvoices: Number(invoiceAgg._sum.amountDue ?? 0),
      retentionHeld: Number(invoiceAgg._sum.retentionAmount ?? 0),
      budgetVariance: totalBudget - Number(allExpenses._sum.totalAmount ?? 0),
    });
  } catch (error) {
    next(error);
  }
});

// GET /projects/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const project = await prisma.project.findUnique({
      where: { id: req.params.id },
      include: {
        createdBy: { select: { id: true, name: true, email: true } },
        team: { include: { user: { select: { id: true, name: true, email: true, role: true } } } },
        _count: {
          select: {
            transactions: true,
            invoices: true,
            tasks: true,
            inspections: true,
            defects: true,
            documents: true,
            variations: true,
          },
        },
      },
    });

    if (!project) {
      throw new AppError('Project not found', 404);
    }

    // Compute financial summary
    const [incomeAgg, expenseAgg] = await Promise.all([
      prisma.transaction.aggregate({
        where: { projectId: project.id, type: 'INCOME', status: { in: ['APPROVED', 'PAID'] } },
        _sum: { totalAmount: true },
      }),
      prisma.transaction.aggregate({
        where: { projectId: project.id, type: 'EXPENSE', status: { in: ['APPROVED', 'PAID'] } },
        _sum: { totalAmount: true },
      }),
    ]);

    const taskStats = await prisma.task.groupBy({
      by: ['status'],
      where: { projectId: project.id },
      _count: true,
    });

    res.json({
      ...project,
      summary: {
        totalIncome: incomeAgg._sum.totalAmount || 0,
        totalExpenses: expenseAgg._sum.totalAmount || 0,
        taskStats,
      },
    });
  } catch (error) {
    next(error);
  }
});

// PUT /projects/:id (Admin, PM, Site Supervisor)
router.put('/:id', authorize('ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR'), validate(updateProjectSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.startDate) data.startDate = new Date(data.startDate);
    if (data.estimatedEndDate) data.estimatedEndDate = new Date(data.estimatedEndDate);
    if (data.actualEndDate) data.actualEndDate = new Date(data.actualEndDate);

    const project = await prisma.project.update({
      where: { id: req.params.id },
      data,
      include: {
        createdBy: { select: { id: true, name: true, email: true } },
      },
    });

    await logAudit(req.user!.id, 'UPDATE', 'Project', req.params.id, data);
    res.json(project);
  } catch (error) {
    next(error);
  }
});

// DELETE /projects/:id (archive by setting status to CANCELLED) — Admin only
router.delete('/:id', authorize('ADMIN', 'PROJECT_MANAGER'), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const project = await prisma.project.findUnique({ where: { id: req.params.id } });
    if (!project) {
      throw new AppError('Project not found', 404);
    }

    await prisma.project.update({
      where: { id: req.params.id },
      data: { status: 'CANCELLED' },
    });

    await logAudit(req.user!.id, 'DELETE', 'Project', req.params.id, { name: project.name });
    res.json({ message: 'Project archived' });
  } catch (error) {
    next(error);
  }
});

// ─── Project Team Management (6.3) ───────────────────────────

const addTeamMemberSchema = z.object({
  userId: z.string().uuid(),
  role: z.string().min(1),
});

// GET /projects/:id/team
router.get('/:id/team', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const members = await prisma.projectTeam.findMany({
      where: { projectId: req.params.id },
      include: { user: { select: { id: true, name: true, email: true, role: true } } },
      orderBy: { createdAt: 'asc' },
    });
    res.json(members);
  } catch (error) {
    next(error);
  }
});

// POST /projects/:id/team
router.post('/:id/team', authorize('ADMIN', 'PROJECT_MANAGER'), validate(addTeamMemberSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const project = await prisma.project.findUnique({ where: { id: req.params.id } });
    if (!project) throw new AppError('Project not found', 404);

    const user = await prisma.user.findUnique({ where: { id: req.body.userId } });
    if (!user) throw new AppError('User not found', 404);

    const member = await prisma.projectTeam.create({
      data: {
        projectId: req.params.id,
        userId: req.body.userId,
        role: req.body.role,
      },
      include: { user: { select: { id: true, name: true, email: true, role: true } } },
    });

    await logAudit(req.user!.id, 'CREATE', 'ProjectTeam', member.id, { projectId: req.params.id, userId: req.body.userId, role: req.body.role });
    res.status(201).json(member);
  } catch (error) {
    next(error);
  }
});

// DELETE /projects/:id/team/:memberId
router.delete('/:id/team/:memberId', authorize('ADMIN', 'PROJECT_MANAGER'), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const member = await prisma.projectTeam.findFirst({
      where: { id: req.params.memberId, projectId: req.params.id },
    });
    if (!member) throw new AppError('Team member not found', 404);

    await prisma.projectTeam.delete({ where: { id: req.params.memberId } });
    await logAudit(req.user!.id, 'DELETE', 'ProjectTeam', req.params.memberId, { projectId: req.params.id, userId: member.userId });
    res.json({ message: 'Team member removed' });
  } catch (error) {
    next(error);
  }
});

// ─── Project Activity Log (6.4 / 3.2.0c) ────────────────────

// GET /projects/:id/activity
router.get('/:id/activity', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.params.id;
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;

    const project = await prisma.project.findUnique({ where: { id: projectId } });
    if (!project) throw new AppError('Project not found', 404);

    const where: Prisma.AuditLogWhereInput = {
      OR: [
        { entityType: 'Project', entityId: projectId },
        { entityType: { in: ['ProjectTeam', 'Transaction', 'Invoice', 'Task', 'Inspection', 'Defect', 'Document', 'BudgetCategory', 'Variation'] }, changes: { contains: projectId } },
      ],
    };

    const [logs, total] = await Promise.all([
      prisma.auditLog.findMany({
        where,
        skip,
        take: limit,
        orderBy: { createdAt: 'desc' },
        include: { user: { select: { id: true, name: true, email: true } } },
      }),
      prisma.auditLog.count({ where }),
    ]);

    res.json({
      data: logs,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/transactions.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

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

const updateTransactionSchema = createTransactionSchema.partial();

router.use(authenticate);

// GET /transactions
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;
    const projectId = req.query.projectId as string;
    const type = req.query.type as string;
    const status = req.query.status as string;
    const category = req.query.category as string;
    const search = req.query.search as string;
    const dateFrom = req.query.dateFrom as string;
    const dateTo = req.query.dateTo as string;

    const where: Prisma.TransactionWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (type) where.type = type as Prisma.EnumTransactionTypeFilter;
    if (status) where.status = status as Prisma.EnumTransactionStatusFilter;
    if (category) where.category = category as Prisma.EnumTransactionCategoryFilter;
    if (search) where.description = { contains: search };
    if (dateFrom || dateTo) {
      where.date = {};
      if (dateFrom) (where.date as Prisma.DateTimeFilter).gte = new Date(dateFrom);
      if (dateTo) (where.date as Prisma.DateTimeFilter).lte = new Date(dateTo);
    }

    const [transactions, total] = await Promise.all([
      prisma.transaction.findMany({
        where,
        skip,
        take: limit,
        orderBy: { date: 'desc' },
        include: {
          project: { select: { id: true, name: true } },
          tradie: { select: { id: true, companyName: true, contactName: true } },
          createdBy: { select: { id: true, name: true } },
        },
      }),
      prisma.transaction.count({ where }),
    ]);

    res.json({
      data: transactions,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /transactions
router.post('/', validate(createTransactionSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    data.date = new Date(data.date);
    if (data.dueDate) data.dueDate = new Date(data.dueDate);
    if (data.paidDate) data.paidDate = new Date(data.paidDate);

    const transaction = await prisma.transaction.create({
      data: { ...data, createdById: req.user!.id },
      include: {
        project: { select: { id: true, name: true } },
        tradie: { select: { id: true, companyName: true } },
      },
    });

    res.status(201).json(transaction);
  } catch (error) {
    next(error);
  }
});

// GET /transactions/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const transaction = await prisma.transaction.findUnique({
      where: { id: req.params.id },
      include: {
        project: { select: { id: true, name: true } },
        tradie: { select: { id: true, companyName: true, contactName: true } },
        createdBy: { select: { id: true, name: true } },
      },
    });

    if (!transaction) {
      throw new AppError('Transaction not found', 404);
    }

    res.json(transaction);
  } catch (error) {
    next(error);
  }
});

// PUT /transactions/:id
router.put('/:id', validate(updateTransactionSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.date) data.date = new Date(data.date);
    if (data.dueDate) data.dueDate = new Date(data.dueDate);
    if (data.paidDate) data.paidDate = new Date(data.paidDate);

    const transaction = await prisma.transaction.update({
      where: { id: req.params.id },
      data,
      include: {
        project: { select: { id: true, name: true } },
        tradie: { select: { id: true, companyName: true } },
      },
    });

    res.json(transaction);
  } catch (error) {
    next(error);
  }
});

// DELETE /transactions/:id
// Data retention policy: Financial records (transactions) must be retained for 7 years
// per Australian regulatory requirements. Records within the retention period are
// soft-deleted (status set to CANCELLED) instead of permanently removed.
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const transaction = await prisma.transaction.findUnique({ where: { id: req.params.id } });
    if (!transaction) {
      throw new AppError('Transaction not found', 404);
    }

    const RETENTION_YEARS = 7;
    const retentionCutoff = new Date();
    retentionCutoff.setFullYear(retentionCutoff.getFullYear() - RETENTION_YEARS);

    if (transaction.createdAt > retentionCutoff) {
      // Within retention period — soft-delete by setting status to CANCELLED
      await prisma.transaction.update({
        where: { id: req.params.id },
        data: { status: 'CANCELLED' },
      });
      res.json({ message: 'Transaction cancelled (retained per 7-year data retention policy)' });
    } else {
      await prisma.transaction.delete({ where: { id: req.params.id } });
      res.json({ message: 'Transaction deleted' });
    }
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/invoices.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

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

const updateInvoiceSchema = createInvoiceSchema.partial();

router.use(authenticate);

// GET /invoices
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;
    const projectId = req.query.projectId as string;
    const status = req.query.status as string;

    const where: Prisma.InvoiceWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (status) where.status = status as Prisma.EnumInvoiceStatusFilter;

    const [invoices, total] = await Promise.all([
      prisma.invoice.findMany({
        where,
        skip,
        take: limit,
        orderBy: { issueDate: 'desc' },
        include: {
          project: { select: { id: true, name: true } },
          lineItems: true,
          createdBy: { select: { id: true, name: true } },
        },
      }),
      prisma.invoice.count({ where }),
    ]);

    res.json({
      data: invoices,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /invoices
router.post('/', validate(createInvoiceSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { lineItems, ...data } = req.body;
    data.issueDate = new Date(data.issueDate);
    data.dueDate = new Date(data.dueDate);

    const invoice = await prisma.invoice.create({
      data: {
        ...data,
        createdById: req.user!.id,
        lineItems: lineItems ? { create: lineItems } : undefined,
      },
      include: {
        project: { select: { id: true, name: true } },
        lineItems: true,
      },
    });

    res.status(201).json(invoice);
  } catch (error) {
    next(error);
  }
});

// GET /invoices/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const invoice = await prisma.invoice.findUnique({
      where: { id: req.params.id },
      include: {
        project: { select: { id: true, name: true, clientName: true, clientEmail: true } },
        lineItems: true,
        createdBy: { select: { id: true, name: true } },
      },
    });

    if (!invoice) {
      throw new AppError('Invoice not found', 404);
    }

    res.json(invoice);
  } catch (error) {
    next(error);
  }
});

// PUT /invoices/:id
router.put('/:id', validate(updateInvoiceSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { lineItems, ...data } = req.body;
    if (data.issueDate) data.issueDate = new Date(data.issueDate);
    if (data.dueDate) data.dueDate = new Date(data.dueDate);
    if (data.paidDate) data.paidDate = new Date(data.paidDate);

    const invoice = await prisma.$transaction(async (tx) => {
      if (lineItems) {
        await tx.invoiceLineItem.deleteMany({ where: { invoiceId: req.params.id } });
      }

      return tx.invoice.update({
        where: { id: req.params.id },
        data: {
          ...data,
          lineItems: lineItems ? { create: lineItems } : undefined,
        },
        include: {
          project: { select: { id: true, name: true } },
          lineItems: true,
        },
      });
    });

    res.json(invoice);
  } catch (error) {
    next(error);
  }
});

// PUT /invoices/:id/status
router.put('/:id/status', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const invoice = await prisma.invoice.findUnique({ where: { id: req.params.id } });
    if (!invoice) throw new AppError('Invoice not found', 404);

    const { status } = req.body;
    const validTransitions: Record<string, string[]> = {
      'DRAFT': ['SENT'],
      'SENT': ['PAID', 'OVERDUE', 'VIEWED'],
      'VIEWED': ['PAID', 'OVERDUE'],
      'OVERDUE': ['PAID'],
    };

    if (!validTransitions[invoice.status]?.includes(status)) {
      throw new AppError(`Cannot transition from ${invoice.status} to ${status}`, 400);
    }

    const updateData: any = { status };
    if (status === 'PAID') {
      updateData.paidDate = new Date();
      updateData.paidAmount = invoice.amountDue;
    }

    const updated = await prisma.invoice.update({
      where: { id: req.params.id },
      data: updateData,
      include: { lineItems: true },
    });
    res.json(updated);
  } catch (error) {
    next(error);
  }
});

// DELETE /invoices/:id
// Data retention policy: Financial records (invoices) must be retained for 7 years
// per Australian regulatory requirements. Non-draft invoices within the retention
// period are soft-deleted (status set to CANCELLED) instead of permanently removed.
// Draft invoices may be deleted outright as they have not been issued.
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const invoice = await prisma.invoice.findUnique({ where: { id: req.params.id } });
    if (!invoice) {
      throw new AppError('Invoice not found', 404);
    }

    // Draft invoices can always be permanently deleted (not yet issued)
    if (invoice.status === 'DRAFT') {
      await prisma.invoice.delete({ where: { id: req.params.id } });
      res.json({ message: 'Invoice deleted' });
      return;
    }

    const RETENTION_YEARS = 7;
    const retentionCutoff = new Date();
    retentionCutoff.setFullYear(retentionCutoff.getFullYear() - RETENTION_YEARS);

    if (invoice.createdAt > retentionCutoff) {
      throw new AppError(
        'Issued invoices within the 7-year retention period cannot be deleted. Contact an administrator.',
        403,
      );
    }

    await prisma.invoice.delete({ where: { id: req.params.id } });
    res.json({ message: 'Invoice deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/budget.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

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

const updateCategorySchema = createCategorySchema.partial();

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

const updateVariationSchema = createVariationSchema.partial();

router.use(authenticate);

// ─── Budget Categories ─────────────────────

// GET /budget/categories
router.get('/categories', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) {
      throw new AppError('projectId query parameter required', 400);
    }

    const categories = await prisma.budgetCategory.findMany({
      where: { projectId },
      orderBy: { sortOrder: 'asc' },
      include: { children: true },
    });

    res.json(categories);
  } catch (error) {
    next(error);
  }
});

// POST /budget/categories
router.post('/categories', validate(createCategorySchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const category = await prisma.budgetCategory.create({
      data: req.body,
    });
    res.status(201).json(category);
  } catch (error) {
    next(error);
  }
});

// PUT /budget/categories/:id
router.put('/categories/:id', validate(updateCategorySchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const category = await prisma.budgetCategory.update({
      where: { id: req.params.id as string },
      data: req.body,
    });
    res.json(category);
  } catch (error) {
    next(error);
  }
});

// DELETE /budget/categories/:id
// Data retention policy: Budget categories linked to financial records must be retained
// for 7 years per Australian regulatory requirements. Categories with associated
// transactions within the retention period cannot be permanently deleted.
router.delete('/categories/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const RETENTION_YEARS = 7;
    const retentionCutoff = new Date();
    retentionCutoff.setFullYear(retentionCutoff.getFullYear() - RETENTION_YEARS);

    // Check if any transactions reference this category's cost code within retention period
    const category = await prisma.budgetCategory.findUnique({ where: { id: req.params.id as string } });
    if (!category) {
      throw new AppError('Budget category not found', 404);
    }

    const linkedTransactions = await prisma.transaction.count({
      where: {
        projectId: category.projectId,
        category: category.costCode,
        createdAt: { gte: retentionCutoff },
      },
    });

    if (linkedTransactions > 0) {
      throw new AppError(
        'Cannot delete budget category with linked transactions within the 7-year retention period',
        403,
      );
    }

    await prisma.budgetCategory.delete({ where: { id: req.params.id as string } });
    res.json({ message: 'Budget category deleted' });
  } catch (error) {
    next(error);
  }
});

// ─── Variations ─────────────────────────────

// GET /budget/variations
router.get('/variations', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) {
      throw new AppError('projectId query parameter required', 400);
    }

    const variations = await prisma.variation.findMany({
      where: { projectId },
      orderBy: { variationNumber: 'asc' },
      include: {
        category: { select: { id: true, costCode: true, name: true } },
        createdBy: { select: { id: true, name: true } },
      },
    });

    res.json(variations);
  } catch (error) {
    next(error);
  }
});

// POST /budget/variations
router.post('/variations', validate(createVariationSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.submittedDate) data.submittedDate = new Date(data.submittedDate);
    if (data.approvedDate) data.approvedDate = new Date(data.approvedDate);

    const variation = await prisma.variation.create({
      data: { ...data, createdById: req.user!.id },
      include: { category: { select: { id: true, costCode: true, name: true } } },
    });

    res.status(201).json(variation);
  } catch (error) {
    next(error);
  }
});

// PUT /budget/variations/:id
router.put('/variations/:id', validate(updateVariationSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.submittedDate) data.submittedDate = new Date(data.submittedDate);
    if (data.approvedDate) data.approvedDate = new Date(data.approvedDate);

    const existing = await prisma.variation.findUnique({ where: { id: req.params.id as string } });

    const variation = await prisma.variation.update({
      where: { id: req.params.id as string },
      data,
      include: { category: { select: { id: true, costCode: true, name: true } } },
    });

    // If status changed to APPROVED and categoryId exists, update the category's revisedBudget
    if (data.status === 'APPROVED' && existing?.status !== 'APPROVED' && variation.categoryId) {
      const category = await prisma.budgetCategory.findUnique({ where: { id: variation.categoryId } });
      if (category) {
        await prisma.budgetCategory.update({
          where: { id: variation.categoryId },
          data: { revisedBudget: Number(category.revisedBudget || category.budgetAmount) + Number(variation.amount) },
        });
      }
    }

    res.json(variation);
  } catch (error) {
    next(error);
  }
});

// DELETE /budget/variations/:id
router.delete('/variations/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.variation.delete({ where: { id: req.params.id as string } });
    res.json({ message: 'Variation deleted' });
  } catch (error) {
    next(error);
  }
});

// ─── Budget Summary ─────────────────────────

// GET /budget/summary/:projectId
router.get('/summary/:projectId', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.params.projectId as string;

    const categories = await prisma.budgetCategory.findMany({
      where: { projectId },
      orderBy: { sortOrder: 'asc' },
    });

    // Get per-category spend (PAID expenses)
    const paidExpenses = await prisma.transaction.groupBy({
      by: ['category'],
      where: { projectId, type: 'EXPENSE', status: 'PAID' },
      _sum: { totalAmount: true },
    });

    // Get per-category committed (APPROVED but not PAID)
    const committedExpenses = await prisma.transaction.groupBy({
      by: ['category'],
      where: { projectId, type: 'EXPENSE', status: 'APPROVED' },
      _sum: { totalAmount: true },
    });

    const paidMap = new Map(paidExpenses.map(e => [e.category, Number(e._sum?.totalAmount || 0)]));
    const committedMap = new Map(committedExpenses.map(e => [e.category, Number(e._sum?.totalAmount || 0)]));

    const enrichedCategories = categories.map(c => {
      const budgetAmount = Number(c.budgetAmount);
      const spentAmount = paidMap.get(c.costCode) || 0;
      const committedAmount = committedMap.get(c.costCode) || 0;
      const forecastToComplete = Number(c.forecastToComplete || 0);
      const revisedBudget = Number(c.revisedBudget || c.budgetAmount);
      const forecastFinal = spentAmount + forecastToComplete;
      const variance = revisedBudget - spentAmount - committedAmount;

      return {
        ...c,
        budgetAmount,
        revisedBudget,
        spentAmount,
        committedAmount,
        forecastToComplete,
        forecastFinal,
        variance,
      };
    });

    const totalBudget = enrichedCategories.reduce((sum, c) => sum + c.budgetAmount, 0);
    const totalSpent = enrichedCategories.reduce((sum, c) => sum + c.spentAmount, 0);
    const totalCommitted = enrichedCategories.reduce((sum, c) => sum + c.committedAmount, 0);
    const totalForecast = enrichedCategories.reduce((sum, c) => sum + c.forecastFinal, 0);
    const totalVariance = enrichedCategories.reduce((sum, c) => sum + c.variance, 0);

    res.json({
      totalBudget,
      totalSpent,
      totalCommitted,
      totalForecast,
      totalVariance,
      categories: enrichedCategories,
    });
  } catch (error) {
    next(error);
  }
});

// ─── Contingency ────────────────────────────

// GET /budget/contingency/:projectId
router.get('/contingency/:projectId', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.params.projectId as string;
    const project = await prisma.project.findUnique({
      where: { id: projectId },
      select: { contingencyAmount: true },
    });

    const drawdowns = await prisma.contingencyDrawdown.findMany({
      where: { projectId },
      orderBy: { date: 'desc' },
    });

    const totalDrawn = drawdowns.reduce((s, d) => s + Number(d.amount), 0);
    const contingencyAmount = Number(project?.contingencyAmount ?? 0);

    res.json({
      contingencyAmount,
      totalDrawn,
      remaining: contingencyAmount - totalDrawn,
      drawdowns,
    });
  } catch (error) {
    next(error);
  }
});

// POST /budget/contingency/:projectId/drawdown
router.post('/contingency/:projectId/drawdown', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.params.projectId as string;
    const { description, amount, date, approvedBy } = req.body;

    const drawdown = await prisma.contingencyDrawdown.create({
      data: {
        projectId,
        description,
        amount,
        date: new Date(date),
        approvedBy,
      },
    });

    res.status(201).json(drawdown);
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/cashflow.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

const createCashFlowSchema = z.object({
  projectId: z.string().uuid(),
  month: z.string().datetime(),
  type: z.enum(['FORECAST', 'ACTUAL']),
  incomeAmount: z.number(),
  expenseAmount: z.number(),
  notes: z.string().optional(),
});

const updateCashFlowSchema = createCashFlowSchema.partial();

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

const updateClaimScheduleSchema = createClaimScheduleSchema.partial();

router.use(authenticate);

// ─── Cash Flow Entries ──────────────────────

// GET /cashflow/entries
router.get('/entries', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) {
      throw new AppError('projectId query parameter required', 400);
    }

    const type = req.query.type as string;

    const where: { projectId: string; type?: 'FORECAST' | 'ACTUAL' } = { projectId };
    if (type === 'FORECAST' || type === 'ACTUAL') where.type = type;

    const entries = await prisma.cashFlowEntry.findMany({
      where,
      orderBy: { month: 'asc' },
      include: { createdBy: { select: { id: true, name: true } } },
    });

    res.json(entries);
  } catch (error) {
    next(error);
  }
});

// POST /cashflow/entries
router.post('/entries', validate(createCashFlowSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    data.month = new Date(data.month);

    const entry = await prisma.cashFlowEntry.create({
      data: { ...data, createdById: req.user!.id },
    });

    res.status(201).json(entry);
  } catch (error) {
    next(error);
  }
});

// PUT /cashflow/entries/:id
router.put('/entries/:id', validate(updateCashFlowSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.month) data.month = new Date(data.month);

    const entry = await prisma.cashFlowEntry.update({
      where: { id: req.params.id },
      data,
    });

    res.json(entry);
  } catch (error) {
    next(error);
  }
});

// DELETE /cashflow/entries/:id
router.delete('/entries/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.cashFlowEntry.delete({ where: { id: req.params.id } });
    res.json({ message: 'Cash flow entry deleted' });
  } catch (error) {
    next(error);
  }
});

// ─── Forecast Generation ────────────────────

// POST /cashflow/forecast/:projectId
router.post('/forecast/:projectId', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { projectId } = req.params;

    const project = await prisma.project.findUnique({ where: { id: projectId } });
    if (!project) {
      throw new AppError('Project not found', 404);
    }
    if (!project.startDate || !project.estimatedEndDate) {
      throw new AppError('Project must have start and estimated end dates', 400);
    }

    const claimSchedules = await prisma.claimSchedule.findMany({
      where: { projectId },
      orderBy: { scheduledDate: 'asc' },
    });

    const budgetCategories = await prisma.budgetCategory.findMany({ where: { projectId } });
    const totalBudget = budgetCategories.reduce((sum, c) => sum + Number(c.budgetAmount), 0);

    const start = new Date(project.startDate);
    const end = new Date(project.estimatedEndDate);
    const months: Date[] = [];
    const current = new Date(start.getFullYear(), start.getMonth(), 1);

    while (current <= end) {
      months.push(new Date(current));
      current.setMonth(current.getMonth() + 1);
    }

    const totalMonths = months.length || 1;
    const monthlyExpense = totalBudget / totalMonths;

    const forecasts = months.map((month) => {
      const claimInMonth = claimSchedules.find((cs) => {
        const csMonth = new Date(cs.scheduledDate);
        return csMonth.getFullYear() === month.getFullYear() && csMonth.getMonth() === month.getMonth();
      });

      return {
        projectId,
        month,
        type: 'FORECAST' as const,
        incomeAmount: claimInMonth ? Number(claimInMonth.amount) : 0,
        expenseAmount: monthlyExpense,
        createdById: req.user!.id,
      };
    });

    // Delete existing forecasts and create new ones
    await prisma.cashFlowEntry.deleteMany({ where: { projectId, type: 'FORECAST' } });
    await prisma.cashFlowEntry.createMany({ data: forecasts });

    const created = await prisma.cashFlowEntry.findMany({
      where: { projectId, type: 'FORECAST' },
      orderBy: { month: 'asc' },
    });

    res.status(201).json(created);
  } catch (error) {
    next(error);
  }
});

// ─── Claim Schedules ────────────────────────

// GET /cashflow/claims
router.get('/claims', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) {
      throw new AppError('projectId query parameter required', 400);
    }

    const claims = await prisma.claimSchedule.findMany({
      where: { projectId },
      orderBy: { claimNumber: 'asc' },
    });

    res.json(claims);
  } catch (error) {
    next(error);
  }
});

// POST /cashflow/claims
router.post('/claims', validate(createClaimScheduleSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    data.scheduledDate = new Date(data.scheduledDate);
    if (data.actualDate) data.actualDate = new Date(data.actualDate);

    const claim = await prisma.claimSchedule.create({ data });
    res.status(201).json(claim);
  } catch (error) {
    next(error);
  }
});

// PUT /cashflow/claims/:id
router.put('/claims/:id', validate(updateClaimScheduleSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.scheduledDate) data.scheduledDate = new Date(data.scheduledDate);
    if (data.actualDate) data.actualDate = new Date(data.actualDate);

    const claim = await prisma.claimSchedule.update({
      where: { id: req.params.id },
      data,
    });

    res.json(claim);
  } catch (error) {
    next(error);
  }
});

// DELETE /cashflow/claims/:id
router.delete('/claims/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.claimSchedule.delete({ where: { id: req.params.id } });
    res.json({ message: 'Claim schedule deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/tradies.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

const tradeValues = [
  'BUILDER', 'CARPENTER', 'ELECTRICIAN', 'PLUMBER', 'PLASTERER', 'PAINTER',
  'TILER', 'BRICKLAYER', 'CONCRETER', 'ROOFER', 'LANDSCAPER', 'DEMOLITION',
  'EXCAVATION', 'STEEL_FIXER', 'CABINET_MAKER', 'GLAZIER', 'RENDERER',
  'WATERPROOFER', 'INSULATION', 'FENCER', 'SCAFFOLDER', 'CLEANER', 'HVAC',
  'FIRE_PROTECTION', 'SECURITY_SYSTEMS', 'FLOORING', 'STONEMASON',
  'POOL_BUILDER', 'SURVEYOR', 'ENGINEER', 'ARCHITECT', 'DRAFTSPERSON', 'OTHER',
] as const;

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

const updateTradieSchema = createTradieSchema.partial();

const createDocumentSchema = z.object({
  tradieId: z.string().uuid(),
  type: z.enum(['LICENCE', 'PUBLIC_LIABILITY', 'WORKERS_COMP', 'ABN_LOOKUP', 'SWMS', 'OTHER']),
  name: z.string().min(1),
  fileUrl: z.string().min(1),
  expiryDate: z.string().datetime().optional(),
});

router.use(authenticate);

// GET /tradies
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;
    const search = req.query.search as string;
    const trade = req.query.trade as string;
    const status = req.query.status as string;

    const where: Prisma.TradieWhereInput = {};

    if (search) {
      where.OR = [
        { companyName: { contains: search, mode: 'insensitive' } },
        { contactName: { contains: search, mode: 'insensitive' } },
        { email: { contains: search, mode: 'insensitive' } },
      ];
    }

    if (trade) where.trade = trade as Prisma.EnumTradeFilter;
    if (status) where.status = status as Prisma.EnumTradieStatusFilter;

    const [tradies, total] = await Promise.all([
      prisma.tradie.findMany({
        where,
        skip,
        take: limit,
        orderBy: { companyName: 'asc' },
        include: {
          _count: { select: { documents: true, projectAssignments: true } },
        },
      }),
      prisma.tradie.count({ where }),
    ]);

    res.json({
      data: tradies,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /tradies
router.post('/', validate(createTradieSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.licenceExpiry) data.licenceExpiry = new Date(data.licenceExpiry);
    if (data.insuranceExpiry) data.insuranceExpiry = new Date(data.insuranceExpiry);
    if (data.workersCompExpiry) data.workersCompExpiry = new Date(data.workersCompExpiry);

    const tradie = await prisma.tradie.create({
      data: { ...data, createdById: req.user!.id },
    });

    res.status(201).json(tradie);
  } catch (error) {
    next(error);
  }
});

// GET /tradies/compliance
router.get('/compliance', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const daysAhead = parseInt(req.query.days as string) || 30;
    const cutoff = new Date();
    cutoff.setDate(cutoff.getDate() + daysAhead);

    const expiringDocs = await prisma.tradieDocument.findMany({
      where: {
        expiryDate: { lte: cutoff },
      },
      include: {
        tradie: { select: { id: true, companyName: true, contactName: true, trade: true } },
      },
      orderBy: { expiryDate: 'asc' },
    });

    const expiringTradies = await prisma.tradie.findMany({
      where: {
        status: 'ACTIVE',
        OR: [
          { licenceExpiry: { lte: cutoff } },
          { insuranceExpiry: { lte: cutoff } },
          { workersCompExpiry: { lte: cutoff } },
        ],
      },
      orderBy: { companyName: 'asc' },
    });

    res.json({
      expiringDocuments: expiringDocs,
      expiringTradies,
      summary: {
        documentsExpiring: expiringDocs.length,
        tradiesWithExpiringCompliance: expiringTradies.length,
        lookAheadDays: daysAhead,
      },
    });
  } catch (error) {
    next(error);
  }
});

// GET /tradies/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const tradie = await prisma.tradie.findUnique({
      where: { id: req.params.id },
      include: {
        documents: { orderBy: { createdAt: 'desc' } },
        projectAssignments: {
          include: { project: { select: { id: true, name: true, status: true } } },
        },
        createdBy: { select: { id: true, name: true } },
      },
    });

    if (!tradie) {
      throw new AppError('Tradie not found', 404);
    }

    res.json(tradie);
  } catch (error) {
    next(error);
  }
});

// PUT /tradies/:id
router.put('/:id', validate(updateTradieSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.licenceExpiry) data.licenceExpiry = new Date(data.licenceExpiry);
    if (data.insuranceExpiry) data.insuranceExpiry = new Date(data.insuranceExpiry);
    if (data.workersCompExpiry) data.workersCompExpiry = new Date(data.workersCompExpiry);

    const tradie = await prisma.tradie.update({
      where: { id: req.params.id },
      data,
    });

    res.json(tradie);
  } catch (error) {
    next(error);
  }
});

// DELETE /tradies/:id
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.tradie.update({
      where: { id: req.params.id },
      data: { status: 'INACTIVE' },
    });
    res.json({ message: 'Tradie deactivated' });
  } catch (error) {
    next(error);
  }
});

// ─── Tradie Documents ───────────────────────

// POST /tradies/documents
router.post('/documents', validate(createDocumentSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.expiryDate) data.expiryDate = new Date(data.expiryDate);

    const doc = await prisma.tradieDocument.create({
      data: { ...data, uploadedById: req.user!.id },
    });

    res.status(201).json(doc);
  } catch (error) {
    next(error);
  }
});

// DELETE /tradies/documents/:id
router.delete('/documents/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.tradieDocument.delete({ where: { id: req.params.id } });
    res.json({ message: 'Document deleted' });
  } catch (error) {
    next(error);
  }
});

// ─── Tradie Documents (nested under tradie) ─────

// GET /tradies/:tradieId/documents
router.get('/:tradieId/documents', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const docs = await prisma.tradieDocument.findMany({
      where: { tradieId: req.params.tradieId },
      orderBy: { createdAt: 'desc' },
    });
    res.json(docs);
  } catch (error) {
    next(error);
  }
});

// POST /tradies/:tradieId/documents
router.post('/:tradieId/documents', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { type, name, fileUrl, expiryDate } = req.body;
    const doc = await prisma.tradieDocument.create({
      data: {
        tradieId: req.params.tradieId,
        type,
        name,
        fileUrl: fileUrl || 'uploaded',
        expiryDate: expiryDate ? new Date(expiryDate) : undefined,
        uploadedById: req.user!.id,
      },
    });
    res.status(201).json(doc);
  } catch (error) {
    next(error);
  }
});

// ─── Tradie Assignments ─────────────────────────

const createAssignmentSchema = z.object({
  projectId: z.string().uuid(),
  role: z.string().optional(),
  agreedRate: z.number().optional(),
  rateType: z.enum(['HOURLY', 'DAILY', 'FIXED', 'SQM']).optional(),
});

// GET /tradies/:tradieId/assignments
router.get('/:tradieId/assignments', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const assignments = await prisma.tradieProjectAssignment.findMany({
      where: { tradieId: req.params.tradieId },
      include: { project: { select: { id: true, name: true, status: true } } },
      orderBy: { createdAt: 'desc' },
    });
    res.json(assignments);
  } catch (error) {
    next(error);
  }
});

// POST /tradies/:tradieId/assignments
router.post('/:tradieId/assignments', validate(createAssignmentSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { projectId, role, agreedRate, rateType } = req.body;
    const assignment = await prisma.tradieProjectAssignment.create({
      data: {
        tradieId: req.params.tradieId,
        projectId,
        role,
        agreedRate,
        rateType,
      },
      include: { project: { select: { id: true, name: true, status: true } } },
    });
    res.status(201).json(assignment);
  } catch (error) {
    next(error);
  }
});

// PUT /tradies/assignments/:id/rating
router.put('/assignments/:id/rating', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { rating } = req.body;
    const assignment = await prisma.tradieProjectAssignment.update({
      where: { id: req.params.id },
      data: { performanceRating: rating },
    });
    res.json(assignment);
  } catch (error) {
    next(error);
  }
});

// ─── Tradie Payment History ─────────────────────

// GET /tradies/:tradieId/payments
router.get('/:tradieId/payments', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const transactions = await prisma.transaction.findMany({
      where: { tradieId: req.params.tradieId },
      include: {
        project: { select: { id: true, name: true } },
      },
      orderBy: { date: 'desc' },
    });
    res.json(transactions);
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/tasks.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

const TASK_STAGES = [
  'INITIATION', 'DESIGN', 'APPROVALS', 'SITE_PREP', 'BASE', 'FRAME',
  'LOCK_UP', 'FIT_OFF', 'FIXING', 'COMPLETION', 'HANDOVER', 'DEFECT_LIABILITY',
] as const;

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
  stage: z.enum(TASK_STAGES).optional(),
  baselineStartDate: z.string().datetime().optional(),
  baselineEndDate: z.string().datetime().optional(),
  notes: z.string().optional(),
});

const updateTaskSchema = createTaskSchema.partial();

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

function withProgress<T extends { percentComplete: number }>(task: T) {
  return { ...task, progress: task.percentComplete };
}

router.use(authenticate);

// GET /tasks
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    const status = req.query.status as string;
    const milestonesOnly = req.query.milestones === 'true';

    const where: Prisma.TaskWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (status) where.status = status as Prisma.EnumTaskStatusFilter;
    if (milestonesOnly) where.isMilestone = true;

    const tasks = await prisma.task.findMany({
      where,
      orderBy: [{ sortOrder: 'asc' }, { startDate: 'asc' }],
      include: {
        assignedTradie: { select: { id: true, companyName: true, contactName: true } },
        assignedUser: { select: { id: true, name: true } },
        costCode: { select: { id: true, costCode: true, name: true } },
        predecessors: { include: { predecessor: { select: { id: true, name: true } } } },
        successors: { include: { successor: { select: { id: true, name: true } } } },
        childTasks: { select: { id: true, name: true, status: true, percentComplete: true } },
      },
    });

    res.json(tasks.map(withProgress));
  } catch (error) {
    next(error);
  }
});

// POST /tasks
router.post('/', validate(createTaskSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.startDate) data.startDate = new Date(data.startDate);
    if (data.endDate) data.endDate = new Date(data.endDate);
    if (data.baselineStartDate) data.baselineStartDate = new Date(data.baselineStartDate);
    if (data.baselineEndDate) data.baselineEndDate = new Date(data.baselineEndDate);

    const task = await prisma.task.create({
      data: { ...data, createdById: req.user!.id },
      include: {
        assignedTradie: { select: { id: true, companyName: true } },
        assignedUser: { select: { id: true, name: true } },
      },
    });

    res.status(201).json(withProgress(task));
  } catch (error) {
    next(error);
  }
});

// POST /tasks/reorder
const reorderTasksSchema = z.object({
  projectId: z.string().uuid(),
  orderedTaskIds: z.array(z.string().uuid()).min(1),
});

router.post('/reorder', validate(reorderTasksSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { projectId, orderedTaskIds } = req.body;

    await prisma.$transaction(
      orderedTaskIds.map((taskId: string, index: number) =>
        prisma.task.update({
          where: { id: taskId },
          data: { sortOrder: index },
        }),
      ),
    );

    res.json({ message: 'Tasks reordered', count: orderedTaskIds.length });
  } catch (error) {
    next(error);
  }
});

// ─── Critical Path ──────────────────────────

// GET /tasks/critical-path?projectId=xxx
router.get('/critical-path', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) throw new AppError('projectId query parameter required', 400);

    const tasks = await prisma.task.findMany({
      where: { projectId },
      select: { id: true, startDate: true, endDate: true, duration: true },
    });

    const deps = await prisma.taskDependency.findMany({
      where: { predecessor: { projectId } },
      select: { predecessorId: true, successorId: true },
    });

    const adj: Record<string, string[]> = {};
    const inDeg: Record<string, number> = {};
    const taskDur: Record<string, number> = {};

    for (const t of tasks) {
      adj[t.id] = [];
      inDeg[t.id] = 0;
      if (t.startDate && t.endDate) {
        taskDur[t.id] = Math.max(1, Math.ceil((t.endDate.getTime() - t.startDate.getTime()) / 86400000));
      } else {
        taskDur[t.id] = t.duration ?? 1;
      }
    }

    for (const d of deps) {
      if (adj[d.predecessorId] && inDeg[d.successorId] !== undefined) {
        adj[d.predecessorId].push(d.successorId);
        inDeg[d.successorId]++;
      }
    }

    const dist: Record<string, number> = {};
    const prev: Record<string, string | null> = {};
    const queue: string[] = [];

    for (const id of Object.keys(inDeg)) {
      dist[id] = taskDur[id];
      prev[id] = null;
      if (inDeg[id] === 0) queue.push(id);
    }

    while (queue.length > 0) {
      const u = queue.shift()!;
      for (const v of adj[u]) {
        const newDist = dist[u] + taskDur[v];
        if (newDist > dist[v]) {
          dist[v] = newDist;
          prev[v] = u;
        }
        inDeg[v]--;
        if (inDeg[v] === 0) queue.push(v);
      }
    }

    let maxId = '';
    let maxDist = 0;
    for (const [id, d] of Object.entries(dist)) {
      if (d > maxDist) { maxDist = d; maxId = id; }
    }

    const criticalPath: string[] = [];
    let cur: string | null = maxId;
    while (cur) {
      criticalPath.unshift(cur);
      cur = prev[cur];
    }

    res.json(criticalPath);
  } catch (error) {
    next(error);
  }
});

// ─── Set Baseline ───────────────────────────

// POST /tasks/set-baseline?projectId=xxx
router.post('/set-baseline', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = (req.query.projectId as string) || req.body.projectId;
    if (!projectId) throw new AppError('projectId is required', 400);

    const tasks = await prisma.task.findMany({
      where: { projectId },
      select: { id: true, startDate: true, endDate: true },
    });

    await Promise.all(
      tasks.map((t) =>
        prisma.task.update({
          where: { id: t.id },
          data: { baselineStartDate: t.startDate, baselineEndDate: t.endDate },
        }),
      ),
    );

    res.json({ message: 'Baseline set', count: tasks.length });
  } catch (error) {
    next(error);
  }
});

// ─── Milestones ─────────────────────────────

// GET /tasks/milestones?projectId=xxx
router.get('/milestones', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) throw new AppError('projectId query parameter required', 400);

    const milestones = await prisma.task.findMany({
      where: { projectId, isMilestone: true },
      orderBy: { startDate: 'asc' },
      include: {
        assignedTradie: { select: { id: true, companyName: true } },
        assignedUser: { select: { id: true, name: true } },
      },
    });

    res.json(milestones.map(withProgress));
  } catch (error) {
    next(error);
  }
});

// GET /tasks/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const task = await prisma.task.findUnique({
      where: { id: req.params.id },
      include: {
        assignedTradie: { select: { id: true, companyName: true, contactName: true } },
        assignedUser: { select: { id: true, name: true } },
        costCode: { select: { id: true, costCode: true, name: true } },
        parentTask: { select: { id: true, name: true } },
        childTasks: { orderBy: { sortOrder: 'asc' } },
        predecessors: { include: { predecessor: { select: { id: true, name: true, status: true } } } },
        successors: { include: { successor: { select: { id: true, name: true, status: true } } } },
        delayLogs: { orderBy: { date: 'desc' } },
        createdBy: { select: { id: true, name: true } },
      },
    });

    if (!task) {
      throw new AppError('Task not found', 404);
    }

    res.json(withProgress(task));
  } catch (error) {
    next(error);
  }
});

// PUT /tasks/:id
router.put('/:id', validate(updateTaskSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.startDate) data.startDate = new Date(data.startDate);
    if (data.endDate) data.endDate = new Date(data.endDate);
    if (data.baselineStartDate) data.baselineStartDate = new Date(data.baselineStartDate);
    if (data.baselineEndDate) data.baselineEndDate = new Date(data.baselineEndDate);

    const task = await prisma.task.update({
      where: { id: req.params.id },
      data,
      include: {
        assignedTradie: { select: { id: true, companyName: true } },
        assignedUser: { select: { id: true, name: true } },
      },
    });

    res.json(withProgress(task));
  } catch (error) {
    next(error);
  }
});

// DELETE /tasks/:id
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.task.delete({ where: { id: req.params.id } });
    res.json({ message: 'Task deleted' });
  } catch (error) {
    next(error);
  }
});

// PATCH /tasks/:id/progress
router.patch('/:id/progress', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { percentComplete } = req.body;
    if (percentComplete === undefined || percentComplete < 0 || percentComplete > 100) {
      throw new AppError('percentComplete must be between 0 and 100', 400);
    }

    let status: 'NOT_STARTED' | 'IN_PROGRESS' | 'COMPLETE' | undefined;
    if (percentComplete === 0) status = 'NOT_STARTED';
    else if (percentComplete === 100) status = 'COMPLETE';
    else status = 'IN_PROGRESS';

    const task = await prisma.task.update({
      where: { id: req.params.id },
      data: { percentComplete, status },
    });

    res.json(withProgress(task));
  } catch (error) {
    next(error);
  }
});

// ─── Dependencies ───────────────────────────

// POST /tasks/dependencies
router.post('/dependencies', validate(createDependencySchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const dependency = await prisma.taskDependency.create({
      data: req.body,
      include: {
        predecessor: { select: { id: true, name: true } },
        successor: { select: { id: true, name: true } },
      },
    });

    res.status(201).json(dependency);
  } catch (error) {
    next(error);
  }
});

// DELETE /tasks/dependencies/:id
router.delete('/dependencies/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.taskDependency.delete({ where: { id: req.params.id } });
    res.json({ message: 'Dependency removed' });
  } catch (error) {
    next(error);
  }
});

// ─── Delay Logs ─────────────────────────────

// GET /tasks/delays
router.get('/delays/list', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    if (!projectId) {
      throw new AppError('projectId query parameter required', 400);
    }

    const delays = await prisma.delayLog.findMany({
      where: { projectId },
      orderBy: { date: 'desc' },
      include: {
        task: { select: { id: true, name: true } },
        createdBy: { select: { id: true, name: true } },
      },
    });

    const totalDelayDays = delays.reduce((sum, d) => sum + d.delayDays, 0);
    const byReason = delays.reduce<Record<string, number>>((acc, d) => {
      acc[d.reason] = (acc[d.reason] || 0) + d.delayDays;
      return acc;
    }, {});

    res.json({
      data: delays,
      summary: { totalDelayDays, byReason },
    });
  } catch (error) {
    next(error);
  }
});

// POST /tasks/delays
router.post('/delays', validate(createDelayLogSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    data.date = new Date(data.date);

    const delay = await prisma.delayLog.create({
      data: { ...data, createdById: req.user!.id },
      include: {
        task: { select: { id: true, name: true } },
      },
    });

    // Update task status to DELAYED if linked
    if (data.taskId) {
      await prisma.task.update({
        where: { id: data.taskId },
        data: { status: 'DELAYED' },
      });
    }

    res.status(201).json(delay);
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/inspections.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();

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

const updateInspectionSchema = createInspectionSchema.partial();

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

const updateDefectSchema = createDefectSchema.partial();

router.use(authenticate);

// ─── Inspections ────────────────────────────

// GET /inspections
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    const status = req.query.status as string;
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;

    const where: Prisma.InspectionWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (status) where.status = status as Prisma.EnumInspectionStatusFilter;

    const [inspections, total] = await Promise.all([
      prisma.inspection.findMany({
        where,
        skip,
        take: limit,
        orderBy: { scheduledDate: 'desc' },
        include: {
          project: { select: { id: true, name: true } },
          _count: { select: { items: true, defects: true } },
          createdBy: { select: { id: true, name: true } },
        },
      }),
      prisma.inspection.count({ where }),
    ]);

    res.json({
      data: inspections,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /inspections
router.post('/', validate(createInspectionSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { items, ...data } = req.body;
    data.scheduledDate = new Date(data.scheduledDate);
    if (data.completedDate) data.completedDate = new Date(data.completedDate);

    const inspection = await prisma.inspection.create({
      data: {
        ...data,
        createdById: req.user!.id,
        items: items ? { create: items } : undefined,
      },
      include: {
        items: { orderBy: { sortOrder: 'asc' } },
        project: { select: { id: true, name: true } },
      },
    });

    res.status(201).json(inspection);
  } catch (error) {
    next(error);
  }
});

// GET /inspections/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const inspection = await prisma.inspection.findUnique({
      where: { id: req.params.id },
      include: {
        items: { orderBy: { sortOrder: 'asc' } },
        defects: {
          include: {
            assignedTradie: { select: { id: true, companyName: true, contactName: true } },
          },
        },
        project: { select: { id: true, name: true } },
        createdBy: { select: { id: true, name: true } },
      },
    });

    if (!inspection) {
      throw new AppError('Inspection not found', 404);
    }

    res.json(inspection);
  } catch (error) {
    next(error);
  }
});

// PUT /inspections/:id
router.put('/:id', validate(updateInspectionSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { items, ...data } = req.body;
    if (data.scheduledDate) data.scheduledDate = new Date(data.scheduledDate);
    if (data.completedDate) data.completedDate = new Date(data.completedDate);

    const inspection = await prisma.$transaction(async (tx) => {
      if (items) {
        await tx.inspectionItem.deleteMany({ where: { inspectionId: req.params.id } });
      }

      return tx.inspection.update({
        where: { id: req.params.id },
        data: {
          ...data,
          items: items ? { create: items } : undefined,
        },
        include: {
          items: { orderBy: { sortOrder: 'asc' } },
          project: { select: { id: true, name: true } },
        },
      });
    });

    res.json(inspection);
  } catch (error) {
    next(error);
  }
});

// DELETE /inspections/:id
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.inspection.delete({ where: { id: req.params.id } });
    res.json({ message: 'Inspection deleted' });
  } catch (error) {
    next(error);
  }
});

// ─── Inspection Items ───────────────────────

// POST /inspections/:id/items
router.post('/:id/items', validate(inspectionItemSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const inspId = req.params.id as string;
    const inspection = await prisma.inspection.findUnique({ where: { id: inspId } });
    if (!inspection) {
      throw new AppError('Inspection not found', 404);
    }

    const maxOrder = await prisma.inspectionItem.aggregate({
      where: { inspectionId: inspId },
      _max: { sortOrder: true },
    });

    const item = await prisma.inspectionItem.create({
      data: {
        ...req.body,
        inspectionId: inspId,
        sortOrder: req.body.sortOrder ?? ((maxOrder._max?.sortOrder ?? -1) + 1),
      },
    });

    res.status(201).json(item);
  } catch (error) {
    next(error);
  }
});

// POST /inspections/:id/items/batch
router.post('/:id/items/batch', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const inspId = req.params.id as string;
    const inspection = await prisma.inspection.findUnique({ where: { id: inspId } });
    if (!inspection) {
      throw new AppError('Inspection not found', 404);
    }

    const items = z.array(inspectionItemSchema).parse(req.body.items);

    const maxOrder = await prisma.inspectionItem.aggregate({
      where: { inspectionId: inspId },
      _max: { sortOrder: true },
    });
    let nextOrder = (maxOrder._max?.sortOrder ?? -1) + 1;

    const created = await prisma.$transaction(
      items.map((item) => {
        const { photoUrls, ...rest } = item;
        return prisma.inspectionItem.create({
          data: {
            ...rest,
            photoUrls: photoUrls ? JSON.stringify(photoUrls) : '[]',
            inspectionId: inspId,
            sortOrder: item.sortOrder ?? nextOrder++,
          },
        });
      }),
    );

    res.status(201).json(created);
  } catch (error) {
    next(error);
  }
});

// PUT /inspection-items/:id
router.put('/items/:itemId', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const updateItemSchema = z.object({
      result: z.enum(['PASS', 'FAIL', 'NA', 'NOT_INSPECTED']).optional(),
      notes: z.string().optional(),
    });
    const data = updateItemSchema.parse(req.body);

    const item = await prisma.inspectionItem.update({
      where: { id: req.params.itemId as string },
      data,
    });

    res.json(item);
  } catch (error) {
    next(error);
  }
});

// ─── Defects ────────────────────────────────

// GET /inspections/defects/list
router.get('/defects/list', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    const status = req.query.status as string;
    const severity = req.query.severity as string;
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;

    const where: Prisma.DefectWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (status) where.status = status as Prisma.EnumDefectStatusFilter;
    if (severity) where.severity = severity as Prisma.EnumDefectSeverityFilter;

    const [defects, total] = await Promise.all([
      prisma.defect.findMany({
        where,
        skip,
        take: limit,
        orderBy: { createdAt: 'desc' },
        include: {
          project: { select: { id: true, name: true } },
          inspection: { select: { id: true, title: true, type: true } },
          assignedTradie: { select: { id: true, companyName: true, contactName: true } },
          createdBy: { select: { id: true, name: true } },
        },
      }),
      prisma.defect.count({ where }),
    ]);

    res.json({
      data: defects,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /inspections/defects
router.post('/defects', validate(createDefectSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.dueDate) data.dueDate = new Date(data.dueDate);
    if (data.rectifiedDate) data.rectifiedDate = new Date(data.rectifiedDate);
    if (data.verifiedDate) data.verifiedDate = new Date(data.verifiedDate);

    const defect = await prisma.defect.create({
      data: { ...data, createdById: req.user!.id },
      include: {
        project: { select: { id: true, name: true } },
        assignedTradie: { select: { id: true, companyName: true } },
      },
    });

    res.status(201).json(defect);
  } catch (error) {
    next(error);
  }
});

// GET /inspections/defects/:id
router.get('/defects/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const defect = await prisma.defect.findUnique({
      where: { id: req.params.id },
      include: {
        project: { select: { id: true, name: true } },
        inspection: { select: { id: true, title: true, type: true } },
        assignedTradie: { select: { id: true, companyName: true, contactName: true } },
        createdBy: { select: { id: true, name: true } },
      },
    });

    if (!defect) {
      throw new AppError('Defect not found', 404);
    }

    res.json(defect);
  } catch (error) {
    next(error);
  }
});

// PUT /inspections/defects/:id
router.put('/defects/:id', validate(updateDefectSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const data = req.body;
    if (data.dueDate) data.dueDate = new Date(data.dueDate);
    if (data.rectifiedDate) data.rectifiedDate = new Date(data.rectifiedDate);
    if (data.verifiedDate) data.verifiedDate = new Date(data.verifiedDate);

    const defect = await prisma.defect.update({
      where: { id: req.params.id },
      data,
      include: {
        project: { select: { id: true, name: true } },
        assignedTradie: { select: { id: true, companyName: true } },
      },
    });

    res.json(defect);
  } catch (error) {
    next(error);
  }
});

// DELETE /inspections/defects/:id
router.delete('/defects/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.defect.delete({ where: { id: req.params.id } });
    res.json({ message: 'Defect deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/documents.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { Prisma } from '@prisma/client';
import prisma from '../lib/prisma';
import multer from 'multer';
import path from 'path';
import fs from 'fs';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';
import { config } from '../config';

const router = Router();

// Multer storage configuration
const storage = multer.diskStorage({
  destination: (_req, _file, cb) => {
    const uploadDir = config.upload.destination;
    if (!fs.existsSync(uploadDir)) {
      fs.mkdirSync(uploadDir, { recursive: true });
    }
    cb(null, uploadDir);
  },
  filename: (_req, file, cb) => {
    const uniqueSuffix = `${Date.now()}-${Math.round(Math.random() * 1e9)}`;
    const ext = path.extname(file.originalname);
    cb(null, `${uniqueSuffix}${ext}`);
  },
});

const upload = multer({
  storage,
  limits: { fileSize: config.upload.maxFileSize },
});

const folderValues = [
  'PLANS', 'CONTRACTS', 'PERMITS', 'SWMS', 'CERTIFICATES',
  'INSURANCE', 'PHOTOS', 'REPORTS', 'CORRESPONDENCE', 'OTHER',
] as const;

const createDocumentSchema = z.object({
  projectId: z.string().uuid(),
  folder: z.enum(folderValues),
  name: z.string().min(1),
  description: z.string().optional(),
});

const updateDocumentSchema = z.object({
  folder: z.enum(folderValues).optional(),
  name: z.string().min(1).optional(),
  description: z.string().optional(),
});

router.use(authenticate);

// GET /documents
router.get('/', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projectId = req.query.projectId as string;
    const folder = req.query.folder as string;
    const search = req.query.search as string;
    const page = parseInt(req.query.page as string) || 1;
    const limit = Math.min(parseInt(req.query.limit as string) || 20, 100);
    const skip = (page - 1) * limit;

    const where: Prisma.DocumentWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (folder) where.folder = folder;
    if (search) {
      where.OR = [
        { name: { contains: search } },
        { description: { contains: search } },
      ];
    }

    const [documents, total] = await Promise.all([
      prisma.document.findMany({
        where,
        skip,
        take: limit,
        orderBy: { createdAt: 'desc' },
        include: {
          project: { select: { id: true, name: true } },
          uploadedBy: { select: { id: true, name: true } },
        },
      }),
      prisma.document.count({ where }),
    ]);

    res.json({
      data: documents,
      pagination: { page, limit, total, pages: Math.ceil(total / limit) },
    });
  } catch (error) {
    next(error);
  }
});

// POST /documents/upload
router.post('/upload', upload.single('file'), validate(createDocumentSchema, 'body'), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    if (!req.file) {
      throw new AppError('File is required', 400);
    }

    const { projectId, folder, name, description } = req.body;

    const document = await prisma.document.create({
      data: {
        projectId,
        folder,
        name,
        description,
        fileUrl: `/uploads/${req.file.filename}`,
        fileSize: req.file.size,
        mimeType: req.file.mimetype,
        uploadedById: req.user!.id,
      },
      include: {
        project: { select: { id: true, name: true } },
        uploadedBy: { select: { id: true, name: true } },
      },
    });

    res.status(201).json(document);
  } catch (error) {
    next(error);
  }
});

// GET /documents/:id
router.get('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const docId = req.params.id as string;
    const document = await prisma.document.findUnique({
      where: { id: docId },
      include: {
        project: { select: { id: true, name: true } },
        uploadedBy: { select: { id: true, name: true } },
      },
    });

    if (!document) {
      throw new AppError('Document not found', 404);
    }

    res.json(document);
  } catch (error) {
    next(error);
  }
});

// GET /documents/:id/download
router.get('/:id/download', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const docId = req.params.id as string;
    const document = await prisma.document.findUnique({
      where: { id: docId },
    });

    if (!document) {
      throw new AppError('Document not found', 404);
    }

    const filePath = path.resolve(document.fileUrl.replace(/^\//, ''));
    if (!fs.existsSync(filePath)) {
      throw new AppError('File not found on disk', 404);
    }

    res.download(filePath, document.name);
  } catch (error) {
    next(error);
  }
});

// PUT /documents/:id
router.put('/:id', validate(updateDocumentSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const docId = req.params.id as string;
    const document = await prisma.document.update({
      where: { id: docId },
      data: req.body,
      include: {
        project: { select: { id: true, name: true } },
        uploadedBy: { select: { id: true, name: true } },
      },
    });

    res.json(document);
  } catch (error) {
    next(error);
  }
});

// POST /documents/:id/version — upload a new version of an existing document
router.post('/:id/version', upload.single('file'), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    if (!req.file) {
      throw new AppError('File is required', 400);
    }

    const docId = req.params.id as string;
    const existing = await prisma.document.findUnique({ where: { id: docId } });
    if (!existing) {
      throw new AppError('Document not found', 404);
    }

    // Find highest version for this document name+project
    const maxVersion = await prisma.document.aggregate({
      where: { projectId: existing.projectId, name: existing.name },
      _max: { version: true },
    });

    const newVersion = (maxVersion._max?.version ?? 1) + 1;

    const document = await prisma.document.create({
      data: {
        projectId: existing.projectId,
        folder: existing.folder,
        name: existing.name,
        description: existing.description,
        fileUrl: `/uploads/${req.file.filename}`,
        fileSize: req.file.size,
        mimeType: req.file.mimetype,
        version: newVersion,
        uploadedById: req.user!.id,
      },
      include: {
        project: { select: { id: true, name: true } },
        uploadedBy: { select: { id: true, name: true } },
      },
    });

    res.status(201).json(document);
  } catch (error) {
    next(error);
  }
});

// GET /documents/:id/versions — get version history for a document
router.get('/:id/versions', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const docId = req.params.id as string;
    const document = await prisma.document.findUnique({ where: { id: docId } });
    if (!document) {
      throw new AppError('Document not found', 404);
    }

    const versions = await prisma.document.findMany({
      where: { projectId: document.projectId, name: document.name },
      orderBy: { version: 'desc' },
      include: {
        uploadedBy: { select: { id: true, name: true } },
      },
    });

    res.json(versions);
  } catch (error) {
    next(error);
  }
});

// DELETE /documents/:id
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const docId = req.params.id as string;
    const document = await prisma.document.findUnique({ where: { id: docId } });
    if (!document) {
      throw new AppError('Document not found', 404);
    }

    // Delete file from disk
    const filePath = path.resolve(document.fileUrl.replace(/^\//, ''));
    if (fs.existsSync(filePath)) {
      fs.unlinkSync(filePath);
    }

    await prisma.document.delete({ where: { id: docId } });
    res.json({ message: 'Document deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/dashboard.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { AuthRequest } from '../types';

const router = Router();

router.use(authenticate);

// GET /dashboard/stats
router.get('/stats', async (_req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const now = new Date();
    const sevenDaysFromNow = new Date();
    sevenDaysFromNow.setDate(now.getDate() + 7);
    const thirtyDaysFromNow = new Date();
    thirtyDaysFromNow.setDate(now.getDate() + 30);

    // Outstanding invoices (status SENT or OVERDUE)
    const outstandingInvoices = await prisma.invoice.aggregate({
      where: { status: { in: ['SENT', 'OVERDUE'] } },
      _sum: { amountDue: true },
      _count: true,
    });

    // Expiring licences (tradies with licence/insurance/workers comp expiring in 30 days)
    const expiringLicences = await prisma.tradie.count({
      where: {
        status: 'ACTIVE',
        OR: [
          { licenceExpiry: { lte: thirtyDaysFromNow } },
          { insuranceExpiry: { lte: thirtyDaysFromNow } },
          { workersCompExpiry: { lte: thirtyDaysFromNow } },
        ],
      },
    });

    // Upcoming milestones (tasks where isMilestone=true and startDate within next 7 days)
    const upcomingMilestones = await prisma.task.findMany({
      where: {
        isMilestone: true,
        startDate: { gte: now, lte: sevenDaysFromNow },
        status: { not: 'COMPLETE' },
      },
      include: { project: { select: { id: true, name: true } } },
      orderBy: { startDate: 'asc' },
      take: 5,
    });

    // Overdue/delayed tasks
    const overdueTasks = await prisma.task.findMany({
      where: { status: 'DELAYED' },
      include: { project: { select: { id: true, name: true } } },
      orderBy: { endDate: 'asc' },
      take: 5,
    });

    // Open defects past due date
    const overdueDefects = await prisma.defect.findMany({
      where: {
        status: 'OPEN',
        dueDate: { lt: now },
      },
      include: { project: { select: { id: true, name: true } } },
      orderBy: { dueDate: 'asc' },
      take: 5,
    });

    // Cash position - aggregate PAID transactions
    const incomeTotal = await prisma.transaction.aggregate({
      where: { type: 'INCOME', status: 'PAID' },
      _sum: { totalAmount: true },
    });
    const expenseTotal = await prisma.transaction.aggregate({
      where: { type: 'EXPENSE', status: 'PAID' },
      _sum: { totalAmount: true },
    });

    res.json({
      outstandingInvoices: {
        count: outstandingInvoices._count,
        total: Number(outstandingInvoices._sum.amountDue ?? 0),
      },
      expiringLicences,
      upcomingMilestones: upcomingMilestones.map((m) => ({
        id: m.id,
        name: m.name,
        startDate: m.startDate,
        projectId: m.project.id,
        projectName: m.project.name,
      })),
      cashPosition: {
        totalIncome: Number(incomeTotal._sum.totalAmount ?? 0),
        totalExpenses: Number(expenseTotal._sum.totalAmount ?? 0),
      },
      overdueItems: {
        tasks: overdueTasks.map((t) => ({
          id: t.id,
          name: t.name,
          endDate: t.endDate,
          projectId: t.project.id,
          projectName: t.project.name,
        })),
        defects: overdueDefects.map((d) => ({
          id: d.id,
          title: d.title,
          dueDate: d.dueDate,
          projectId: d.project.id,
          projectName: d.project.name,
        })),
        totalCount: overdueTasks.length + overdueDefects.length,
      },
    });
  } catch (error) {
    next(error);
  }
});

// GET /dashboard/recent-activity
router.get('/recent-activity', async (_req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const activities = await prisma.auditLog.findMany({
      orderBy: { createdAt: 'desc' },
      take: 20,
      include: {
        user: { select: { id: true, name: true } },
      },
    });

    res.json(activities);
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/reports.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import prisma from '../lib/prisma';
import { authenticate } from '../middleware/auth';
import { AuthRequest, AppError } from '../types';

const router = Router();

router.use(authenticate);

// GET /reports/gst?startDate=YYYY-MM-DD&endDate=YYYY-MM-DD
router.get('/gst', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { startDate, endDate } = req.query;
    if (!startDate || !endDate) {
      throw new AppError('startDate and endDate query parameters required', 400);
    }

    const start = new Date(startDate as string);
    const end = new Date(endDate as string);

    const transactions = await prisma.transaction.findMany({
      where: {
        date: { gte: start, lte: end },
        status: { in: ['APPROVED', 'PAID'] },
      },
      orderBy: { date: 'asc' },
      select: {
        id: true,
        type: true,
        category: true,
        description: true,
        amount: true,
        gstAmount: true,
        totalAmount: true,
        date: true,
      },
    });

    let gstCollected = 0;
    let gstPaid = 0;

    const mapped = transactions.map((t) => {
      const gst = Number(t.gstAmount);
      if (t.type === 'INCOME') {
        gstCollected += gst;
      } else {
        gstPaid += gst;
      }
      return {
        date: t.date,
        description: t.description,
        type: t.type,
        amount: Number(t.totalAmount),
        gstAmount: gst,
      };
    });

    res.json({
      gstCollected,
      gstPaid,
      netGst: gstCollected - gstPaid,
      transactions: mapped,
    });
  } catch (error) {
    next(error);
  }
});

// GET /reports/budget?startDate=YYYY-MM-DD&endDate=YYYY-MM-DD
router.get('/budget', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { startDate, endDate } = req.query;

    const projects = await prisma.project.findMany({
      where: { status: { not: 'CANCELLED' } },
      include: {
        budgetCategories: { select: { budgetAmount: true, revisedBudget: true } },
        transactions: {
          where: {
            type: 'EXPENSE',
            status: { in: ['APPROVED', 'PAID'] },
            ...(startDate && endDate
              ? { date: { gte: new Date(startDate as string), lte: new Date(endDate as string) } }
              : {}),
          },
          select: { totalAmount: true },
        },
      },
    });

    const result = projects.map((p) => {
      const totalBudget = p.budgetCategories.reduce(
        (sum, c) => sum + Number(c.revisedBudget ?? c.budgetAmount),
        0,
      );
      const totalSpent = p.transactions.reduce((sum, t) => sum + Number(t.totalAmount), 0);
      return {
        id: p.id,
        name: p.name,
        status: p.status,
        totalBudget,
        totalSpent,
        variance: totalBudget - totalSpent,
      };
    });

    res.json({ projects: result });
  } catch (error) {
    next(error);
  }
});

// GET /reports/cashflow?startDate=YYYY-MM-DD&endDate=YYYY-MM-DD
router.get('/cashflow', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { startDate, endDate } = req.query;

    const where: Record<string, unknown> = { type: 'ACTUAL' };
    if (startDate && endDate) {
      where.month = { gte: new Date(startDate as string), lte: new Date(endDate as string) };
    }

    const entries = await prisma.cashFlowEntry.findMany({
      where,
      orderBy: { month: 'asc' },
      select: { month: true, incomeAmount: true, expenseAmount: true },
    });

    // Group by month string (YYYY-MM)
    const grouped: Record<string, { totalIncome: number; totalExpense: number }> = {};
    for (const e of entries) {
      const key = e.month.toISOString().slice(0, 7);
      if (!grouped[key]) grouped[key] = { totalIncome: 0, totalExpense: 0 };
      grouped[key].totalIncome += Number(e.incomeAmount);
      grouped[key].totalExpense += Number(e.expenseAmount);
    }

    let cumulative = 0;
    const result = Object.entries(grouped)
      .sort(([a], [b]) => a.localeCompare(b))
      .map(([month, { totalIncome, totalExpense }]) => {
        cumulative += totalIncome - totalExpense;
        return { month, totalIncome, totalExpense, cumulative };
      });

    res.json({ entries: result });
  } catch (error) {
    next(error);
  }
});

// GET /reports/status
router.get('/status', async (_req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const projects = await prisma.project.findMany({
      where: { status: { not: 'CANCELLED' } },
      select: {
        id: true,
        name: true,
        status: true,
        contractValue: true,
        startDate: true,
        estimatedEndDate: true,
        tasks: { select: { percentComplete: true } },
      },
    });

    const result = projects.map((p) => {
      const progress =
        p.tasks.length > 0
          ? Math.round(p.tasks.reduce((sum, t) => sum + t.percentComplete, 0) / p.tasks.length)
          : 0;
      return {
        id: p.id,
        name: p.name,
        status: p.status,
        progress,
        contractValue: Number(p.contractValue ?? 0),
        startDate: p.startDate,
        estimatedEndDate: p.estimatedEndDate,
      };
    });

    res.json({ projects: result });
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/routes/users.ts

```typescript
import { Router, Response, NextFunction } from 'express';
import prisma from '../lib/prisma';
import { authenticate, authorize } from '../middleware/auth';
import { AuthRequest, AppError } from '../types';
import { logAudit } from '../lib/auditLog';

const router = Router();

router.use(authenticate);

// GET /users - list all users (admin only)
router.get('/', authorize('ADMIN'), async (_req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const users = await prisma.user.findMany({
      select: { id: true, email: true, name: true, phone: true, role: true, createdAt: true, updatedAt: true },
      orderBy: { createdAt: 'desc' },
    });
    res.json(users);
  } catch (error) {
    next(error);
  }
});

// PUT /users/:id/role - update user role (admin only)
router.put('/:id/role', authorize('ADMIN'), async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { role } = req.body;
    const validRoles = ['ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR', 'ACCOUNTS', 'TRADIE'];
    if (!validRoles.includes(role)) {
      throw new AppError('Invalid role', 400);
    }
    const user = await prisma.user.update({
      where: { id: req.params.id as string },
      data: { role },
      select: { id: true, email: true, name: true, phone: true, role: true, createdAt: true, updatedAt: true },
    });
    await logAudit(req.user!.id, 'UPDATE', 'User', req.params.id, { role });
    res.json(user);
  } catch (error) {
    next(error);
  }
});

export default router;

```

## server/src/__tests__/auth.test.ts

```typescript
import { describe, it, expect, vi, beforeAll, beforeEach } from 'vitest';
import request from 'supertest';
import express from 'express';
import bcrypt from 'bcryptjs';

const mockPrisma = {
  $queryRaw: vi.fn().mockResolvedValue([{ 1: 1 }]),
  $connect: vi.fn(),
  $disconnect: vi.fn(),
  user: {
    findUnique: vi.fn(),
    create: vi.fn(),
    update: vi.fn(),
  },
};

vi.mock('@prisma/client', () => {
  class MockPrismaClient {
    $queryRaw = mockPrisma.$queryRaw;
    $connect = mockPrisma.$connect;
    $disconnect = mockPrisma.$disconnect;
    user = mockPrisma.user;
  }
  return { PrismaClient: MockPrismaClient };
});

// Bypass rate limiting in tests
vi.mock('express-rate-limit', () => ({
  default: () => (_req: unknown, _res: unknown, next: () => void) => next(),
}));

let app: express.Express;
let hashedPassword: string;

beforeAll(async () => {
  hashedPassword = await bcrypt.hash('password123', 12);

  // Build a minimal Express app with auth routes (avoids app.listen and rate limiter state)
  const authRoutes = (await import('../routes/auth')).default;
  const { errorHandler } = await import('../middleware/errorHandler');

  app = express();
  app.use(express.json());
  app.use('/api/auth', authRoutes);
  app.use(errorHandler);
});

describe('Auth routes', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  describe('POST /api/auth/register', () => {
    it('returns 400 for missing required fields', async () => {
      const res = await request(app)
        .post('/api/auth/register')
        .send({ email: 'test@example.com' });

      expect(res.status).toBe(400);
      expect(res.body).toHaveProperty('error', 'Validation failed');
      expect(res.body.details).toBeInstanceOf(Array);
    });

    it('returns 400 for invalid email format', async () => {
      const res = await request(app)
        .post('/api/auth/register')
        .send({ email: 'not-an-email', password: 'password123', name: 'Test' });

      expect(res.status).toBe(400);
      expect(res.body).toHaveProperty('error', 'Validation failed');
    });

    it('returns 400 for password too short', async () => {
      const res = await request(app)
        .post('/api/auth/register')
        .send({ email: 'test@example.com', password: 'short', name: 'Test' });

      expect(res.status).toBe(400);
      expect(res.body).toHaveProperty('error', 'Validation failed');
    });

    it('returns 201 with token on successful registration', async () => {
      mockPrisma.user.findUnique.mockResolvedValue(null);
      mockPrisma.user.create.mockResolvedValue({
        id: 'new-user',
        email: 'new@example.com',
        name: 'New User',
        phone: null,
        role: 'ADMIN',
        createdAt: new Date(),
      });

      const res = await request(app)
        .post('/api/auth/register')
        .send({ email: 'new@example.com', password: 'password123', name: 'New User' });

      expect(res.status).toBe(201);
      expect(res.body).toHaveProperty('token');
      expect(res.body).toHaveProperty('refreshToken');
      expect(res.body.user).toHaveProperty('email', 'new@example.com');
    });

    it('returns 409 when email already registered', async () => {
      mockPrisma.user.findUnique.mockResolvedValue({
        id: 'user-1',
        email: 'test@example.com',
        name: 'Test User',
        role: 'ADMIN',
        password: hashedPassword,
      });

      const res = await request(app)
        .post('/api/auth/register')
        .send({ email: 'test@example.com', password: 'password123', name: 'Test' });

      expect(res.status).toBe(409);
      expect(res.body).toHaveProperty('error', 'Email already registered');
    });
  });

  describe('POST /api/auth/login', () => {
    it('returns 400 for missing credentials', async () => {
      const res = await request(app)
        .post('/api/auth/login')
        .send({});

      expect(res.status).toBe(400);
      expect(res.body).toHaveProperty('error', 'Validation failed');
    });

    it('returns 401 for non-existent user', async () => {
      mockPrisma.user.findUnique.mockResolvedValue(null);

      const res = await request(app)
        .post('/api/auth/login')
        .send({ email: 'nobody@example.com', password: 'password123' });

      expect(res.status).toBe(401);
      expect(res.body).toHaveProperty('error', 'Invalid email or password');
    });

    it('returns 401 for wrong password', async () => {
      mockPrisma.user.findUnique.mockResolvedValue({
        id: 'user-1',
        email: 'test@example.com',
        name: 'Test User',
        phone: '0412345678',
        role: 'ADMIN',
        password: hashedPassword,
      });

      const res = await request(app)
        .post('/api/auth/login')
        .send({ email: 'test@example.com', password: 'wrongpassword' });

      expect(res.status).toBe(401);
      expect(res.body).toHaveProperty('error', 'Invalid email or password');
    });

    it('returns 200 with tokens on valid login', async () => {
      mockPrisma.user.findUnique.mockResolvedValue({
        id: 'user-1',
        email: 'test@example.com',
        name: 'Test User',
        phone: '0412345678',
        role: 'ADMIN',
        password: hashedPassword,
      });

      const res = await request(app)
        .post('/api/auth/login')
        .send({ email: 'test@example.com', password: 'password123' });

      expect(res.status).toBe(200);
      expect(res.body).toHaveProperty('token');
      expect(res.body).toHaveProperty('refreshToken');
      expect(res.body.user).toHaveProperty('email', 'test@example.com');
      expect(res.body.user).toHaveProperty('firstName');
      expect(res.body.user).toHaveProperty('lastName');
    });
  });
});

```

## server/src/__tests__/health.test.ts

```typescript
import { describe, it, expect, vi, beforeAll } from 'vitest';
import request from 'supertest';
import express from 'express';
import { PrismaClient } from '@prisma/client';

const mockPrisma = {
  $queryRaw: vi.fn().mockResolvedValue([{ 1: 1 }]),
};

vi.mock('@prisma/client', () => {
  class MockPrismaClient {
    $queryRaw = mockPrisma.$queryRaw;
  }
  return { PrismaClient: MockPrismaClient };
});

describe('GET /api/health', () => {
  let app: express.Express;

  beforeAll(() => {
    const prisma = new PrismaClient();
    const startTime = Date.now();

    app = express();
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
  });

  it('returns 200 with status ok when DB is connected', async () => {
    mockPrisma.$queryRaw.mockResolvedValue([{ 1: 1 }]);

    const res = await request(app).get('/api/health');
    expect(res.status).toBe(200);
    expect(res.body).toHaveProperty('status', 'ok');
    expect(res.body).toHaveProperty('version', '1.0.0');
    expect(res.body).toHaveProperty('db', 'connected');
    expect(res.body).toHaveProperty('uptime');
    expect(typeof res.body.uptime).toBe('number');
  });

  it('returns degraded status when DB query fails', async () => {
    mockPrisma.$queryRaw.mockRejectedValue(new Error('DB down'));

    const res = await request(app).get('/api/health');
    expect(res.status).toBe(200);
    expect(res.body).toHaveProperty('status', 'degraded');
    expect(res.body).toHaveProperty('db', 'error');
  });
});

```
