---
name: buildmate-review
description: Reviews BuildMate code changes for consistency with the project's design system, coding patterns, and architecture. Use when reviewing PRs, validating implementations, or auditing code quality.
compatibility: amp
tags: [buildmate, review, quality]
---

# BuildMate Code Review Skill

Review code changes against BuildMate's established patterns and conventions.

## Review Checklist

### 1. Architecture Compliance
- [ ] Server routes use `router.use(authenticate)` at top level
- [ ] Each route file creates its own `new PrismaClient()` at module level
- [ ] Zod schemas are defined inline in route files (not in separate files)
- [ ] API responses follow the correct format: paginated `{data, pagination}` or flat arrays
- [ ] New routes are registered in `server/src/index.ts`
- [ ] New pages are registered in `web/src/App.tsx`

### 2. Frontend Patterns
- [ ] Uses `@/` path alias for imports
- [ ] Uses existing UI components (Button, Input, Modal, Card, Badge, Table, EmptyState, LoadingSpinner)
- [ ] Uses `useQuery`/`useMutation` from TanStack React Query (NOT useEffect + fetch)
- [ ] Query keys are consistent and include relevant IDs
- [ ] Mutations call `queryClient.invalidateQueries` on success
- [ ] Forms follow the modal pattern with `isPending` state on submit button
- [ ] Loading states use `<LoadingSpinner className="py-20" size="lg" />`
- [ ] Empty states use `<EmptyState>` component with icon, title, description

### 3. Styling Consistency
- [ ] Uses `cn()` for conditional classes
- [ ] Follows typography scale: `text-xl font-bold` (headings), `text-sm` (body), `text-xs` (labels)
- [ ] Uses custom colour tokens (`text-primary`, `text-success`, `text-warning`, `text-danger`)
- [ ] Page layout: `<div className="space-y-6">`
- [ ] Cards use `rounded-xl border border-gray-200 bg-white shadow-sm`
- [ ] Select elements match Input styling
- [ ] Income = green (`text-success`), Expense = red (`text-danger`)
- [ ] Currency formatted with `formatCurrency()`, dates with `formatDate()`

### 4. Data Integrity
- [ ] Server validates all inputs with Zod schemas
- [ ] Dates converted from strings to `new Date()` before Prisma operations
- [ ] User ID attached via `req.user!.id` (not from request body)
- [ ] Soft deletes used for projects (CANCELLED) and tradies (INACTIVE)
- [ ] Error handling uses `AppError` class with appropriate HTTP status codes

### 5. Known Issues Preserved
- [ ] `mapUser()` workaround in `web/src/api/auth.ts` not broken
- [ ] Paginated response unwrapping pattern maintained
- [ ] `accessToken → token` mapping preserved
- [ ] `percentComplete` used in server, `progress` in frontend types

### 6. Security
- [ ] No secrets or keys in source code
- [ ] JWT auth required on all non-auth endpoints
- [ ] Password never returned in API responses (Prisma `select` excludes it)
- [ ] File uploads go to `./uploads` directory
- [ ] No raw SQL — all queries through Prisma ORM
