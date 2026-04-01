# BuildMate — Web Frontend Source Code (Config, Core & API)

## Table of Contents
- web/package.json through web/src/api/users.ts (29 files)

---


## web/package.json

```json
{
  "name": "buildmate-web",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage"
  },
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
    "@testing-library/jest-dom": "^6.9.1",
    "@testing-library/react": "^16.3.2",
    "@testing-library/user-event": "^14.6.1",
    "@types/react": "^18.3.11",
    "@types/react-dom": "^18.3.1",
    "@vitejs/plugin-react": "^4.3.2",
    "@vitest/coverage-v8": "^2.1.9",
    "autoprefixer": "^10.4.20",
    "jsdom": "^25.0.1",
    "postcss": "^8.4.47",
    "tailwindcss": "^3.4.13",
    "typescript": "^5.6.3",
    "vite": "^5.4.9",
    "vitest": "^2.1.9"
  }
}

```

## web/tsconfig.json

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

## web/vite.config.ts

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3001',
        changeOrigin: true,
      },
      '/uploads': {
        target: 'http://localhost:3001',
        changeOrigin: true,
      },
    },
  },
});

```

## web/vitest.config.ts

```typescript
/// <reference types="vitest" />
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    include: ['src/**/*.test.{ts,tsx}'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'lcov'],
      include: ['src/**/*.{ts,tsx}'],
      exclude: ['src/test/**', 'src/**/*.test.{ts,tsx}', 'src/vite-env.d.ts'],
    },
  },
});

```

## web/tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#EFF6FF',
          100: '#DBEAFE',
          200: '#BFDBFE',
          300: '#93C5FD',
          400: '#60A5FA',
          500: '#3B82F6',
          600: '#1E40AF',
          700: '#1E3A8A',
          800: '#1E3380',
          900: '#1E2D70',
          DEFAULT: '#1E40AF',
        },
        success: {
          50: '#ECFDF5',
          100: '#D1FAE5',
          500: '#059669',
          600: '#047857',
          DEFAULT: '#059669',
        },
        warning: {
          50: '#FFFBEB',
          100: '#FEF3C7',
          500: '#D97706',
          600: '#B45309',
          DEFAULT: '#D97706',
        },
        danger: {
          50: '#FEF2F2',
          100: '#FEE2E2',
          500: '#DC2626',
          600: '#B91C1C',
          DEFAULT: '#DC2626',
        },
      },
    },
  },
  plugins: [],
};

```

## web/postcss.config.js

```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};

```

## web/index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <meta name="theme-color" content="#1E40AF" />
    <title>BuildMate - Construction Management</title>
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
  </head>
  <body class="bg-gray-50 text-gray-900 antialiased">
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

```

## web/src/index.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body {
    @apply min-h-screen;
  }
}

```

## web/src/main.tsx

```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { AuthProvider } from '@/store/authStore';
import App from '@/App';
import './index.css';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,
      retry: 1,
    },
  },
});

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <BrowserRouter>
        <AuthProvider>
          <App />
        </AuthProvider>
      </BrowserRouter>
    </QueryClientProvider>
  </React.StrictMode>,
);

```

## web/src/App.tsx

```tsx
import { Routes, Route, Navigate } from 'react-router-dom';
import { useAuth } from '@/hooks/useAuth';
import AppLayout from '@/components/layout/AppLayout';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import LoginPage from '@/pages/Login';
import RegisterPage from '@/pages/Register';
import ProfilePage from '@/pages/Profile';
import DashboardPage from '@/pages/Dashboard';
import ProjectsPage from '@/pages/projects/ProjectsPage';
import ProjectDetailPage from '@/pages/projects/ProjectDetailPage';
import BudgetPage from '@/pages/budget/BudgetPage';
import CashFlowPage from '@/pages/cashflow/CashFlowPage';
import PlanningPage from '@/pages/planning/PlanningPage';
import FinancialsPage from '@/pages/financials/FinancialsPage';
import InspectionsPage from '@/pages/inspections/InspectionsPage';
import InspectionDetailPage from '@/pages/inspections/InspectionDetailPage';
import DefectDetailPage from '@/pages/inspections/DefectDetailPage';
import DocumentsPage from '@/pages/documents/DocumentsPage';
import TradiesPage from '@/pages/tradies/TradiesPage';
import TradieDetailPage from '@/pages/tradies/TradieDetailPage';
import CompliancePage from '@/pages/tradies/CompliancePage';
import InvoicesPage from '@/pages/invoices/InvoicesPage';
import InvoiceDetailPage from '@/pages/invoices/InvoiceDetailPage';
import ReportsPage from '@/pages/reports/ReportsPage';
import GstReportPage from '@/pages/reports/GstReportPage';
import BudgetReportPage from '@/pages/reports/BudgetReportPage';
import CashFlowReportPage from '@/pages/reports/CashFlowReportPage';
import ComplianceReportPage from '@/pages/reports/ComplianceReportPage';
import ProjectStatusReportPage from '@/pages/reports/ProjectStatusReportPage';
import SettingsPage from '@/pages/Settings';
import UsersPage from '@/pages/admin/UsersPage';
import type { ReactNode } from 'react';

function ProtectedRoute({ children }: { children: ReactNode }) {
  const { token, isLoading } = useAuth();

  if (isLoading) {
    return (
      <div className="flex min-h-screen items-center justify-center">
        <LoadingSpinner size="lg" />
      </div>
    );
  }

  if (!token) {
    return <Navigate to="/login" replace />;
  }

  return <>{children}</>;
}

export default function App() {
  return (
    <Routes>
      <Route path="/login" element={<LoginPage />} />
      <Route path="/register" element={<RegisterPage />} />
      <Route
        element={
          <ProtectedRoute>
            <AppLayout />
          </ProtectedRoute>
        }
      >
        <Route path="/" element={<DashboardPage />} />
        <Route path="/projects" element={<ProjectsPage />} />
        <Route path="/projects/:id" element={<ProjectDetailPage />} />
        <Route path="/projects/:id/team" element={<ProjectDetailPage />} />
        <Route path="/projects/:id/budget" element={<BudgetPage />} />
        <Route path="/projects/:id/cashflow" element={<CashFlowPage />} />
        <Route path="/projects/:id/planning" element={<PlanningPage />} />
        <Route path="/projects/:id/financials" element={<FinancialsPage />} />
        <Route path="/projects/:id/invoices" element={<InvoicesPage />} />
        <Route path="/projects/:id/invoices/:invoiceId" element={<InvoiceDetailPage />} />
        <Route path="/projects/:id/inspections" element={<InspectionsPage />} />
        <Route path="/projects/:id/inspections/:inspectionId" element={<InspectionDetailPage />} />
        <Route path="/projects/:id/defects/:defectId" element={<DefectDetailPage />} />
        <Route path="/projects/:id/documents" element={<DocumentsPage />} />
        <Route path="/tradies" element={<TradiesPage />} />
        <Route path="/tradies/compliance" element={<CompliancePage />} />
        <Route path="/tradies/:id" element={<TradieDetailPage />} />
        <Route path="/reports" element={<ReportsPage />} />
        <Route path="/reports/gst" element={<GstReportPage />} />
        <Route path="/reports/budget" element={<BudgetReportPage />} />
        <Route path="/reports/cashflow" element={<CashFlowReportPage />} />
        <Route path="/reports/compliance" element={<ComplianceReportPage />} />
        <Route path="/reports/status" element={<ProjectStatusReportPage />} />
        <Route path="/admin/users" element={<UsersPage />} />
        <Route path="/profile" element={<ProfilePage />} />
        <Route path="/settings" element={<SettingsPage />} />
      </Route>
    </Routes>
  );
}

```

