# BuildMate — Web Frontend Components Source Code

---


## web/src/components/ui/Badge.tsx

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
  viewed: 'bg-purple-50 text-purple-600',
  submitted: 'bg-blue-50 text-blue-600',
  upcoming: 'bg-blue-50 text-blue-600',
  assigned: 'bg-blue-50 text-blue-600',
  rectified: 'bg-success-50 text-success-500',
  verified: 'bg-success-50 text-success-500',
  pass: 'bg-success-50 text-success-500',
  fail: 'bg-danger-50 text-danger-500',
  minor: 'bg-gray-100 text-gray-600',
  moderate: 'bg-warning-50 text-warning-500',
  major: 'bg-orange-50 text-orange-600',
  pre_slab: 'bg-blue-50 text-blue-600',
  frame: 'bg-purple-50 text-purple-600',
  lock_up: 'bg-indigo-50 text-indigo-600',
  waterproofing: 'bg-cyan-50 text-cyan-600',
  final: 'bg-success-50 text-success-500',
  defect_rectification: 'bg-orange-50 text-orange-600',
  safety: 'bg-danger-50 text-danger-500',
  custom: 'bg-gray-100 text-gray-600',
  na: 'bg-gray-100 text-gray-600',
  not_inspected: 'bg-gray-100 text-gray-600',
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

## web/src/components/ui/Button.tsx

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

## web/src/components/ui/Card.tsx

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

## web/src/components/ui/EmptyState.tsx

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

## web/src/components/ui/Input.tsx

```tsx
import { forwardRef, type InputHTMLAttributes } from 'react';
import { cn } from '@/lib/utils';

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
}

const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ className, label, error, id, ...props }, ref) => {
    const errorId = error && id ? `${id}-error` : undefined;
    return (
      <div className="w-full">
        {label && (
          <label htmlFor={id} className="mb-1 block text-sm font-medium text-gray-700">
            {label}
          </label>
        )}
        <input
          ref={ref}
          id={id}
          aria-invalid={error ? true : undefined}
          aria-describedby={errorId}
          className={cn(
            'block w-full rounded-lg border px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary',
            error ? 'border-danger' : 'border-gray-300',
            className,
          )}
          {...props}
        />
        {error && <p id={errorId} role="alert" className="mt-1 text-xs text-danger">{error}</p>}
      </div>
    );
  },
);

Input.displayName = 'Input';

export default Input;

```

## web/src/components/ui/LoadingSpinner.tsx

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

## web/src/components/ui/Modal.tsx

```tsx
import { useEffect, useRef, useCallback, type ReactNode } from 'react';
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
  const dialogRef = useRef<HTMLDivElement>(null);
  const previousFocusRef = useRef<HTMLElement | null>(null);

  // Focus trap: keep focus within the modal
  const handleKeyDown = useCallback(
    (e: KeyboardEvent) => {
      if (e.key === 'Escape') {
        onClose();
        return;
      }
      if (e.key !== 'Tab' || !dialogRef.current) return;

      const focusable = dialogRef.current.querySelectorAll<HTMLElement>(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])',
      );
      if (focusable.length === 0) return;

      const first = focusable[0];
      const last = focusable[focusable.length - 1];

      if (e.shiftKey) {
        if (document.activeElement === first) {
          e.preventDefault();
          last.focus();
        }
      } else {
        if (document.activeElement === last) {
          e.preventDefault();
          first.focus();
        }
      }
    },
    [onClose],
  );

  useEffect(() => {
    if (open) {
      previousFocusRef.current = document.activeElement as HTMLElement;
      document.body.style.overflow = 'hidden';
      document.addEventListener('keydown', handleKeyDown);

      // Focus the dialog on open
      requestAnimationFrame(() => {
        dialogRef.current?.focus();
      });

      return () => {
        document.body.style.overflow = '';
        document.removeEventListener('keydown', handleKeyDown);
        previousFocusRef.current?.focus();
      };
    }
  }, [open, handleKeyDown]);

  if (!open) return null;

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4" role="presentation">
      <div className="fixed inset-0 bg-black/50" aria-hidden="true" onClick={onClose} />
      <div
        ref={dialogRef}
        role="dialog"
        aria-modal="true"
        aria-label={title}
        tabIndex={-1}
        className={cn(
          'relative z-10 w-full max-w-lg rounded-xl bg-white shadow-xl focus:outline-none',
          className,
        )}
      >
        {title && (
          <div className="flex items-center justify-between border-b px-6 py-4">
            <h2 className="text-lg font-semibold">{title}</h2>
            <button
              onClick={onClose}
              aria-label="Close dialog"
              className="rounded-lg p-1 hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:ring-offset-2"
            >
              <X className="h-5 w-5" aria-hidden="true" />
            </button>
          </div>
        )}
        <div className="p-6">{children}</div>
      </div>
    </div>
  );
}

```

## web/src/components/ui/Table.tsx

```tsx
import type { ReactNode } from 'react';
import { cn } from '@/lib/utils';

interface TableProps {
  headers: string[];
  children: ReactNode;
  className?: string;
  caption?: string;
}

export default function Table({ headers, children, className, caption }: TableProps) {
  return (
    <div className={cn('overflow-x-auto rounded-lg border border-gray-200', className)} role="region" aria-label={caption || 'Data table'} tabIndex={0}>
      <table className="min-w-full divide-y divide-gray-200 text-sm">
        {caption && <caption className="sr-only">{caption}</caption>}
        <thead className="bg-gray-50">
          <tr>
            {headers.map((h) => (
              <th
                key={h}
                scope="col"
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

## web/src/components/layout/AppLayout.tsx

```tsx
import { useState, useEffect } from 'react';
import { Outlet } from 'react-router-dom';
import { WifiOff } from 'lucide-react';
import Sidebar from './Sidebar';
import BottomNav from './BottomNav';
import Header from './Header';
import MobileDrawer from './MobileDrawer';

