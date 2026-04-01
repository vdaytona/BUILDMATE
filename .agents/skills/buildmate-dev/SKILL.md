---
name: buildmate-dev
description: Develops and maintains the BuildMate construction management platform. Use when working on any BuildMate feature, bug fix, or task from TASKS.md. Provides project-specific conventions, patterns, and validation rules.
compatibility: amp
tags: [buildmate, construction, fullstack, express, react, prisma]
---

# BuildMate Development Skill

You are working on **BuildMate**, a construction management platform for Australian builders. Follow these rules precisely.

## Step 1: Read Context

Before ANY code changes, read these files:
1. `TASKS.md` — Find the task you're implementing, understand its requirements
2. `DESIGN_DOCUMENT.md` — Sections relevant to your task (see section index below)
3. The existing source files you'll be modifying

### Section Index
- **Section 5:** Database schema & enum values
- **Section 6:** API endpoints & response formats
- **Section 7:** Frontend routing, state management, API client patterns
- **Section 15:** UI/UX design system (colours, typography, spacing, component specs)
- **Section 16:** Zod schemas, route implementation patterns, dependency versions

## Step 2: Implement Following These Patterns

### Server-Side (Express + Prisma)

**New route file pattern:**
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

// Zod schemas defined inline

router.use(authenticate);

// Routes...

export default router;
```

**Paginated endpoints:** Return `{ data: [], pagination: { page, limit, total, pages } }`
**Non-paginated endpoints:** Return flat arrays (tasks, budget categories, cashflow entries)
**Date handling:** Convert `z.string().datetime()` inputs to `new Date()` before Prisma
**Soft deletes:** Projects → set status CANCELLED. Tradies → set status INACTIVE.
**Validation errors:** Return `{ error: "Validation failed", details: [{ field, message }] }`

### Frontend (React + TypeScript + Tailwind)

**New page pattern:**
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus } from 'lucide-react';
// API imports, UI component imports
import { cn, formatCurrency, formatDate } from '@/lib/utils';

export default function NewPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();
  // State, mutations, queries...
  
  return (
    <div className="space-y-6">
      {/* Back link */}
      <div>
        <Link to={`/projects/${id}`} className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700">
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Page Title</h2>
          <Button size="sm" onClick={() => setShowCreate(true)}>
            <Plus className="h-4 w-4" /> Add Item
          </Button>
        </div>
      </div>
      {/* Content */}
    </div>
  );
}
```

**New API function pattern:**
```typescript
import client from './client';
import type { Entity } from '@/types';

export async function getEntities(projectId: string): Promise<Entity[]> {
  const { data } = await client.get('/endpoint', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}
```

**Modal form pattern:**
```tsx
<Modal open={showCreate} onClose={() => setShowCreate(false)} title="Create Item">
  <form onSubmit={handleSubmit} className="space-y-4">
    <Input id="name" label="Name" required value={form.name} onChange={...} />
    <div className="flex justify-end gap-2 pt-2">
      <Button variant="secondary" type="button" onClick={() => setShowCreate(false)}>Cancel</Button>
      <Button type="submit" disabled={mutation.isPending}>
        {mutation.isPending ? 'Saving…' : 'Save'}
      </Button>
    </div>
  </form>
</Modal>
```

### Styling Rules

| Element | Classes |
|---------|---------|
| Page heading | `text-xl font-bold text-gray-900` |
| Card title | `text-sm font-semibold text-gray-900` |
| Body text | `text-sm text-gray-900` |
| Secondary text | `text-sm text-gray-500` |
| Small label | `text-xs text-gray-500` |
| Form label | `text-sm font-medium text-gray-700` |
| Tab bar container | `flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1` |
| Active tab | `rounded-md bg-white text-gray-900 shadow-sm` |
| Income amounts | `text-success` |
| Expense amounts | `text-danger` |
| Section spacing | `space-y-6` |
| Card grid gap | `gap-4` |
| Form field gap | `space-y-4` |
| Button icon gap | `gap-2` |

### Component Usage

- **Always** use existing UI components: `Button`, `Input`, `Modal`, `Card`, `Badge`, `Table`, `EmptyState`, `LoadingSpinner`
- **Never** create new UI components unless the task specifically requires it
- **Badge:** Pass lowercase status strings (e.g., `paid`, `active`, `in_progress`)
- **Table:** Pass `headers` array and render `<tr>` children
- **EmptyState:** Always include icon (h-12 w-12), title, description, and action button

## Step 3: Validate

After implementing:
1. Check the server compiles: `cd server && npx tsc --noEmit`
2. Check the web compiles: `cd web && npx tsc --noEmit`
3. Update `TASKS.md`: Change the task status from 🔲 to ✅
4. If you added a new route, register it in `server/src/index.ts`
5. If you added a new page, add the route in `web/src/App.tsx`

## Known Gotchas

- SQLite doesn't support enums — all enum fields are `String` type in Prisma
- Frontend types in `web/src/types/index.ts` don't match server responses (see DESIGN_DOCUMENT §13.1)
- `mode: 'insensitive'` in Prisma `contains` works differently in SQLite vs PostgreSQL
- Prisma `Decimal` fields come back as strings in JSON — may need `Number()` conversion on frontend
- The `company` field exists on frontend `User` type but NOT on server `User` model
- Task `progress` (frontend) vs `percentComplete` (server/Prisma) — use `percentComplete` when talking to server