## web/src/types/index.ts

```typescript
export interface User {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  phone?: string;
  company?: string;
  role: 'admin' | 'builder' | 'viewer';
  createdAt: string;
  updatedAt: string;
}

export interface Project {
  id: string;
  name: string;
  address: string;
  suburb?: string;
  state?: string;
  postcode?: string;
  status: 'planning' | 'active' | 'on_hold' | 'complete' | 'cancelled';
  clientName?: string;
  clientEmail?: string;
  clientPhone?: string;
  contractValue: number;
  startDate?: string;
  endDate?: string;
  estimatedEndDate?: string;
  progress: number;
  description?: string;
  notes?: string;
  userId: string;
  createdAt: string;
  updatedAt: string;
}

export interface Transaction {
  id: string;
  projectId: string;
  type: 'income' | 'expense';
  category: string;
  description: string;
  amount: number;
  date: string;
  status: 'pending' | 'paid' | 'overdue' | 'cancelled';
  invoiceNumber?: string;
  tradieId?: string;
  tradie?: Tradie;
  createdAt: string;
  updatedAt: string;
}

export interface Invoice {
  id: string;
  projectId: string;
  transactionId?: string;
  invoiceNumber: string;
  amount: number;
  dueDate: string;
  status: 'draft' | 'sent' | 'paid' | 'overdue';
  tradieId?: string;
  description?: string;
  createdAt: string;
  updatedAt: string;
}

export interface BudgetCategory {
  id: string;
  projectId: string;
  costCode: string;
  name: string;
  budgetAmount: number;
  revisedBudget: number;
  spentAmount: number;
  committedAmount: number;
  forecastToComplete: number;
  forecastFinal: number;
  variance: number;
  parentId?: string;
  sortOrder: number;
  createdAt: string;
  updatedAt: string;
}

export interface Variation {
  id: string;
  projectId: string;
  categoryId?: string;
  category?: { id: string; costCode: string; name: string };
  variationNumber: number;
  title: string;
  description?: string;
  amount: number;
  status: 'DRAFT' | 'SUBMITTED' | 'APPROVED' | 'REJECTED';
  submittedDate?: string;
  approvedDate?: string;
  approvedBy?: string;
  createdAt: string;
  updatedAt: string;
}

export interface BudgetSummary {
  totalBudget: number;
  totalSpent: number;
  totalCommitted: number;
  totalForecast: number;
  totalVariance: number;
  categories: BudgetCategory[];
}

export interface CashFlowEntry {
  id: string;
  projectId: string;
  month: string;
  income: number;
  expense: number;
  net: number;
  cumulative: number;
  plannedIncome?: number;
  plannedExpense?: number;
  plannedCumulative?: number;
  createdAt: string;
  updatedAt: string;
}

export interface ClaimSchedule {
  id: string;
  projectId: string;
  claimNumber: number;
  description: string;
  amount: number;
  dueDate: string;
  status: 'upcoming' | 'submitted' | 'approved' | 'paid';
  submittedDate?: string;
  paidDate?: string;
  createdAt: string;
  updatedAt: string;
}

export interface Tradie {
  id: string;
  firstName: string;
  lastName: string;
  company?: string;
  companyName?: string;
  contactName?: string;
  trade: string;
  email?: string;
  phone: string;
  abn?: string;
  licenceNumber?: string;
  licenceExpiry?: string;
  insuranceProvider?: string;
  insurancePolicyNumber?: string;
  insuranceExpiry?: string;
  workersCompProvider?: string;
  workersCompPolicyNumber?: string;
  workersCompExpiry?: string;
  rating?: number;
  notes?: string;
  status?: string;
  userId: string;
  createdAt: string;
  updatedAt: string;
}

export interface Task {
  id: string;
  projectId: string;
  name: string;
  description?: string;
  startDate?: string;
  endDate?: string;
  duration?: number;
  percentComplete?: number;
  progress: number;
  status: 'not_started' | 'in_progress' | 'complete' | 'on_hold' | 'blocked';
  priority: 'low' | 'medium' | 'high' | 'critical';
  isMilestone: boolean;
  assignedTo?: string;
  tradieId?: string;
  tradie?: Tradie;
  parentTaskId?: string;
  parentId?: string;
  assignedTradieId?: string;
  assignedUserId?: string;
  assignedTradie?: { id: string; companyName: string; contactName?: string };
  assignedUser?: { id: string; name: string };
  colour?: string;
  stage?: string;
  baselineStartDate?: string;
  baselineEndDate?: string;
  sortOrder: number;
  predecessors?: TaskDependencyRecord[];
  successors?: TaskDependencyRecord[];
  dependencies?: TaskDependency[];
  childTasks?: { id: string; name: string; status: string; percentComplete: number }[];
  createdAt: string;
  updatedAt: string;
}

export interface TaskDependencyRecord {
  id: string;
  predecessorId: string;
  successorId: string;
  type: string;
  lagDays: number;
  predecessor?: { id: string; name: string; status?: string };
  successor?: { id: string; name: string; status?: string };
}

export interface TaskDependency {
  id: string;
  taskId: string;
  dependsOnTaskId: string;
  type: 'finish_to_start' | 'start_to_start' | 'finish_to_finish' | 'start_to_finish';
}

export interface Inspection {
  id: string;
  projectId: string;
  type: string;
  title?: string;
  inspectionDate: string;
  scheduledDate?: string;
  completedDate?: string;
  inspector?: string;
  inspectorName?: string;
  status: 'scheduled' | 'passed' | 'failed' | 'conditional';
  overallResult?: string;
  result?: string;
  notes?: string;
  items?: InspectionItem[];
  defects?: Defect[];
  project?: { id: string; name: string };
  createdBy?: { id: string; name: string };
  _count?: { items: number; defects: number };
  createdAt: string;
  updatedAt: string;
}

export interface InspectionItem {
  id: string;
  inspectionId: string;
  category: string;
  description: string;
  result: 'PASS' | 'FAIL' | 'NA' | 'NOT_INSPECTED';
  notes?: string;
  photoUrls?: string;
  sortOrder: number;
}

export interface Defect {
  id: string;
  projectId: string;
  inspectionId?: string;
  title: string;
  description: string;
  location?: string;
  severity: 'low' | 'medium' | 'high' | 'critical';
  status: 'open' | 'in_progress' | 'resolved' | 'closed';
  assignedTo?: string;
  assignedTradieId?: string;
  assignedTradie?: { id: string; companyName: string; contactName?: string };
  tradieId?: string;
  dueDate?: string;
  rectifiedDate?: string;
  verifiedDate?: string;
  resolvedDate?: string;
  photoUrls?: string;
  rectificationPhotoUrls?: string;
  inspection?: { id: string; title: string; type: string };
  project?: { id: string; name: string };
  createdBy?: { id: string; name: string };
  createdAt: string;
  updatedAt: string;
}

export interface Document {
  id: string;
  projectId: string;
  name: string;
  fileName: string;
  fileSize: number;
  mimeType: string;
  category: 'plans' | 'contracts' | 'permits' | 'swms' | 'photos' | 'reports' | 'other';
  folder?: string;
  description?: string;
  version?: number;
  url: string;
  fileUrl?: string;
  uploadedBy: string;
  createdAt: string;
  updatedAt: string;
}

export interface AuthResponse {
  token: string;
  user: User;
}

export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  limit: number;
}

```

