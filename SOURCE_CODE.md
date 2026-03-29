# BuildMate — Complete Source Code Reference

**Purpose:** This file contains the verbatim source code for every file in the BuildMate application. An AI agent can use this plus DESIGN_DOCUMENT.md to recreate the entire project from scratch.

**Date:** 30 March 2026

---

## Part 1: Web Frontend

### web/index.html
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

### web/src/index.css
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

### web/src/main.tsx
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

### web/src/App.tsx
```tsx
import { Routes, Route, Navigate } from 'react-router-dom';
import { useAuth } from '@/hooks/useAuth';
import AppLayout from '@/components/layout/AppLayout';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import LoginPage from '@/pages/Login';
import DashboardPage from '@/pages/Dashboard';
import ProjectsPage from '@/pages/projects/ProjectsPage';
import ProjectDetailPage from '@/pages/projects/ProjectDetailPage';
import BudgetPage from '@/pages/budget/BudgetPage';
import CashFlowPage from '@/pages/cashflow/CashFlowPage';
import PlanningPage from '@/pages/planning/PlanningPage';
import FinancialsPage from '@/pages/financials/FinancialsPage';
import InspectionsPage from '@/pages/inspections/InspectionsPage';
import DocumentsPage from '@/pages/documents/DocumentsPage';
import TradiesPage from '@/pages/tradies/TradiesPage';
import ReportsPage from '@/pages/reports/ReportsPage';
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
        <Route path="/projects/:id/budget" element={<BudgetPage />} />
        <Route path="/projects/:id/cashflow" element={<CashFlowPage />} />
        <Route path="/projects/:id/planning" element={<PlanningPage />} />
        <Route path="/projects/:id/financials" element={<FinancialsPage />} />
        <Route path="/projects/:id/inspections" element={<InspectionsPage />} />
        <Route path="/projects/:id/documents" element={<DocumentsPage />} />
        <Route path="/tradies" element={<TradiesPage />} />
        <Route path="/reports" element={<ReportsPage />} />
      </Route>
    </Routes>
  );
}
```

### web/src/types/index.ts
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
  spentAmount: number;
  committedAmount: number;
  forecastAmount: number;
  variance: number;
  parentId?: string;
  sortOrder: number;
  createdAt: string;
  updatedAt: string;
}

export interface Variation {
  id: string;
  projectId: string;
  budgetCategoryId?: string;
  variationNumber: string;
  description: string;
  amount: number;
  status: 'pending' | 'approved' | 'rejected';
  requestedDate: string;
  approvedDate?: string;
  requestedBy?: string;
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
  trade: string;
  email?: string;
  phone: string;
  abn?: string;
  licenceNumber?: string;
  licenceExpiry?: string;
  insuranceExpiry?: string;
  rating?: number;
  notes?: string;
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
  progress: number;
  status: 'not_started' | 'in_progress' | 'complete' | 'on_hold' | 'blocked';
  priority: 'low' | 'medium' | 'high' | 'critical';
  isMilestone: boolean;
  assignedTo?: string;
  tradieId?: string;
  tradie?: Tradie;
  parentId?: string;
  sortOrder: number;
  dependencies?: TaskDependency[];
  createdAt: string;
  updatedAt: string;
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
  inspectionDate: string;
  inspector?: string;
  status: 'scheduled' | 'passed' | 'failed' | 'conditional';
  result?: string;
  notes?: string;
  createdAt: string;
  updatedAt: string;
}

