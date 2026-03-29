# BuildMate — AI Agent Instructions

## Project Overview

BuildMate is a construction management platform for Australian builders. Full-stack monorepo: Express/Prisma/SQLite backend + React/Vite/Tailwind frontend.

## Required Reading (in order)

Before making ANY changes, read these documents:

1. **`PRODUCT_SPEC.md`** — Product requirements (what to build)
2. **`DESIGN_DOCUMENT.md`** — Technical design (how it's built) — Sections 1-16
3. **`TASKS.md`** — Task breakdown with 231 tasks across 9 phases (what's done/remaining)
4. **`SOURCE_CODE.md`** — Complete source code reference for all 50+ files
5. **`AGENT_PROMPT.md`** — Comprehensive prompt with all conventions and patterns

## Critical Rules

1. **Read before writing.** Always read existing files before modifying them.
2. **Follow existing patterns exactly.** See DESIGN_DOCUMENT.md Section 16 for all conventions.
3. **Update TASKS.md** when completing any task (change 🔲 to ✅).
4. **Use existing UI components** — never create new ones unless a task requires it.
5. **Use `@/` path alias** for all frontend imports.
6. **Use `cn()` from `@/lib/utils`** for conditional Tailwind classes.
7. **Known field mismatches exist** between frontend types and server responses — see DESIGN_DOCUMENT.md Section 13.1. Don't "fix" them unless working on tasks 1.11.1-1.11.4.

## Quick Reference

- **Server port:** 3001
- **Web port:** 5173 (Vite dev server, proxies /api to :3001)
- **Database:** SQLite at `server/prisma/dev.db`
- **Auth:** JWT in localStorage as `buildmate_token`
- **Currency format:** AUD, no decimals, `formatCurrency()` from `@/lib/utils`
- **Date format:** en-AU (e.g., "26 Mar 2026"), `formatDate()` from `@/lib/utils`