## web/src/lib/utils.ts

```typescript
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

export function formatCurrency(amount: number): string {
  return new Intl.NumberFormat('en-AU', {
    style: 'currency',
    currency: 'AUD',
    minimumFractionDigits: 0,
    maximumFractionDigits: 0,
  }).format(amount);
}

export function formatDate(date: string): string {
  return new Date(date).toLocaleDateString('en-AU', {
    day: '2-digit',
    month: 'short',
    year: 'numeric',
  });
}

```

## web/src/lib/permissions.ts

```typescript
type Role = string;

const ROLE_PERMISSIONS: Record<string, Role[]> = {
  viewReports: ['ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR', 'ACCOUNTS'],
  manageUsers: ['ADMIN'],
  createProject: ['ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR'],
  editProject: ['ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR'],
  deleteProject: ['ADMIN', 'PROJECT_MANAGER'],
  manageTeam: ['ADMIN', 'PROJECT_MANAGER'],
  manageFinancials: ['ADMIN', 'PROJECT_MANAGER', 'ACCOUNTS'],
};

export function hasPermission(role: string | undefined, action: keyof typeof ROLE_PERMISSIONS): boolean {
  if (!role) return false;
  const allowed = ROLE_PERMISSIONS[action];
  return allowed ? allowed.includes(role.toUpperCase()) : false;
}

export function isAdmin(role: string | undefined): boolean {
  return role?.toUpperCase() === 'ADMIN';
}

export function isTradie(role: string | undefined): boolean {
  return role?.toUpperCase() === 'TRADIE';
}

```

## web/src/store/authStore.tsx