export interface Defect {
  id: string;
  projectId: string;
  inspectionId?: string;
  title: string;
  description: string;
  severity: 'low' | 'medium' | 'high' | 'critical';
  status: 'open' | 'in_progress' | 'resolved' | 'closed';
  assignedTo?: string;
  tradieId?: string;
  dueDate?: string;
  resolvedDate?: string;
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
  url: string;
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

### web/src/lib/utils.ts
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

### web/src/store/authStore.tsx
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

### web/src/hooks/useAuth.ts
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

### web/src/api/client.ts
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

### web/src/api/auth.ts
```typescript
import client from './client';
import type { AuthResponse, User } from '@/types';

function mapUser(raw: Record<string, unknown>): User {
  const name = (raw.name as string) || '';
  const [firstName = '', ...rest] = name.split(' ');
  return {
    id: raw.id as string,
    email: raw.email as string,
    firstName,
    lastName: rest.join(' ') || '',
    phone: (raw.phone as string) || undefined,
    role: ((raw.role as string) || 'builder').toLowerCase() as User['role'],
    createdAt: (raw.createdAt as string) || new Date().toISOString(),
    updatedAt: (raw.updatedAt as string) || new Date().toISOString(),
  };
}

export async function login(email: string, password: string): Promise<AuthResponse> {
  const { data } = await client.post('/auth/login', { email, password });
  return { token: data.accessToken, user: mapUser(data.user) };
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
  return { token: data.accessToken, user: mapUser(data.user) };
}

export async function getMe(): Promise<User> {
  const { data } = await client.get('/auth/me');
  return mapUser(data);
}

export async function updateProfile(payload: Partial<User>): Promise<User> {
  const { data } = await client.put('/auth/profile', payload);
  return mapUser(data);
}
```

### web/src/api/projects.ts
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
```

### web/src/api/transactions.ts
```typescript
import client from './client';
import type { Transaction } from '@/types';

export async function getTransactions(projectId: string, params?: {
  type?: string;
  status?: string;
}): Promise<Transaction[]> {
  const { data } = await client.get('/transactions', { params: { projectId, ...params } });
  return Array.isArray(data) ? data : data.data ?? [];
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

### web/src/api/budget.ts
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
```

### web/src/api/cashflow.ts
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
```

### web/src/api/tasks.ts
```typescript
import client from './client';
import type { Task, TaskDependency } from '@/types';

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

export async function updateTask(projectId: string, id: string, payload: Partial<Task>): Promise<Task> {
  const { data } = await client.put<Task>(`/tasks/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteTask(projectId: string, id: string): Promise<void> {
  await client.delete(`/tasks/${id}`, { params: { projectId } });
}

export async function addDependency(projectId: string, taskId: string, payload: Partial<TaskDependency>): Promise<TaskDependency> {
  const { data } = await client.post<TaskDependency>(`/tasks/${taskId}/dependencies`, { ...payload, projectId });
  return data;
}

export async function removeDependency(projectId: string, taskId: string, depId: string): Promise<void> {
  await client.delete(`/tasks/${taskId}/dependencies/${depId}`, { params: { projectId } });
}
```

### web/src/api/inspections.ts
```typescript
import client from './client';
import type { Inspection, Defect } from '@/types';

export async function getInspections(projectId: string): Promise<Inspection[]> {
  const { data } = await client.get('/inspections', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function createInspection(projectId: string, payload: Partial<Inspection>): Promise<Inspection> {
  const { data } = await client.post<Inspection>('/inspections', { ...payload, projectId });
  return data;
}

export async function updateInspection(projectId: string, id: string, payload: Partial<Inspection>): Promise<Inspection> {
  const { data } = await client.put<Inspection>(`/inspections/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteInspection(projectId: string, id: string): Promise<void> {
  await client.delete(`/inspections/${id}`, { params: { projectId } });
}

export async function getDefects(projectId: string): Promise<Defect[]> {
  const { data } = await client.get('/inspections/defects/list', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function createDefect(projectId: string, payload: Partial<Defect>): Promise<Defect> {
  const { data } = await client.post<Defect>('/inspections/defects', { ...payload, projectId });
  return data;
}

export async function updateDefect(projectId: string, id: string, payload: Partial<Defect>): Promise<Defect> {
  const { data } = await client.put<Defect>(`/inspections/defects/${id}`, { ...payload, projectId });
  return data;
}
```

### web/src/api/documents.ts
```typescript
import client from './client';
import type { Document } from '@/types';

export async function getDocuments(projectId: string, params?: {
  category?: string;
}): Promise<Document[]> {
  const { data } = await client.get('/documents', { params: { projectId, folder: params?.category } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function uploadDocument(projectId: string, file: File, folder: string): Promise<Document> {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('folder', folder.toUpperCase());
  formData.append('projectId', projectId);
  formData.append('name', file.name);
  const { data } = await client.post<Document>('/documents/upload', formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
  });
  return data;
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
```

### web/src/api/tradies.ts
```typescript
import client from './client';
import type { Tradie } from '@/types';

export async function getTradies(params?: {
  trade?: string;
  search?: string;
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
```

### web/src/components/ui/Button.tsx
```tsx
import { forwardRef, type ButtonHTMLAttributes } from 'react';
import { cn } from '@/lib/utils';

const variants = {
  primary: 'bg-primary text-white hover:bg-primary-700 focus:ring-primary-500',
  secondary: 'bg-gray-100 text-gray-700 hover:bg-gray-200 focus:ring-gray-400',
  danger: 'bg-danger text-white hover:bg-danger-600 focus:ring-danger-500',
  ghost: 'bg-transparent text-gray-700 hover:bg-gray-100 focus:ring-gray-400',
} as const;

const sizes = {
  sm: 'px-3 py-1.5 text-sm',
  md: 'px-4 py-2 text-sm',
  lg: 'px-6 py-3 text-base',
} as const;

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: keyof typeof variants;
  size?: keyof typeof sizes;
}

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant = 'primary', size = 'md', disabled, ...props }, ref) => (
    <button
      ref={ref}
      className={cn(
        'inline-flex items-center justify-center gap-2 rounded-lg font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none',
        variants[variant],
        sizes[size],
        className,
      )}
      disabled={disabled}
      {...props}
    />
  ),
);

Button.displayName = 'Button';

export default Button;
```

### web/src/components/ui/Input.tsx
```tsx
import { forwardRef, type InputHTMLAttributes } from 'react';
import { cn } from '@/lib/utils';

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
}

const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ className, label, error, id, ...props }, ref) => (
    <div className="w-full">
      {label && (
        <label htmlFor={id} className="mb-1 block text-sm font-medium text-gray-700">
          {label}
        </label>
      )}
      <input
        ref={ref}
        id={id}
        className={cn(
          'block w-full rounded-lg border px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary',
          error ? 'border-danger' : 'border-gray-300',
          className,
        )}
        {...props}
      />
      {error && <p className="mt-1 text-xs text-danger">{error}</p>}
    </div>
  ),
);

Input.displayName = 'Input';

export default Input;
```

### web/src/components/ui/Modal.tsx
```tsx
import { useEffect, type ReactNode } from 'react';
import { X } from 'lucide-react';
import { cn } from '@/lib/utils';

interface ModalProps {
  open: boolean;
  onClose: () => void;
  title?: string;
  children: ReactNode;
  className?: string;
}

export default function Modal({ open, onClose, title, children, className }: ModalProps) {
  useEffect(() => {
    if (open) {
      document.body.style.overflow = 'hidden';
      return () => { document.body.style.overflow = ''; };
    }
  }, [open]);

  if (!open) return null;

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
      <div className="fixed inset-0 bg-black/50" onClick={onClose} />
      <div
        className={cn(
          'relative z-10 w-full max-w-lg rounded-xl bg-white shadow-xl',
          className,
        )}
      >
        {title && (
          <div className="flex items-center justify-between border-b px-6 py-4">
            <h2 className="text-lg font-semibold">{title}</h2>
            <button onClick={onClose} className="rounded-lg p-1 hover:bg-gray-100">
              <X className="h-5 w-5" />
            </button>
          </div>
        )}
        <div className="p-6">{children}</div>
      </div>
    </div>
  );
}
```

### web/src/components/ui/Card.tsx
```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface CardProps {
  children: ReactNode;
  title?: string;
  action?: ReactNode;
  className?: string;
}

export default function Card({ children, title, action, className }: CardProps) {
  return (
    <div className={cn('rounded-xl border border-gray-200 bg-white shadow-sm', className)}>
      {(title || action) && (
        <div className="flex items-center justify-between border-b border-gray-100 px-4 py-3 sm:px-6">
          {title && <h3 className="text-sm font-semibold text-gray-900">{title}</h3>}
          {action}
        </div>
      )}
      <div className="p-4 sm:p-6">{children}</div>
    </div>
  );
}
```

### web/src/components/ui/Badge.tsx
```tsx
import { cn } from '@/lib/utils';

const colorMap: Record<string, string> = {
  active: 'bg-success-50 text-success-500',
  complete: 'bg-blue-50 text-blue-600',
  planning: 'bg-gray-100 text-gray-600',
  on_hold: 'bg-warning-50 text-warning-500',
  cancelled: 'bg-danger-50 text-danger-500',
  paid: 'bg-success-50 text-success-500',
  pending: 'bg-warning-50 text-warning-500',
  overdue: 'bg-danger-50 text-danger-500',
  approved: 'bg-success-50 text-success-500',
  rejected: 'bg-danger-50 text-danger-500',
  open: 'bg-blue-50 text-blue-600',
  in_progress: 'bg-warning-50 text-warning-500',
  resolved: 'bg-success-50 text-success-500',
  closed: 'bg-gray-100 text-gray-600',
  passed: 'bg-success-50 text-success-500',
  failed: 'bg-danger-50 text-danger-500',
  scheduled: 'bg-blue-50 text-blue-600',
  conditional: 'bg-warning-50 text-warning-500',
  low: 'bg-gray-100 text-gray-600',
  medium: 'bg-warning-50 text-warning-500',
  high: 'bg-orange-50 text-orange-600',
  critical: 'bg-danger-50 text-danger-500',
  not_started: 'bg-gray-100 text-gray-600',
  blocked: 'bg-danger-50 text-danger-500',
  draft: 'bg-gray-100 text-gray-600',
  sent: 'bg-blue-50 text-blue-600',
  submitted: 'bg-blue-50 text-blue-600',
  upcoming: 'bg-blue-50 text-blue-600',
};

interface BadgeProps {
  status: string;
  className?: string;
}

export default function Badge({ status, className }: BadgeProps) {
  const color = colorMap[status] ?? 'bg-gray-100 text-gray-600';
  const label = status.replace(/_/g, ' ');

  return (
    <span
      className={cn(
        'inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium capitalize',
        color,
        className,
      )}
    >
      {label}
    </span>
  );
}
```

### web/src/components/ui/Table.tsx
```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface TableProps {
  headers: string[];
  children: ReactNode;
  className?: string;
}

export default function Table({ headers, children, className }: TableProps) {
  return (
    <div className={cn('overflow-x-auto rounded-lg border border-gray-200', className)}>
      <table className="min-w-full divide-y divide-gray-200 text-sm">
        <thead className="bg-gray-50">
          <tr>
            {headers.map((h) => (
              <th
                key={h}
                className="whitespace-nowrap px-4 py-3 text-left text-xs font-medium uppercase tracking-wider text-gray-500"
              >
                {h}
              </th>
            ))}
          </tr>
        </thead>
        <tbody className="divide-y divide-gray-100 bg-white">{children}</tbody>
      </table>
    </div>
  );
}
```

### web/src/components/ui/EmptyState.tsx
```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface EmptyStateProps {
  icon: ReactNode;
  title: string;
  description?: string;
  action?: ReactNode;
  className?: string;
}

export default function EmptyState({ icon, title, description, action, className }: EmptyStateProps) {
  return (
    <div className={cn('flex flex-col items-center justify-center py-12 text-center', className)}>
      <div className="mb-4 text-gray-400">{icon}</div>
      <h3 className="text-sm font-semibold text-gray-900">{title}</h3>
      {description && <p className="mt-1 text-sm text-gray-500">{description}</p>}
      {action && <div className="mt-4">{action}</div>}
    </div>
  );
}
```

### web/src/components/ui/LoadingSpinner.tsx
```tsx
import { cn } from '@/lib/utils';

interface LoadingSpinnerProps {
  className?: string;
  size?: 'sm' | 'md' | 'lg';
}

const sizeMap = { sm: 'h-4 w-4', md: 'h-8 w-8', lg: 'h-12 w-12' };

export default function LoadingSpinner({ className, size = 'md' }: LoadingSpinnerProps) {
  return (
    <div className={cn('flex items-center justify-center', className)}>
      <div
        className={cn(
          'animate-spin rounded-full border-2 border-gray-300 border-t-primary',
          sizeMap[size],
        )}
      />
    </div>
  );
}
```

### web/src/components/layout/AppLayout.tsx
```tsx
import { Outlet } from 'react-router-dom';
import Sidebar from './Sidebar';
import BottomNav from './BottomNav';
import Header from './Header';

export default function AppLayout() {
  return (
    <div className="min-h-screen bg-gray-50">
      <Sidebar />
      <div className="md:pl-64">
        <Header />
        <main className="p-4 pb-20 sm:p-6 md:pb-6">
          <Outlet />
        </main>
      </div>
      <BottomNav />
    </div>
  );
}
```

### web/src/components/layout/Sidebar.tsx
```tsx
import { NavLink } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  HardHat,
} from 'lucide-react';
import { cn } from '@/lib/utils';

const links = [
  { to: '/', icon: LayoutDashboard, label: 'Dashboard' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports' },
];

export default function Sidebar() {
  return (
    <aside className="hidden md:flex md:w-64 md:flex-col md:fixed md:inset-y-0 border-r border-gray-200 bg-white">
      <div className="flex h-16 items-center gap-2 border-b border-gray-200 px-6">
        <HardHat className="h-7 w-7 text-primary" />
        <span className="text-lg font-bold text-primary">BuildMate</span>
      </div>
      <nav className="flex-1 space-y-1 px-3 py-4">
        {links.map(({ to, icon: Icon, label }) => (
          <NavLink
            key={to}
            to={to}
            end={to === '/'}
            className={({ isActive }) =>
              cn(
                'flex items-center gap-3 rounded-lg px-3 py-2.5 text-sm font-medium transition-colors',
                isActive
                  ? 'bg-primary-50 text-primary'
                  : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900',
              )
            }
          >
            <Icon className="h-5 w-5" />
            {label}
          </NavLink>
        ))}
      </nav>
    </aside>
  );
}
```

### web/src/components/layout/Header.tsx
```tsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { Menu, Bell, LogOut, User } from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { cn } from '@/lib/utils';

interface HeaderProps {
  title?: string;
}

export default function Header({ title = 'BuildMate' }: HeaderProps) {
  const { user, logout } = useAuth();
  const navigate = useNavigate();
  const [menuOpen, setMenuOpen] = useState(false);

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <header className="sticky top-0 z-30 flex h-16 items-center justify-between border-b border-gray-200 bg-white px-4 sm:px-6">
      <div className="flex items-center gap-3">
        <button className="rounded-lg p-2 hover:bg-gray-100 md:hidden">
          <Menu className="h-5 w-5" />
        </button>
        <h1 className="text-lg font-semibold text-gray-900">{title}</h1>
      </div>

      <div className="flex items-center gap-2">
        <button className="relative rounded-lg p-2 hover:bg-gray-100">
          <Bell className="h-5 w-5 text-gray-500" />
        </button>

        <div className="relative">
          <button
            onClick={() => setMenuOpen(!menuOpen)}
            className="flex items-center gap-2 rounded-lg p-2 hover:bg-gray-100"
          >
            <div className="flex h-8 w-8 items-center justify-center rounded-full bg-primary text-sm font-medium text-white">
              {user?.firstName?.[0]}
              {user?.lastName?.[0]}
            </div>
            <span className="hidden text-sm font-medium text-gray-700 sm:block">
              {user?.firstName} {user?.lastName}
            </span>
          </button>

          {menuOpen && (
            <div className="absolute right-0 top-full mt-1 w-48 rounded-lg border border-gray-200 bg-white py-1 shadow-lg">
              <button
                onClick={() => { setMenuOpen(false); }}
                className="flex w-full items-center gap-2 px-4 py-2 text-sm text-gray-700 hover:bg-gray-50"
              >
                <User className="h-4 w-4" />
                Profile
              </button>
              <button
                onClick={handleLogout}
                className={cn(
                  'flex w-full items-center gap-2 px-4 py-2 text-sm text-danger hover:bg-gray-50',
                )}
              >
                <LogOut className="h-4 w-4" />
                Sign out
              </button>
            </div>
          )}
        </div>
      </div>
    </header>
  );
}
```

### web/src/components/layout/BottomNav.tsx
```tsx
import { NavLink } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  MoreHorizontal,
} from 'lucide-react';
import { cn } from '@/lib/utils';

const tabs = [
  { to: '/', icon: LayoutDashboard, label: 'Home' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports' },
  { to: '#more', icon: MoreHorizontal, label: 'More' },
];

export default function BottomNav() {
  return (
    <nav className="fixed bottom-0 left-0 right-0 z-40 border-t border-gray-200 bg-white md:hidden">
      <div className="flex items-center justify-around">
        {tabs.map(({ to, icon: Icon, label }) => (
          <NavLink
            key={to}
            to={to}
            end={to === '/'}
            className={({ isActive }) =>
              cn(
                'flex flex-1 flex-col items-center gap-0.5 py-2 text-[10px] font-medium transition-colors',
                isActive && to !== '#more'
                  ? 'text-primary'
                  : 'text-gray-500',
              )
            }
          >
            <Icon className="h-5 w-5" />
            {label}
          </NavLink>
        ))}
      </div>
    </nav>
  );
}
```

### web/src/components/GanttChart.tsx
```tsx
import { useMemo, useRef, useState } from 'react';
import { cn } from '@/lib/utils';

interface GanttTask {
  id: string;
  name: string;
  startDate?: string | null;
  endDate?: string | null;
  percentComplete?: number;
  status: string;
  priority?: string;
  isMilestone?: boolean;
}

interface GanttChartProps {
  tasks: GanttTask[];
  dayWidth?: number;
}

const STATUS_COLORS: Record<string, string> = {
  COMPLETE: 'bg-emerald-500',
  IN_PROGRESS: 'bg-blue-500',
  NOT_STARTED: 'bg-gray-400',
  ON_HOLD: 'bg-amber-500',
  BLOCKED: 'bg-red-500',
};

const STATUS_BG: Record<string, string> = {
  COMPLETE: 'bg-emerald-100',
  IN_PROGRESS: 'bg-blue-100',
  NOT_STARTED: 'bg-gray-200',
  ON_HOLD: 'bg-amber-100',
  BLOCKED: 'bg-red-100',
};

function daysBetween(a: Date, b: Date) {
  return Math.ceil((b.getTime() - a.getTime()) / 86400000);
}

function formatShortDate(d: Date) {
  return d.toLocaleDateString('en-AU', { day: 'numeric', month: 'short' });
}

function getMonday(d: Date) {
  const date = new Date(d);
  const day = date.getDay();
  const diff = date.getDate() - day + (day === 0 ? -6 : 1);
  date.setDate(diff);
  date.setHours(0, 0, 0, 0);
  return date;
}

export default function GanttChart({ tasks, dayWidth: dayWidthProp = 28 }: GanttChartProps) {
  const scrollRef = useRef<HTMLDivElement>(null);
  const [hoveredTask, setHoveredTask] = useState<string | null>(null);
  const dayWidth = dayWidthProp;

  const { weeks, timelineStart, totalDays, validTasks } = useMemo(() => {
    const valid = tasks.filter((t) => t.startDate);
    if (valid.length === 0) return { weeks: [], timelineStart: new Date(), totalDays: 0, validTasks: [] };

    const starts = valid.map((t) => new Date(t.startDate!).getTime());
    const ends = valid.map((t) => new Date(t.endDate || t.startDate!).getTime());
    const minDate = getMonday(new Date(Math.min(...starts)));
    const maxDate = new Date(Math.max(...ends));

    // Add padding
    minDate.setDate(minDate.getDate() - 7);
    maxDate.setDate(maxDate.getDate() + 14);

    const total = daysBetween(minDate, maxDate);

    // Generate weeks
    const wks: { date: Date; label: string }[] = [];
    const curr = new Date(minDate);
    while (curr <= maxDate) {
      wks.push({ date: new Date(curr), label: formatShortDate(curr) });
      curr.setDate(curr.getDate() + 7);
    }

    return { weeks: wks, timelineStart: minDate, totalDays: total, validTasks: valid };
  }, [tasks]);

  if (validTasks.length === 0) {
    return <p className="py-8 text-center text-sm text-gray-500">Add tasks with dates to see the Gantt chart.</p>;
  }

  const rowHeight = 36;
  const labelWidth = 200;
  const timelineWidth = totalDays * dayWidth;
  const todayOffset = daysBetween(timelineStart, new Date()) * dayWidth;

  return (
    <div className="rounded-xl border border-gray-200 bg-white overflow-hidden">
      {/* Legend */}
      <div className="flex flex-wrap items-center gap-3 border-b px-4 py-2 text-xs text-gray-500">
        {Object.entries(STATUS_COLORS).map(([status, color]) => (
          <span key={status} className="flex items-center gap-1">
            <span className={cn('inline-block h-2.5 w-2.5 rounded-sm', color)} />
            {status.replace(/_/g, ' ')}
          </span>
        ))}
        <span className="flex items-center gap-1">
          <span className="inline-block h-0 w-0 border-l-[5px] border-r-[5px] border-b-[8px] border-l-transparent border-r-transparent border-b-purple-600" />
          Milestone
        </span>
      </div>

      <div className="flex overflow-hidden">
        {/* Task labels (fixed left) */}
        <div className="shrink-0 border-r border-gray-200" style={{ width: labelWidth }}>
          {/* Header */}
          <div className="flex h-10 items-center border-b bg-gray-50 px-3">
            <span className="text-xs font-semibold text-gray-600">Task</span>
          </div>
          {/* Rows */}
          {validTasks.map((task) => (
            <div
              key={task.id}
              className={cn(
                'flex items-center border-b border-gray-100 px-3 transition-colors',
                hoveredTask === task.id && 'bg-blue-50',
              )}
              style={{ height: rowHeight }}
              onMouseEnter={() => setHoveredTask(task.id)}
              onMouseLeave={() => setHoveredTask(null)}
            >
              <span className="truncate text-xs font-medium text-gray-800" title={task.name}>
                {task.isMilestone && <span className="mr-1 text-purple-600">◆</span>}
                {task.name}
              </span>
            </div>
          ))}
        </div>

        {/* Timeline (scrollable) */}
        <div ref={scrollRef} className="flex-1 overflow-x-auto">
          <div style={{ width: timelineWidth, minWidth: '100%' }}>
            {/* Week headers */}
            <div className="flex h-10 border-b bg-gray-50">
              {weeks.map((w, i) => (
                <div
                  key={i}
                  className="shrink-0 border-r border-gray-200 px-1 flex items-center"
                  style={{ width: 7 * dayWidth }}
                >
                  <span className="text-[10px] font-medium text-gray-500">{w.label}</span>
                </div>
              ))}
            </div>

            {/* Task rows */}
            <div className="relative">
              {/* Today line */}
              {todayOffset > 0 && todayOffset < timelineWidth && (
                <div
                  className="absolute top-0 bottom-0 z-20 w-px bg-red-400"
                  style={{ left: todayOffset }}
                >
                  <div className="absolute -top-0 -left-3 rounded-b bg-red-400 px-1 text-[9px] font-bold text-white">
                    Today
                  </div>
                </div>
              )}

              {/* Weekend shading */}
              {Array.from({ length: Math.ceil(totalDays / 7) }).map((_, wi) => (
                <div
                  key={`we-${wi}`}
                  className="absolute top-0 bottom-0 bg-gray-50/60"
                  style={{ left: (wi * 7 + 5) * dayWidth, width: 2 * dayWidth }}
                />
              ))}

              {/* Grid lines (weekly) */}
              {weeks.map((_, i) => (
                <div
                  key={`gl-${i}`}
                  className="absolute top-0 bottom-0 border-r border-gray-100"
                  style={{ left: (i + 1) * 7 * dayWidth }}
                />
              ))}

              {validTasks.map((task) => {
                const start = new Date(task.startDate!);
                const end = new Date(task.endDate || task.startDate!);
                const startOffset = daysBetween(timelineStart, start) * dayWidth;
                const duration = Math.max(daysBetween(start, end), 1);
                const barWidth = duration * dayWidth;
                const progress = task.percentComplete ?? 0;
                const color = STATUS_COLORS[task.status] || STATUS_COLORS.NOT_STARTED;
                const bgColor = STATUS_BG[task.status] || STATUS_BG.NOT_STARTED;

                return (
                  <div
                    key={task.id}
                    className={cn(
                      'relative flex items-center border-b border-gray-100 transition-colors',
                      hoveredTask === task.id && 'bg-blue-50/50',
                    )}
                    style={{ height: rowHeight }}
                    onMouseEnter={() => setHoveredTask(task.id)}
                    onMouseLeave={() => setHoveredTask(null)}
                  >
                    {task.isMilestone ? (
                      /* Milestone diamond */
                      <div
                        className="absolute z-10 flex items-center justify-center"
                        style={{ left: startOffset - 8 }}
                      >
                        <div className="h-4 w-4 rotate-45 bg-purple-600 rounded-sm shadow-sm" />
                      </div>
                    ) : (
                      /* Task bar */
                      <div
                        className={cn('absolute z-10 rounded-md shadow-sm', bgColor)}
                        style={{ left: startOffset, width: barWidth, height: 20 }}
                      >
                        {/* Progress fill */}
                        <div
                          className={cn('h-full rounded-md transition-all', color)}
                          style={{ width: `${progress}%`, opacity: 0.85 }}
                        />
                        {/* Label on bar */}
                        {barWidth > 60 && (
                          <span
                            className="absolute inset-0 flex items-center px-2 text-[10px] font-medium text-gray-800 truncate"
                          >
                            {progress > 0 && `${progress}%`}
                          </span>
                        )}
                      </div>
                    )}

                    {/* Tooltip on hover */}
                    {hoveredTask === task.id && (
                      <div
                        className="absolute z-30 rounded-lg bg-gray-900 px-3 py-2 text-xs text-white shadow-lg whitespace-nowrap"
                        style={{ left: startOffset + barWidth + 8, top: 2 }}
                      >
                        <div className="font-semibold">{task.name}</div>
                        <div className="text-gray-300">
                          {formatShortDate(start)} — {formatShortDate(end)} · {duration}d · {progress}%
                        </div>
                      </div>
                    )}
                  </div>
                );
              })}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

### web/src/pages/Login.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { HardHat } from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { login as loginApi } from '@/api/auth';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';

export default function LoginPage() {
  const { login } = useAuth();
  const navigate = useNavigate();
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    setError('');
    setLoading(true);
    try {
      const res = await loginApi(email, password);
      login(res.token, res.user);
      navigate('/');
    } catch {
      setError('Invalid email or password');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="flex min-h-screen items-center justify-center bg-gray-50 p-4">
      <div className="w-full max-w-md">
        <div className="mb-8 text-center">
          <div className="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-2xl bg-primary">
            <HardHat className="h-9 w-9 text-white" />
          </div>
          <h1 className="text-2xl font-bold text-gray-900">BuildMate</h1>
          <p className="mt-1 text-sm text-gray-500">Construction Management Platform</p>
        </div>

        <div className="rounded-xl border border-gray-200 bg-white p-6 shadow-sm sm:p-8">
          <h2 className="mb-6 text-lg font-semibold text-gray-900">Sign in to your account</h2>

          {error && (
            <div className="mb-4 rounded-lg bg-danger-50 px-4 py-3 text-sm text-danger">
              {error}
            </div>
          )}

          <form onSubmit={handleSubmit} className="space-y-4">
            <Input
              id="email"
              label="Email"
              type="email"
              placeholder="you@example.com"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              required
            />
            <Input
              id="password"
              label="Password"
              type="password"
              placeholder="••••••••"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              required
            />
            <Button type="submit" className="w-full" disabled={loading}>
              {loading ? 'Signing in…' : 'Sign in'}
            </Button>
          </form>

          <p className="mt-6 text-center text-sm text-gray-500">
            Don't have an account?{' '}
            <Link to="/login" className="font-medium text-primary hover:underline">
              Register
            </Link>
          </p>
        </div>
      </div>
    </div>
  );
}
```

### web/src/pages/Dashboard.tsx
```tsx
import { useQuery } from '@tanstack/react-query';
import { Link } from 'react-router-dom';
import {
  FolderKanban,
  DollarSign,
  FileText,
  AlertTriangle,
  ArrowRight,
} from 'lucide-react';
import { getProjects } from '@/api/projects';
import Card from '@/components/ui/Card';
import Badge from '@/components/ui/Badge';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

export default function DashboardPage() {
  const { data: projects, isLoading } = useQuery({
    queryKey: ['projects'],
    queryFn: () => getProjects(),
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const activeProjects = projects?.filter((p) => p.status === 'active') ?? [];
  const totalBudget = projects?.reduce((s, p) => s + p.contractValue, 0) ?? 0;

  const stats = [
    { label: 'Active Projects', value: activeProjects.length, icon: FolderKanban, color: 'text-primary' },
    { label: 'Total Budget', value: formatCurrency(totalBudget), icon: DollarSign, color: 'text-success' },
    { label: 'Outstanding Invoices', value: '—', icon: FileText, color: 'text-warning' },
    { label: 'Expiring Licences', value: '—', icon: AlertTriangle, color: 'text-danger' },
  ];

  return (
    <div className="space-y-6">
      <h2 className="text-xl font-bold text-gray-900">Dashboard</h2>

      {/* Stats */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
        {stats.map((s) => (
          <Card key={s.label}>
            <div className="flex items-center gap-4">
              <div className={`rounded-lg bg-gray-50 p-3 ${s.color}`}>
                <s.icon className="h-6 w-6" />
              </div>
              <div>
                <p className="text-sm text-gray-500">{s.label}</p>
                <p className="text-xl font-bold text-gray-900">{s.value}</p>
              </div>
            </div>
          </Card>
        ))}
      </div>

      <div className="grid grid-cols-1 gap-6 lg:grid-cols-3">
        {/* Recent Projects */}
        <Card
          title="Recent Projects"
          action={
            <Link to="/projects" className="flex items-center gap-1 text-xs font-medium text-primary hover:underline">
              View all <ArrowRight className="h-3 w-3" />
            </Link>
          }
          className="lg:col-span-2"
        >
          <div className="space-y-3">
            {(projects ?? []).slice(0, 5).map((p) => (
              <Link
                key={p.id}
                to={`/projects/${p.id}`}
                className="flex items-center justify-between rounded-lg p-3 transition-colors hover:bg-gray-50"
              >
                <div>
                  <p className="text-sm font-medium text-gray-900">{p.name}</p>
                  <p className="text-xs text-gray-500">{p.address}</p>
                </div>
                <div className="flex items-center gap-3">
                  <Badge status={p.status} />
                  <span className="text-sm font-medium text-gray-900">
                    {formatCurrency(p.contractValue)}
                  </span>
                </div>
              </Link>
            ))}
            {(!projects || projects.length === 0) && (
              <p className="py-4 text-center text-sm text-gray-500">No projects yet</p>
            )}
          </div>
        </Card>

        {/* Cash Position */}
        <Card title="Cash Position">
          <div className="space-y-4">
            <div>
              <p className="text-sm text-gray-500">Total Contract Value</p>
              <p className="text-2xl font-bold text-gray-900">{formatCurrency(totalBudget)}</p>
            </div>
            <div className="grid grid-cols-2 gap-4">
              <div>
                <p className="text-xs text-gray-500">Income</p>
                <p className="text-lg font-semibold text-success">—</p>
              </div>
              <div>
                <p className="text-xs text-gray-500">Expenses</p>
                <p className="text-lg font-semibold text-danger">—</p>
              </div>
            </div>
          </div>
        </Card>
      </div>

      {/* Upcoming Milestones */}
      <Card title="Upcoming Milestones">
        <p className="py-4 text-center text-sm text-gray-500">No upcoming milestones</p>
      </Card>
    </div>
  );
}
```

### web/src/pages/projects/ProjectsPage.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { Link } from 'react-router-dom';
import { Plus, Search, FolderKanban } from 'lucide-react';
import { getProjects, createProject } from '@/api/projects';
import Button from '@/components/ui/Button';
import Badge from '@/components/ui/Badge';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import EmptyState from '@/components/ui/EmptyState';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const INITIAL_FORM = {
  name: '',
  address: '',
  clientName: '',
  contractValue: '',
  startDate: '',
  estimatedEndDate: '',
};

const STATUS_TABS = ['all', 'active', 'complete', 'on_hold'] as const;

export default function ProjectsPage() {
  const [search, setSearch] = useState('');
  const [statusFilter, setStatusFilter] = useState<string>('all');
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState(INITIAL_FORM);

  const queryClient = useQueryClient();

  const createMutation = useMutation({
    mutationFn: createProject,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['projects'] });
      setShowCreate(false);
      setForm(INITIAL_FORM);
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    createMutation.mutate({
      name: form.name,
      address: form.address || undefined,
      clientName: form.clientName || undefined,
      contractValue: form.contractValue ? Number(form.contractValue) : undefined,
      startDate: form.startDate || undefined,
      estimatedEndDate: form.estimatedEndDate || undefined,
    });
  };

  const updateField = (field: keyof typeof INITIAL_FORM) => (e: React.ChangeEvent<HTMLInputElement>) =>
    setForm((prev) => ({ ...prev, [field]: e.target.value }));

  const { data: projects, isLoading } = useQuery({
    queryKey: ['projects', statusFilter !== 'all' ? statusFilter : undefined, search],
    queryFn: () =>
      getProjects({
        status: statusFilter !== 'all' ? statusFilter : undefined,
        search: search || undefined,
      }),
  });

  return (
    <div className="space-y-6">
      <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
        <h2 className="text-xl font-bold text-gray-900">Projects</h2>
        <Button size="sm" onClick={() => setShowCreate(true)}>
          <Plus className="h-4 w-4" /> New Project
        </Button>
      </div>

      {/* Search */}
      <div className="relative">
        <Search className="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400" />
        <input
          type="text"
          placeholder="Search projects…"
          value={search}
          onChange={(e) => setSearch(e.target.value)}
          className="w-full rounded-lg border border-gray-300 py-2 pl-10 pr-4 text-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
        />
      </div>

      {/* Status Tabs */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {STATUS_TABS.map((tab) => (
          <button
            key={tab}
            onClick={() => setStatusFilter(tab)}
            className={cn(
              'whitespace-nowrap rounded-md px-4 py-1.5 text-sm font-medium capitalize transition-colors',
              statusFilter === tab
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {tab === 'on_hold' ? 'On Hold' : tab}
          </button>
        ))}
      </div>

      {/* Projects Grid */}
      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : !projects || projects.length === 0 ? (
        <EmptyState
          icon={<FolderKanban className="h-12 w-12" />}
          title="No projects found"
          description="Create your first project to get started."
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> New Project
            </Button>
          }
        />
      ) : (
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
          {projects.map((project) => (
            <Link
              key={project.id}
              to={`/projects/${project.id}`}
              className="rounded-xl border border-gray-200 bg-white p-5 shadow-sm transition-shadow hover:shadow-md"
            >
              <div className="mb-3 flex items-start justify-between">
                <div>
                  <h3 className="font-semibold text-gray-900">{project.name}</h3>
                  <p className="text-xs text-gray-500">{project.address}</p>
                </div>
                <Badge status={project.status} />
              </div>

              <p className="mb-3 text-lg font-bold text-gray-900">
                {formatCurrency(project.contractValue)}
              </p>

              {/* Progress */}
              <div className="mb-3">
                <div className="mb-1 flex justify-between text-xs text-gray-500">
                  <span>Progress</span>
                  <span>{project.progress}%</span>
                </div>
                <div className="h-2 overflow-hidden rounded-full bg-gray-100">
                  <div
                    className="h-full rounded-full bg-primary transition-all"
                    style={{ width: `${project.progress}%` }}
                  />
                </div>
              </div>

              <div className="flex justify-between text-xs text-gray-500">
                {project.startDate && <span>Start: {formatDate(project.startDate)}</span>}
                {project.estimatedEndDate && (
                  <span>End: {formatDate(project.estimatedEndDate)}</span>
                )}
              </div>
            </Link>
          ))}
        </div>
      )}

      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="New Project">
        <form onSubmit={handleSubmit} className="space-y-4">
          <Input
            id="name"
            label="Project Name"
            placeholder="e.g. 42 Wallaby Way Extension"
            value={form.name}
            onChange={updateField('name')}
            required
          />
          <Input
            id="address"
            label="Address"
            placeholder="Street address"
            value={form.address}
            onChange={updateField('address')}
          />
          <Input
            id="clientName"
            label="Client Name"
            placeholder="Client full name"
            value={form.clientName}
            onChange={updateField('clientName')}
          />
          <Input
            id="contractValue"
            label="Contract Value"
            type="number"
            placeholder="0.00"
            value={form.contractValue}
            onChange={updateField('contractValue')}
          />
          <div className="grid grid-cols-2 gap-4">
            <Input
              id="startDate"
              label="Start Date"
              type="date"
              value={form.startDate}
              onChange={updateField('startDate')}
            />
            <Input
              id="estimatedEndDate"
              label="Est. End Date"
              type="date"
              value={form.estimatedEndDate}
              onChange={updateField('estimatedEndDate')}
            />
          </div>
          <div className="flex justify-end gap-2 pt-2">
            <Button variant="secondary" type="button" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" disabled={!form.name || createMutation.isPending}>
              {createMutation.isPending ? 'Creating…' : 'Create Project'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}
```

### web/src/pages/projects/ProjectDetailPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import {
  ArrowLeft,
  DollarSign,
  TrendingUp,
  CalendarDays,
  Receipt,
  ClipboardCheck,
  FileText,
  Users,
} from 'lucide-react';
import { getProject } from '@/api/projects';
import Badge from '@/components/ui/Badge';
import Card from '@/components/ui/Card';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const TABS = [
  { key: 'overview', label: 'Overview' },
  { key: 'budget', label: 'Budget', path: 'budget', icon: DollarSign },
  { key: 'cashflow', label: 'Cash Flow', path: 'cashflow', icon: TrendingUp },
  { key: 'planning', label: 'Planning', path: 'planning', icon: CalendarDays },
  { key: 'financials', label: 'Financials', path: 'financials', icon: Receipt },
  { key: 'inspections', label: 'Inspections', path: 'inspections', icon: ClipboardCheck },
  { key: 'documents', label: 'Documents', path: 'documents', icon: FileText },
  { key: 'team', label: 'Team', icon: Users },
] as const;

export default function ProjectDetailPage() {
  const { id } = useParams<{ id: string }>();
  const [activeTab, setActiveTab] = useState('overview');

  const { data: project, isLoading } = useQuery({
    queryKey: ['project', id],
    queryFn: () => getProject(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;
  if (!project) return <p className="py-20 text-center text-gray-500">Project not found</p>;

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link
          to="/projects"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Projects
        </Link>
        <div className="flex flex-col gap-2 sm:flex-row sm:items-center sm:justify-between">
          <div>
            <h2 className="text-xl font-bold text-gray-900">{project.name}</h2>
            <p className="text-sm text-gray-500">{project.address}</p>
          </div>
          <div className="flex items-center gap-3">
            <Badge status={project.status} />
            {project.clientName && (
              <span className="text-sm text-gray-500">Client: {project.clientName}</span>
            )}
          </div>
        </div>
      </div>

      {/* Tab Navigation */}
      <div className="-mx-4 overflow-x-auto px-4 sm:mx-0 sm:px-0">
        <div className="flex gap-1 border-b border-gray-200">
          {TABS.map((tab) => {
            if (tab.path) {
              return (
                <Link
                  key={tab.key}
                  to={`/projects/${id}/${tab.path}`}
                  className="whitespace-nowrap border-b-2 border-transparent px-4 py-2.5 text-sm font-medium text-gray-500 hover:text-gray-700"
                >
                  {tab.label}
                </Link>
              );
            }
            return (
              <button
                key={tab.key}
                onClick={() => setActiveTab(tab.key)}
                className={cn(
                  'whitespace-nowrap border-b-2 px-4 py-2.5 text-sm font-medium transition-colors',
                  activeTab === tab.key
                    ? 'border-primary text-primary'
                    : 'border-transparent text-gray-500 hover:text-gray-700',
                )}
              >
                {tab.label}
              </button>
            );
          })}
        </div>
      </div>

      {/* Overview Tab */}
      {activeTab === 'overview' && (
        <div className="space-y-6">
          <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
            <Card>
              <p className="text-sm text-gray-500">Contract Value</p>
              <p className="text-xl font-bold">{formatCurrency(project.contractValue)}</p>
            </Card>
            <Card>
              <p className="text-sm text-gray-500">Progress</p>
              <p className="text-xl font-bold">{project.progress}%</p>
              <div className="mt-2 h-2 overflow-hidden rounded-full bg-gray-100">
                <div
                  className="h-full rounded-full bg-primary"
                  style={{ width: `${project.progress}%` }}
                />
              </div>
            </Card>
            <Card>
              <p className="text-sm text-gray-500">Start Date</p>
              <p className="text-xl font-bold">
                {project.startDate ? formatDate(project.startDate) : '—'}
              </p>
            </Card>
            <Card>
              <p className="text-sm text-gray-500">Est. Completion</p>
              <p className="text-xl font-bold">
                {project.estimatedEndDate ? formatDate(project.estimatedEndDate) : '—'}
              </p>
            </Card>
          </div>

          {/* Quick Actions */}
          <Card title="Quick Actions">
            <div className="grid grid-cols-2 gap-3 sm:grid-cols-4">
              {TABS.filter((t) => t.path).map((tab) => (
                <Link
                  key={tab.key}
                  to={`/projects/${id}/${tab.path}`}
                  className="flex flex-col items-center gap-2 rounded-lg border border-gray-200 p-4 text-center transition-colors hover:bg-gray-50"
                >
                  {tab.icon && <tab.icon className="h-6 w-6 text-primary" />}
                  <span className="text-xs font-medium text-gray-700">{tab.label}</span>
                </Link>
              ))}
            </div>
          </Card>

          {project.description && (
            <Card title="Description">
              <p className="text-sm text-gray-600">{project.description}</p>
            </Card>
          )}
        </div>
      )}

      {activeTab === 'team' && (
        <Card title="Project Team">
          <p className="py-4 text-center text-sm text-gray-500">
            No team members assigned yet
          </p>
        </Card>
      )}
    </div>
  );
}
```

### web/src/pages/financials/FinancialsPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, Receipt } from 'lucide-react';
import { getTransactions, createTransaction } from '@/api/transactions';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const CATEGORIES = [
  'PROGRESS_CLAIM',
  'SUBCONTRACTOR',
  'MATERIALS',
  'PLANT_HIRE',
  'LABOUR',
  'PERMIT_FEE',
  'INSURANCE',
  'OVERHEAD',
  'OTHER',
] as const;

const STATUSES = ['DRAFT', 'SUBMITTED', 'APPROVED', 'PAID', 'OVERDUE', 'CANCELLED'] as const;

export default function FinancialsPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();
  const [typeFilter, setTypeFilter] = useState<'income' | 'expense'>('expense');
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState({
    type: 'expense' as 'income' | 'expense',
    category: 'OTHER' as (typeof CATEGORIES)[number],
    description: '',
    amount: 0,
    gstAmount: 0,
    totalAmount: 0,
    date: new Date().toISOString().slice(0, 10),
    status: 'DRAFT' as (typeof STATUSES)[number],
    invoiceNumber: '',
    notes: '',
  });

  const resetForm = () =>
    setForm({
      type: 'expense',
      category: 'OTHER',
      description: '',
      amount: 0,
      gstAmount: 0,
      totalAmount: 0,
      date: new Date().toISOString().slice(0, 10),
      status: 'DRAFT',
      invoiceNumber: '',
      notes: '',
    });

  const mutation = useMutation({
    mutationFn: (payload: typeof form) =>
      createTransaction(id!, {
        ...payload,
        date: new Date(payload.date).toISOString(),
      } as any),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['transactions', id] });
      setShowCreate(false);
      resetForm();
    },
  });

  const handleAmountChange = (value: number) => {
    const gst = Math.round(value * 0.1 * 100) / 100;
    setForm((f) => ({ ...f, amount: value, gstAmount: gst, totalAmount: value + gst }));
  };

  const { data: transactions, isLoading } = useQuery({
    queryKey: ['transactions', id, typeFilter],
    queryFn: () => getTransactions(id!, { type: typeFilter }),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const items = transactions ?? [];
  const totalIncome = items.filter((t) => t.type === 'income').reduce((s, t) => s + t.amount, 0);
  const totalExpenses = items.filter((t) => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
  const outstanding = items.filter((t) => t.status === 'pending' || t.status === 'overdue').reduce((s, t) => s + t.amount, 0);

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Financials</h2>
          <Button size="sm" onClick={() => setShowCreate(true)}>
            <Plus className="h-4 w-4" /> Add Transaction
          </Button>
        </div>
      </div>

      {/* Summary Cards */}
      <div className="grid grid-cols-2 gap-3 sm:grid-cols-4">
        <Card>
          <p className="text-xs text-gray-500">Total Income</p>
          <p className="text-lg font-bold text-success">{formatCurrency(totalIncome)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Total Expenses</p>
          <p className="text-lg font-bold text-danger">{formatCurrency(totalExpenses)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Net</p>
          <p className={`text-lg font-bold ${totalIncome - totalExpenses >= 0 ? 'text-success' : 'text-danger'}`}>
            {formatCurrency(totalIncome - totalExpenses)}
          </p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Outstanding</p>
          <p className="text-lg font-bold text-warning">{formatCurrency(outstanding)}</p>
        </Card>
      </div>

      {/* Type Toggle */}
      <div className="flex gap-1 rounded-lg bg-gray-100 p-1 w-fit">
        {(['income', 'expense'] as const).map((t) => (
          <button
            key={t}
            onClick={() => setTypeFilter(t)}
            className={cn(
              'rounded-md px-4 py-1.5 text-sm font-medium capitalize transition-colors',
              typeFilter === t
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {t}
          </button>
        ))}
      </div>

      {/* Transactions List */}
      {items.length === 0 ? (
        <EmptyState
          icon={<Receipt className="h-12 w-12" />}
          title="No transactions found"
          description={`Add your first ${typeFilter} transaction.`}
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Transaction
            </Button>
          }
        />
      ) : (
        <Card>
          <Table headers={['Date', 'Description', 'Category', 'Amount', 'Status']}>
            {items.map((t) => (
              <tr key={t.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatDate(t.date)}
                </td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">{t.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{t.category}</td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${t.type === 'income' ? 'text-success' : 'text-danger'}`}>
                  {t.type === 'income' ? '+' : '-'}{formatCurrency(t.amount)}
                </td>
                <td className="px-4 py-3"><Badge status={t.status} /></td>
              </tr>
            ))}
          </Table>
        </Card>
      )}

      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="Add Transaction">
        <form
          onSubmit={(e) => {
            e.preventDefault();
            mutation.mutate(form);
          }}
          className="space-y-4"
        >
          <div className="grid grid-cols-2 gap-4">
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Type</label>
              <select
                value={form.type}
                onChange={(e) => setForm((f) => ({ ...f, type: e.target.value as 'income' | 'expense' }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                <option value="income">Income</option>
                <option value="expense">Expense</option>
              </select>
            </div>
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Category</label>
              <select
                value={form.category}
                onChange={(e) => setForm((f) => ({ ...f, category: e.target.value as (typeof CATEGORIES)[number] }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                {CATEGORIES.map((c) => (
                  <option key={c} value={c}>
                    {c.replace(/_/g, ' ')}
                  </option>
                ))}
              </select>
            </div>
          </div>

          <Input
            label="Description"
            id="description"
            required
            value={form.description}
            onChange={(e) => setForm((f) => ({ ...f, description: e.target.value }))}
          />

          <div className="grid grid-cols-3 gap-4">
            <Input
              label="Amount"
              id="amount"
              type="number"
              min={0}
              step={0.01}
              required
              value={form.amount || ''}
              onChange={(e) => handleAmountChange(parseFloat(e.target.value) || 0)}
            />
            <Input
              label="GST (10%)"
              id="gstAmount"
              type="number"
              readOnly
              value={form.gstAmount}
            />
            <Input
              label="Total"
              id="totalAmount"
              type="number"
              readOnly
              value={form.totalAmount}
            />
          </div>

          <div className="grid grid-cols-2 gap-4">
            <Input
              label="Date"
              id="date"
              type="date"
              required
              value={form.date}
              onChange={(e) => setForm((f) => ({ ...f, date: e.target.value }))}
            />
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Status</label>
              <select
                value={form.status}
                onChange={(e) => setForm((f) => ({ ...f, status: e.target.value as (typeof STATUSES)[number] }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                {STATUSES.map((s) => (
                  <option key={s} value={s}>
                    {s}
                  </option>
                ))}
              </select>
            </div>
          </div>

          <Input
            label="Invoice Number"
            id="invoiceNumber"
            value={form.invoiceNumber}
            onChange={(e) => setForm((f) => ({ ...f, invoiceNumber: e.target.value }))}
          />

          <div>
            <label htmlFor="notes" className="mb-1 block text-sm font-medium text-gray-700">
              Notes
            </label>
            <textarea
              id="notes"
              rows={3}
              value={form.notes}
              onChange={(e) => setForm((f) => ({ ...f, notes: e.target.value }))}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            />
          </div>

          <div className="flex justify-end gap-3 pt-2">
            <Button type="button" variant="secondary" size="sm" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" size="sm" disabled={mutation.isPending}>
              {mutation.isPending ? 'Saving…' : 'Save Transaction'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}
```

### web/src/pages/budget/BudgetPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus } from 'lucide-react';
import {
  BarChart,
  Bar,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from 'recharts';
import { getBudgetSummary, getVariations, createBudgetCategory, createVariation } from '@/api/budget';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

export default function BudgetPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();

  const [catOpen, setCatOpen] = useState(false);
  const [catForm, setCatForm] = useState({ costCode: '', name: '', budgetAmount: '' });

  const [varOpen, setVarOpen] = useState(false);
  const [varForm, setVarForm] = useState({ variationNumber: '', title: '', description: '', amount: '', status: '' });

  const categoryMutation = useMutation({
    mutationFn: () =>
      createBudgetCategory(id!, {
        costCode: catForm.costCode,
        name: catForm.name,
        budgetAmount: Number(catForm.budgetAmount),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['budget-summary', id] });
      setCatOpen(false);
      setCatForm({ costCode: '', name: '', budgetAmount: '' });
    },
  });

  const variationMutation = useMutation({
    mutationFn: () =>
      createVariation(id!, {
        variationNumber: Number(varForm.variationNumber),
        title: varForm.title,
        ...(varForm.description && { description: varForm.description }),
        amount: Number(varForm.amount),
        ...(varForm.status && { status: varForm.status as 'DRAFT' | 'SUBMITTED' | 'APPROVED' | 'REJECTED' }),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['variations', id] });
      setVarOpen(false);
      setVarForm({ variationNumber: '', title: '', description: '', amount: '', status: '' });
    },
  });

  const { data: summary, isLoading } = useQuery({
    queryKey: ['budget-summary', id],
    queryFn: () => getBudgetSummary(id!),
    enabled: !!id,
  });

  const { data: variations } = useQuery({
    queryKey: ['variations', id],
    queryFn: () => getVariations(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const summaryItems = [
    { label: 'Budget', value: summary?.totalBudget ?? 0, color: 'text-gray-900' },
    { label: 'Spent', value: summary?.totalSpent ?? 0, color: 'text-danger' },
    { label: 'Committed', value: summary?.totalCommitted ?? 0, color: 'text-warning' },
    { label: 'Forecast', value: summary?.totalForecast ?? 0, color: 'text-blue-600' },
    { label: 'Variance', value: summary?.totalVariance ?? 0, color: summary?.totalVariance && summary.totalVariance >= 0 ? 'text-success' : 'text-danger' },
  ];

  const chartData = (summary?.categories ?? []).map((c) => ({
    name: c.name.length > 12 ? c.name.slice(0, 12) + '…' : c.name,
    Budget: c.budgetAmount,
    Spent: c.spentAmount,
  }));

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Budget</h2>
      </div>

      {/* Summary Bar */}
      <div className="grid grid-cols-2 gap-3 sm:grid-cols-5">
        {summaryItems.map((s) => (
          <Card key={s.label}>
            <p className="text-xs text-gray-500">{s.label}</p>
            <p className={`text-lg font-bold ${s.color}`}>{formatCurrency(s.value)}</p>
          </Card>
        ))}
      </div>

      {/* Budget Categories Table */}
      <Card
        title="Budget Categories"
        action={
          <Button size="sm" variant="ghost" onClick={() => setCatOpen(true)}>
            <Plus className="h-4 w-4" /> Add Category
          </Button>
        }
      >
        <Table headers={['Cost Code', 'Category', 'Budget', 'Spent', '% Spent', 'Variance']}>
          {(summary?.categories ?? []).map((cat) => {
            const pct = cat.budgetAmount > 0 ? (cat.spentAmount / cat.budgetAmount) * 100 : 0;
            return (
              <tr key={cat.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono text-gray-600">
                  {cat.costCode}
                </td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">{cat.name}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.budgetAmount)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.spentAmount)}
                </td>
                <td className="px-4 py-3">
                  <div className="flex items-center gap-2">
                    <div className="h-2 w-20 overflow-hidden rounded-full bg-gray-100">
                      <div
                        className={`h-full rounded-full ${pct > 100 ? 'bg-danger' : pct > 80 ? 'bg-warning' : 'bg-success'}`}
                        style={{ width: `${Math.min(pct, 100)}%` }}
                      />
                    </div>
                    <span className="text-xs text-gray-500">{pct.toFixed(0)}%</span>
                  </div>
                </td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${cat.variance >= 0 ? 'text-success' : 'text-danger'}`}>
                  {formatCurrency(cat.variance)}
                </td>
              </tr>
            );
          })}
        </Table>
      </Card>

      {/* Budget vs Actual Chart */}
      {chartData.length > 0 && (
        <Card title="Budget vs Actual">
          <div className="h-72">
            <ResponsiveContainer width="100%" height="100%">
              <BarChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" />
                <XAxis dataKey="name" tick={{ fontSize: 12 }} />
                <YAxis tick={{ fontSize: 12 }} />
                <Tooltip formatter={(v: number) => formatCurrency(v)} />
                <Legend />
                <Bar dataKey="Budget" fill="#1E40AF" radius={[4, 4, 0, 0]} />
                <Bar dataKey="Spent" fill="#DC2626" radius={[4, 4, 0, 0]} />
              </BarChart>
            </ResponsiveContainer>
          </div>
        </Card>
      )}

      {/* Variations */}
      <Card
        title="Variations"
        action={
          <Button size="sm" variant="ghost" onClick={() => setVarOpen(true)}>
            <Plus className="h-4 w-4" /> Add Variation
          </Button>
        }
      >
        {(!variations || variations.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No variations</p>
        ) : (
          <Table headers={['#', 'Description', 'Amount', 'Status', 'Date']}>
            {variations.map((v) => (
              <tr key={v.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono">{v.variationNumber}</td>
                <td className="px-4 py-3 text-sm text-gray-900">{v.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(v.amount)}</td>
                <td className="px-4 py-3"><Badge status={v.status} /></td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{v.requestedDate}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Add Category Modal */}
      <Modal open={catOpen} onClose={() => setCatOpen(false)} title="Add Category">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            categoryMutation.mutate();
          }}
        >
          <Input
            id="costCode"
            label="Cost Code"
            value={catForm.costCode}
            onChange={(e) => setCatForm({ ...catForm, costCode: e.target.value })}
            required
          />
          <Input
            id="catName"
            label="Name"
            value={catForm.name}
            onChange={(e) => setCatForm({ ...catForm, name: e.target.value })}
            required
          />
          <Input
            id="budgetAmount"
            label="Budget Amount"
            type="number"
            value={catForm.budgetAmount}
            onChange={(e) => setCatForm({ ...catForm, budgetAmount: e.target.value })}
            required
          />
          <div className="flex justify-end gap-2">
            <Button type="button" variant="ghost" onClick={() => setCatOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={categoryMutation.isPending}>
              {categoryMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Add Variation Modal */}
      <Modal open={varOpen} onClose={() => setVarOpen(false)} title="Add Variation">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            variationMutation.mutate();
          }}
        >
          <Input
            id="variationNumber"
            label="Variation #"
            type="number"
            value={varForm.variationNumber}
            onChange={(e) => setVarForm({ ...varForm, variationNumber: e.target.value })}
            required
          />
          <Input
            id="varTitle"
            label="Title"
            value={varForm.title}
            onChange={(e) => setVarForm({ ...varForm, title: e.target.value })}
            required
          />
          <Input
            id="varDescription"
            label="Description"
            value={varForm.description}
            onChange={(e) => setVarForm({ ...varForm, description: e.target.value })}
          />
          <Input
            id="varAmount"
            label="Amount"
            type="number"
            value={varForm.amount}
            onChange={(e) => setVarForm({ ...varForm, amount: e.target.value })}
            required
          />
          <div className="w-full">
            <label htmlFor="varStatus" className="mb-1 block text-sm font-medium text-gray-700">Status</label>
            <select
              id="varStatus"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={varForm.status}
              onChange={(e) => setVarForm({ ...varForm, status: e.target.value })}
            >
              <option value="">Select status</option>
              <option value="DRAFT">Draft</option>
              <option value="SUBMITTED">Submitted</option>
              <option value="APPROVED">Approved</option>
              <option value="REJECTED">Rejected</option>
            </select>
          </div>
          <div className="flex justify-end gap-2">
            <Button type="button" variant="ghost" onClick={() => setVarOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={variationMutation.isPending}>
              {variationMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}
```

### web/src/pages/cashflow/CashFlowPage.tsx
```tsx
import { useParams, Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft } from 'lucide-react';
import {
  LineChart,
  Line,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from 'recharts';
import { getCashFlow, getClaimSchedule } from '@/api/cashflow';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

export default function CashFlowPage() {
  const { id } = useParams<{ id: string }>();

  const { data: cashflow, isLoading } = useQuery({
    queryKey: ['cashflow', id],
    queryFn: () => getCashFlow(id!),
    enabled: !!id,
  });

  const { data: claims } = useQuery({
    queryKey: ['claims', id],
    queryFn: () => getClaimSchedule(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const entries = cashflow ?? [];
  const totalIncome = entries.reduce((s, e) => s + e.income, 0);
  const totalExpense = entries.reduce((s, e) => s + e.expense, 0);
  const netPosition = totalIncome - totalExpense;

  const chartData = entries.map((e) => ({
    month: e.month,
    Actual: e.cumulative,
    Planned: e.plannedCumulative ?? 0,
  }));

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Cash Flow</h2>
      </div>

      {/* Cash Position */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-3">
        <Card>
          <p className="text-xs text-gray-500">Total Income</p>
          <p className="text-xl font-bold text-success">{formatCurrency(totalIncome)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Total Expenses</p>
          <p className="text-xl font-bold text-danger">{formatCurrency(totalExpense)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Net Position</p>
          <p className={`text-xl font-bold ${netPosition >= 0 ? 'text-success' : 'text-danger'}`}>
            {formatCurrency(netPosition)}
          </p>
        </Card>
      </div>

      {/* S-Curve Chart */}
      {chartData.length > 0 && (
        <Card title="S-Curve: Planned vs Actual">
          <div className="h-72">
            <ResponsiveContainer width="100%" height="100%">
              <LineChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" />
                <XAxis dataKey="month" tick={{ fontSize: 12 }} />
                <YAxis tick={{ fontSize: 12 }} />
                <Tooltip formatter={(v: number) => formatCurrency(v)} />
                <Legend />
                <Line type="monotone" dataKey="Planned" stroke="#93C5FD" strokeWidth={2} strokeDasharray="5 5" />
                <Line type="monotone" dataKey="Actual" stroke="#1E40AF" strokeWidth={2} />
              </LineChart>
            </ResponsiveContainer>
          </div>
        </Card>
      )}

      {/* Monthly Cash Flow Table */}
      <Card title="Monthly Cash Flow">
        {entries.length === 0 ? (
          <p className="py-4 text-center text-sm text-gray-500">No cash flow data</p>
        ) : (
          <Table headers={['Month', 'Income', 'Expense', 'Net', 'Cumulative']}>
            {entries.map((e) => (
              <tr key={e.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{e.month}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-success">{formatCurrency(e.income)}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-danger">{formatCurrency(e.expense)}</td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${e.net >= 0 ? 'text-success' : 'text-danger'}`}>
                  {formatCurrency(e.net)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(e.cumulative)}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Claim Schedule */}
      <Card title="Claim Schedule">
        {(!claims || claims.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No claims scheduled</p>
        ) : (
          <Table headers={['Claim #', 'Description', 'Amount', 'Due Date', 'Status']}>
            {claims.map((c) => (
              <tr key={c.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono">#{c.claimNumber}</td>
                <td className="px-4 py-3 text-sm text-gray-900">{c.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(c.amount)}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{c.dueDate}</td>
                <td className="px-4 py-3"><Badge status={c.status} /></td>
              </tr>
            ))}
          </Table>
        )}
      </Card>
    </div>
  );
}
```

### web/src/pages/planning/PlanningPage.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, CalendarDays, BarChart3, List, ZoomIn, ZoomOut } from 'lucide-react';
import { getTasks, createTask } from '@/api/tasks';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import GanttChart from '@/components/GanttChart';
import { cn, formatDate } from '@/lib/utils';

const STATUS_FILTERS = ['all', 'not_started', 'in_progress', 'complete', 'on_hold', 'blocked'] as const;

const PRIORITY_OPTIONS = ['LOW', 'MEDIUM', 'HIGH', 'CRITICAL'] as const;

const INITIAL_FORM = {
  name: '',
  description: '',
  startDate: '',
  endDate: '',
  priority: '',
  isMilestone: false,
};

export default function PlanningPage() {
  const { id } = useParams<{ id: string }>();
  const [statusFilter, setStatusFilter] = useState<string>('all');
  const [view, setView] = useState<'gantt' | 'list'>('gantt');
  const ZOOM_LEVELS = [8, 14, 20, 28, 40, 56, 80];
  const [zoomIdx, setZoomIdx] = useState(3);
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState(INITIAL_FORM);

  const queryClient = useQueryClient();

  const { mutate, isPending } = useMutation({
    mutationFn: (payload: Record<string, unknown>) => createTask(id!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setShowCreate(false);
      setForm(INITIAL_FORM);
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    const payload: Record<string, unknown> = { name: form.name };
    if (form.description) payload.description = form.description;
    if (form.startDate) payload.startDate = new Date(form.startDate).toISOString();
    if (form.endDate) payload.endDate = new Date(form.endDate).toISOString();
    if (form.priority) payload.priority = form.priority;
    if (form.isMilestone) payload.isMilestone = true;
    mutate(payload);
  };

  const { data: tasks, isLoading } = useQuery({
    queryKey: ['tasks', id, statusFilter !== 'all' ? statusFilter : undefined],
    queryFn: () => getTasks(id!, { status: statusFilter !== 'all' ? statusFilter : undefined }),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Planning</h2>
          <div className="flex items-center gap-2">
            <div className="flex rounded-lg border border-gray-300 p-0.5">
              <button
                onClick={() => setView('gantt')}
                className={cn('rounded-md px-2.5 py-1 text-xs font-medium transition-colors', view === 'gantt' ? 'bg-primary text-white' : 'text-gray-500 hover:text-gray-700')}
              >
                <BarChart3 className="inline h-3.5 w-3.5 mr-1" />Gantt
              </button>
              <button
                onClick={() => setView('list')}
                className={cn('rounded-md px-2.5 py-1 text-xs font-medium transition-colors', view === 'list' ? 'bg-primary text-white' : 'text-gray-500 hover:text-gray-700')}
              >
                <List className="inline h-3.5 w-3.5 mr-1" />List
              </button>
            </div>
            {view === 'gantt' && (
              <div className="flex items-center gap-1">
                <button
                  onClick={() => setZoomIdx((z) => Math.max(0, z - 1))}
                  disabled={zoomIdx === 0}
                  className="flex h-8 w-8 items-center justify-center rounded-lg border border-gray-300 bg-white text-gray-600 hover:bg-gray-100 disabled:opacity-30"
                  title="Zoom out"
                >
                  <ZoomOut className="h-4 w-4" />
                </button>
                <button
                  onClick={() => setZoomIdx((z) => Math.min(ZOOM_LEVELS.length - 1, z + 1))}
                  disabled={zoomIdx === ZOOM_LEVELS.length - 1}
                  className="flex h-8 w-8 items-center justify-center rounded-lg border border-gray-300 bg-white text-gray-600 hover:bg-gray-100 disabled:opacity-30"
                  title="Zoom in"
                >
                  <ZoomIn className="h-4 w-4" />
                </button>
              </div>
            )}
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Task
            </Button>
          </div>
        </div>
      </div>

      {/* Gantt Chart */}
      {view === 'gantt' && tasks && tasks.length > 0 && (
        <GanttChart tasks={tasks} dayWidth={ZOOM_LEVELS[zoomIdx]} />
      )}

      {/* Status Filters */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {STATUS_FILTERS.map((s) => (
          <button
            key={s}
            onClick={() => setStatusFilter(s)}
            className={cn(
              'whitespace-nowrap rounded-md px-3 py-1.5 text-xs font-medium capitalize transition-colors',
              statusFilter === s
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {s.replace(/_/g, ' ')}
          </button>
        ))}
      </div>

      {/* Task List */}
      {(!tasks || tasks.length === 0) ? (
        <EmptyState
          icon={<CalendarDays className="h-12 w-12" />}
          title="No tasks found"
          description="Add tasks to plan your project schedule."
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Task
            </Button>
          }
        />
      ) : (
        <Card>
          <Table headers={['Task', 'Start', 'End', 'Duration', 'Progress', 'Status', 'Assigned To']}>
            {tasks.map((task) => (
              <tr key={task.id} className={task.isMilestone ? 'bg-blue-50' : ''}>
                <td className="px-4 py-3">
                  <div className="flex items-center gap-2">
                    {task.isMilestone && (
                      <span className="text-xs font-medium text-blue-600">◆</span>
                    )}
                    <span className="text-sm font-medium text-gray-900">{task.name}</span>
                  </div>
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.startDate ? formatDate(task.startDate) : '—'}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.endDate ? formatDate(task.endDate) : '—'}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.duration ? `${task.duration}d` : '—'}
                </td>
                <td className="px-4 py-3">
                  <div className="flex items-center gap-2">
                    <div className="h-2 w-16 overflow-hidden rounded-full bg-gray-100">
                      <div
                        className="h-full rounded-full bg-primary"
                        style={{ width: `${task.progress}%` }}
                      />
                    </div>
                    <span className="text-xs text-gray-500">{task.progress}%</span>
                  </div>
                </td>
                <td className="px-4 py-3">
                  <Badge status={task.status} />
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.assignedTo ?? '—'}
                </td>
              </tr>
            ))}
          </Table>
        </Card>
      )}

      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="Add Task">
        <form onSubmit={handleSubmit} className="space-y-4">
          <Input
            id="name"
            label="Name"
            required
            value={form.name}
            onChange={(e) => setForm({ ...form, name: e.target.value })}
          />

          <div className="w-full">
            <label htmlFor="description" className="mb-1 block text-sm font-medium text-gray-700">
              Description
            </label>
            <textarea
              id="description"
              rows={3}
              value={form.description}
              onChange={(e) => setForm({ ...form, description: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            />
          </div>

          <div className="grid grid-cols-2 gap-4">
            <Input
              id="startDate"
              label="Start Date"
              type="date"
              value={form.startDate}
              onChange={(e) => setForm({ ...form, startDate: e.target.value })}
            />
            <Input
              id="endDate"
              label="End Date"
              type="date"
              value={form.endDate}
              onChange={(e) => setForm({ ...form, endDate: e.target.value })}
            />
          </div>

          <div className="w-full">
            <label htmlFor="priority" className="mb-1 block text-sm font-medium text-gray-700">
              Priority
            </label>
            <select
              id="priority"
              value={form.priority}
              onChange={(e) => setForm({ ...form, priority: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="">None</option>
              {PRIORITY_OPTIONS.map((p) => (
                <option key={p} value={p}>
                  {p.charAt(0) + p.slice(1).toLowerCase()}
                </option>
              ))}
            </select>
          </div>

          <label className="flex items-center gap-2 text-sm font-medium text-gray-700">
            <input
              type="checkbox"
              checked={form.isMilestone}
              onChange={(e) => setForm({ ...form, isMilestone: e.target.checked })}
              className="h-4 w-4 rounded border-gray-300 text-primary focus:ring-primary"
            />
            Milestone
          </label>

          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" size="sm" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" size="sm" disabled={!form.name.trim() || isPending}>
              {isPending ? 'Creating…' : 'Create Task'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}
```

### web/src/api/projects.ts
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

```

### web/src/api/transactions.ts
```typescript
import client from './client';
import type { Transaction } from '@/types';

export async function getTransactions(projectId: string, params?: {
  type?: string;
  status?: string;
}): Promise<Transaction[]> {
  const { data } = await client.get('/transactions', { params: { projectId, ...params } });
  return Array.isArray(data) ? data : data.data ?? [];
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

### web/src/api/budget.ts
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

```

### web/src/api/cashflow.ts
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

```

### web/src/api/tasks.ts
```typescript
import client from './client';
import type { Task, TaskDependency } from '@/types';

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

export async function updateTask(projectId: string, id: string, payload: Partial<Task>): Promise<Task> {
  const { data } = await client.put<Task>(`/tasks/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteTask(projectId: string, id: string): Promise<void> {
  await client.delete(`/tasks/${id}`, { params: { projectId } });
}

export async function addDependency(projectId: string, taskId: string, payload: Partial<TaskDependency>): Promise<TaskDependency> {
  const { data } = await client.post<TaskDependency>(`/tasks/${taskId}/dependencies`, { ...payload, projectId });
  return data;
}

export async function removeDependency(projectId: string, taskId: string, depId: string): Promise<void> {
  await client.delete(`/tasks/${taskId}/dependencies/${depId}`, { params: { projectId } });
}

```

### web/src/api/inspections.ts
```typescript
import client from './client';
import type { Inspection, Defect } from '@/types';

export async function getInspections(projectId: string): Promise<Inspection[]> {
  const { data } = await client.get('/inspections', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function createInspection(projectId: string, payload: Partial<Inspection>): Promise<Inspection> {
  const { data } = await client.post<Inspection>('/inspections', { ...payload, projectId });
  return data;
}

export async function updateInspection(projectId: string, id: string, payload: Partial<Inspection>): Promise<Inspection> {
  const { data } = await client.put<Inspection>(`/inspections/${id}`, { ...payload, projectId });
  return data;
}

export async function deleteInspection(projectId: string, id: string): Promise<void> {
  await client.delete(`/inspections/${id}`, { params: { projectId } });
}

export async function getDefects(projectId: string): Promise<Defect[]> {
  const { data } = await client.get('/inspections/defects/list', { params: { projectId } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function createDefect(projectId: string, payload: Partial<Defect>): Promise<Defect> {
  const { data } = await client.post<Defect>('/inspections/defects', { ...payload, projectId });
  return data;
}

export async function updateDefect(projectId: string, id: string, payload: Partial<Defect>): Promise<Defect> {
  const { data } = await client.put<Defect>(`/inspections/defects/${id}`, { ...payload, projectId });
  return data;
}

```

### web/src/api/documents.ts
```typescript
import client from './client';
import type { Document } from '@/types';

export async function getDocuments(projectId: string, params?: {
  category?: string;
}): Promise<Document[]> {
  const { data } = await client.get('/documents', { params: { projectId, folder: params?.category } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function uploadDocument(projectId: string, file: File, folder: string): Promise<Document> {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('folder', folder.toUpperCase());
  formData.append('projectId', projectId);
  formData.append('name', file.name);
  const { data } = await client.post<Document>('/documents/upload', formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
  });
  return data;
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

```

### web/src/api/tradies.ts
```typescript
import client from './client';
import type { Tradie } from '@/types';

export async function getTradies(params?: {
  trade?: string;
  search?: string;
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

```

### web/src/components/ui/Button.tsx
```tsx
import { forwardRef, type ButtonHTMLAttributes } from 'react';
import { cn } from '@/lib/utils';

const variants = {
  primary: 'bg-primary text-white hover:bg-primary-700 focus:ring-primary-500',
  secondary: 'bg-gray-100 text-gray-700 hover:bg-gray-200 focus:ring-gray-400',
  danger: 'bg-danger text-white hover:bg-danger-600 focus:ring-danger-500',
  ghost: 'bg-transparent text-gray-700 hover:bg-gray-100 focus:ring-gray-400',
} as const;

const sizes = {
  sm: 'px-3 py-1.5 text-sm',
  md: 'px-4 py-2 text-sm',
  lg: 'px-6 py-3 text-base',
} as const;

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: keyof typeof variants;
  size?: keyof typeof sizes;
}

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant = 'primary', size = 'md', disabled, ...props }, ref) => (
    <button
      ref={ref}
      className={cn(
        'inline-flex items-center justify-center gap-2 rounded-lg font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none',
        variants[variant],
        sizes[size],
        className,
      )}
      disabled={disabled}
      {...props}
    />
  ),
);

Button.displayName = 'Button';

export default Button;

```

### web/src/components/ui/Input.tsx
```tsx
import { forwardRef, type InputHTMLAttributes } from 'react';
import { cn } from '@/lib/utils';

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
}

const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ className, label, error, id, ...props }, ref) => (
    <div className="w-full">
      {label && (
        <label htmlFor={id} className="mb-1 block text-sm font-medium text-gray-700">
          {label}
        </label>
      )}
      <input
        ref={ref}
        id={id}
        className={cn(
          'block w-full rounded-lg border px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary',
          error ? 'border-danger' : 'border-gray-300',
          className,
        )}
        {...props}
      />
      {error && <p className="mt-1 text-xs text-danger">{error}</p>}
    </div>
  ),
);

Input.displayName = 'Input';

export default Input;

```

### web/src/components/ui/Modal.tsx
```tsx
import { useEffect, type ReactNode } from 'react';
import { X } from 'lucide-react';
import { cn } from '@/lib/utils';

interface ModalProps {
  open: boolean;
  onClose: () => void;
  title?: string;
  children: ReactNode;
  className?: string;
}

export default function Modal({ open, onClose, title, children, className }: ModalProps) {
  useEffect(() => {
    if (open) {
      document.body.style.overflow = 'hidden';
      return () => { document.body.style.overflow = ''; };
    }
  }, [open]);

  if (!open) return null;

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
      <div className="fixed inset-0 bg-black/50" onClick={onClose} />
      <div
        className={cn(
          'relative z-10 w-full max-w-lg rounded-xl bg-white shadow-xl',
          className,
        )}
      >
        {title && (
          <div className="flex items-center justify-between border-b px-6 py-4">
            <h2 className="text-lg font-semibold">{title}</h2>
            <button onClick={onClose} className="rounded-lg p-1 hover:bg-gray-100">
              <X className="h-5 w-5" />
            </button>
          </div>
        )}
        <div className="p-6">{children}</div>
      </div>
    </div>
  );
}

```

### web/src/components/ui/Card.tsx
```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface CardProps {
  children: ReactNode;
  title?: string;
  action?: ReactNode;
  className?: string;
}

export default function Card({ children, title, action, className }: CardProps) {
  return (
    <div className={cn('rounded-xl border border-gray-200 bg-white shadow-sm', className)}>
      {(title || action) && (
        <div className="flex items-center justify-between border-b border-gray-100 px-4 py-3 sm:px-6">
          {title && <h3 className="text-sm font-semibold text-gray-900">{title}</h3>}
          {action}
        </div>
      )}
      <div className="p-4 sm:p-6">{children}</div>
    </div>
  );
}

```

### web/src/components/ui/Badge.tsx
```tsx
import { cn } from '@/lib/utils';

const colorMap: Record<string, string> = {
  active: 'bg-success-50 text-success-500',
  complete: 'bg-blue-50 text-blue-600',
  planning: 'bg-gray-100 text-gray-600',
  on_hold: 'bg-warning-50 text-warning-500',
  cancelled: 'bg-danger-50 text-danger-500',
  paid: 'bg-success-50 text-success-500',
  pending: 'bg-warning-50 text-warning-500',
  overdue: 'bg-danger-50 text-danger-500',
  approved: 'bg-success-50 text-success-500',
  rejected: 'bg-danger-50 text-danger-500',
  open: 'bg-blue-50 text-blue-600',
  in_progress: 'bg-warning-50 text-warning-500',
  resolved: 'bg-success-50 text-success-500',
  closed: 'bg-gray-100 text-gray-600',
  passed: 'bg-success-50 text-success-500',
  failed: 'bg-danger-50 text-danger-500',
  scheduled: 'bg-blue-50 text-blue-600',
  conditional: 'bg-warning-50 text-warning-500',
  low: 'bg-gray-100 text-gray-600',
  medium: 'bg-warning-50 text-warning-500',
  high: 'bg-orange-50 text-orange-600',
  critical: 'bg-danger-50 text-danger-500',
  not_started: 'bg-gray-100 text-gray-600',
  blocked: 'bg-danger-50 text-danger-500',
  draft: 'bg-gray-100 text-gray-600',
  sent: 'bg-blue-50 text-blue-600',
  submitted: 'bg-blue-50 text-blue-600',
  upcoming: 'bg-blue-50 text-blue-600',
};

interface BadgeProps {
  status: string;
  className?: string;
}

export default function Badge({ status, className }: BadgeProps) {
  const color = colorMap[status] ?? 'bg-gray-100 text-gray-600';
  const label = status.replace(/_/g, ' ');

  return (
    <span
      className={cn(
        'inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium capitalize',
        color,
        className,
      )}
    >
      {label}
    </span>
  );
}

```

### web/src/components/ui/Table.tsx
```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface TableProps {
  headers: string[];
  children: ReactNode;
  className?: string;
}

export default function Table({ headers, children, className }: TableProps) {
  return (
    <div className={cn('overflow-x-auto rounded-lg border border-gray-200', className)}>
      <table className="min-w-full divide-y divide-gray-200 text-sm">
        <thead className="bg-gray-50">
          <tr>
            {headers.map((h) => (
              <th
                key={h}
                className="whitespace-nowrap px-4 py-3 text-left text-xs font-medium uppercase tracking-wider text-gray-500"
              >
                {h}
              </th>
            ))}
          </tr>
        </thead>
        <tbody className="divide-y divide-gray-100 bg-white">{children}</tbody>
      </table>
    </div>
  );
}

```

### web/src/components/ui/EmptyState.tsx
```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface EmptyStateProps {
  icon: ReactNode;
  title: string;
  description?: string;
  action?: ReactNode;
  className?: string;
}

export default function EmptyState({ icon, title, description, action, className }: EmptyStateProps) {
  return (
    <div className={cn('flex flex-col items-center justify-center py-12 text-center', className)}>
      <div className="mb-4 text-gray-400">{icon}</div>
      <h3 className="text-sm font-semibold text-gray-900">{title}</h3>
      {description && <p className="mt-1 text-sm text-gray-500">{description}</p>}
      {action && <div className="mt-4">{action}</div>}
    </div>
  );
}

```

### web/src/components/ui/LoadingSpinner.tsx
```tsx
import { cn } from '@/lib/utils';

interface LoadingSpinnerProps {
  className?: string;
  size?: 'sm' | 'md' | 'lg';
}

const sizeMap = { sm: 'h-4 w-4', md: 'h-8 w-8', lg: 'h-12 w-12' };

export default function LoadingSpinner({ className, size = 'md' }: LoadingSpinnerProps) {
  return (
    <div className={cn('flex items-center justify-center', className)}>
      <div
        className={cn(
          'animate-spin rounded-full border-2 border-gray-300 border-t-primary',
          sizeMap[size],
        )}
      />
    </div>
  );
}

```

### web/src/components/layout/AppLayout.tsx
```tsx
import { Outlet } from 'react-router-dom';
import Sidebar from './Sidebar';
import BottomNav from './BottomNav';
import Header from './Header';

export default function AppLayout() {
  return (
    <div className="min-h-screen bg-gray-50">
      <Sidebar />
      <div className="md:pl-64">
        <Header />
        <main className="p-4 pb-20 sm:p-6 md:pb-6">
          <Outlet />
        </main>
      </div>
      <BottomNav />
    </div>
  );
}

```

### web/src/components/layout/Sidebar.tsx
```tsx
import { NavLink } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  HardHat,
} from 'lucide-react';
import { cn } from '@/lib/utils';

const links = [
  { to: '/', icon: LayoutDashboard, label: 'Dashboard' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports' },
];

export default function Sidebar() {
  return (
    <aside className="hidden md:flex md:w-64 md:flex-col md:fixed md:inset-y-0 border-r border-gray-200 bg-white">
      <div className="flex h-16 items-center gap-2 border-b border-gray-200 px-6">
        <HardHat className="h-7 w-7 text-primary" />
        <span className="text-lg font-bold text-primary">BuildMate</span>
      </div>
      <nav className="flex-1 space-y-1 px-3 py-4">
        {links.map(({ to, icon: Icon, label }) => (
          <NavLink
            key={to}
            to={to}
            end={to === '/'}
            className={({ isActive }) =>
              cn(
                'flex items-center gap-3 rounded-lg px-3 py-2.5 text-sm font-medium transition-colors',
                isActive
                  ? 'bg-primary-50 text-primary'
                  : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900',
              )
            }
          >
            <Icon className="h-5 w-5" />
            {label}
          </NavLink>
        ))}
      </nav>
    </aside>
  );
}

```

### web/src/components/layout/Header.tsx
```tsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { Menu, Bell, LogOut, User } from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { cn } from '@/lib/utils';

interface HeaderProps {
  title?: string;
}

export default function Header({ title = 'BuildMate' }: HeaderProps) {
  const { user, logout } = useAuth();
  const navigate = useNavigate();
  const [menuOpen, setMenuOpen] = useState(false);

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <header className="sticky top-0 z-30 flex h-16 items-center justify-between border-b border-gray-200 bg-white px-4 sm:px-6">
      <div className="flex items-center gap-3">
        <button className="rounded-lg p-2 hover:bg-gray-100 md:hidden">
          <Menu className="h-5 w-5" />
        </button>
        <h1 className="text-lg font-semibold text-gray-900">{title}</h1>
      </div>

      <div className="flex items-center gap-2">
        <button className="relative rounded-lg p-2 hover:bg-gray-100">
          <Bell className="h-5 w-5 text-gray-500" />
        </button>

        <div className="relative">
          <button
            onClick={() => setMenuOpen(!menuOpen)}
            className="flex items-center gap-2 rounded-lg p-2 hover:bg-gray-100"
          >
            <div className="flex h-8 w-8 items-center justify-center rounded-full bg-primary text-sm font-medium text-white">
              {user?.firstName?.[0]}
              {user?.lastName?.[0]}
            </div>
            <span className="hidden text-sm font-medium text-gray-700 sm:block">
              {user?.firstName} {user?.lastName}
            </span>
          </button>

          {menuOpen && (
            <div className="absolute right-0 top-full mt-1 w-48 rounded-lg border border-gray-200 bg-white py-1 shadow-lg">
              <button
                onClick={() => { setMenuOpen(false); }}
                className="flex w-full items-center gap-2 px-4 py-2 text-sm text-gray-700 hover:bg-gray-50"
              >
                <User className="h-4 w-4" />
                Profile
              </button>
              <button
                onClick={handleLogout}
                className={cn(
                  'flex w-full items-center gap-2 px-4 py-2 text-sm text-danger hover:bg-gray-50',
                )}
              >
                <LogOut className="h-4 w-4" />
                Sign out
              </button>
            </div>
          )}
        </div>
      </div>
    </header>
  );
}

```

### web/src/components/layout/BottomNav.tsx
```tsx
import { NavLink } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  MoreHorizontal,
} from 'lucide-react';
import { cn } from '@/lib/utils';

const tabs = [
  { to: '/', icon: LayoutDashboard, label: 'Home' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports' },
  { to: '#more', icon: MoreHorizontal, label: 'More' },
];

export default function BottomNav() {
  return (
    <nav className="fixed bottom-0 left-0 right-0 z-40 border-t border-gray-200 bg-white md:hidden">
      <div className="flex items-center justify-around">
        {tabs.map(({ to, icon: Icon, label }) => (
          <NavLink
            key={to}
            to={to}
            end={to === '/'}
            className={({ isActive }) =>
              cn(
                'flex flex-1 flex-col items-center gap-0.5 py-2 text-[10px] font-medium transition-colors',
                isActive && to !== '#more'
                  ? 'text-primary'
                  : 'text-gray-500',
              )
            }
          >
            <Icon className="h-5 w-5" />
            {label}
          </NavLink>
        ))}
      </div>
    </nav>
  );
}

```

### web/src/components/GanttChart.tsx
```tsx
import { useMemo, useRef, useState } from 'react';
import { cn } from '@/lib/utils';

interface GanttTask {
  id: string;
  name: string;
  startDate?: string | null;
  endDate?: string | null;
  percentComplete?: number;
  status: string;
  priority?: string;
  isMilestone?: boolean;
}

interface GanttChartProps {
  tasks: GanttTask[];
  dayWidth?: number;
}

const STATUS_COLORS: Record<string, string> = {
  COMPLETE: 'bg-emerald-500',
  IN_PROGRESS: 'bg-blue-500',
  NOT_STARTED: 'bg-gray-400',
  ON_HOLD: 'bg-amber-500',
  BLOCKED: 'bg-red-500',
};

const STATUS_BG: Record<string, string> = {
  COMPLETE: 'bg-emerald-100',
  IN_PROGRESS: 'bg-blue-100',
  NOT_STARTED: 'bg-gray-200',
  ON_HOLD: 'bg-amber-100',
  BLOCKED: 'bg-red-100',
};

function daysBetween(a: Date, b: Date) {
  return Math.ceil((b.getTime() - a.getTime()) / 86400000);
}

function formatShortDate(d: Date) {
  return d.toLocaleDateString('en-AU', { day: 'numeric', month: 'short' });
}

function getMonday(d: Date) {
  const date = new Date(d);
  const day = date.getDay();
  const diff = date.getDate() - day + (day === 0 ? -6 : 1);
  date.setDate(diff);
  date.setHours(0, 0, 0, 0);
  return date;
}

export default function GanttChart({ tasks, dayWidth: dayWidthProp = 28 }: GanttChartProps) {
  const scrollRef = useRef<HTMLDivElement>(null);
  const [hoveredTask, setHoveredTask] = useState<string | null>(null);
  const dayWidth = dayWidthProp;

  const { weeks, timelineStart, totalDays, validTasks } = useMemo(() => {
    const valid = tasks.filter((t) => t.startDate);
    if (valid.length === 0) return { weeks: [], timelineStart: new Date(), totalDays: 0, validTasks: [] };

    const starts = valid.map((t) => new Date(t.startDate!).getTime());
    const ends = valid.map((t) => new Date(t.endDate || t.startDate!).getTime());
    const minDate = getMonday(new Date(Math.min(...starts)));
    const maxDate = new Date(Math.max(...ends));

    // Add padding
    minDate.setDate(minDate.getDate() - 7);
    maxDate.setDate(maxDate.getDate() + 14);

    const total = daysBetween(minDate, maxDate);

    // Generate weeks
    const wks: { date: Date; label: string }[] = [];
    const curr = new Date(minDate);
    while (curr <= maxDate) {
      wks.push({ date: new Date(curr), label: formatShortDate(curr) });
      curr.setDate(curr.getDate() + 7);
    }

    return { weeks: wks, timelineStart: minDate, totalDays: total, validTasks: valid };
  }, [tasks]);

  if (validTasks.length === 0) {
    return <p className="py-8 text-center text-sm text-gray-500">Add tasks with dates to see the Gantt chart.</p>;
  }

  const rowHeight = 36;
  const labelWidth = 200;
  const timelineWidth = totalDays * dayWidth;
  const todayOffset = daysBetween(timelineStart, new Date()) * dayWidth;

  return (
    <div className="rounded-xl border border-gray-200 bg-white overflow-hidden">
      {/* Legend */}
      <div className="flex flex-wrap items-center gap-3 border-b px-4 py-2 text-xs text-gray-500">
        {Object.entries(STATUS_COLORS).map(([status, color]) => (
          <span key={status} className="flex items-center gap-1">
            <span className={cn('inline-block h-2.5 w-2.5 rounded-sm', color)} />
            {status.replace(/_/g, ' ')}
          </span>
        ))}
        <span className="flex items-center gap-1">
          <span className="inline-block h-0 w-0 border-l-[5px] border-r-[5px] border-b-[8px] border-l-transparent border-r-transparent border-b-purple-600" />
          Milestone
        </span>
      </div>

      <div className="flex overflow-hidden">
        {/* Task labels (fixed left) */}
        <div className="shrink-0 border-r border-gray-200" style={{ width: labelWidth }}>
          {/* Header */}
          <div className="flex h-10 items-center border-b bg-gray-50 px-3">
            <span className="text-xs font-semibold text-gray-600">Task</span>
          </div>
          {/* Rows */}
          {validTasks.map((task) => (
            <div
              key={task.id}
              className={cn(
                'flex items-center border-b border-gray-100 px-3 transition-colors',
                hoveredTask === task.id && 'bg-blue-50',
              )}
              style={{ height: rowHeight }}
              onMouseEnter={() => setHoveredTask(task.id)}
              onMouseLeave={() => setHoveredTask(null)}
            >
              <span className="truncate text-xs font-medium text-gray-800" title={task.name}>
                {task.isMilestone && <span className="mr-1 text-purple-600">◆</span>}
                {task.name}
              </span>
            </div>
          ))}
        </div>

        {/* Timeline (scrollable) */}
        <div ref={scrollRef} className="flex-1 overflow-x-auto">
          <div style={{ width: timelineWidth, minWidth: '100%' }}>
            {/* Week headers */}
            <div className="flex h-10 border-b bg-gray-50">
              {weeks.map((w, i) => (
                <div
                  key={i}
                  className="shrink-0 border-r border-gray-200 px-1 flex items-center"
                  style={{ width: 7 * dayWidth }}
                >
                  <span className="text-[10px] font-medium text-gray-500">{w.label}</span>
                </div>
              ))}
            </div>

            {/* Task rows */}
            <div className="relative">
              {/* Today line */}
              {todayOffset > 0 && todayOffset < timelineWidth && (
                <div
                  className="absolute top-0 bottom-0 z-20 w-px bg-red-400"
                  style={{ left: todayOffset }}
                >
                  <div className="absolute -top-0 -left-3 rounded-b bg-red-400 px-1 text-[9px] font-bold text-white">
                    Today
                  </div>
                </div>
              )}

              {/* Weekend shading */}
              {Array.from({ length: Math.ceil(totalDays / 7) }).map((_, wi) => (
                <div
                  key={`we-${wi}`}
                  className="absolute top-0 bottom-0 bg-gray-50/60"
                  style={{ left: (wi * 7 + 5) * dayWidth, width: 2 * dayWidth }}
                />
              ))}

              {/* Grid lines (weekly) */}
              {weeks.map((_, i) => (
                <div
                  key={`gl-${i}`}
                  className="absolute top-0 bottom-0 border-r border-gray-100"
                  style={{ left: (i + 1) * 7 * dayWidth }}
                />
              ))}

              {validTasks.map((task) => {
                const start = new Date(task.startDate!);
                const end = new Date(task.endDate || task.startDate!);
                const startOffset = daysBetween(timelineStart, start) * dayWidth;
                const duration = Math.max(daysBetween(start, end), 1);
                const barWidth = duration * dayWidth;
                const progress = task.percentComplete ?? 0;
                const color = STATUS_COLORS[task.status] || STATUS_COLORS.NOT_STARTED;
                const bgColor = STATUS_BG[task.status] || STATUS_BG.NOT_STARTED;

                return (
                  <div
                    key={task.id}
                    className={cn(
                      'relative flex items-center border-b border-gray-100 transition-colors',
                      hoveredTask === task.id && 'bg-blue-50/50',
                    )}
                    style={{ height: rowHeight }}
                    onMouseEnter={() => setHoveredTask(task.id)}
                    onMouseLeave={() => setHoveredTask(null)}
                  >
                    {task.isMilestone ? (
                      /* Milestone diamond */
                      <div
                        className="absolute z-10 flex items-center justify-center"
                        style={{ left: startOffset - 8 }}
                      >
                        <div className="h-4 w-4 rotate-45 bg-purple-600 rounded-sm shadow-sm" />
                      </div>
                    ) : (
                      /* Task bar */
                      <div
                        className={cn('absolute z-10 rounded-md shadow-sm', bgColor)}
                        style={{ left: startOffset, width: barWidth, height: 20 }}
                      >
                        {/* Progress fill */}
                        <div
                          className={cn('h-full rounded-md transition-all', color)}
                          style={{ width: `${progress}%`, opacity: 0.85 }}
                        />
                        {/* Label on bar */}
                        {barWidth > 60 && (
                          <span
                            className="absolute inset-0 flex items-center px-2 text-[10px] font-medium text-gray-800 truncate"
                          >
                            {progress > 0 && `${progress}%`}
                          </span>
                        )}
                      </div>
                    )}

                    {/* Tooltip on hover */}
                    {hoveredTask === task.id && (
                      <div
                        className="absolute z-30 rounded-lg bg-gray-900 px-3 py-2 text-xs text-white shadow-lg whitespace-nowrap"
                        style={{ left: startOffset + barWidth + 8, top: 2 }}
                      >
                        <div className="font-semibold">{task.name}</div>
                        <div className="text-gray-300">
                          {formatShortDate(start)} — {formatShortDate(end)} · {duration}d · {progress}%
                        </div>
                      </div>
                    )}
                  </div>
                );
              })}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}

```

### web/src/pages/Login.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { HardHat } from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { login as loginApi } from '@/api/auth';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';

export default function LoginPage() {
  const { login } = useAuth();
  const navigate = useNavigate();
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    setError('');
    setLoading(true);
    try {
      const res = await loginApi(email, password);
      login(res.token, res.user);
      navigate('/');
    } catch {
      setError('Invalid email or password');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="flex min-h-screen items-center justify-center bg-gray-50 p-4">
      <div className="w-full max-w-md">
        <div className="mb-8 text-center">
          <div className="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-2xl bg-primary">
            <HardHat className="h-9 w-9 text-white" />
          </div>
          <h1 className="text-2xl font-bold text-gray-900">BuildMate</h1>
          <p className="mt-1 text-sm text-gray-500">Construction Management Platform</p>
        </div>

        <div className="rounded-xl border border-gray-200 bg-white p-6 shadow-sm sm:p-8">
          <h2 className="mb-6 text-lg font-semibold text-gray-900">Sign in to your account</h2>

          {error && (
            <div className="mb-4 rounded-lg bg-danger-50 px-4 py-3 text-sm text-danger">
              {error}
            </div>
          )}

          <form onSubmit={handleSubmit} className="space-y-4">
            <Input
              id="email"
              label="Email"
              type="email"
              placeholder="you@example.com"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              required
            />
            <Input
              id="password"
              label="Password"
              type="password"
              placeholder="••••••••"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              required
            />
            <Button type="submit" className="w-full" disabled={loading}>
              {loading ? 'Signing in…' : 'Sign in'}
            </Button>
          </form>

          <p className="mt-6 text-center text-sm text-gray-500">
            Don't have an account?{' '}
            <Link to="/login" className="font-medium text-primary hover:underline">
              Register
            </Link>
          </p>
        </div>
      </div>
    </div>
  );
}

```

### web/src/pages/Dashboard.tsx
```tsx
import { useQuery } from '@tanstack/react-query';
import { Link } from 'react-router-dom';
import {
  FolderKanban,
  DollarSign,
  FileText,
  AlertTriangle,
  ArrowRight,
} from 'lucide-react';
import { getProjects } from '@/api/projects';
import Card from '@/components/ui/Card';
import Badge from '@/components/ui/Badge';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

export default function DashboardPage() {
  const { data: projects, isLoading } = useQuery({
    queryKey: ['projects'],
    queryFn: () => getProjects(),
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const activeProjects = projects?.filter((p) => p.status === 'active') ?? [];
  const totalBudget = projects?.reduce((s, p) => s + p.contractValue, 0) ?? 0;

  const stats = [
    { label: 'Active Projects', value: activeProjects.length, icon: FolderKanban, color: 'text-primary' },
    { label: 'Total Budget', value: formatCurrency(totalBudget), icon: DollarSign, color: 'text-success' },
    { label: 'Outstanding Invoices', value: '—', icon: FileText, color: 'text-warning' },
    { label: 'Expiring Licences', value: '—', icon: AlertTriangle, color: 'text-danger' },
  ];

  return (
    <div className="space-y-6">
      <h2 className="text-xl font-bold text-gray-900">Dashboard</h2>

      {/* Stats */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
        {stats.map((s) => (
          <Card key={s.label}>
            <div className="flex items-center gap-4">
              <div className={`rounded-lg bg-gray-50 p-3 ${s.color}`}>
                <s.icon className="h-6 w-6" />
              </div>
              <div>
                <p className="text-sm text-gray-500">{s.label}</p>
                <p className="text-xl font-bold text-gray-900">{s.value}</p>
              </div>
            </div>
          </Card>
        ))}
      </div>

      <div className="grid grid-cols-1 gap-6 lg:grid-cols-3">
        {/* Recent Projects */}
        <Card
          title="Recent Projects"
          action={
            <Link to="/projects" className="flex items-center gap-1 text-xs font-medium text-primary hover:underline">
              View all <ArrowRight className="h-3 w-3" />
            </Link>
          }
          className="lg:col-span-2"
        >
          <div className="space-y-3">
            {(projects ?? []).slice(0, 5).map((p) => (
              <Link
                key={p.id}
                to={`/projects/${p.id}`}
                className="flex items-center justify-between rounded-lg p-3 transition-colors hover:bg-gray-50"
              >
                <div>
                  <p className="text-sm font-medium text-gray-900">{p.name}</p>
                  <p className="text-xs text-gray-500">{p.address}</p>
                </div>
                <div className="flex items-center gap-3">
                  <Badge status={p.status} />
                  <span className="text-sm font-medium text-gray-900">
                    {formatCurrency(p.contractValue)}
                  </span>
                </div>
              </Link>
            ))}
            {(!projects || projects.length === 0) && (
              <p className="py-4 text-center text-sm text-gray-500">No projects yet</p>
            )}
          </div>
        </Card>

        {/* Cash Position */}
        <Card title="Cash Position">
          <div className="space-y-4">
            <div>
              <p className="text-sm text-gray-500">Total Contract Value</p>
              <p className="text-2xl font-bold text-gray-900">{formatCurrency(totalBudget)}</p>
            </div>
            <div className="grid grid-cols-2 gap-4">
              <div>
                <p className="text-xs text-gray-500">Income</p>
                <p className="text-lg font-semibold text-success">—</p>
              </div>
              <div>
                <p className="text-xs text-gray-500">Expenses</p>
                <p className="text-lg font-semibold text-danger">—</p>
              </div>
            </div>
          </div>
        </Card>
      </div>

      {/* Upcoming Milestones */}
      <Card title="Upcoming Milestones">
        <p className="py-4 text-center text-sm text-gray-500">No upcoming milestones</p>
      </Card>
    </div>
  );
}

```

### web/src/pages/projects/ProjectsPage.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { Link } from 'react-router-dom';
import { Plus, Search, FolderKanban } from 'lucide-react';
import { getProjects, createProject } from '@/api/projects';
import Button from '@/components/ui/Button';
import Badge from '@/components/ui/Badge';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import EmptyState from '@/components/ui/EmptyState';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const INITIAL_FORM = {
  name: '',
  address: '',
  clientName: '',
  contractValue: '',
  startDate: '',
  estimatedEndDate: '',
};

const STATUS_TABS = ['all', 'active', 'complete', 'on_hold'] as const;

export default function ProjectsPage() {
  const [search, setSearch] = useState('');
  const [statusFilter, setStatusFilter] = useState<string>('all');
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState(INITIAL_FORM);

  const queryClient = useQueryClient();

  const createMutation = useMutation({
    mutationFn: createProject,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['projects'] });
      setShowCreate(false);
      setForm(INITIAL_FORM);
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    createMutation.mutate({
      name: form.name,
      address: form.address || undefined,
      clientName: form.clientName || undefined,
      contractValue: form.contractValue ? Number(form.contractValue) : undefined,
      startDate: form.startDate || undefined,
      estimatedEndDate: form.estimatedEndDate || undefined,
    });
  };

  const updateField = (field: keyof typeof INITIAL_FORM) => (e: React.ChangeEvent<HTMLInputElement>) =>
    setForm((prev) => ({ ...prev, [field]: e.target.value }));

  const { data: projects, isLoading } = useQuery({
    queryKey: ['projects', statusFilter !== 'all' ? statusFilter : undefined, search],
    queryFn: () =>
      getProjects({
        status: statusFilter !== 'all' ? statusFilter : undefined,
        search: search || undefined,
      }),
  });

  return (
    <div className="space-y-6">
      <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
        <h2 className="text-xl font-bold text-gray-900">Projects</h2>
        <Button size="sm" onClick={() => setShowCreate(true)}>
          <Plus className="h-4 w-4" /> New Project
        </Button>
      </div>

      {/* Search */}
      <div className="relative">
        <Search className="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400" />
        <input
          type="text"
          placeholder="Search projects…"
          value={search}
          onChange={(e) => setSearch(e.target.value)}
          className="w-full rounded-lg border border-gray-300 py-2 pl-10 pr-4 text-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
        />
      </div>

      {/* Status Tabs */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {STATUS_TABS.map((tab) => (
          <button
            key={tab}
            onClick={() => setStatusFilter(tab)}
            className={cn(
              'whitespace-nowrap rounded-md px-4 py-1.5 text-sm font-medium capitalize transition-colors',
              statusFilter === tab
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {tab === 'on_hold' ? 'On Hold' : tab}
          </button>
        ))}
      </div>

      {/* Projects Grid */}
      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : !projects || projects.length === 0 ? (
        <EmptyState
          icon={<FolderKanban className="h-12 w-12" />}
          title="No projects found"
          description="Create your first project to get started."
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> New Project
            </Button>
          }
        />
      ) : (
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
          {projects.map((project) => (
            <Link
              key={project.id}
              to={`/projects/${project.id}`}
              className="rounded-xl border border-gray-200 bg-white p-5 shadow-sm transition-shadow hover:shadow-md"
            >
              <div className="mb-3 flex items-start justify-between">
                <div>
                  <h3 className="font-semibold text-gray-900">{project.name}</h3>
                  <p className="text-xs text-gray-500">{project.address}</p>
                </div>
                <Badge status={project.status} />
              </div>

              <p className="mb-3 text-lg font-bold text-gray-900">
                {formatCurrency(project.contractValue)}
              </p>

              {/* Progress */}
              <div className="mb-3">
                <div className="mb-1 flex justify-between text-xs text-gray-500">
                  <span>Progress</span>
                  <span>{project.progress}%</span>
                </div>
                <div className="h-2 overflow-hidden rounded-full bg-gray-100">
                  <div
                    className="h-full rounded-full bg-primary transition-all"
                    style={{ width: `${project.progress}%` }}
                  />
                </div>
              </div>

              <div className="flex justify-between text-xs text-gray-500">
                {project.startDate && <span>Start: {formatDate(project.startDate)}</span>}
                {project.estimatedEndDate && (
                  <span>End: {formatDate(project.estimatedEndDate)}</span>
                )}
              </div>
            </Link>
          ))}
        </div>
      )}

      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="New Project">
        <form onSubmit={handleSubmit} className="space-y-4">
          <Input
            id="name"
            label="Project Name"
            placeholder="e.g. 42 Wallaby Way Extension"
            value={form.name}
            onChange={updateField('name')}
            required
          />
          <Input
            id="address"
            label="Address"
            placeholder="Street address"
            value={form.address}
            onChange={updateField('address')}
          />
          <Input
            id="clientName"
            label="Client Name"
            placeholder="Client full name"
            value={form.clientName}
            onChange={updateField('clientName')}
          />
          <Input
            id="contractValue"
            label="Contract Value"
            type="number"
            placeholder="0.00"
            value={form.contractValue}
            onChange={updateField('contractValue')}
          />
          <div className="grid grid-cols-2 gap-4">
            <Input
              id="startDate"
              label="Start Date"
              type="date"
              value={form.startDate}
              onChange={updateField('startDate')}
            />
            <Input
              id="estimatedEndDate"
              label="Est. End Date"
              type="date"
              value={form.estimatedEndDate}
              onChange={updateField('estimatedEndDate')}
            />
          </div>
          <div className="flex justify-end gap-2 pt-2">
            <Button variant="secondary" type="button" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" disabled={!form.name || createMutation.isPending}>
              {createMutation.isPending ? 'Creating…' : 'Create Project'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

### web/src/pages/projects/ProjectDetailPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import {
  ArrowLeft,
  DollarSign,
  TrendingUp,
  CalendarDays,
  Receipt,
  ClipboardCheck,
  FileText,
  Users,
} from 'lucide-react';
import { getProject } from '@/api/projects';
import Badge from '@/components/ui/Badge';
import Card from '@/components/ui/Card';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const TABS = [
  { key: 'overview', label: 'Overview' },
  { key: 'budget', label: 'Budget', path: 'budget', icon: DollarSign },
  { key: 'cashflow', label: 'Cash Flow', path: 'cashflow', icon: TrendingUp },
  { key: 'planning', label: 'Planning', path: 'planning', icon: CalendarDays },
  { key: 'financials', label: 'Financials', path: 'financials', icon: Receipt },
  { key: 'inspections', label: 'Inspections', path: 'inspections', icon: ClipboardCheck },
  { key: 'documents', label: 'Documents', path: 'documents', icon: FileText },
  { key: 'team', label: 'Team', icon: Users },
] as const;

export default function ProjectDetailPage() {
  const { id } = useParams<{ id: string }>();
  const [activeTab, setActiveTab] = useState('overview');

  const { data: project, isLoading } = useQuery({
    queryKey: ['project', id],
    queryFn: () => getProject(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;
  if (!project) return <p className="py-20 text-center text-gray-500">Project not found</p>;

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link
          to="/projects"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Projects
        </Link>
        <div className="flex flex-col gap-2 sm:flex-row sm:items-center sm:justify-between">
          <div>
            <h2 className="text-xl font-bold text-gray-900">{project.name}</h2>
            <p className="text-sm text-gray-500">{project.address}</p>
          </div>
          <div className="flex items-center gap-3">
            <Badge status={project.status} />
            {project.clientName && (
              <span className="text-sm text-gray-500">Client: {project.clientName}</span>
            )}
          </div>
        </div>
      </div>

      {/* Tab Navigation */}
      <div className="-mx-4 overflow-x-auto px-4 sm:mx-0 sm:px-0">
        <div className="flex gap-1 border-b border-gray-200">
          {TABS.map((tab) => {
            if (tab.path) {
              return (
                <Link
                  key={tab.key}
                  to={`/projects/${id}/${tab.path}`}
                  className="whitespace-nowrap border-b-2 border-transparent px-4 py-2.5 text-sm font-medium text-gray-500 hover:text-gray-700"
                >
                  {tab.label}
                </Link>
              );
            }
            return (
              <button
                key={tab.key}
                onClick={() => setActiveTab(tab.key)}
                className={cn(
                  'whitespace-nowrap border-b-2 px-4 py-2.5 text-sm font-medium transition-colors',
                  activeTab === tab.key
                    ? 'border-primary text-primary'
                    : 'border-transparent text-gray-500 hover:text-gray-700',
                )}
              >
                {tab.label}
              </button>
            );
          })}
        </div>
      </div>

      {/* Overview Tab */}
      {activeTab === 'overview' && (
        <div className="space-y-6">
          <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
            <Card>
              <p className="text-sm text-gray-500">Contract Value</p>
              <p className="text-xl font-bold">{formatCurrency(project.contractValue)}</p>
            </Card>
            <Card>
              <p className="text-sm text-gray-500">Progress</p>
              <p className="text-xl font-bold">{project.progress}%</p>
              <div className="mt-2 h-2 overflow-hidden rounded-full bg-gray-100">
                <div
                  className="h-full rounded-full bg-primary"
                  style={{ width: `${project.progress}%` }}
                />
              </div>
            </Card>
            <Card>
              <p className="text-sm text-gray-500">Start Date</p>
              <p className="text-xl font-bold">
                {project.startDate ? formatDate(project.startDate) : '—'}
              </p>
            </Card>
            <Card>
              <p className="text-sm text-gray-500">Est. Completion</p>
              <p className="text-xl font-bold">
                {project.estimatedEndDate ? formatDate(project.estimatedEndDate) : '—'}
              </p>
            </Card>
          </div>

          {/* Quick Actions */}
          <Card title="Quick Actions">
            <div className="grid grid-cols-2 gap-3 sm:grid-cols-4">
              {TABS.filter((t) => t.path).map((tab) => (
                <Link
                  key={tab.key}
                  to={`/projects/${id}/${tab.path}`}
                  className="flex flex-col items-center gap-2 rounded-lg border border-gray-200 p-4 text-center transition-colors hover:bg-gray-50"
                >
                  {tab.icon && <tab.icon className="h-6 w-6 text-primary" />}
                  <span className="text-xs font-medium text-gray-700">{tab.label}</span>
                </Link>
              ))}
            </div>
          </Card>

          {project.description && (
            <Card title="Description">
              <p className="text-sm text-gray-600">{project.description}</p>
            </Card>
          )}
        </div>
      )}

      {activeTab === 'team' && (
        <Card title="Project Team">
          <p className="py-4 text-center text-sm text-gray-500">
            No team members assigned yet
          </p>
        </Card>
      )}
    </div>
  );
}

```

### web/src/pages/financials/FinancialsPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, Receipt } from 'lucide-react';
import { getTransactions, createTransaction } from '@/api/transactions';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const CATEGORIES = [
  'PROGRESS_CLAIM',
  'SUBCONTRACTOR',
  'MATERIALS',
  'PLANT_HIRE',
  'LABOUR',
  'PERMIT_FEE',
  'INSURANCE',
  'OVERHEAD',
  'OTHER',
] as const;

const STATUSES = ['DRAFT', 'SUBMITTED', 'APPROVED', 'PAID', 'OVERDUE', 'CANCELLED'] as const;

export default function FinancialsPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();
  const [typeFilter, setTypeFilter] = useState<'income' | 'expense'>('expense');
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState({
    type: 'expense' as 'income' | 'expense',
    category: 'OTHER' as (typeof CATEGORIES)[number],
    description: '',
    amount: 0,
    gstAmount: 0,
    totalAmount: 0,
    date: new Date().toISOString().slice(0, 10),
    status: 'DRAFT' as (typeof STATUSES)[number],
    invoiceNumber: '',
    notes: '',
  });

  const resetForm = () =>
    setForm({
      type: 'expense',
      category: 'OTHER',
      description: '',
      amount: 0,
      gstAmount: 0,
      totalAmount: 0,
      date: new Date().toISOString().slice(0, 10),
      status: 'DRAFT',
      invoiceNumber: '',
      notes: '',
    });

  const mutation = useMutation({
    mutationFn: (payload: typeof form) =>
      createTransaction(id!, {
        ...payload,
        date: new Date(payload.date).toISOString(),
      } as any),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['transactions', id] });
      setShowCreate(false);
      resetForm();
    },
  });

  const handleAmountChange = (value: number) => {
    const gst = Math.round(value * 0.1 * 100) / 100;
    setForm((f) => ({ ...f, amount: value, gstAmount: gst, totalAmount: value + gst }));
  };

  const { data: transactions, isLoading } = useQuery({
    queryKey: ['transactions', id, typeFilter],
    queryFn: () => getTransactions(id!, { type: typeFilter }),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const items = transactions ?? [];
  const totalIncome = items.filter((t) => t.type === 'income').reduce((s, t) => s + t.amount, 0);
  const totalExpenses = items.filter((t) => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
  const outstanding = items.filter((t) => t.status === 'pending' || t.status === 'overdue').reduce((s, t) => s + t.amount, 0);

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Financials</h2>
          <Button size="sm" onClick={() => setShowCreate(true)}>
            <Plus className="h-4 w-4" /> Add Transaction
          </Button>
        </div>
      </div>

      {/* Summary Cards */}
      <div className="grid grid-cols-2 gap-3 sm:grid-cols-4">
        <Card>
          <p className="text-xs text-gray-500">Total Income</p>
          <p className="text-lg font-bold text-success">{formatCurrency(totalIncome)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Total Expenses</p>
          <p className="text-lg font-bold text-danger">{formatCurrency(totalExpenses)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Net</p>
          <p className={`text-lg font-bold ${totalIncome - totalExpenses >= 0 ? 'text-success' : 'text-danger'}`}>
            {formatCurrency(totalIncome - totalExpenses)}
          </p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Outstanding</p>
          <p className="text-lg font-bold text-warning">{formatCurrency(outstanding)}</p>
        </Card>
      </div>

      {/* Type Toggle */}
      <div className="flex gap-1 rounded-lg bg-gray-100 p-1 w-fit">
        {(['income', 'expense'] as const).map((t) => (
          <button
            key={t}
            onClick={() => setTypeFilter(t)}
            className={cn(
              'rounded-md px-4 py-1.5 text-sm font-medium capitalize transition-colors',
              typeFilter === t
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {t}
          </button>
        ))}
      </div>

      {/* Transactions List */}
      {items.length === 0 ? (
        <EmptyState
          icon={<Receipt className="h-12 w-12" />}
          title="No transactions found"
          description={`Add your first ${typeFilter} transaction.`}
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Transaction
            </Button>
          }
        />
      ) : (
        <Card>
          <Table headers={['Date', 'Description', 'Category', 'Amount', 'Status']}>
            {items.map((t) => (
              <tr key={t.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatDate(t.date)}
                </td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">{t.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{t.category}</td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${t.type === 'income' ? 'text-success' : 'text-danger'}`}>
                  {t.type === 'income' ? '+' : '-'}{formatCurrency(t.amount)}
                </td>
                <td className="px-4 py-3"><Badge status={t.status} /></td>
              </tr>
            ))}
          </Table>
        </Card>
      )}

      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="Add Transaction">
        <form
          onSubmit={(e) => {
            e.preventDefault();
            mutation.mutate(form);
          }}
          className="space-y-4"
        >
          <div className="grid grid-cols-2 gap-4">
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Type</label>
              <select
                value={form.type}
                onChange={(e) => setForm((f) => ({ ...f, type: e.target.value as 'income' | 'expense' }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                <option value="income">Income</option>
                <option value="expense">Expense</option>
              </select>
            </div>
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Category</label>
              <select
                value={form.category}
                onChange={(e) => setForm((f) => ({ ...f, category: e.target.value as (typeof CATEGORIES)[number] }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                {CATEGORIES.map((c) => (
                  <option key={c} value={c}>
                    {c.replace(/_/g, ' ')}
                  </option>
                ))}
              </select>
            </div>
          </div>

          <Input
            label="Description"
            id="description"
            required
            value={form.description}
            onChange={(e) => setForm((f) => ({ ...f, description: e.target.value }))}
          />

          <div className="grid grid-cols-3 gap-4">
            <Input
              label="Amount"
              id="amount"
              type="number"
              min={0}
              step={0.01}
              required
              value={form.amount || ''}
              onChange={(e) => handleAmountChange(parseFloat(e.target.value) || 0)}
            />
            <Input
              label="GST (10%)"
              id="gstAmount"
              type="number"
              readOnly
              value={form.gstAmount}
            />
            <Input
              label="Total"
              id="totalAmount"
              type="number"
              readOnly
              value={form.totalAmount}
            />
          </div>

          <div className="grid grid-cols-2 gap-4">
            <Input
              label="Date"
              id="date"
              type="date"
              required
              value={form.date}
              onChange={(e) => setForm((f) => ({ ...f, date: e.target.value }))}
            />
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Status</label>
              <select
                value={form.status}
                onChange={(e) => setForm((f) => ({ ...f, status: e.target.value as (typeof STATUSES)[number] }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                {STATUSES.map((s) => (
                  <option key={s} value={s}>
                    {s}
                  </option>
                ))}
              </select>
            </div>
          </div>

          <Input
            label="Invoice Number"
            id="invoiceNumber"
            value={form.invoiceNumber}
            onChange={(e) => setForm((f) => ({ ...f, invoiceNumber: e.target.value }))}
          />

          <div>
            <label htmlFor="notes" className="mb-1 block text-sm font-medium text-gray-700">
              Notes
            </label>
            <textarea
              id="notes"
              rows={3}
              value={form.notes}
              onChange={(e) => setForm((f) => ({ ...f, notes: e.target.value }))}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            />
          </div>

          <div className="flex justify-end gap-3 pt-2">
            <Button type="button" variant="secondary" size="sm" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" size="sm" disabled={mutation.isPending}>
              {mutation.isPending ? 'Saving…' : 'Save Transaction'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

### web/src/pages/budget/BudgetPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus } from 'lucide-react';
import {
  BarChart,
  Bar,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from 'recharts';
import { getBudgetSummary, getVariations, createBudgetCategory, createVariation } from '@/api/budget';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

export default function BudgetPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();

  const [catOpen, setCatOpen] = useState(false);
  const [catForm, setCatForm] = useState({ costCode: '', name: '', budgetAmount: '' });

  const [varOpen, setVarOpen] = useState(false);
  const [varForm, setVarForm] = useState({ variationNumber: '', title: '', description: '', amount: '', status: '' });

  const categoryMutation = useMutation({
    mutationFn: () =>
      createBudgetCategory(id!, {
        costCode: catForm.costCode,
        name: catForm.name,
        budgetAmount: Number(catForm.budgetAmount),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['budget-summary', id] });
      setCatOpen(false);
      setCatForm({ costCode: '', name: '', budgetAmount: '' });
    },
  });

  const variationMutation = useMutation({
    mutationFn: () =>
      createVariation(id!, {
        variationNumber: Number(varForm.variationNumber),
        title: varForm.title,
        ...(varForm.description && { description: varForm.description }),
        amount: Number(varForm.amount),
        ...(varForm.status && { status: varForm.status as 'DRAFT' | 'SUBMITTED' | 'APPROVED' | 'REJECTED' }),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['variations', id] });
      setVarOpen(false);
      setVarForm({ variationNumber: '', title: '', description: '', amount: '', status: '' });
    },
  });

  const { data: summary, isLoading } = useQuery({
    queryKey: ['budget-summary', id],
    queryFn: () => getBudgetSummary(id!),
    enabled: !!id,
  });

  const { data: variations } = useQuery({
    queryKey: ['variations', id],
    queryFn: () => getVariations(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const summaryItems = [
    { label: 'Budget', value: summary?.totalBudget ?? 0, color: 'text-gray-900' },
    { label: 'Spent', value: summary?.totalSpent ?? 0, color: 'text-danger' },
    { label: 'Committed', value: summary?.totalCommitted ?? 0, color: 'text-warning' },
    { label: 'Forecast', value: summary?.totalForecast ?? 0, color: 'text-blue-600' },
    { label: 'Variance', value: summary?.totalVariance ?? 0, color: summary?.totalVariance && summary.totalVariance >= 0 ? 'text-success' : 'text-danger' },
  ];

  const chartData = (summary?.categories ?? []).map((c) => ({
    name: c.name.length > 12 ? c.name.slice(0, 12) + '…' : c.name,
    Budget: c.budgetAmount,
    Spent: c.spentAmount,
  }));

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Budget</h2>
      </div>

      {/* Summary Bar */}
      <div className="grid grid-cols-2 gap-3 sm:grid-cols-5">
        {summaryItems.map((s) => (
          <Card key={s.label}>
            <p className="text-xs text-gray-500">{s.label}</p>
            <p className={`text-lg font-bold ${s.color}`}>{formatCurrency(s.value)}</p>
          </Card>
        ))}
      </div>

      {/* Budget Categories Table */}
      <Card
        title="Budget Categories"
        action={
          <Button size="sm" variant="ghost" onClick={() => setCatOpen(true)}>
            <Plus className="h-4 w-4" /> Add Category
          </Button>
        }
      >
        <Table headers={['Cost Code', 'Category', 'Budget', 'Spent', '% Spent', 'Variance']}>
          {(summary?.categories ?? []).map((cat) => {
            const pct = cat.budgetAmount > 0 ? (cat.spentAmount / cat.budgetAmount) * 100 : 0;
            return (
              <tr key={cat.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono text-gray-600">
                  {cat.costCode}
                </td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">{cat.name}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.budgetAmount)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.spentAmount)}
                </td>
                <td className="px-4 py-3">
                  <div className="flex items-center gap-2">
                    <div className="h-2 w-20 overflow-hidden rounded-full bg-gray-100">
                      <div
                        className={`h-full rounded-full ${pct > 100 ? 'bg-danger' : pct > 80 ? 'bg-warning' : 'bg-success'}`}
                        style={{ width: `${Math.min(pct, 100)}%` }}
                      />
                    </div>
                    <span className="text-xs text-gray-500">{pct.toFixed(0)}%</span>
                  </div>
                </td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${cat.variance >= 0 ? 'text-success' : 'text-danger'}`}>
                  {formatCurrency(cat.variance)}
                </td>
              </tr>
            );
          })}
        </Table>
      </Card>

      {/* Budget vs Actual Chart */}
      {chartData.length > 0 && (
        <Card title="Budget vs Actual">
          <div className="h-72">
            <ResponsiveContainer width="100%" height="100%">
              <BarChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" />
                <XAxis dataKey="name" tick={{ fontSize: 12 }} />
                <YAxis tick={{ fontSize: 12 }} />
                <Tooltip formatter={(v: number) => formatCurrency(v)} />
                <Legend />
                <Bar dataKey="Budget" fill="#1E40AF" radius={[4, 4, 0, 0]} />
                <Bar dataKey="Spent" fill="#DC2626" radius={[4, 4, 0, 0]} />
              </BarChart>
            </ResponsiveContainer>
          </div>
        </Card>
      )}

      {/* Variations */}
      <Card
        title="Variations"
        action={
          <Button size="sm" variant="ghost" onClick={() => setVarOpen(true)}>
            <Plus className="h-4 w-4" /> Add Variation
          </Button>
        }
      >
        {(!variations || variations.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No variations</p>
        ) : (
          <Table headers={['#', 'Description', 'Amount', 'Status', 'Date']}>
            {variations.map((v) => (
              <tr key={v.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono">{v.variationNumber}</td>
                <td className="px-4 py-3 text-sm text-gray-900">{v.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(v.amount)}</td>
                <td className="px-4 py-3"><Badge status={v.status} /></td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{v.requestedDate}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Add Category Modal */}
      <Modal open={catOpen} onClose={() => setCatOpen(false)} title="Add Category">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            categoryMutation.mutate();
          }}
        >
          <Input
            id="costCode"
            label="Cost Code"
            value={catForm.costCode}
            onChange={(e) => setCatForm({ ...catForm, costCode: e.target.value })}
            required
          />
          <Input
            id="catName"
            label="Name"
            value={catForm.name}
            onChange={(e) => setCatForm({ ...catForm, name: e.target.value })}
            required
          />
          <Input
            id="budgetAmount"
            label="Budget Amount"
            type="number"
            value={catForm.budgetAmount}
            onChange={(e) => setCatForm({ ...catForm, budgetAmount: e.target.value })}
            required
          />
          <div className="flex justify-end gap-2">
            <Button type="button" variant="ghost" onClick={() => setCatOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={categoryMutation.isPending}>
              {categoryMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Add Variation Modal */}
      <Modal open={varOpen} onClose={() => setVarOpen(false)} title="Add Variation">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            variationMutation.mutate();
          }}
        >
          <Input
            id="variationNumber"
            label="Variation #"
            type="number"
            value={varForm.variationNumber}
            onChange={(e) => setVarForm({ ...varForm, variationNumber: e.target.value })}
            required
          />
          <Input
            id="varTitle"
            label="Title"
            value={varForm.title}
            onChange={(e) => setVarForm({ ...varForm, title: e.target.value })}
            required
          />
          <Input
            id="varDescription"
            label="Description"
            value={varForm.description}
            onChange={(e) => setVarForm({ ...varForm, description: e.target.value })}
          />
          <Input
            id="varAmount"
            label="Amount"
            type="number"
            value={varForm.amount}
            onChange={(e) => setVarForm({ ...varForm, amount: e.target.value })}
            required
          />
          <div className="w-full">
            <label htmlFor="varStatus" className="mb-1 block text-sm font-medium text-gray-700">Status</label>
            <select
              id="varStatus"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={varForm.status}
              onChange={(e) => setVarForm({ ...varForm, status: e.target.value })}
            >
              <option value="">Select status</option>
              <option value="DRAFT">Draft</option>
              <option value="SUBMITTED">Submitted</option>
              <option value="APPROVED">Approved</option>
              <option value="REJECTED">Rejected</option>
            </select>
          </div>
          <div className="flex justify-end gap-2">
            <Button type="button" variant="ghost" onClick={() => setVarOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={variationMutation.isPending}>
              {variationMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

### web/src/pages/cashflow/CashFlowPage.tsx
```tsx
import { useParams, Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft } from 'lucide-react';
import {
  LineChart,
  Line,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from 'recharts';
import { getCashFlow, getClaimSchedule } from '@/api/cashflow';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

export default function CashFlowPage() {
  const { id } = useParams<{ id: string }>();

  const { data: cashflow, isLoading } = useQuery({
    queryKey: ['cashflow', id],
    queryFn: () => getCashFlow(id!),
    enabled: !!id,
  });

  const { data: claims } = useQuery({
    queryKey: ['claims', id],
    queryFn: () => getClaimSchedule(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const entries = cashflow ?? [];
  const totalIncome = entries.reduce((s, e) => s + e.income, 0);
  const totalExpense = entries.reduce((s, e) => s + e.expense, 0);
  const netPosition = totalIncome - totalExpense;

  const chartData = entries.map((e) => ({
    month: e.month,
    Actual: e.cumulative,
    Planned: e.plannedCumulative ?? 0,
  }));

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Cash Flow</h2>
      </div>

      {/* Cash Position */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-3">
        <Card>
          <p className="text-xs text-gray-500">Total Income</p>
          <p className="text-xl font-bold text-success">{formatCurrency(totalIncome)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Total Expenses</p>
          <p className="text-xl font-bold text-danger">{formatCurrency(totalExpense)}</p>
        </Card>
        <Card>
          <p className="text-xs text-gray-500">Net Position</p>
          <p className={`text-xl font-bold ${netPosition >= 0 ? 'text-success' : 'text-danger'}`}>
            {formatCurrency(netPosition)}
          </p>
        </Card>
      </div>

      {/* S-Curve Chart */}
      {chartData.length > 0 && (
        <Card title="S-Curve: Planned vs Actual">
          <div className="h-72">
            <ResponsiveContainer width="100%" height="100%">
              <LineChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" />
                <XAxis dataKey="month" tick={{ fontSize: 12 }} />
                <YAxis tick={{ fontSize: 12 }} />
                <Tooltip formatter={(v: number) => formatCurrency(v)} />
                <Legend />
                <Line type="monotone" dataKey="Planned" stroke="#93C5FD" strokeWidth={2} strokeDasharray="5 5" />
                <Line type="monotone" dataKey="Actual" stroke="#1E40AF" strokeWidth={2} />
              </LineChart>
            </ResponsiveContainer>
          </div>
        </Card>
      )}

      {/* Monthly Cash Flow Table */}
      <Card title="Monthly Cash Flow">
        {entries.length === 0 ? (
          <p className="py-4 text-center text-sm text-gray-500">No cash flow data</p>
        ) : (
          <Table headers={['Month', 'Income', 'Expense', 'Net', 'Cumulative']}>
            {entries.map((e) => (
              <tr key={e.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{e.month}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-success">{formatCurrency(e.income)}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-danger">{formatCurrency(e.expense)}</td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${e.net >= 0 ? 'text-success' : 'text-danger'}`}>
                  {formatCurrency(e.net)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(e.cumulative)}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Claim Schedule */}
      <Card title="Claim Schedule">
        {(!claims || claims.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No claims scheduled</p>
        ) : (
          <Table headers={['Claim #', 'Description', 'Amount', 'Due Date', 'Status']}>
            {claims.map((c) => (
              <tr key={c.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono">#{c.claimNumber}</td>
                <td className="px-4 py-3 text-sm text-gray-900">{c.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(c.amount)}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{c.dueDate}</td>
                <td className="px-4 py-3"><Badge status={c.status} /></td>
              </tr>
            ))}
          </Table>
        )}
      </Card>
    </div>
  );
}

```

### web/src/pages/planning/PlanningPage.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, CalendarDays, BarChart3, List, ZoomIn, ZoomOut } from 'lucide-react';
import { getTasks, createTask } from '@/api/tasks';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import GanttChart from '@/components/GanttChart';
import { cn, formatDate } from '@/lib/utils';

const STATUS_FILTERS = ['all', 'not_started', 'in_progress', 'complete', 'on_hold', 'blocked'] as const;

const PRIORITY_OPTIONS = ['LOW', 'MEDIUM', 'HIGH', 'CRITICAL'] as const;

const INITIAL_FORM = {
  name: '',
  description: '',
  startDate: '',
  endDate: '',
  priority: '',
  isMilestone: false,
};

export default function PlanningPage() {
  const { id } = useParams<{ id: string }>();
  const [statusFilter, setStatusFilter] = useState<string>('all');
  const [view, setView] = useState<'gantt' | 'list'>('gantt');
  const ZOOM_LEVELS = [8, 14, 20, 28, 40, 56, 80];
  const [zoomIdx, setZoomIdx] = useState(3);
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState(INITIAL_FORM);

  const queryClient = useQueryClient();

  const { mutate, isPending } = useMutation({
    mutationFn: (payload: Record<string, unknown>) => createTask(id!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setShowCreate(false);
      setForm(INITIAL_FORM);
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    const payload: Record<string, unknown> = { name: form.name };
    if (form.description) payload.description = form.description;
    if (form.startDate) payload.startDate = new Date(form.startDate).toISOString();
    if (form.endDate) payload.endDate = new Date(form.endDate).toISOString();
    if (form.priority) payload.priority = form.priority;
    if (form.isMilestone) payload.isMilestone = true;
    mutate(payload);
  };

  const { data: tasks, isLoading } = useQuery({
    queryKey: ['tasks', id, statusFilter !== 'all' ? statusFilter : undefined],
    queryFn: () => getTasks(id!, { status: statusFilter !== 'all' ? statusFilter : undefined }),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Planning</h2>
          <div className="flex items-center gap-2">
            <div className="flex rounded-lg border border-gray-300 p-0.5">
              <button
                onClick={() => setView('gantt')}
                className={cn('rounded-md px-2.5 py-1 text-xs font-medium transition-colors', view === 'gantt' ? 'bg-primary text-white' : 'text-gray-500 hover:text-gray-700')}
              >
                <BarChart3 className="inline h-3.5 w-3.5 mr-1" />Gantt
              </button>
              <button
                onClick={() => setView('list')}
                className={cn('rounded-md px-2.5 py-1 text-xs font-medium transition-colors', view === 'list' ? 'bg-primary text-white' : 'text-gray-500 hover:text-gray-700')}
              >
                <List className="inline h-3.5 w-3.5 mr-1" />List
              </button>
            </div>
            {view === 'gantt' && (
              <div className="flex items-center gap-1">
                <button
                  onClick={() => setZoomIdx((z) => Math.max(0, z - 1))}
                  disabled={zoomIdx === 0}
                  className="flex h-8 w-8 items-center justify-center rounded-lg border border-gray-300 bg-white text-gray-600 hover:bg-gray-100 disabled:opacity-30"
                  title="Zoom out"
                >
                  <ZoomOut className="h-4 w-4" />
                </button>
                <button
                  onClick={() => setZoomIdx((z) => Math.min(ZOOM_LEVELS.length - 1, z + 1))}
                  disabled={zoomIdx === ZOOM_LEVELS.length - 1}
                  className="flex h-8 w-8 items-center justify-center rounded-lg border border-gray-300 bg-white text-gray-600 hover:bg-gray-100 disabled:opacity-30"
                  title="Zoom in"
                >
                  <ZoomIn className="h-4 w-4" />
                </button>
              </div>
            )}
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Task
            </Button>
          </div>
        </div>
      </div>

      {/* Gantt Chart */}
      {view === 'gantt' && tasks && tasks.length > 0 && (
        <GanttChart tasks={tasks} dayWidth={ZOOM_LEVELS[zoomIdx]} />
      )}

      {/* Status Filters */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {STATUS_FILTERS.map((s) => (
          <button
            key={s}
            onClick={() => setStatusFilter(s)}
            className={cn(
              'whitespace-nowrap rounded-md px-3 py-1.5 text-xs font-medium capitalize transition-colors',
              statusFilter === s
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {s.replace(/_/g, ' ')}
          </button>
        ))}
      </div>

      {/* Task List */}
      {(!tasks || tasks.length === 0) ? (
        <EmptyState
          icon={<CalendarDays className="h-12 w-12" />}
          title="No tasks found"
          description="Add tasks to plan your project schedule."
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Task
            </Button>
          }
        />
      ) : (
        <Card>
          <Table headers={['Task', 'Start', 'End', 'Duration', 'Progress', 'Status', 'Assigned To']}>
            {tasks.map((task) => (
              <tr key={task.id} className={task.isMilestone ? 'bg-blue-50' : ''}>
                <td className="px-4 py-3">
                  <div className="flex items-center gap-2">
                    {task.isMilestone && (
                      <span className="text-xs font-medium text-blue-600">◆</span>
                    )}
                    <span className="text-sm font-medium text-gray-900">{task.name}</span>
                  </div>
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.startDate ? formatDate(task.startDate) : '—'}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.endDate ? formatDate(task.endDate) : '—'}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.duration ? `${task.duration}d` : '—'}
                </td>
                <td className="px-4 py-3">
                  <div className="flex items-center gap-2">
                    <div className="h-2 w-16 overflow-hidden rounded-full bg-gray-100">
                      <div
                        className="h-full rounded-full bg-primary"
                        style={{ width: `${task.progress}%` }}
                      />
                    </div>
                    <span className="text-xs text-gray-500">{task.progress}%</span>
                  </div>
                </td>
                <td className="px-4 py-3">
                  <Badge status={task.status} />
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {task.assignedTo ?? '—'}
                </td>
              </tr>
            ))}
          </Table>
        </Card>
      )}

      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="Add Task">
        <form onSubmit={handleSubmit} className="space-y-4">
          <Input
            id="name"
            label="Name"
            required
            value={form.name}
            onChange={(e) => setForm({ ...form, name: e.target.value })}
          />

          <div className="w-full">
            <label htmlFor="description" className="mb-1 block text-sm font-medium text-gray-700">
              Description
            </label>
            <textarea
              id="description"
              rows={3}
              value={form.description}
              onChange={(e) => setForm({ ...form, description: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            />
          </div>

          <div className="grid grid-cols-2 gap-4">
            <Input
              id="startDate"
              label="Start Date"
              type="date"
              value={form.startDate}
              onChange={(e) => setForm({ ...form, startDate: e.target.value })}
            />
            <Input
              id="endDate"
              label="End Date"
              type="date"
              value={form.endDate}
              onChange={(e) => setForm({ ...form, endDate: e.target.value })}
            />
          </div>

          <div className="w-full">
            <label htmlFor="priority" className="mb-1 block text-sm font-medium text-gray-700">
              Priority
            </label>
            <select
              id="priority"
              value={form.priority}
              onChange={(e) => setForm({ ...form, priority: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="">None</option>
              {PRIORITY_OPTIONS.map((p) => (
                <option key={p} value={p}>
                  {p.charAt(0) + p.slice(1).toLowerCase()}
                </option>
              ))}
            </select>
          </div>

          <label className="flex items-center gap-2 text-sm font-medium text-gray-700">
            <input
              type="checkbox"
              checked={form.isMilestone}
              onChange={(e) => setForm({ ...form, isMilestone: e.target.checked })}
              className="h-4 w-4 rounded border-gray-300 text-primary focus:ring-primary"
            />
            Milestone
          </label>

          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" size="sm" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" size="sm" disabled={!form.name.trim() || isPending}>
              {isPending ? 'Creating…' : 'Create Task'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

### web/src/pages/inspections/InspectionsPage.tsx
```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, ClipboardCheck } from 'lucide-react';
import { getInspections, getDefects, createInspection, createDefect } from '@/api/inspections';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatDate } from '@/lib/utils';

const INITIAL_INSPECTION = { type: '', title: '', scheduledDate: '', inspectorName: '', notes: '' };
const INITIAL_DEFECT = { title: '', description: '', location: '', severity: '', dueDate: '' };

export default function InspectionsPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();

  const [inspOpen, setInspOpen] = useState(false);
  const [inspForm, setInspForm] = useState(INITIAL_INSPECTION);

  const [defectOpen, setDefectOpen] = useState(false);
  const [defectForm, setDefectForm] = useState(INITIAL_DEFECT);

  const inspMutation = useMutation({
    mutationFn: () =>
      createInspection(id!, {
        projectId: id!,
        type: inspForm.type,
        title: inspForm.title,
        scheduledDate: new Date(inspForm.scheduledDate).toISOString(),
        ...(inspForm.inspectorName && { inspectorName: inspForm.inspectorName }),
        ...(inspForm.notes && { notes: inspForm.notes }),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['inspections', id] });
      setInspOpen(false);
      setInspForm(INITIAL_INSPECTION);
    },
  });

  const defectMutation = useMutation({
    mutationFn: () =>
      createDefect(id!, {
        projectId: id!,
        title: defectForm.title,
        severity: defectForm.severity,
        ...(defectForm.description && { description: defectForm.description }),
        ...(defectForm.location && { location: defectForm.location }),
        ...(defectForm.dueDate && { dueDate: new Date(defectForm.dueDate).toISOString() }),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['defects', id] });
      setDefectOpen(false);
      setDefectForm(INITIAL_DEFECT);
    },
  });

  const { data: inspections, isLoading } = useQuery({
    queryKey: ['inspections', id],
    queryFn: () => getInspections(id!),
    enabled: !!id,
  });

  const { data: defects } = useQuery({
    queryKey: ['defects', id],
    queryFn: () => getDefects(id!),
    enabled: !!id,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Inspections</h2>
          <div className="flex gap-2">
            <Button size="sm" onClick={() => setInspOpen(true)}>
              <Plus className="h-4 w-4" /> Add Inspection
            </Button>
            <Button size="sm" variant="secondary" onClick={() => setDefectOpen(true)}>
              <Plus className="h-4 w-4" /> Add Defect
            </Button>
          </div>
        </div>
      </div>

      {/* Inspections */}
      <Card title="Inspections">
        {(!inspections || inspections.length === 0) ? (
          <EmptyState
            icon={<ClipboardCheck className="h-10 w-10" />}
            title="No inspections yet"
            description="Schedule your first inspection."
          />
        ) : (
          <Table headers={['Type', 'Date', 'Inspector', 'Status', 'Result']}>
            {inspections.map((insp) => (
              <tr key={insp.id}>
                <td className="px-4 py-3">
                  <Badge status={insp.type} />
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatDate(insp.inspectionDate)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {insp.inspector ?? '—'}
                </td>
                <td className="px-4 py-3">
                  <Badge status={insp.status} />
                </td>
                <td className="px-4 py-3 text-sm text-gray-500">{insp.result ?? '—'}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Defects */}
      <Card title="Defects">
        {(!defects || defects.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No defects recorded</p>
        ) : (
          <Table headers={['Title', 'Severity', 'Status', 'Assigned To', 'Due Date']}>
            {defects.map((d) => (
              <tr key={d.id}>
                <td className="px-4 py-3">
                  <p className="text-sm font-medium text-gray-900">{d.title}</p>
                  <p className="text-xs text-gray-500">{d.description}</p>
                </td>
                <td className="px-4 py-3"><Badge status={d.severity} /></td>
                <td className="px-4 py-3"><Badge status={d.status} /></td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {d.assignedTo ?? '—'}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {d.dueDate ? formatDate(d.dueDate) : '—'}
                </td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Add Inspection Modal */}
      <Modal open={inspOpen} onClose={() => setInspOpen(false)} title="Add Inspection">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            inspMutation.mutate();
          }}
        >
          <Input
            id="inspTitle"
            label="Title"
            value={inspForm.title}
            onChange={(e) => setInspForm({ ...inspForm, title: e.target.value })}
            required
          />
          <div className="w-full">
            <label htmlFor="inspType" className="mb-1 block text-sm font-medium text-gray-700">Type</label>
            <select
              id="inspType"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={inspForm.type}
              onChange={(e) => setInspForm({ ...inspForm, type: e.target.value })}
              required
            >
              <option value="">Select type</option>
              <option value="PRE_SLAB">Pre-Slab</option>
              <option value="FRAME">Frame</option>
              <option value="LOCK_UP">Lock Up</option>
              <option value="WATERPROOFING">Waterproofing</option>
              <option value="FINAL">Final</option>
              <option value="DEFECT_RECTIFICATION">Defect Rectification</option>
              <option value="SAFETY">Safety</option>
              <option value="CUSTOM">Custom</option>
            </select>
          </div>
          <Input
            id="scheduledDate"
            label="Scheduled Date"
            type="date"
            value={inspForm.scheduledDate}
            onChange={(e) => setInspForm({ ...inspForm, scheduledDate: e.target.value })}
            required
          />
          <Input
            id="inspectorName"
            label="Inspector Name"
            value={inspForm.inspectorName}
            onChange={(e) => setInspForm({ ...inspForm, inspectorName: e.target.value })}
          />
          <Input
            id="inspNotes"
            label="Notes"
            value={inspForm.notes}
            onChange={(e) => setInspForm({ ...inspForm, notes: e.target.value })}
          />
          <div className="flex justify-end gap-2">
            <Button type="button" variant="ghost" onClick={() => setInspOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={inspMutation.isPending}>
              {inspMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Add Defect Modal */}
      <Modal open={defectOpen} onClose={() => setDefectOpen(false)} title="Add Defect">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            defectMutation.mutate();
          }}
        >
          <Input
            id="defectTitle"
            label="Title"
            value={defectForm.title}
            onChange={(e) => setDefectForm({ ...defectForm, title: e.target.value })}
            required
          />
          <Input
            id="defectDescription"
            label="Description"
            value={defectForm.description}
            onChange={(e) => setDefectForm({ ...defectForm, description: e.target.value })}
          />
          <Input
            id="defectLocation"
            label="Location"
            value={defectForm.location}
            onChange={(e) => setDefectForm({ ...defectForm, location: e.target.value })}
          />
          <div className="w-full">
            <label htmlFor="defectSeverity" className="mb-1 block text-sm font-medium text-gray-700">Severity</label>
            <select
              id="defectSeverity"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={defectForm.severity}
              onChange={(e) => setDefectForm({ ...defectForm, severity: e.target.value })}
              required
            >
              <option value="">Select severity</option>
              <option value="MINOR">Minor</option>
              <option value="MODERATE">Moderate</option>
              <option value="MAJOR">Major</option>
              <option value="CRITICAL">Critical</option>
            </select>
          </div>
          <Input
            id="defectDueDate"
            label="Due Date"
            type="date"
            value={defectForm.dueDate}
            onChange={(e) => setDefectForm({ ...defectForm, dueDate: e.target.value })}
          />
          <div className="flex justify-end gap-2">
            <Button type="button" variant="ghost" onClick={() => setDefectOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={defectMutation.isPending}>
              {defectMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

### web/src/pages/documents/DocumentsPage.tsx
```tsx
import { useState, useCallback } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import {
  ArrowLeft,
  Upload,
  FileText,
  FileImage,
  File,
  Download,
  Trash2,
} from 'lucide-react';
import { getDocuments, uploadDocument, deleteDocument } from '@/api/documents';
import Card from '@/components/ui/Card';
import Button from '@/components/ui/Button';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatDate } from '@/lib/utils';

const CATEGORIES = ['all', 'plans', 'contracts', 'permits', 'swms', 'photos', 'reports', 'other'] as const;

function fileIcon(mimeType: string) {
  if (mimeType.startsWith('image/')) return FileImage;
  if (mimeType.includes('pdf')) return FileText;
  return File;
}

function formatFileSize(bytes: number): string {
  if (bytes < 1024) return `${bytes} B`;
  if (bytes < 1024 * 1024) return `${(bytes / 1024).toFixed(1)} KB`;
  return `${(bytes / (1024 * 1024)).toFixed(1)} MB`;
}

export default function DocumentsPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();
  const [category, setCategory] = useState<string>('all');
  const [dragOver, setDragOver] = useState(false);

  const { data: documents, isLoading } = useQuery({
    queryKey: ['documents', id, category !== 'all' ? category : undefined],
    queryFn: () => getDocuments(id!, { category: category !== 'all' ? category : undefined }),
    enabled: !!id,
  });

  const uploadMutation = useMutation({
    mutationFn: ({ file, cat }: { file: File; cat: string }) => uploadDocument(id!, file, cat),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['documents', id] }),
  });

  const deleteMutation = useMutation({
    mutationFn: (docId: string) => deleteDocument(id!, docId),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['documents', id] }),
  });

  const handleFiles = useCallback(
    (files: FileList) => {
      Array.from(files).forEach((file) => {
        uploadMutation.mutate({ file, cat: category !== 'all' ? category : 'other' });
      });
    },
    [category, uploadMutation],
  );

  const handleDrop = useCallback(
    (e: React.DragEvent) => {
      e.preventDefault();
      setDragOver(false);
      if (e.dataTransfer.files.length > 0) handleFiles(e.dataTransfer.files);
    },
    [handleFiles],
  );

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  return (
    <div className="space-y-6">
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Documents</h2>
          <label>
            <Button size="sm" as-child className="cursor-pointer">
              <Upload className="h-4 w-4" /> Upload
            </Button>
            <input
              type="file"
              multiple
              className="hidden"
              onChange={(e) => e.target.files && handleFiles(e.target.files)}
            />
          </label>
        </div>
      </div>

      {/* Category Tabs */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {CATEGORIES.map((c) => (
          <button
            key={c}
            onClick={() => setCategory(c)}
            className={cn(
              'whitespace-nowrap rounded-md px-3 py-1.5 text-xs font-medium capitalize transition-colors',
              category === c
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {c === 'swms' ? 'SWMS' : c}
          </button>
        ))}
      </div>

      {/* Drop Zone */}
      <div
        onDragOver={(e) => { e.preventDefault(); setDragOver(true); }}
        onDragLeave={() => setDragOver(false)}
        onDrop={handleDrop}
        className={cn(
          'flex flex-col items-center justify-center rounded-xl border-2 border-dashed p-8 transition-colors',
          dragOver ? 'border-primary bg-primary-50' : 'border-gray-300',
        )}
      >
        <Upload className="mb-2 h-8 w-8 text-gray-400" />
        <p className="text-sm text-gray-500">Drag & drop files here, or click Upload</p>
      </div>

      {/* File List */}
      {(!documents || documents.length === 0) ? (
        <EmptyState
          icon={<FileText className="h-12 w-12" />}
          title="No documents"
          description="Upload your first document."
        />
      ) : (
        <Card>
          <div className="divide-y divide-gray-100">
            {documents.map((doc) => {
              const Icon = fileIcon(doc.mimeType);
              return (
                <div
                  key={doc.id}
                  className="flex items-center justify-between gap-4 py-3 first:pt-0 last:pb-0"
                >
                  <div className="flex items-center gap-3 min-w-0">
                    <Icon className="h-8 w-8 shrink-0 text-gray-400" />
                    <div className="min-w-0">
                      <p className="truncate text-sm font-medium text-gray-900">{doc.name}</p>
                      <p className="text-xs text-gray-500">
                        {formatFileSize(doc.fileSize)} · {formatDate(doc.createdAt)} · {doc.uploadedBy}
                      </p>
                    </div>
                  </div>
                  <div className="flex items-center gap-1 shrink-0">
                    <a
                      href={doc.url}
                      target="_blank"
                      rel="noopener noreferrer"
                      className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                    >
                      <Download className="h-4 w-4" />
                    </a>
                    <button
                      onClick={() => deleteMutation.mutate(doc.id)}
                      className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-danger"
                    >
                      <Trash2 className="h-4 w-4" />
                    </button>
                  </div>
                </div>
              );
            })}
          </div>
        </Card>
      )}
    </div>
  );
}

```

### web/src/pages/tradies/TradiesPage.tsx
```tsx
import { useState, type FormEvent } from 'react';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { Plus, Search, Users, Star, AlertTriangle, Phone, Mail } from 'lucide-react';
import { getTradies, createTradie } from '@/api/tradies';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatDate } from '@/lib/utils';

const TRADES = [
  'BUILDER','CARPENTER','ELECTRICIAN','PLUMBER','PLASTERER','PAINTER','TILER',
  'BRICKLAYER','CONCRETER','ROOFER','LANDSCAPER','DEMOLITION','EXCAVATION',
  'STEEL_FIXER','CABINET_MAKER','GLAZIER','RENDERER','WATERPROOFER','INSULATION',
  'FENCER','SCAFFOLDER','CLEANER','HVAC','FIRE_PROTECTION','SECURITY_SYSTEMS',
  'FLOORING','STONEMASON','POOL_BUILDER','SURVEYOR','ENGINEER','ARCHITECT',
  'DRAFTSPERSON','OTHER',
] as const;

const TRADE_FILTERS = [
  'all', 'carpenter', 'electrician', 'plumber', 'painter', 'tiler',
  'concreter', 'bricklayer', 'roofer', 'landscaper', 'other',
];

const INITIAL_FORM = { companyName: '', contactName: '', trade: 'CARPENTER' as string, email: '', phone: '', abn: '', licenceNumber: '' };

export default function TradiesPage() {
  const queryClient = useQueryClient();
  const [search, setSearch] = useState('');
  const [tradeFilter, setTradeFilter] = useState('all');
  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState(INITIAL_FORM);

  const createMutation = useMutation({
    mutationFn: (payload: typeof INITIAL_FORM) => createTradie(payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradies'] });
      setShowCreate(false);
      setForm(INITIAL_FORM);
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    createMutation.mutate(form);
  };

  const { data: tradies, isLoading } = useQuery({
    queryKey: ['tradies', tradeFilter !== 'all' ? tradeFilter : undefined, search],
    queryFn: () =>
      getTradies({
        trade: tradeFilter !== 'all' ? tradeFilter : undefined,
        search: search || undefined,
      }),
  });

  const expiringCount = (tradies ?? []).filter((t) => {
    if (!t.licenceExpiry) return false;
    const exp = new Date(t.licenceExpiry);
    const inThirtyDays = new Date();
    inThirtyDays.setDate(inThirtyDays.getDate() + 30);
    return exp <= inThirtyDays;
  }).length;

  return (
    <div className="space-y-6">
      <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
        <h2 className="text-xl font-bold text-gray-900">Tradies</h2>
        <Button size="sm" onClick={() => setShowCreate(true)}>
          <Plus className="h-4 w-4" /> Add Tradie
        </Button>
      </div>

      {/* Create Modal */}
      <Modal open={showCreate} onClose={() => setShowCreate(false)} title="Add Tradie">
        <form onSubmit={handleSubmit} className="space-y-4">
          <Input id="companyName" label="Company Name" required value={form.companyName} onChange={(e) => setForm({ ...form, companyName: e.target.value })} />
          <Input id="contactName" label="Contact Name" required value={form.contactName} onChange={(e) => setForm({ ...form, contactName: e.target.value })} />
          <div>
            <label htmlFor="trade" className="mb-1 block text-sm font-medium text-gray-700">Trade</label>
            <select id="trade" required value={form.trade} onChange={(e) => setForm({ ...form, trade: e.target.value })} className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm">
              {TRADES.map((t) => <option key={t} value={t}>{t.replace(/_/g, ' ')}</option>)}
            </select>
          </div>
          <div className="grid grid-cols-2 gap-3">
            <Input id="email" label="Email" type="email" value={form.email} onChange={(e) => setForm({ ...form, email: e.target.value })} />
            <Input id="phone" label="Phone" value={form.phone} onChange={(e) => setForm({ ...form, phone: e.target.value })} />
          </div>
          <div className="grid grid-cols-2 gap-3">
            <Input id="abn" label="ABN" value={form.abn} onChange={(e) => setForm({ ...form, abn: e.target.value })} />
            <Input id="licenceNumber" label="Licence #" value={form.licenceNumber} onChange={(e) => setForm({ ...form, licenceNumber: e.target.value })} />
          </div>
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setShowCreate(false)}>Cancel</Button>
            <Button type="submit" disabled={createMutation.isPending}>{createMutation.isPending ? 'Saving…' : 'Save'}</Button>
          </div>
        </form>
      </Modal>

      {/* Compliance Alert */}
      {expiringCount > 0 && (
        <div className="flex items-center gap-3 rounded-lg border border-warning bg-warning-50 px-4 py-3">
          <AlertTriangle className="h-5 w-5 text-warning" />
          <p className="text-sm text-warning-600">
            <strong>{expiringCount}</strong> tradie{expiringCount > 1 ? 's' : ''} with expiring licences or insurance
          </p>
        </div>
      )}

      {/* Search */}
      <div className="relative">
        <Search className="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400" />
        <input
          type="text"
          placeholder="Search tradies…"
          value={search}
          onChange={(e) => setSearch(e.target.value)}
          className="w-full rounded-lg border border-gray-300 py-2 pl-10 pr-4 text-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
        />
      </div>

      {/* Trade Filter */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {TRADE_FILTERS.map((f) => (
          <button
            key={f}
            onClick={() => setTradeFilter(f)}
            className={cn(
              'whitespace-nowrap rounded-md px-3 py-1.5 text-xs font-medium capitalize transition-colors',
              tradeFilter === f
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {f}
          </button>
        ))}
      </div>

      {/* Tradies Grid */}
      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : !tradies || tradies.length === 0 ? (
        <EmptyState
          icon={<Users className="h-12 w-12" />}
          title="No tradies found"
          description="Add your first tradie to the directory."
          action={
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Tradie
            </Button>
          }
        />
      ) : (
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
          {tradies.map((tradie) => {
            const isExpiring = tradie.licenceExpiry && new Date(tradie.licenceExpiry) <= new Date(Date.now() + 30 * 86400000);
            return (
              <div
                key={tradie.id}
                className="rounded-xl border border-gray-200 bg-white p-5 shadow-sm"
              >
                <div className="mb-3 flex items-start justify-between">
                  <div>
                    <h3 className="font-semibold text-gray-900">
                      {tradie.firstName} {tradie.lastName}
                    </h3>
                    {tradie.company && (
                      <p className="text-xs text-gray-500">{tradie.company}</p>
                    )}
                  </div>
                  <Badge status={tradie.trade} />
                </div>

                {/* Rating */}
                {tradie.rating != null && (
                  <div className="mb-3 flex items-center gap-1">
                    {Array.from({ length: 5 }).map((_, i) => (
                      <Star
                        key={i}
                        className={cn(
                          'h-4 w-4',
                          i < tradie.rating! ? 'fill-warning text-warning' : 'text-gray-200',
                        )}
                      />
                    ))}
                    <span className="ml-1 text-xs text-gray-500">{tradie.rating.toFixed(1)}</span>
                  </div>
                )}

                <div className="mb-3 space-y-1">
                  <div className="flex items-center gap-2 text-sm text-gray-600">
                    <Phone className="h-3.5 w-3.5" /> {tradie.phone}
                  </div>
                  {tradie.email && (
                    <div className="flex items-center gap-2 text-sm text-gray-600">
                      <Mail className="h-3.5 w-3.5" /> {tradie.email}
                    </div>
                  )}
                </div>

                {tradie.licenceExpiry && (
                  <div className={cn(
                    'rounded-lg px-3 py-2 text-xs',
                    isExpiring ? 'bg-danger-50 text-danger' : 'bg-gray-50 text-gray-500',
                  )}>
                    {isExpiring && <AlertTriangle className="mr-1 inline h-3 w-3" />}
                    Licence expires: {formatDate(tradie.licenceExpiry)}
                  </div>
                )}
              </div>
            );
          })}
        </div>
      )}
    </div>
  );
}

```

### web/src/pages/reports/ReportsPage.tsx
```tsx
import { useState } from 'react';
import {
  DollarSign,
  TrendingUp,
  Users,
  FolderKanban,
  Calendar,
  ArrowRight,
} from 'lucide-react';
import Card from '@/components/ui/Card';
import Input from '@/components/ui/Input';

const REPORTS = [
  {
    key: 'budget',
    title: 'Budget Summary',
    description: 'Overview of budget vs actual spend across all projects.',
    icon: DollarSign,
    color: 'text-primary bg-primary-50',
  },
  {
    key: 'cashflow',
    title: 'Cash Flow',
    description: 'Monthly cash flow analysis with S-curve projections.',
    icon: TrendingUp,
    color: 'text-success bg-success-50',
  },
  {
    key: 'compliance',
    title: 'Tradie Compliance',
    description: 'Licence and insurance expiry status for all tradies.',
    icon: Users,
    color: 'text-warning bg-warning-50',
  },
  {
    key: 'status',
    title: 'Project Status',
    description: 'Status summary across all active projects.',
    icon: FolderKanban,
    color: 'text-blue-600 bg-blue-50',
  },
];

export default function ReportsPage() {
  const [startDate, setStartDate] = useState('');
  const [endDate, setEndDate] = useState('');

  return (
    <div className="space-y-6">
      <h2 className="text-xl font-bold text-gray-900">Reports</h2>

      {/* Date Range */}
      <Card title="Date Range">
        <div className="flex flex-col gap-3 sm:flex-row sm:items-end">
          <div className="flex items-center gap-2">
            <Calendar className="h-4 w-4 text-gray-400" />
            <Input
              id="start-date"
              type="date"
              label="From"
              value={startDate}
              onChange={(e) => setStartDate(e.target.value)}
            />
          </div>
          <div className="flex items-center gap-2">
            <Calendar className="h-4 w-4 text-gray-400" />
            <Input
              id="end-date"
              type="date"
              label="To"
              value={endDate}
              onChange={(e) => setEndDate(e.target.value)}
            />
          </div>
        </div>
      </Card>

      {/* Report Cards */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
        {REPORTS.map((report) => (
          <button
            key={report.key}
            className="flex items-start gap-4 rounded-xl border border-gray-200 bg-white p-5 text-left shadow-sm transition-shadow hover:shadow-md"
          >
            <div className={`rounded-lg p-3 ${report.color}`}>
              <report.icon className="h-6 w-6" />
            </div>
            <div className="flex-1">
              <div className="flex items-center justify-between">
                <h3 className="font-semibold text-gray-900">{report.title}</h3>
                <ArrowRight className="h-4 w-4 text-gray-400" />
              </div>
              <p className="mt-1 text-sm text-gray-500">{report.description}</p>
            </div>
          </button>
        ))}
      </div>
    </div>
  );
}

```

---

## Part 2: Server Backend

### server/src/index.ts
```typescript
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import path from 'path';
import { config } from './config';
import { errorHandler } from './middleware/errorHandler';

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

const app = express();

// Middleware
app.use(helmet());
app.use(cors({ origin: config.corsOrigin, credentials: true }));
app.use(morgan('dev'));
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true }));

// Static file serving for uploads
app.use('/uploads', express.static(path.join(__dirname, '..', 'uploads')));

// Health check
app.get('/api/health', (_req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
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

// Global error handler
app.use(errorHandler);

app.listen(config.port, () => {
  console.log(`BuildMate API running on port ${config.port}`);
});

export default app;

```

### server/src/config/index.ts
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

### server/src/types/index.ts
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

### server/src/middleware/auth.ts
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

### server/src/middleware/validate.ts
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

### server/src/middleware/errorHandler.ts
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

### server/src/routes/auth.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import bcrypt from 'bcryptjs';
import jwt from 'jsonwebtoken';
import { z } from 'zod';
import { PrismaClient } from '@prisma/client';
import { config } from '../config';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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
router.post('/register', validate(registerSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
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

    res.status(201).json({ user, ...tokens });
  } catch (error) {
    next(error);
  }
});

// POST /auth/login
router.post('/login', validate(loginSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
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
      user: { id: user.id, email: user.email, name: user.name, phone: user.phone, role: user.role },
      ...tokens,
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

    res.json(user);
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

    res.json(user);
  } catch (error) {
    next(error);
  }
});

export default router;

```

### server/src/routes/projects.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

// POST /projects
router.post('/', validate(createProjectSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
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

    res.status(201).json(project);
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

// PUT /projects/:id
router.put('/:id', validate(updateProjectSchema), async (req: AuthRequest, res: Response, next: NextFunction) => {
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

    res.json(project);
  } catch (error) {
    next(error);
  }
});

// DELETE /projects/:id (archive by setting status to CANCELLED)
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const project = await prisma.project.findUnique({ where: { id: req.params.id } });
    if (!project) {
      throw new AppError('Project not found', 404);
    }

    await prisma.project.update({
      where: { id: req.params.id },
      data: { status: 'CANCELLED' },
    });

    res.json({ message: 'Project archived' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

### server/src/routes/transactions.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

    const where: Prisma.TransactionWhereInput = {};
    if (projectId) where.projectId = projectId;
    if (type) where.type = type as Prisma.EnumTransactionTypeFilter;
    if (status) where.status = status as Prisma.EnumTransactionStatusFilter;
    if (category) where.category = category as Prisma.EnumTransactionCategoryFilter;

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
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const transaction = await prisma.transaction.findUnique({ where: { id: req.params.id } });
    if (!transaction) {
      throw new AppError('Transaction not found', 404);
    }

    await prisma.transaction.delete({ where: { id: req.params.id } });
    res.json({ message: 'Transaction deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

### server/src/routes/invoices.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

// DELETE /invoices/:id
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const invoice = await prisma.invoice.findUnique({ where: { id: req.params.id } });
    if (!invoice) {
      throw new AppError('Invoice not found', 404);
    }

    await prisma.invoice.delete({ where: { id: req.params.id } });
    res.json({ message: 'Invoice deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

### server/src/routes/budget.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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
      where: { id: req.params.id },
      data: req.body,
    });
    res.json(category);
  } catch (error) {
    next(error);
  }
});

// DELETE /budget/categories/:id
router.delete('/categories/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.budgetCategory.delete({ where: { id: req.params.id } });
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

    const variation = await prisma.variation.update({
      where: { id: req.params.id },
      data,
      include: { category: { select: { id: true, costCode: true, name: true } } },
    });

    res.json(variation);
  } catch (error) {
    next(error);
  }
});

// DELETE /budget/variations/:id
router.delete('/variations/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    await prisma.variation.delete({ where: { id: req.params.id } });
    res.json({ message: 'Variation deleted' });
  } catch (error) {
    next(error);
  }
});

// ─── Budget Summary ─────────────────────────

// GET /budget/summary/:projectId
router.get('/summary/:projectId', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const { projectId } = req.params;

    const categories = await prisma.budgetCategory.findMany({
      where: { projectId },
      orderBy: { sortOrder: 'asc' },
    });

    const approvedVariations = await prisma.variation.aggregate({
      where: { projectId, status: 'APPROVED' },
      _sum: { amount: true },
      _count: true,
    });

    const expenses = await prisma.transaction.aggregate({
      where: { projectId, type: 'EXPENSE', status: { in: ['APPROVED', 'PAID'] } },
      _sum: { totalAmount: true },
    });

    const totalBudget = categories.reduce((sum, c) => sum + Number(c.budgetAmount), 0);
    const totalRevised = categories.reduce((sum, c) => sum + Number(c.revisedBudget || c.budgetAmount), 0);
    const totalForecast = categories.reduce((sum, c) => sum + Number(c.forecastToComplete || 0), 0);
    const totalSpent = Number(expenses._sum.totalAmount || 0);
    const totalVariations = Number(approvedVariations._sum.amount || 0);

    res.json({
      categories,
      summary: {
        totalBudget,
        totalRevised,
        totalVariations,
        variationCount: approvedVariations._count,
        adjustedBudget: totalRevised + totalVariations,
        totalSpent,
        totalForecast,
        variance: totalRevised + totalVariations - totalSpent - totalForecast,
      },
    });
  } catch (error) {
    next(error);
  }
});

export default router;

```

### server/src/routes/cashflow.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

### server/src/routes/tasks.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

    res.json(tasks);
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

    res.status(201).json(task);
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

    res.json(task);
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

    res.json(task);
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

    res.json(task);
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

### server/src/routes/inspections.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

### server/src/routes/documents.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import multer from 'multer';
import path from 'path';
import fs from 'fs';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';
import { config } from '../config';

const router = Router();
const prisma = new PrismaClient();

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
    if (folder) where.folder = folder as Prisma.EnumDocumentFolderFilter;
    if (search) {
      where.OR = [
        { name: { contains: search, mode: 'insensitive' } },
        { description: { contains: search, mode: 'insensitive' } },
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
    const document = await prisma.document.findUnique({
      where: { id: req.params.id },
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
    const document = await prisma.document.findUnique({
      where: { id: req.params.id },
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
    const document = await prisma.document.update({
      where: { id: req.params.id },
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

// DELETE /documents/:id
router.delete('/:id', async (req: AuthRequest, res: Response, next: NextFunction) => {
  try {
    const document = await prisma.document.findUnique({ where: { id: req.params.id } });
    if (!document) {
      throw new AppError('Document not found', 404);
    }

    // Delete file from disk
    const filePath = path.resolve(document.fileUrl.replace(/^\//, ''));
    if (fs.existsSync(filePath)) {
      fs.unlinkSync(filePath);
    }

    await prisma.document.delete({ where: { id: req.params.id } });
    res.json({ message: 'Document deleted' });
  } catch (error) {
    next(error);
  }
});

export default router;

```

### server/src/routes/tradies.ts
```typescript
import { Router, Response, NextFunction } from 'express';
import { z } from 'zod';
import { PrismaClient, Prisma } from '@prisma/client';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { AuthRequest, AppError } from '../types';

const router = Router();
const prisma = new PrismaClient();

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

export default router;

```

### server/prisma/schema.prisma
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

```

---

*End of Source Code Reference — 50 files total*