export default function AppLayout() {
  const [drawerOpen, setDrawerOpen] = useState(false);
  const [isOffline, setIsOffline] = useState(!navigator.onLine);

  useEffect(() => {
    const handleOnline = () => setIsOffline(false);
    const handleOffline = () => setIsOffline(true);
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return (
    <div className="min-h-screen bg-gray-50">
      <Sidebar />
      <MobileDrawer open={drawerOpen} onClose={() => setDrawerOpen(false)} />
      <div className="md:pl-64">
        <Header onMenuClick={() => setDrawerOpen(true)} />
        {isOffline && (
          <div className="flex items-center gap-2 bg-warning-50 px-4 py-2 text-sm font-medium text-warning-700">
            <WifiOff className="h-4 w-4" />
            You are offline. Some features may not be available.
          </div>
        )}
        <main className="p-4 pb-20 sm:p-6 md:pb-6">
          <Outlet />
        </main>
      </div>
      <BottomNav />
    </div>
  );
}

```

## web/src/components/layout/Sidebar.tsx

```tsx
import { NavLink } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  Settings,
  HardHat,
  Shield,
} from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { hasPermission, isAdmin } from '@/lib/permissions';
import { cn } from '@/lib/utils';

const links = [
  { to: '/', icon: LayoutDashboard, label: 'Dashboard' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports', permission: 'viewReports' as const },
  { to: '/settings', icon: Settings, label: 'Settings' },
];

export default function Sidebar() {
  const { user } = useAuth();
  const role = user?.role;

  return (
    <aside className="hidden md:flex md:w-64 md:flex-col md:fixed md:inset-y-0 border-r border-gray-200 bg-white">
      <div className="flex h-16 items-center gap-2 border-b border-gray-200 px-6">
        <HardHat className="h-7 w-7 text-primary" />
        <span className="text-lg font-bold text-primary">BuildMate</span>
      </div>
      <nav className="flex-1 space-y-1 px-3 py-4">
        {links
          .filter((link) => !link.permission || hasPermission(role, link.permission))
          .map(({ to, icon: Icon, label }) => (
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
        {isAdmin(role) && (
          <NavLink
            to="/admin/users"
            className={({ isActive }) =>
              cn(
                'flex items-center gap-3 rounded-lg px-3 py-2.5 text-sm font-medium transition-colors',
                isActive
                  ? 'bg-primary-50 text-primary'
                  : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900',
              )
            }
          >
            <Shield className="h-5 w-5" />
            Admin
          </NavLink>
        )}
      </nav>
    </aside>
  );
}

```

## web/src/components/layout/Header.tsx

```tsx
import { useState, useEffect, useRef } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { Menu, Bell, LogOut, User, AlertTriangle } from 'lucide-react';
import { useQuery } from '@tanstack/react-query';
import { useAuth } from '@/hooks/useAuth';
import { getCompliance } from '@/api/tradies';
import { cn, formatDate } from '@/lib/utils';

interface HeaderProps {
  title?: string;
  onMenuClick?: () => void;
}

export default function Header({ title = 'BuildMate', onMenuClick }: HeaderProps) {
  const { user, logout } = useAuth();
  const navigate = useNavigate();
  const [menuOpen, setMenuOpen] = useState(false);
  const [bellOpen, setBellOpen] = useState(false);
  const bellRef = useRef<HTMLDivElement>(null);

  const { data: compliance } = useQuery({
    queryKey: ['compliance-header'],
    queryFn: () => getCompliance(30),
    refetchInterval: 5 * 60 * 1000,
  });

  const notifCount = (compliance?.summary.documentsExpiring ?? 0) + (compliance?.summary.tradiesWithExpiringCompliance ?? 0);

  useEffect(() => {
    const handler = (e: MouseEvent) => {
      if (bellRef.current && !bellRef.current.contains(e.target as Node)) setBellOpen(false);
    };
    document.addEventListener('mousedown', handler);
    return () => document.removeEventListener('mousedown', handler);
  }, []);

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <header className="sticky top-0 z-30 flex h-16 items-center justify-between border-b border-gray-200 bg-white px-4 sm:px-6">
      <div className="flex items-center gap-3">
        <button onClick={onMenuClick} className="rounded-lg p-2 hover:bg-gray-100 md:hidden">
          <Menu className="h-5 w-5" />
        </button>
        <h1 className="text-lg font-semibold text-gray-900">{title}</h1>
      </div>

      <div className="flex items-center gap-2">
        <div className="relative" ref={bellRef}>
          <button onClick={() => setBellOpen(!bellOpen)} className="relative rounded-lg p-2 hover:bg-gray-100">
            <Bell className="h-5 w-5 text-gray-500" />
            {notifCount > 0 && (
              <span className="absolute right-1 top-1 flex h-4 min-w-[1rem] items-center justify-center rounded-full bg-danger px-1 text-[10px] font-bold text-white">
                {notifCount}
              </span>
            )}
          </button>

          {bellOpen && (
            <div className="absolute right-0 top-full mt-1 w-80 rounded-lg border border-gray-200 bg-white py-1 shadow-lg">
              <div className="border-b px-4 py-2">
                <h3 className="text-sm font-semibold text-gray-900">Notifications</h3>
              </div>
              <div className="max-h-64 overflow-y-auto">
                {notifCount === 0 ? (
                  <p className="px-4 py-3 text-sm text-gray-500">No notifications</p>
                ) : (
                  <>
                    {(compliance?.expiringTradies ?? []).map((t) => (
                      <Link
                        key={t.id}
                        to={`/tradies/${t.id}`}
                        onClick={() => setBellOpen(false)}
                        className="flex items-start gap-2 px-4 py-2 text-sm hover:bg-gray-50"
                      >
                        <AlertTriangle className="mt-0.5 h-4 w-4 flex-shrink-0 text-warning" />
                        <div>
                          <p className="font-medium text-gray-900">{t.contactName || `${t.firstName} ${t.lastName}`}</p>
                          <p className="text-xs text-gray-500">
                            {t.licenceExpiry && `Licence: ${formatDate(t.licenceExpiry)}`}
                            {t.insuranceExpiry && ` · Insurance: ${formatDate(t.insuranceExpiry)}`}
                          </p>
                        </div>
                      </Link>
                    ))}
                    {(compliance?.expiringDocuments ?? []).slice(0, 5).map((d) => (
                      <Link
                        key={d.id}
                        to={`/tradies/${d.tradie.id}`}
                        onClick={() => setBellOpen(false)}
                        className="flex items-start gap-2 px-4 py-2 text-sm hover:bg-gray-50"
                      >
                        <AlertTriangle className="mt-0.5 h-4 w-4 flex-shrink-0 text-warning" />
                        <div>
                          <p className="font-medium text-gray-900">{d.tradie.contactName} — {d.type.replace(/_/g, ' ')}</p>
                          <p className="text-xs text-gray-500">{d.expiryDate ? `Expires: ${formatDate(d.expiryDate)}` : ''}</p>
                        </div>
                      </Link>
                    ))}
                  </>
                )}
              </div>
              <div className="border-t px-4 py-2">
                <Link to="/tradies/compliance" onClick={() => setBellOpen(false)} className="text-xs font-medium text-primary hover:underline">
                  View Compliance Dashboard →
                </Link>
              </div>
            </div>
          )}
        </div>

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
                onClick={() => { setMenuOpen(false); navigate('/profile'); }}
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

## web/src/components/layout/BottomNav.tsx

```tsx
import { useState } from 'react';
import { NavLink, useNavigate } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  MoreHorizontal,
  User,
  Settings,
  Shield,
  LogOut,
} from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { hasPermission, isAdmin } from '@/lib/permissions';
import { cn } from '@/lib/utils';

const allTabs = [
  { to: '/', icon: LayoutDashboard, label: 'Home' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports', permission: 'viewReports' as const },
];

export default function BottomNav() {
  const [moreOpen, setMoreOpen] = useState(false);
  const { user, logout } = useAuth();
  const navigate = useNavigate();
  const role = user?.role;

  const tabs = allTabs.filter((tab) => !tab.permission || hasPermission(role, tab.permission));

  const handleLogout = () => {
    setMoreOpen(false);
    logout();
    navigate('/login');
  };

  return (
    <nav className="fixed bottom-0 left-0 right-0 z-40 border-t border-gray-200 bg-white md:hidden">
      {moreOpen && (
        <>
          <div className="fixed inset-0 z-30" onClick={() => setMoreOpen(false)} />
          <div className="absolute bottom-full right-2 z-40 mb-2 w-48 rounded-lg border border-gray-200 bg-white py-1 shadow-lg">
            <button
              onClick={() => { setMoreOpen(false); navigate('/profile'); }}
              className="flex w-full items-center gap-2 px-4 py-2.5 text-sm text-gray-700 hover:bg-gray-50"
            >
              <User className="h-4 w-4" /> Profile
            </button>
            <button
              onClick={() => { setMoreOpen(false); navigate('/settings'); }}
              className="flex w-full items-center gap-2 px-4 py-2.5 text-sm text-gray-700 hover:bg-gray-50"
            >
              <Settings className="h-4 w-4" /> Settings
            </button>
            {isAdmin(role) && (
              <button
                onClick={() => { setMoreOpen(false); navigate('/admin/users'); }}
                className="flex w-full items-center gap-2 px-4 py-2.5 text-sm text-gray-700 hover:bg-gray-50"
              >
                <Shield className="h-4 w-4" /> Admin
              </button>
            )}
            <button
              onClick={handleLogout}
              className="flex w-full items-center gap-2 px-4 py-2.5 text-sm text-danger hover:bg-gray-50"
            >
              <LogOut className="h-4 w-4" /> Sign out
            </button>
          </div>
        </>
      )}

      <div className="flex items-center justify-around">
        {tabs.map(({ to, icon: Icon, label }) => (
          <NavLink
            key={to}
            to={to}
            end={to === '/'}
            className={({ isActive }) =>
              cn(
                'flex flex-1 flex-col items-center gap-0.5 py-2 text-[10px] font-medium transition-colors',
                isActive ? 'text-primary' : 'text-gray-500',
              )
            }
          >
            <Icon className="h-5 w-5" />
            {label}
          </NavLink>
        ))}
        <button
          onClick={() => setMoreOpen(!moreOpen)}
          className={cn(
            'flex flex-1 flex-col items-center gap-0.5 py-2 text-[10px] font-medium transition-colors',
            moreOpen ? 'text-primary' : 'text-gray-500',
          )}
        >
          <MoreHorizontal className="h-5 w-5" />
          More
        </button>
      </div>
    </nav>
  );
}

```

## web/src/components/layout/MobileDrawer.tsx

```tsx
import { NavLink } from 'react-router-dom';
import {
  LayoutDashboard,
  FolderKanban,
  Users,
  BarChart3,
  Settings,
  HardHat,
  X,
} from 'lucide-react';
import { cn } from '@/lib/utils';

const links = [
  { to: '/', icon: LayoutDashboard, label: 'Dashboard' },
  { to: '/projects', icon: FolderKanban, label: 'Projects' },
  { to: '/tradies', icon: Users, label: 'Tradies' },
  { to: '/reports', icon: BarChart3, label: 'Reports' },
  { to: '/settings', icon: Settings, label: 'Settings' },
];

interface MobileDrawerProps {
  open: boolean;
  onClose: () => void;
}

export default function MobileDrawer({ open, onClose }: MobileDrawerProps) {
  if (!open) return null;

  return (
    <div className="fixed inset-0 z-50 md:hidden">
      <div className="fixed inset-0 bg-black/50" onClick={onClose} />
      <div className="fixed inset-y-0 left-0 w-64 bg-white shadow-xl">
        <div className="flex h-16 items-center justify-between border-b border-gray-200 px-6">
          <div className="flex items-center gap-2">
            <HardHat className="h-7 w-7 text-primary" />
            <span className="text-lg font-bold text-primary">BuildMate</span>
          </div>
          <button onClick={onClose} className="rounded-lg p-2 hover:bg-gray-100">
            <X className="h-5 w-5 text-gray-500" />
          </button>
        </div>
        <nav className="flex-1 space-y-1 px-3 py-4">
          {links.map(({ to, icon: Icon, label }) => (
            <NavLink
              key={to}
              to={to}
              end={to === '/'}
              onClick={onClose}
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
      </div>
    </div>
  );
}

```

## web/src/components/GanttChart.tsx

```tsx
import { useCallback, useEffect, useMemo, useRef, useState } from 'react';
import { ChevronRight, ChevronDown, GripVertical } from 'lucide-react';
import { cn } from '@/lib/utils';
import type { TaskDependencyRecord } from '@/types';

export interface GanttTask {
  id: string;
  name: string;
  startDate?: string | null;
  endDate?: string | null;
  percentComplete?: number;
  status: string;
  priority?: string;
  stage?: string | null;
  isMilestone?: boolean;
  parentTaskId?: string | null;
  colour?: string | null;
  baselineStartDate?: string | null;
  baselineEndDate?: string | null;
  childTasks?: { id: string; name: string; status: string; percentComplete: number }[];
}

interface GanttChartProps {
  tasks: GanttTask[];
  dependencies?: TaskDependencyRecord[];
  criticalPathIds?: string[];
  dayWidth?: number;
  onTaskClick?: (task: GanttTask) => void;
  onTaskUpdate?: (taskId: string, updates: { startDate?: string; endDate?: string }) => void;
  onTaskReorder?: (draggedTaskId: string, targetTaskId: string) => void;
  canReorder?: boolean;
}

interface DragState {
  taskId: string;
  startX: number;
  origLeft: number;
  origWidth: number;
  type: 'move' | 'resize';
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

function addDaysToDate(base: Date, offsetDays: number): string {
  const d = new Date(base);
  d.setDate(d.getDate() + offsetDays);
  return d.toISOString();
}

export default function GanttChart({
  tasks,
  dependencies = [],
  criticalPathIds = [],
  dayWidth: dayWidthProp = 28,
  onTaskClick,
  onTaskUpdate,
  onTaskReorder,
  canReorder = false,
}: GanttChartProps) {
  const scrollRef = useRef<HTMLDivElement>(null);
  const criticalSet = useMemo(() => new Set(criticalPathIds), [criticalPathIds]);
  const containerRef = useRef<HTMLDivElement>(null);
  const [hoveredTask, setHoveredTask] = useState<string | null>(null);
  const [dragState, setDragState] = useState<DragState | null>(null);
  const [dragOffset, setDragOffset] = useState(0);
  const dragTaskIdRef = useRef<string | null>(null);
  const [collapsedParents, setCollapsedParents] = useState<Set<string>>(new Set());
  const [reorderDragId, setReorderDragId] = useState<string | null>(null);
  const [reorderOverId, setReorderOverId] = useState<string | null>(null);
  const [panState, setPanState] = useState<{ startX: number; startScrollLeft: number } | null>(null);
  const dayWidth = dayWidthProp;

  // Build parent-child hierarchy
  const { displayTasks, parentIds } = useMemo(() => {
    const valid = tasks.filter((t) => t.startDate);
    const parents = new Set<string>();
    const childMap = new Map<string, GanttTask[]>();

    for (const t of valid) {
      if (t.childTasks && t.childTasks.length > 0) parents.add(t.id);
      const pid = t.parentTaskId;
      if (pid) {
        if (!childMap.has(pid)) childMap.set(pid, []);
        childMap.get(pid)!.push(t);
      }
    }

    // Also mark parents from parentTaskId references
    for (const t of valid) {
      if (t.parentTaskId) parents.add(t.parentTaskId);
    }

    // Build ordered list: parents followed by children
    const topLevel = valid.filter((t) => !t.parentTaskId);
    const ordered: GanttTask[] = [];
    for (const t of topLevel) {
      ordered.push(t);
      if (parents.has(t.id) && !collapsedParents.has(t.id)) {
        const children = childMap.get(t.id) || [];
        ordered.push(...children);
      }
    }

    return { displayTasks: ordered, parentIds: parents };
  }, [tasks, collapsedParents]);

  const { weeks, timelineStart, totalDays, validTasks } = useMemo(() => {
    if (displayTasks.length === 0) return { weeks: [], timelineStart: new Date(), totalDays: 0, validTasks: [] };

    const starts = displayTasks.map((t) => new Date(t.startDate!).getTime());
    const ends = displayTasks.map((t) => new Date(t.endDate || t.startDate!).getTime());
    const minDate = getMonday(new Date(Math.min(...starts)));
    const maxDate = new Date(Math.max(...ends));

    minDate.setDate(minDate.getDate() - 7);
    maxDate.setDate(maxDate.getDate() + 14);

    const total = daysBetween(minDate, maxDate);

    const wks: { date: Date; label: string }[] = [];
    const curr = new Date(minDate);
    while (curr <= maxDate) {
      wks.push({ date: new Date(curr), label: formatShortDate(curr) });
      curr.setDate(curr.getDate() + 7);
    }

    return { weeks: wks, timelineStart: minDate, totalDays: total, validTasks: displayTasks };
  }, [displayTasks]);

  const rowHeight = 36;
  const labelWidth = 200;

  // Compute bar positions for dependency arrows and drag
  const barPositions = useMemo(() => {
    const positions = new Map<string, { left: number; width: number; top: number }>();
    validTasks.forEach((task, index) => {
      const start = new Date(task.startDate!);
      const end = new Date(task.endDate || task.startDate!);
      const startOffset = daysBetween(timelineStart, start) * dayWidth;
      const duration = Math.max(daysBetween(start, end), 1);
      const barWidth = duration * dayWidth;
      positions.set(task.id, { left: startOffset, width: barWidth, top: index * rowHeight + rowHeight / 2 });
    });
    return positions;
  }, [validTasks, timelineStart, dayWidth]);
  const timelineWidth = totalDays * dayWidth;
  const todayOffset = daysBetween(timelineStart, new Date()) * dayWidth;

  // Drag handlers — click detection is handled in mouseup, not onClick
  const handleBarMouseDown = useCallback(
    (e: React.MouseEvent, taskId: string, type: 'move' | 'resize') => {
      e.stopPropagation();
      e.preventDefault();
      setPanState(null);
      dragTaskIdRef.current = taskId;
      const pos = barPositions.get(taskId);
      if (!pos) return;
      setDragState({
        taskId,
        startX: e.clientX,
        origLeft: pos.left,
        origWidth: pos.width,
        type,
      });
      setDragOffset(0);
    },
    [barPositions],
  );

  useEffect(() => {
    if (!dragState) return;
    let moved = false;

    const handleMouseMove = (e: MouseEvent) => {
      const dx = e.clientX - dragState.startX;
      setDragOffset(dx);
      if (Math.abs(dx) > 3) moved = true;
    };

    const handleMouseUp = (e: MouseEvent) => {
      const dx = e.clientX - dragState.startX;
      const daysDelta = Math.round(dx / dayWidth);
      const taskId = dragState.taskId;

      if (moved && daysDelta !== 0 && onTaskUpdate) {
        const task = validTasks.find((t) => t.id === taskId);
        if (task) {
          if (dragState.type === 'move') {
            const newStart = addDaysToDate(new Date(task.startDate!), daysDelta);
            const newEnd = task.endDate ? addDaysToDate(new Date(task.endDate), daysDelta) : undefined;
            onTaskUpdate(task.id, { startDate: newStart, endDate: newEnd });
          } else {
            const end = new Date(task.endDate || task.startDate!);
            const newEnd = addDaysToDate(end, daysDelta);
            onTaskUpdate(task.id, { endDate: newEnd });
          }
        }
      }

      // If mouse didn't move, treat as a click → open edit modal
      if (!moved && onTaskClick) {
        const task = validTasks.find((t) => t.id === taskId);
        if (task) onTaskClick(task);
      }

      setDragState(null);
      setDragOffset(0);
      setPanState(null);
      dragTaskIdRef.current = null;
    };

    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('mouseup', handleMouseUp);
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
      window.removeEventListener('mouseup', handleMouseUp);
    };
  }, [dragState, dayWidth, onTaskUpdate, onTaskClick, validTasks]);

  const toggleCollapse = (parentId: string) => {
    setCollapsedParents((prev) => {
      const next = new Set(prev);
      if (next.has(parentId)) next.delete(parentId);
      else next.add(parentId);
      return next;
    });
  };

  // Horizontal panning on the timeline
  const handleTimelineMouseDown = useCallback((e: React.MouseEvent) => {
    // Only pan from empty space — not from task bars, resize handles, or buttons
    const target = e.target as HTMLElement;
    if (target.closest('[data-task-bar], [data-resize-handle], [data-row-handle], button')) return;
    if (e.button !== 0) return;
    if (!scrollRef.current) return;
    // Don't pan if a bar drag is starting
    if (dragState) return;
    e.preventDefault();
    setPanState({ startX: e.clientX, startScrollLeft: scrollRef.current.scrollLeft });
  }, [dragState]);

  useEffect(() => {
    if (!panState) return;
    const handleMove = (e: MouseEvent) => {
      if (!scrollRef.current) return;
      scrollRef.current.scrollLeft = panState.startScrollLeft - (e.clientX - panState.startX);
    };
    const handleUp = () => setPanState(null);
    window.addEventListener('mousemove', handleMove);
    window.addEventListener('mouseup', handleUp);
    return () => {
      window.removeEventListener('mousemove', handleMove);
      window.removeEventListener('mouseup', handleUp);
    };
  }, [panState]);

  // Build dependency arrow paths
  const dependencyPaths = useMemo(() => {
    const paths: { d: string; key: string }[] = [];
    for (const dep of dependencies) {
      const predPos = barPositions.get(dep.predecessorId);
      const succPos = barPositions.get(dep.successorId);
      if (!predPos || !succPos) continue;

      const depType = dep.type || 'FINISH_TO_START';
      let startX: number, startY: number, endX: number, endY: number;

      if (depType === 'FINISH_TO_START') {
        startX = predPos.left + predPos.width;
        startY = predPos.top;
        endX = succPos.left;
        endY = succPos.top;
      } else if (depType === 'START_TO_START') {
        startX = predPos.left;
        startY = predPos.top;
        endX = succPos.left;
        endY = succPos.top;
      } else if (depType === 'FINISH_TO_FINISH') {
        startX = predPos.left + predPos.width;
        startY = predPos.top;
        endX = succPos.left + succPos.width;
        endY = succPos.top;
      } else {
        startX = predPos.left;
        startY = predPos.top;
        endX = succPos.left + succPos.width;
        endY = succPos.top;
      }

      const midX = startX + 12;
      const d = `M ${startX} ${startY} L ${midX} ${startY} L ${midX} ${endY} L ${endX} ${endY}`;
      paths.push({ d, key: dep.id });
    }
    return paths;
  }, [dependencies, barPositions]);

  if (validTasks.length === 0) {
    return <p className="py-8 text-center text-sm text-gray-500">Add tasks with dates to see the Gantt chart.</p>;
  }

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
          <div className="flex items-center border-b bg-gray-50 px-3" style={{ height: dayWidth >= 28 ? 44 : 40 }}>
            <span className="text-xs font-semibold text-gray-600">Task</span>
          </div>
          {validTasks.map((task) => {
            const isParent = parentIds.has(task.id);
            const isChild = !!task.parentTaskId;
            const isCollapsed = collapsedParents.has(task.id);

            return (
              <div
                key={task.id}
                className={cn(
                  'flex items-center border-b border-gray-100 px-1 transition-colors cursor-pointer',
                  hoveredTask === task.id && 'bg-blue-50',
                  reorderOverId === task.id && reorderDragId !== task.id && 'border-t-2 border-t-primary',
                )}
                style={{ height: rowHeight }}
                onMouseEnter={() => setHoveredTask(task.id)}
                onMouseLeave={() => setHoveredTask(null)}
                onClick={() => onTaskClick?.(task)}
                onDragOver={(e) => { if (reorderDragId) { e.preventDefault(); setReorderOverId(task.id); } }}
                onDragLeave={() => setReorderOverId(null)}
                onDrop={(e) => {
                  e.preventDefault();
                  if (reorderDragId && reorderDragId !== task.id && onTaskReorder) {
                    onTaskReorder(reorderDragId, task.id);
                  }
                  setReorderDragId(null);
                  setReorderOverId(null);
                }}
              >
                {canReorder && (
                  <div
                    data-row-handle
                    draggable
                    onDragStart={(e) => {
                      e.stopPropagation();
                      setReorderDragId(task.id);
                      e.dataTransfer.effectAllowed = 'move';
                    }}
                    onDragEnd={() => { setReorderDragId(null); setReorderOverId(null); }}
                    className="mr-0.5 flex-shrink-0 cursor-grab text-gray-300 hover:text-gray-500 active:cursor-grabbing"
                    onClick={(e) => e.stopPropagation()}
                  >
                    <GripVertical className="h-3.5 w-3.5" />
                  </div>
                )}
                {isParent && (
                  <button
                    className="mr-1 flex-shrink-0 text-gray-400 hover:text-gray-600"
                    onClick={(e) => { e.stopPropagation(); toggleCollapse(task.id); }}
                  >
                    {isCollapsed ? <ChevronRight className="h-3.5 w-3.5" /> : <ChevronDown className="h-3.5 w-3.5" />}
                  </button>
                )}
                <span
                  className={cn('truncate text-xs font-medium text-gray-800', isChild && 'pl-4')}
                  title={task.name}
                >
                  {task.isMilestone && <span className="mr-1 text-purple-600">◆</span>}
                  {task.name}
                </span>
              </div>
            );
          })}
        </div>

        {/* Timeline (scrollable — click-drag empty space to pan) */}
        <div
          ref={scrollRef}
          className={cn('flex-1 overflow-x-auto', panState ? 'cursor-grabbing' : 'cursor-grab')}
          onMouseDown={handleTimelineMouseDown}
        >
          <div style={{ width: timelineWidth, minWidth: '100%' }}>
            {/* Week headers */}
            <div className="flex border-b bg-gray-50" style={{ height: dayWidth >= 28 ? 44 : 40 }}>
              {weeks.map((w, i) => (
                <div
                  key={i}
                  className="shrink-0 border-r border-gray-200 px-1 flex flex-col justify-center"
                  style={{ width: 7 * dayWidth }}
                >
                  <span className="text-[10px] font-medium text-gray-500">{w.label}</span>
                  {dayWidth >= 28 && (
                    <div className="flex">
                      {['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'].map((day) => (
                        <span
                          key={day}
                          className="text-[9px] text-gray-400 truncate"
                          style={{ width: dayWidth, flexShrink: 0 }}
                        >
                          {dayWidth >= 40 ? day : day.charAt(0)}
                        </span>
                      ))}
                    </div>
                  )}
                </div>
              ))}
            </div>

            {/* Task rows */}
            <div ref={containerRef} className="relative">
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

              {/* Dependency arrows SVG overlay */}
              {dependencyPaths.length > 0 && (
                <svg
                  className="absolute inset-0 z-[5] pointer-events-none"
                  style={{ width: timelineWidth, height: validTasks.length * rowHeight }}
                >
                  <defs>
                    <marker
                      id="gantt-arrowhead"
                      markerWidth="8"
                      markerHeight="6"
                      refX="8"
                      refY="3"
                      orient="auto"
                    >
                      <polygon points="0 0, 8 3, 0 6" fill="#9ca3af" />
                    </marker>
                  </defs>
                  {dependencyPaths.map((p) => (
                    <path
                      key={p.key}
                      d={p.d}
                      fill="none"
                      stroke="#9ca3af"
                      strokeWidth="1.5"
                      markerEnd="url(#gantt-arrowhead)"
                    />
                  ))}
                </svg>
              )}

              {validTasks.map((task) => {
                const start = new Date(task.startDate!);
                const end = new Date(task.endDate || task.startDate!);
                let startOffset = daysBetween(timelineStart, start) * dayWidth;
                const duration = Math.max(daysBetween(start, end), 1);
                let barWidth = duration * dayWidth;
                const progress = task.percentComplete ?? 0;
                const barColor = task.colour || null;
                const color = STATUS_COLORS[task.status] || STATUS_COLORS.NOT_STARTED;
                const bgColor = STATUS_BG[task.status] || STATUS_BG.NOT_STARTED;

                // Apply drag offset
                if (dragState && dragState.taskId === task.id) {
                  if (dragState.type === 'move') {
                    startOffset = dragState.origLeft + dragOffset;
                  } else {
                    barWidth = Math.max(dragState.origWidth + dragOffset, dayWidth);
                  }
                }

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
                      <div
                        className="absolute z-10 flex items-center justify-center cursor-pointer"
                        style={{ left: startOffset - 8 }}
                        onClick={() => onTaskClick?.(task)}
                      >
                        <div className={cn('h-4 w-4 rotate-45 bg-purple-600 rounded-sm shadow-sm', criticalSet.has(task.id) && 'ring-2 ring-red-500')} />
                      </div>
                    ) : (
                      <div
                        data-task-bar
                        className={cn(
                          'absolute z-10 rounded-md shadow-sm group',
                          !barColor && bgColor,
                          criticalSet.has(task.id) && 'ring-2 ring-red-500',
                        )}
                        style={{
                          left: startOffset,
                          width: barWidth,
                          height: 20,
                          cursor: dragState ? 'grabbing' : 'pointer',
                          ...(barColor ? { backgroundColor: barColor, opacity: 0.85 } : {}),
                        }}
                        onMouseDown={(e) => handleBarMouseDown(e, task.id, 'move')}
                      >
                        {/* Progress fill */}
                        {!barColor && (
                          <div
                            className={cn('h-full rounded-md transition-all', color)}
                            style={{ width: `${progress}%`, opacity: 0.85 }}
                          />
                        )}
                        {barColor && (
                          <div
                            className="h-full rounded-md"
                            style={{ width: `${progress}%`, backgroundColor: 'rgba(0,0,0,0.2)' }}
                          />
                        )}
                        {/* Label on bar */}
                        {barWidth > 60 && (
                          <span className="absolute inset-0 flex items-center px-2 text-[10px] font-medium text-gray-800 truncate">
                            {progress > 0 && `${progress}%`}
                          </span>
                        )}
                        {/* Resize handle */}
                        <div
                          data-resize-handle
                          className="absolute right-0 top-0 h-full w-2 cursor-col-resize opacity-0 group-hover:opacity-100 bg-black/10 rounded-r-md"
                          onMouseDown={(e) => handleBarMouseDown(e, task.id, 'resize')}
                        />
                      </div>
                    )}

                    {/* Baseline bar */}
                    {!task.isMilestone && task.baselineStartDate && task.baselineEndDate && (() => {
                      const bStart = new Date(task.baselineStartDate!);
                      const bEnd = new Date(task.baselineEndDate!);
                      const bOffset = daysBetween(timelineStart, bStart) * dayWidth;
                      const bDur = Math.max(daysBetween(bStart, bEnd), 1);
                      const bWidth = bDur * dayWidth;
                      return (
                        <div
                          className="absolute z-[5] rounded-sm bg-gray-300 opacity-50"
                          style={{ left: bOffset, width: bWidth, height: 6, top: 26 }}
                        />
                      );
                    })()}

                    {/* Tooltip on hover */}
                    {hoveredTask === task.id && !dragState && (
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

## web/src/components/ui/__tests__/Badge.test.tsx

```tsx
import { render, screen } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import Badge from '../Badge';

describe('Badge', () => {
  it('renders status text with underscores replaced by spaces', () => {
    render(<Badge status="in_progress" />);
    expect(screen.getByText('in progress')).toBeInTheDocument();
  });

  it('renders simple status text', () => {
    render(<Badge status="active" />);
    expect(screen.getByText('active')).toBeInTheDocument();
  });

  it('applies success styling for active status', () => {
    render(<Badge status="active" />);
    const badge = screen.getByText('active');
    expect(badge.className).toContain('bg-success-50');
    expect(badge.className).toContain('text-success-500');
  });

  it('applies warning styling for pending status', () => {
    render(<Badge status="pending" />);
    const badge = screen.getByText('pending');
    expect(badge.className).toContain('bg-warning-50');
    expect(badge.className).toContain('text-warning-500');
  });

  it('applies danger styling for overdue status', () => {
    render(<Badge status="overdue" />);
    const badge = screen.getByText('overdue');
    expect(badge.className).toContain('bg-danger-50');
    expect(badge.className).toContain('text-danger-500');
  });

  it('applies blue styling for scheduled status', () => {
    render(<Badge status="scheduled" />);
    const badge = screen.getByText('scheduled');
    expect(badge.className).toContain('bg-blue-50');
    expect(badge.className).toContain('text-blue-600');
  });

  it('applies default gray styling for unknown status', () => {
    render(<Badge status="unknown_status" />);
    const badge = screen.getByText('unknown status');
    expect(badge.className).toContain('bg-gray-100');
    expect(badge.className).toContain('text-gray-600');
  });

  it('has capitalize and rounded-full classes', () => {
    render(<Badge status="paid" />);
    const badge = screen.getByText('paid');
    expect(badge.className).toContain('capitalize');
    expect(badge.className).toContain('rounded-full');
  });

  it('merges custom className', () => {
    render(<Badge status="active" className="ml-2" />);
    const badge = screen.getByText('active');
    expect(badge.className).toContain('ml-2');
  });
});

```

## web/src/components/ui/__tests__/Button.test.tsx

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import Button from '../Button';

describe('Button', () => {
  it('renders children text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument();
  });

  it('handles click events', () => {
    const onClick = vi.fn();
    render(<Button onClick={onClick}>Click</Button>);
    fireEvent.click(screen.getByRole('button'));
    expect(onClick).toHaveBeenCalledTimes(1);
  });

  it('does not fire click when disabled', () => {
    const onClick = vi.fn();
    render(<Button disabled onClick={onClick}>Click</Button>);
    const button = screen.getByRole('button');
    expect(button).toBeDisabled();
    fireEvent.click(button);
    expect(onClick).not.toHaveBeenCalled();
  });

  it('applies primary variant classes by default', () => {
    render(<Button>Primary</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('bg-primary');
  });

  it('applies secondary variant classes', () => {
    render(<Button variant="secondary">Secondary</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('bg-gray-100');
  });

  it('applies danger variant classes', () => {
    render(<Button variant="danger">Delete</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('bg-danger');
  });

  it('applies ghost variant classes', () => {
    render(<Button variant="ghost">Ghost</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('bg-transparent');
  });

  it('applies size sm classes', () => {
    render(<Button size="sm">Small</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('px-3');
    expect(button.className).toContain('py-1.5');
  });

  it('applies size lg classes', () => {
    render(<Button size="lg">Large</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('px-6');
    expect(button.className).toContain('py-3');
  });

  it('applies disabled opacity class', () => {
    render(<Button disabled>Disabled</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('disabled:opacity-50');
  });

  it('merges custom className', () => {
    render(<Button className="mt-4">Custom</Button>);
    const button = screen.getByRole('button');
    expect(button.className).toContain('mt-4');
  });

  it('forwards additional HTML attributes', () => {
    render(<Button type="submit" data-testid="submit-btn">Submit</Button>);
    const button = screen.getByTestId('submit-btn');
    expect(button).toHaveAttribute('type', 'submit');
  });
});

```

## web/src/components/ui/__tests__/Card.test.tsx

```tsx
import { render, screen } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import Card from '../Card';

describe('Card', () => {
  it('renders children content', () => {
    render(<Card><p>Card body</p></Card>);
    expect(screen.getByText('Card body')).toBeInTheDocument();
  });

  it('renders title when provided', () => {
    render(<Card title="Budget Summary"><p>Content</p></Card>);
    expect(screen.getByText('Budget Summary')).toBeInTheDocument();
  });

  it('does not render header when no title or action', () => {
    const { container } = render(<Card><p>Content</p></Card>);
    expect(container.querySelector('.border-b')).not.toBeInTheDocument();
  });

  it('renders header when title is provided', () => {
    const { container } = render(<Card title="Title"><p>Content</p></Card>);
    expect(container.querySelector('.border-b')).toBeInTheDocument();
  });

  it('renders action element', () => {
    render(
      <Card title="Projects" action={<button>Add</button>}>
        <p>List</p>
      </Card>,
    );
    expect(screen.getByRole('button', { name: 'Add' })).toBeInTheDocument();
  });

  it('renders action without title', () => {
    render(
      <Card action={<button>Action</button>}>
        <p>Body</p>
      </Card>,
    );
    expect(screen.getByRole('button', { name: 'Action' })).toBeInTheDocument();
  });

  it('applies base card classes', () => {
    const { container } = render(<Card><p>Content</p></Card>);
    const card = container.firstChild as HTMLElement;
    expect(card.className).toContain('rounded-xl');
    expect(card.className).toContain('border');
    expect(card.className).toContain('bg-white');
    expect(card.className).toContain('shadow-sm');
  });

  it('merges custom className', () => {
    const { container } = render(<Card className="mt-6"><p>Content</p></Card>);
    const card = container.firstChild as HTMLElement;
    expect(card.className).toContain('mt-6');
  });
});

```

## web/src/components/ui/__tests__/EmptyState.test.tsx

```tsx
import { render, screen } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import EmptyState from '../EmptyState';

function TestIcon() {
  return <svg data-testid="empty-icon" />;
}

describe('EmptyState', () => {
  it('renders the icon', () => {
    render(<EmptyState icon={<TestIcon />} title="No projects" />);
    expect(screen.getByTestId('empty-icon')).toBeInTheDocument();
  });

  it('renders the title', () => {
    render(<EmptyState icon={<TestIcon />} title="No items found" />);
    expect(screen.getByText('No items found')).toBeInTheDocument();
  });

  it('renders the description when provided', () => {
    render(
      <EmptyState
        icon={<TestIcon />}
        title="No projects"
        description="Create your first project to get started."
      />,
    );
    expect(screen.getByText('Create your first project to get started.')).toBeInTheDocument();
  });

  it('does not render description when not provided', () => {
    render(<EmptyState icon={<TestIcon />} title="Empty" />);
    const description = document.querySelector('.text-gray-500');
    expect(description).not.toBeInTheDocument();
  });

  it('renders action when provided', () => {
    render(
      <EmptyState
        icon={<TestIcon />}
        title="No tradies"
        action={<button>Add Tradie</button>}
      />,
    );
    expect(screen.getByRole('button', { name: 'Add Tradie' })).toBeInTheDocument();
  });

  it('does not render action wrapper when no action', () => {
    const { container } = render(<EmptyState icon={<TestIcon />} title="Empty" />);
    const actionWrapper = container.querySelector('.mt-4');
    expect(actionWrapper).not.toBeInTheDocument();
  });

  it('applies centered layout classes', () => {
    const { container } = render(<EmptyState icon={<TestIcon />} title="Empty" />);
    const wrapper = container.firstChild as HTMLElement;
    expect(wrapper.className).toContain('flex');
    expect(wrapper.className).toContain('flex-col');
    expect(wrapper.className).toContain('items-center');
    expect(wrapper.className).toContain('text-center');
  });

  it('merges custom className', () => {
    const { container } = render(
      <EmptyState icon={<TestIcon />} title="Empty" className="py-8" />,
    );
    const wrapper = container.firstChild as HTMLElement;
    expect(wrapper.className).toContain('py-8');
  });
});

```

## web/src/components/ui/__tests__/Input.test.tsx

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import Input from '../Input';

describe('Input', () => {
  it('renders an input element', () => {
    render(<Input id="test" />);
    expect(screen.getByRole('textbox')).toBeInTheDocument();
  });

  it('renders a label when provided', () => {
    render(<Input id="email" label="Email" />);
    expect(screen.getByLabelText('Email')).toBeInTheDocument();
  });

  it('does not render a label when not provided', () => {
    render(<Input id="email" />);
    expect(screen.queryByText('Email')).not.toBeInTheDocument();
  });

  it('handles value changes', () => {
    const onChange = vi.fn();
    render(<Input id="name" onChange={onChange} />);
    fireEvent.change(screen.getByRole('textbox'), { target: { value: 'John' } });
    expect(onChange).toHaveBeenCalledTimes(1);
  });

  it('displays error message', () => {
    render(<Input id="email" error="Email is required" />);
    expect(screen.getByRole('alert')).toHaveTextContent('Email is required');
  });

  it('sets aria-invalid when error is present', () => {
    render(<Input id="email" error="Required" />);
    expect(screen.getByRole('textbox')).toHaveAttribute('aria-invalid', 'true');
  });

  it('does not set aria-invalid when no error', () => {
    render(<Input id="email" />);
    expect(screen.getByRole('textbox')).not.toHaveAttribute('aria-invalid');
  });

  it('applies error border styling when error is present', () => {
    render(<Input id="email" error="Required" />);
    expect(screen.getByRole('textbox').className).toContain('border-danger');
  });

  it('applies normal border styling when no error', () => {
    render(<Input id="email" />);
    expect(screen.getByRole('textbox').className).toContain('border-gray-300');
  });

  it('sets aria-describedby linking to error message', () => {
    render(<Input id="email" error="Invalid email" />);
    const input = screen.getByRole('textbox');
    expect(input).toHaveAttribute('aria-describedby', 'email-error');
    expect(screen.getByRole('alert')).toHaveAttribute('id', 'email-error');
  });

  it('forwards placeholder prop', () => {
    render(<Input id="search" placeholder="Search..." />);
    expect(screen.getByPlaceholderText('Search...')).toBeInTheDocument();
  });
});

```

## web/src/components/ui/__tests__/LoadingSpinner.test.tsx

```tsx
import { render } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import LoadingSpinner from '../LoadingSpinner';

describe('LoadingSpinner', () => {
  it('renders with default md size', () => {
    const { container } = render(<LoadingSpinner />);
    const spinner = container.querySelector('.animate-spin') as HTMLElement;
    expect(spinner).toBeInTheDocument();
    expect(spinner.className).toContain('h-8');
    expect(spinner.className).toContain('w-8');
  });

  it('renders with sm size', () => {
    const { container } = render(<LoadingSpinner size="sm" />);
    const spinner = container.querySelector('.animate-spin') as HTMLElement;
    expect(spinner.className).toContain('h-4');
    expect(spinner.className).toContain('w-4');
  });

  it('renders with lg size', () => {
    const { container } = render(<LoadingSpinner size="lg" />);
    const spinner = container.querySelector('.animate-spin') as HTMLElement;
    expect(spinner.className).toContain('h-12');
    expect(spinner.className).toContain('w-12');
  });

  it('has spin animation class', () => {
    const { container } = render(<LoadingSpinner />);
    const spinner = container.querySelector('.animate-spin');
    expect(spinner).toBeInTheDocument();
  });

  it('has centering wrapper classes', () => {
    const { container } = render(<LoadingSpinner />);
    const wrapper = container.firstChild as HTMLElement;
    expect(wrapper.className).toContain('flex');
    expect(wrapper.className).toContain('items-center');
    expect(wrapper.className).toContain('justify-center');
  });

  it('merges custom className on wrapper', () => {
    const { container } = render(<LoadingSpinner className="mt-10" />);
    const wrapper = container.firstChild as HTMLElement;
    expect(wrapper.className).toContain('mt-10');
  });

  it('has border styling for spinner appearance', () => {
    const { container } = render(<LoadingSpinner />);
    const spinner = container.querySelector('.animate-spin') as HTMLElement;
    expect(spinner.className).toContain('border-2');
    expect(spinner.className).toContain('rounded-full');
    expect(spinner.className).toContain('border-t-primary');
  });
});

```

## web/src/components/ui/__tests__/Modal.test.tsx

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import Modal from '../Modal';

describe('Modal', () => {
  it('renders nothing when closed', () => {
    render(
      <Modal open={false} onClose={vi.fn()}>
        <p>Content</p>
      </Modal>,
    );
    expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
  });

  it('renders children when open', () => {
    render(
      <Modal open={true} onClose={vi.fn()} title="Test Modal">
        <p>Modal content</p>
      </Modal>,
    );
    expect(screen.getByRole('dialog')).toBeInTheDocument();
    expect(screen.getByText('Modal content')).toBeInTheDocument();
  });

  it('renders the title', () => {
    render(
      <Modal open={true} onClose={vi.fn()} title="My Title">
        <p>Body</p>
      </Modal>,
    );
    expect(screen.getByText('My Title')).toBeInTheDocument();
  });

  it('calls onClose when close button is clicked', () => {
    const onClose = vi.fn();
    render(
      <Modal open={true} onClose={onClose} title="Closeable">
        <p>Body</p>
      </Modal>,
    );
    fireEvent.click(screen.getByLabelText('Close dialog'));
    expect(onClose).toHaveBeenCalledTimes(1);
  });

  it('calls onClose when backdrop is clicked', () => {
    const onClose = vi.fn();
    render(
      <Modal open={true} onClose={onClose} title="Backdrop">
        <p>Body</p>
      </Modal>,
    );
    const backdrop = document.querySelector('[aria-hidden="true"]');
    if (backdrop) fireEvent.click(backdrop);
    expect(onClose).toHaveBeenCalledTimes(1);
  });

  it('calls onClose when Escape key is pressed', () => {
    const onClose = vi.fn();
    render(
      <Modal open={true} onClose={onClose} title="Escape">
        <p>Body</p>
      </Modal>,
    );
    fireEvent.keyDown(document, { key: 'Escape' });
    expect(onClose).toHaveBeenCalledTimes(1);
  });

  it('sets aria-modal and aria-label', () => {
    render(
      <Modal open={true} onClose={vi.fn()} title="Accessible">
        <p>Body</p>
      </Modal>,
    );
    const dialog = screen.getByRole('dialog');
    expect(dialog).toHaveAttribute('aria-modal', 'true');
    expect(dialog).toHaveAttribute('aria-label', 'Accessible');
  });

  it('renders without title', () => {
    render(
      <Modal open={true} onClose={vi.fn()}>
        <p>No title modal</p>
      </Modal>,
    );
    expect(screen.getByText('No title modal')).toBeInTheDocument();
    expect(screen.queryByLabelText('Close dialog')).not.toBeInTheDocument();
  });
});

```

## web/src/test/setup.ts

```typescript
import '@testing-library/jest-dom';

```