```tsx
import { createContext, useState, useEffect, useCallback, type ReactNode } from 'react';
import type { User } from '@/types';
import { getMe } from '@/api/auth';

export interface AuthContextValue {
  token: string | null;
  user: User | null;
  isLoading: boolean;
  login: (token: string, user: User) => void;
  logout: () => void;
}

export const AuthContext = createContext<AuthContextValue | null>(null);

export function AuthProvider({ children }: { children: ReactNode }) {
  const [token, setToken] = useState<string | null>(() =>
    localStorage.getItem('buildmate_token'),
  );
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(!!token);

  useEffect(() => {
    if (token && !user) {
      getMe()
        .then(setUser)
        .catch(() => {
          localStorage.removeItem('buildmate_token');
          setToken(null);
        })
        .finally(() => setIsLoading(false));
    }
  }, [token, user]);

  const login = useCallback((newToken: string, newUser: User) => {
    localStorage.setItem('buildmate_token', newToken);
    setToken(newToken);
    setUser(newUser);
  }, []);

  const logout = useCallback(() => {
    localStorage.removeItem('buildmate_token');
    setToken(null);
    setUser(null);
  }, []);

  return (
    <AuthContext.Provider value={{ token, user, isLoading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

```

## web/src/hooks/useAuth.ts

```typescript
import { useContext } from 'react';
import { AuthContext, type AuthContextValue } from '@/store/authStore';

export function useAuth(): AuthContextValue {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
}

```

## web/src/api/client.ts

```typescript
import axios from 'axios';

const client = axios.create({
  baseURL: '/api',
  headers: { 'Content-Type': 'application/json' },
});

client.interceptors.request.use((config) => {
  const token = localStorage.getItem('buildmate_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

client.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401 && !error.config?.url?.includes('/auth/login')) {
      localStorage.removeItem('buildmate_token');
    }
    return Promise.reject(error);
  },
);

export default client;

```

## web/src/api/auth.ts

```typescript
import client from './client';
import type { AuthResponse, User } from '@/types';

export async function login(email: string, password: string): Promise<AuthResponse> {
  const { data } = await client.post('/auth/login', { email, password });
  return { token: data.token, user: data.user };
}

export async function register(payload: {
  email: string;
  password: string;
  firstName: string;
  lastName: string;
  company?: string;
}): Promise<AuthResponse> {
  const { data } = await client.post('/auth/register', {
    email: payload.email,
    password: payload.password,
    name: `${payload.firstName} ${payload.lastName}`.trim(),
  });
  return { token: data.token, user: data.user };
}

export async function getMe(): Promise<User> {
  const { data } = await client.get('/auth/me');
  return data;
}

export async function updateProfile(payload: Partial<User>): Promise<User> {
  const serverPayload: Record<string, unknown> = {};
  if (payload.firstName !== undefined || payload.lastName !== undefined) {
    serverPayload.name = `${payload.firstName ?? ''} ${payload.lastName ?? ''}`.trim();
  }
  if (payload.email !== undefined) serverPayload.email = payload.email;
  if (payload.phone !== undefined) serverPayload.phone = payload.phone;
  const { data } = await client.put('/auth/me', serverPayload);
  return data;
}

```

## web/src/api/projects.ts

```typescript
import client from './client';
import type { Project } from '@/types';

export async function getProjects(params?: {
  status?: string;
  search?: string;
}): Promise<Project[]> {
  const { data } = await client.get('/projects', { params });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function getProject(id: string): Promise<Project> {
  const { data } = await client.get<Project>(`/projects/${id}`);
  return data;
}

export async function createProject(payload: Partial<Project>): Promise<Project> {
  const { data } = await client.post<Project>('/projects', payload);
  return data;
}

export async function updateProject(id: string, payload: Partial<Project>): Promise<Project> {
  const { data } = await client.put<Project>(`/projects/${id}`, payload);
  return data;
}

export async function deleteProject(id: string): Promise<void> {
  await client.delete(`/projects/${id}`);
}

export interface FinancialSummary {
  totalIncome: number;
  totalExpenses: number;
  netPosition: number;
  outstandingInvoices: number;
  retentionHeld: number;
  budgetVariance: number;
}

export async function getFinancialSummary(projectId: string): Promise<FinancialSummary> {
  const { data } = await client.get<FinancialSummary>(`/projects/${projectId}/financial-summary`);
  return data;
}

// ─── Project Team ────────────────────────────────

export interface ProjectTeamMember {
  id: string;
  projectId: string;
  userId: string;
  role: string;
  createdAt: string;
  user: { id: string; name: string; email: string; role: string };
}

export async function getProjectTeam(projectId: string): Promise<ProjectTeamMember[]> {
  const { data } = await client.get(`/projects/${projectId}/team`);
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function addProjectTeamMember(projectId: string, payload: { userId: string; role: string }): Promise<ProjectTeamMember> {
  const { data } = await client.post<ProjectTeamMember>(`/projects/${projectId}/team`, payload);
  return data;
}

export async function removeProjectTeamMember(projectId: string, memberId: string): Promise<void> {
  await client.delete(`/projects/${projectId}/team/${memberId}`);
}

// ─── Project Activity ────────────────────────────

export interface AuditLogEntry {
  id: string;
  userId: string;
  action: string;
  entityType: string;
  entityId: string;
  changes: string | null;
  createdAt: string;
  user: { id: string; name: string; email: string };
}

export async function getProjectActivity(projectId: string): Promise<AuditLogEntry[]> {
  const { data } = await client.get(`/projects/${projectId}/activity`);
  return Array.isArray(data) ? data : data.data ?? [];
}

```

## web/src/api/transactions.ts

```typescript
import client from './client';
import type { Transaction } from '@/types';

export interface TransactionParams {
  type?: string;
  status?: string;
  page?: number;
  limit?: number;
  search?: string;
  category?: string;
  dateFrom?: string;
  dateTo?: string;
}

export interface TransactionListResponse {
  data: Transaction[];
  pagination: { page: number; limit: number; total: number; pages: number };
}

export async function getTransactions(projectId: string, params?: TransactionParams): Promise<TransactionListResponse> {
  const { data } = await client.get('/transactions', { params: { projectId, ...params } });
  if (Array.isArray(data)) {
    return { data, pagination: { page: 1, limit: data.length, total: data.length, pages: 1 } };
  }
  return data as TransactionListResponse;
}

export async function getTransaction(projectId: string, id: string): Promise<Transaction> {
  const { data } = await client.get<Transaction>(`/transactions/${id}`, { params: { projectId } });
  return data;
}

export async function createTransaction(projectId: string, payload: Partial<Transaction>): Promise<Transaction> {
  const { data } = await client.post<Transaction>('/transactions', { ...payload, projectId });
  return data;
}

export async function updateTransaction(projectId: string, id: string, payload: Partial<Transaction>): Promise<Transaction> {
  const { data } = await client.put<Transaction>(`/transactions/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteTransaction(projectId: string, id: string): Promise<void> {
  await client.delete(`/transactions/${id}`, { params: { projectId } });
}

```

## web/src/api/invoices.ts

```typescript
import client from './client';

export interface InvoiceLineItem {
  id?: string;
  description: string;
  quantity: number;
  unit: string;
  unitPrice: number;
  amount: number;
  costCode?: string;
}

export interface InvoiceDetail {
  id: string;
  projectId: string;
  invoiceNumber: string;
  type: string;
  toName: string;
  toEmail?: string;
  toAddress?: string;
  subtotal: number;
  gstTotal: number;
  total: number;
  retentionPercent?: number;
  retentionAmount?: number;
  amountDue: number;
  issueDate: string;
  dueDate: string;
  status: string;
  paidDate?: string;
  paidAmount?: number;
  lineItems: InvoiceLineItem[];
  createdAt: string;
}

export async function getInvoices(projectId: string): Promise<InvoiceDetail[]> {
  const { data } = await client.get('/invoices', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function getInvoice(id: string): Promise<InvoiceDetail> {
  const { data } = await client.get(`/invoices/${id}`);
  return data;
}

export async function createInvoice(projectId: string, payload: any): Promise<InvoiceDetail> {
  const { data } = await client.post('/invoices', { ...payload, projectId });
  return data;
}

export async function updateInvoice(id: string, payload: any): Promise<InvoiceDetail> {
  const { data } = await client.put(`/invoices/${id}`, payload);
  return data;
}

export async function deleteInvoice(id: string): Promise<void> {
  await client.delete(`/invoices/${id}`);
}

export async function updateInvoiceStatus(id: string, status: string): Promise<InvoiceDetail> {
  const { data } = await client.put(`/invoices/${id}/status`, { status });
  return data;
}

```

## web/src/api/budget.ts

```typescript
import client from './client';
import type { BudgetCategory, Variation, BudgetSummary } from '@/types';

export async function getBudgetSummary(projectId: string): Promise<BudgetSummary> {
  const { data } = await client.get<BudgetSummary>(`/budget/summary/${projectId}`);
  return data;
}

export async function getBudgetCategories(projectId: string): Promise<BudgetCategory[]> {
  const { data } = await client.get<BudgetCategory[]>('/budget/categories', { params: { projectId } });
  return data;
}

export async function createBudgetCategory(projectId: string, payload: Partial<BudgetCategory>): Promise<BudgetCategory> {
  const { data } = await client.post<BudgetCategory>('/budget/categories', { ...payload, projectId });
  return data;
}

export async function updateBudgetCategory(projectId: string, id: string, payload: Partial<BudgetCategory>): Promise<BudgetCategory> {
  const { data } = await client.put<BudgetCategory>(`/budget/categories/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteBudgetCategory(projectId: string, id: string): Promise<void> {
  await client.delete(`/budget/categories/${id}`, { params: { projectId } });
}

export async function getVariations(projectId: string): Promise<Variation[]> {
  const { data } = await client.get<Variation[]>('/budget/variations', { params: { projectId } });
  return data;
}

export async function createVariation(projectId: string, payload: Partial<Variation>): Promise<Variation> {
  const { data } = await client.post<Variation>('/budget/variations', { ...payload, projectId });
  return data;
}

export async function updateVariation(projectId: string, id: string, payload: Partial<Variation>): Promise<Variation> {
  const { data } = await client.put<Variation>(`/budget/variations/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteVariation(projectId: string, id: string): Promise<void> {
  await client.delete(`/budget/variations/${id}`, { params: { projectId } });
}

// ─── Contingency ────────────────────────────

export interface ContingencyData {
  contingencyAmount: number;
  totalDrawn: number;
  remaining: number;
  drawdowns: { id: string; description: string; amount: number; date: string; approvedBy?: string }[];
}

export async function getContingency(projectId: string): Promise<ContingencyData> {
  const { data } = await client.get(`/budget/contingency/${projectId}`);
  return data;
}

export async function createDrawdown(projectId: string, payload: { description: string; amount: number; date: string; approvedBy?: string }): Promise<any> {
  const { data } = await client.post(`/budget/contingency/${projectId}/drawdown`, payload);
  return data;
}

```

## web/src/api/cashflow.ts

```typescript
import client from './client';
import type { CashFlowEntry, ClaimSchedule } from '@/types';

export async function getCashFlow(projectId: string): Promise<CashFlowEntry[]> {
  const { data } = await client.get<CashFlowEntry[]>('/cashflow/entries', { params: { projectId } });
  return data;
}

export async function createCashFlowEntry(projectId: string, payload: Partial<CashFlowEntry>): Promise<CashFlowEntry> {
  const { data } = await client.post<CashFlowEntry>('/cashflow/entries', { ...payload, projectId });
  return data;
}

export async function updateCashFlowEntry(projectId: string, id: string, payload: Partial<CashFlowEntry>): Promise<CashFlowEntry> {
  const { data } = await client.put<CashFlowEntry>(`/cashflow/entries/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteCashFlowEntry(id: string): Promise<void> {
  await client.delete(`/cashflow/entries/${id}`);
}

export async function generateForecast(projectId: string): Promise<CashFlowEntry[]> {
  const { data } = await client.post<CashFlowEntry[]>(`/cashflow/forecast/${projectId}`);
  return data;
}

export async function getClaimSchedule(projectId: string): Promise<ClaimSchedule[]> {
  const { data } = await client.get<ClaimSchedule[]>('/cashflow/claims', { params: { projectId } });
  return data;
}

export async function createClaim(projectId: string, payload: Partial<ClaimSchedule>): Promise<ClaimSchedule> {
  const { data } = await client.post<ClaimSchedule>('/cashflow/claims', { ...payload, projectId });
  return data;
}

export async function updateClaim(projectId: string, id: string, payload: Partial<ClaimSchedule>): Promise<ClaimSchedule> {
  const { data } = await client.put<ClaimSchedule>(`/cashflow/claims/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteClaim(id: string): Promise<void> {
  await client.delete(`/cashflow/claims/${id}`);
}

```

## web/src/api/tasks.ts

```typescript
import client from './client';
import type { Task, TaskDependencyRecord } from '@/types';

export async function getTasks(projectId: string, params?: {
  status?: string;
}): Promise<Task[]> {
  const { data } = await client.get<Task[]>('/tasks', { params: { projectId, ...params } });
  return data;
}

export async function getTask(projectId: string, id: string): Promise<Task> {
  const { data } = await client.get<Task>(`/tasks/${id}`, { params: { projectId } });
  return data;
}

export async function createTask(projectId: string, payload: Partial<Task>): Promise<Task> {
  const { data } = await client.post<Task>('/tasks', { ...payload, projectId });
  return data;
}

export async function updateTask(projectId: string, id: string, payload: Record<string, unknown>): Promise<Task> {
  const { data } = await client.put<Task>(`/tasks/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteTask(projectId: string, id: string): Promise<void> {
  await client.delete(`/tasks/${id}`, { params: { projectId } });
}

export async function addDependency(payload: {
  predecessorId: string;
  successorId: string;
  type?: string;
  lagDays?: number;
}): Promise<TaskDependencyRecord> {
  const { data } = await client.post<TaskDependencyRecord>('/tasks/dependencies', payload);
  return data;
}

export async function removeDependency(depId: string): Promise<void> {
  await client.delete(`/tasks/dependencies/${depId}`);
}

export async function getCriticalPath(projectId: string): Promise<string[]> {
  const { data } = await client.get<string[]>('/tasks/critical-path', { params: { projectId } });
  return data;
}

export async function setBaseline(projectId: string): Promise<{ message: string; count: number }> {
  const { data } = await client.post<{ message: string; count: number }>('/tasks/set-baseline', { projectId });
  return data;
}

export async function getMilestones(projectId: string): Promise<Task[]> {
  const { data } = await client.get<Task[]>('/tasks/milestones', { params: { projectId } });
  return data;
}

export async function reorderTasks(projectId: string, orderedTaskIds: string[]): Promise<void> {
  await client.post('/tasks/reorder', { projectId, orderedTaskIds });
}

export async function logDelay(projectId: string, payload: {
  taskId?: string;
  reason: string;
  description?: string;
  delayDays: number;
}): Promise<unknown> {
  const { data } = await client.post('/tasks/delays', {
    ...payload,
    projectId,
    date: new Date().toISOString(),
  });
  return data;
}

```

## web/src/api/inspections.ts

```typescript
import client from './client';
import type { Inspection, InspectionItem, Defect } from '@/types';

export async function getInspections(projectId: string): Promise<Inspection[]> {
  const { data } = await client.get('/inspections', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function getInspection(id: string): Promise<Inspection> {
  const { data } = await client.get<Inspection>(`/inspections/${id}`);
  return data;
}

export async function createInspection(projectId: string, payload: Partial<Inspection>): Promise<Inspection> {
  const { data } = await client.post<Inspection>('/inspections', { ...payload, projectId });
  return data;
}

export async function updateInspection(projectId: string, id: string, payload: Record<string, unknown>): Promise<Inspection> {
  const { data } = await client.put<Inspection>(`/inspections/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteInspection(projectId: string, id: string): Promise<void> {
  await client.delete(`/inspections/${id}`, { params: { projectId } });
}

// ─── Inspection Items ───────────────────────

export async function createInspectionItem(inspectionId: string, payload: { category: string; description: string }): Promise<InspectionItem> {
  const { data } = await client.post<InspectionItem>(`/inspections/${inspectionId}/items`, payload);
  return data;
}

export async function createInspectionItemsBatch(inspectionId: string, items: { category: string; description: string }[]): Promise<InspectionItem[]> {
  const { data } = await client.post<InspectionItem[]>(`/inspections/${inspectionId}/items/batch`, { items });
  return data;
}

export async function updateInspectionItem(itemId: string, payload: { result?: string; notes?: string }): Promise<InspectionItem> {
  const { data } = await client.put<InspectionItem>(`/inspections/items/${itemId}`, payload);
  return data;
}

// ─── Defects ────────────────────────────────

export async function getDefects(projectId: string): Promise<Defect[]> {
  const { data } = await client.get('/inspections/defects/list', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function getDefect(id: string): Promise<Defect> {
  const { data } = await client.get<Defect>(`/inspections/defects/${id}`);
  return data;
}

export async function createDefect(projectId: string, payload: Partial<Defect>): Promise<Defect> {
  const { data } = await client.post<Defect>('/inspections/defects', { ...payload, projectId });
  return data;
}

export async function updateDefect(projectId: string, id: string, payload: Record<string, unknown>): Promise<Defect> {
  const { data } = await client.put<Defect>(`/inspections/defects/${id}`, { ...payload, projectId });
  return data;
}

```

## web/src/api/documents.ts

```typescript
import client from './client';
import type { Document } from '@/types';

const CATEGORY_TO_FOLDER: Record<string, string> = {
  plans: 'PLANS',
  contracts: 'CONTRACTS',
  permits: 'PERMITS',
  swms: 'SWMS',
  photos: 'PHOTOS',
  reports: 'REPORTS',
  other: 'OTHER',
};

const FOLDER_TO_CATEGORY: Record<string, Document['category']> = {
  PLANS: 'plans',
  CONTRACTS: 'contracts',
  PERMITS: 'permits',
  SWMS: 'swms',
  PHOTOS: 'photos',
  REPORTS: 'reports',
  CERTIFICATES: 'other',
  INSURANCE: 'other',
  CORRESPONDENCE: 'other',
  OTHER: 'other',
};

// eslint-disable-next-line @typescript-eslint/no-explicit-any
function normalizeDocument(raw: any): Document {
  const folder = raw.folder ?? 'OTHER';
  const fileUrl = raw.fileUrl ?? '';

  return {
    id: raw.id,
    projectId: raw.projectId,
    name: raw.name,
    fileName: raw.name,
    fileSize: raw.fileSize ?? 0,
    mimeType: raw.mimeType ?? '',
    category: FOLDER_TO_CATEGORY[folder] ?? 'other',
    folder,
    description: raw.description ?? undefined,
    version: raw.version ?? 1,
    url: fileUrl,
    fileUrl: raw.fileUrl ?? undefined,
    uploadedBy:
      typeof raw.uploadedBy === 'object' && raw.uploadedBy?.name
        ? raw.uploadedBy.name
        : typeof raw.uploadedBy === 'string'
          ? raw.uploadedBy
          : 'Unknown',
    createdAt: raw.createdAt,
    updatedAt: raw.updatedAt,
  };
}

export async function getDocuments(projectId: string, params?: {
  category?: string;
}): Promise<Document[]> {
  const folder = params?.category ? CATEGORY_TO_FOLDER[params.category] : undefined;
  const { data } = await client.get('/documents', { params: { projectId, folder } });
  const items = Array.isArray(data) ? data : data.data ?? [];
  return items.map(normalizeDocument);
}

export async function uploadDocument(projectId: string, file: File, folder: string): Promise<Document> {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('folder', folder.toUpperCase());
  formData.append('projectId', projectId);
  formData.append('name', file.name);
  const { data } = await client.post('/documents/upload', formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
  });
  return normalizeDocument(data);
}

export async function deleteDocument(projectId: string, id: string): Promise<void> {
  await client.delete(`/documents/${id}`, { params: { projectId } });
}

export async function downloadDocument(projectId: string, id: string): Promise<Blob> {
  const { data } = await client.get(`/documents/${id}/download`, {
    params: { projectId },
    responseType: 'blob',
  });
  return data;
}

export async function updateDocument(id: string, payload: { name?: string; folder?: string; description?: string }): Promise<Document> {
  const { data } = await client.put(`/documents/${id}`, payload);
  return normalizeDocument(data);
}

export async function uploadDocumentVersion(documentId: string, file: File): Promise<Document> {
  const formData = new FormData();
  formData.append('file', file);
  const { data } = await client.post(`/documents/${documentId}/version`, formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
  });
  return normalizeDocument(data);
}

export async function getDocumentVersions(documentId: string): Promise<Document[]> {
  const { data } = await client.get(`/documents/${documentId}/versions`);
  const items = Array.isArray(data) ? data : data.data ?? [];
  return items.map(normalizeDocument);
}

```

## web/src/api/tradies.ts

```typescript
import client from './client';
import type { Tradie } from '@/types';

export async function getTradies(params?: {
  trade?: string;
  search?: string;
  status?: string;
}): Promise<Tradie[]> {
  const { data } = await client.get('/tradies', { params });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function getTradie(id: string): Promise<Tradie> {
  const { data } = await client.get<Tradie>(`/tradies/${id}`);
  return data;
}

export async function createTradie(payload: Partial<Tradie>): Promise<Tradie> {
  const { data } = await client.post<Tradie>('/tradies', payload);
  return data;
}

export async function updateTradie(id: string, payload: Partial<Tradie>): Promise<Tradie> {
  const { data } = await client.put<Tradie>(`/tradies/${id}`, payload);
  return data;
}

export async function deleteTradie(id: string): Promise<void> {
  await client.delete(`/tradies/${id}`);
}

// ─── Documents ──────────────────────────────────

export interface TradieDocument {
  id: string;
  tradieId: string;
  type: string;
  name: string;
  fileUrl: string;
  expiryDate?: string;
  uploadedById: string;
  createdAt: string;
}

export async function getTradieDocuments(tradieId: string): Promise<TradieDocument[]> {
  const { data } = await client.get(`/tradies/${tradieId}/documents`);
  return Array.isArray(data) ? data : [];
}

export async function uploadTradieDocument(
  tradieId: string,
  payload: { type: string; name: string; fileUrl?: string; expiryDate?: string },
): Promise<TradieDocument> {
  const { data } = await client.post(`/tradies/${tradieId}/documents`, payload);
  return data;
}

export async function deleteTradieDocument(docId: string): Promise<void> {
  await client.delete(`/tradies/documents/${docId}`);
}

// ─── Assignments ────────────────────────────────

export interface TradieAssignment {
  id: string;
  tradieId: string;
  projectId: string;
  role?: string;
  agreedRate?: number;
  rateType?: string;
  performanceRating?: number;
  startDate?: string;
  endDate?: string;
  createdAt: string;
  project?: { id: string; name: string; status: string };
}

export async function getTradieAssignments(tradieId: string): Promise<TradieAssignment[]> {
  const { data } = await client.get(`/tradies/${tradieId}/assignments`);
  return Array.isArray(data) ? data : [];
}

export async function assignTradieToProject(payload: {
  tradieId: string;
  projectId: string;
  role?: string;
  agreedRate?: number;
  rateType?: string;
}): Promise<TradieAssignment> {
  const { data } = await client.post(`/tradies/${payload.tradieId}/assignments`, payload);
  return data;
}

export async function updateAssignmentRating(assignmentId: string, rating: number): Promise<TradieAssignment> {
  const { data } = await client.put(`/tradies/assignments/${assignmentId}/rating`, { rating });
  return data;
}

// ─── Payments ───────────────────────────────────

export interface TradiePayment {
  id: string;
  projectId: string;
  type: string;
  category: string;
  description: string;
  amount: number;
  date: string;
  status: string;
  project?: { id: string; name: string };
}

export async function getTradiePayments(tradieId: string): Promise<TradiePayment[]> {
  const { data } = await client.get(`/tradies/${tradieId}/payments`);
  return Array.isArray(data) ? data : [];
}

// ─── Compliance ─────────────────────────────────

export interface ComplianceData {
  expiringDocuments: (TradieDocument & { tradie: { id: string; companyName: string; contactName: string; trade: string } })[];
  expiringTradies: Tradie[];
  summary: { documentsExpiring: number; tradiesWithExpiringCompliance: number; lookAheadDays: number };
}

export async function getCompliance(days = 30): Promise<ComplianceData> {
  const { data } = await client.get('/tradies/compliance', { params: { days } });
  return data;
}

```

## web/src/api/dashboard.ts

```typescript
import client from './client';

export interface DashboardStats {
  outstandingInvoices: { count: number; total: number };
  expiringLicences: number;
  upcomingMilestones: {
    id: string;
    name: string;
    startDate: string;
    projectId: string;
    projectName: string;
  }[];
  cashPosition: { totalIncome: number; totalExpenses: number };
  overdueItems: {
    tasks: { id: string; name: string; endDate: string | null; projectId: string; projectName: string }[];
    defects: { id: string; title: string; dueDate: string | null; projectId: string; projectName: string }[];
    totalCount: number;
  };
}

export async function getDashboardStats(): Promise<DashboardStats> {
  const { data } = await client.get<DashboardStats>('/dashboard/stats');
  return data;
}

export interface ActivityEntry {
  id: string;
  userId: string;
  action: string;
  entityType: string;
  entityId: string;
  changes?: string;
  createdAt: string;
  user: { id: string; name: string };
}

export async function getRecentActivity(): Promise<ActivityEntry[]> {
  const { data } = await client.get<ActivityEntry[]>('/dashboard/recent-activity');
  return data;
}

```

## web/src/api/reports.ts

```typescript
import client from './client';

export interface GstTransaction {
  date: string;
  description: string;
  type: string;
  amount: number;
  gstAmount: number;
}

export interface GstReportData {
  gstCollected: number;
  gstPaid: number;
  netGst: number;
  transactions: GstTransaction[];
}

export async function getGstReport(startDate: string, endDate: string): Promise<GstReportData> {
  const { data } = await client.get('/reports/gst', { params: { startDate, endDate } });
  return data;
}

// ─── Budget Report ──────────────────────────────

export interface BudgetReportProject {
  id: string;
  name: string;
  status: string;
  totalBudget: number;
  totalSpent: number;
  variance: number;
}

export interface BudgetReportData {
  projects: BudgetReportProject[];
}

export async function getBudgetReport(startDate?: string, endDate?: string): Promise<BudgetReportData> {
  const { data } = await client.get('/reports/budget', { params: { startDate, endDate } });
  return data;
}

// ─── Cash Flow Report ───────────────────────────

export interface CashFlowReportEntry {
  month: string;
  totalIncome: number;
  totalExpense: number;
  cumulative: number;
}

export interface CashFlowReportData {
  entries: CashFlowReportEntry[];
}

export async function getCashFlowReport(startDate?: string, endDate?: string): Promise<CashFlowReportData> {
  const { data } = await client.get('/reports/cashflow', { params: { startDate, endDate } });
  return data;
}

// ─── Project Status Report ──────────────────────

export interface ProjectStatusReportProject {
  id: string;
  name: string;
  status: string;
  progress: number;
  contractValue: number;
  startDate: string | null;
  estimatedEndDate: string | null;
}

export interface ProjectStatusReportData {
  projects: ProjectStatusReportProject[];
}

export async function getProjectStatusReport(): Promise<ProjectStatusReportData> {
  const { data } = await client.get('/reports/status');
  return data;
}

```

## web/src/api/users.ts

```typescript
import client from './client';

export interface AdminUser {
  id: string;
  email: string;
  name: string;
  phone?: string;
  role: string;
  createdAt: string;
  updatedAt: string;
}

export async function getUsers(): Promise<AdminUser[]> {
  const { data } = await client.get('/users');
  return Array.isArray(data) ? data : [];
}

export async function updateUserRole(id: string, role: string): Promise<AdminUser> {
  const { data } = await client.put(`/users/${id}/role`, { role });
  return data;
}

```
