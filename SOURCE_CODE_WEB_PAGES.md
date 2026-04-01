# BuildMate — Web Frontend Pages Source Code

---


## web/src/pages/Login.tsx

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
            <Link to="/register" className="font-medium text-primary hover:underline">
              Register
            </Link>
          </p>
        </div>
      </div>
    </div>
  );
}

```

## web/src/pages/Register.tsx

```tsx
import { useState, type FormEvent } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { HardHat } from 'lucide-react';
import { useAuth } from '@/hooks/useAuth';
import { register as registerApi } from '@/api/auth';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';

export default function RegisterPage() {
  const { login } = useAuth();
  const navigate = useNavigate();
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [confirmPassword, setConfirmPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    setError('');

    if (password !== confirmPassword) {
      setError('Passwords do not match');
      return;
    }

    if (password.length < 8) {
      setError('Password must be at least 8 characters');
      return;
    }

    setLoading(true);
    try {
      const res = await registerApi({ email, password, firstName, lastName });
      login(res.token, res.user);
      navigate('/');
    } catch {
      setError('Registration failed. Email may already be in use.');
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
          <h2 className="mb-6 text-lg font-semibold text-gray-900">Create your account</h2>

          {error && (
            <div className="mb-4 rounded-lg bg-danger-50 px-4 py-3 text-sm text-danger">
              {error}
            </div>
          )}

          <form onSubmit={handleSubmit} className="space-y-4">
            <div className="grid grid-cols-2 gap-4">
              <Input
                id="firstName"
                label="First name"
                placeholder="John"
                value={firstName}
                onChange={(e) => setFirstName(e.target.value)}
                required
              />
              <Input
                id="lastName"
                label="Last name"
                placeholder="Smith"
                value={lastName}
                onChange={(e) => setLastName(e.target.value)}
                required
              />
            </div>
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
            <Input
              id="confirmPassword"
              label="Confirm password"
              type="password"
              placeholder="••••••••"
              value={confirmPassword}
              onChange={(e) => setConfirmPassword(e.target.value)}
              required
            />
            <Button type="submit" className="w-full" disabled={loading}>
              {loading ? 'Creating account…' : 'Create account'}
            </Button>
          </form>

          <p className="mt-6 text-center text-sm text-gray-500">
            Already have an account?{' '}
            <Link to="/login" className="font-medium text-primary hover:underline">
              Sign in
            </Link>
          </p>
        </div>
      </div>
    </div>
  );
}

```

## web/src/pages/Dashboard.tsx

```tsx
import { useQuery } from '@tanstack/react-query';
import { Link } from 'react-router-dom';
import {
  FolderKanban,
  DollarSign,
  FileText,
  AlertTriangle,
  ArrowRight,
  Plus,
  Pencil,
  Trash2,
  Activity,
} from 'lucide-react';
import { getProjects } from '@/api/projects';
import { getDashboardStats, getRecentActivity } from '@/api/dashboard';
import type { ActivityEntry } from '@/api/dashboard';
import Card from '@/components/ui/Card';
import Badge from '@/components/ui/Badge';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency, formatDate } from '@/lib/utils';

function timeAgo(dateStr: string): string {
  const now = Date.now();
  const then = new Date(dateStr).getTime();
  const seconds = Math.floor((now - then) / 1000);
  if (seconds < 60) return 'just now';
  const minutes = Math.floor(seconds / 60);
  if (minutes < 60) return `${minutes} minute${minutes > 1 ? 's' : ''} ago`;
  const hours = Math.floor(minutes / 60);
  if (hours < 24) return `${hours} hour${hours > 1 ? 's' : ''} ago`;
  const days = Math.floor(hours / 24);
  return `${days} day${days > 1 ? 's' : ''} ago`;
}

function activityIcon(action: string) {
  if (action === 'CREATE') return Plus;
  if (action === 'UPDATE') return Pencil;
  if (action === 'DELETE') return Trash2;
  return Activity;
}

function activityDescription(entry: ActivityEntry): string {
  const verb = entry.action === 'CREATE' ? 'Created' : entry.action === 'UPDATE' ? 'Updated' : 'Deleted';
  const entity = entry.entityType.replace(/([A-Z])/g, ' $1').trim().toLowerCase();
  return `${verb} ${entity}`;
}

export default function DashboardPage() {
  const { data: projects, isLoading } = useQuery({
    queryKey: ['projects'],
    queryFn: () => getProjects(),
  });

  const { data: dashboardStats } = useQuery({
    queryKey: ['dashboard-stats'],
    queryFn: getDashboardStats,
  });

  const { data: recentActivity } = useQuery({
    queryKey: ['recent-activity'],
    queryFn: getRecentActivity,
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const activeProjects = projects?.filter((p) => p.status === 'active') ?? [];
  const totalBudget = projects?.reduce((s, p) => s + p.contractValue, 0) ?? 0;

  const stats = [
    { label: 'Active Projects', value: activeProjects.length, icon: FolderKanban, color: 'text-primary' },
    { label: 'Total Budget', value: formatCurrency(totalBudget), icon: DollarSign, color: 'text-success' },
    { label: 'Outstanding Invoices', value: dashboardStats && dashboardStats.outstandingInvoices.count > 0 ? formatCurrency(dashboardStats.outstandingInvoices.total) : '—', icon: FileText, color: 'text-warning' },
    { label: 'Expiring Licences', value: dashboardStats?.expiringLicences || '—', icon: AlertTriangle, color: 'text-danger' },
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
                <p className="text-lg font-semibold text-success">
                  {dashboardStats ? formatCurrency(dashboardStats.cashPosition.totalIncome) : '—'}
                </p>
              </div>
              <div>
                <p className="text-xs text-gray-500">Expenses</p>
                <p className="text-lg font-semibold text-danger">
                  {dashboardStats ? formatCurrency(dashboardStats.cashPosition.totalExpenses) : '—'}
                </p>
              </div>
            </div>
          </div>
        </Card>
      </div>

      {/* Upcoming Milestones */}
      <Card title="Upcoming Milestones">
        {dashboardStats && dashboardStats.upcomingMilestones.length > 0 ? (
          <div className="space-y-3">
            {dashboardStats.upcomingMilestones.map((m) => (
              <Link
                key={m.id}
                to={`/projects/${m.projectId}`}
                className="flex items-center justify-between rounded-lg p-3 transition-colors hover:bg-gray-50"
              >
                <div>
                  <p className="text-sm font-medium text-gray-900">{m.name}</p>
                  <p className="text-xs text-gray-500">{m.projectName}</p>
                </div>
                <p className="text-sm text-gray-500">{formatDate(m.startDate)}</p>
              </Link>
            ))}
          </div>
        ) : (
          <p className="py-4 text-center text-sm text-gray-500">No upcoming milestones</p>
        )}
      </Card>

      {/* Recent Activity (Task 1.9.5) */}
      <Card title="Recent Activity">
        {recentActivity && recentActivity.length > 0 ? (
          <div className="space-y-3">
            {recentActivity.slice(0, 10).map((entry) => {
              const Icon = activityIcon(entry.action);
              return (
                <div
                  key={entry.id}
                  className="flex items-center gap-3 rounded-lg p-2"
                >
                  <div className="rounded-lg bg-gray-50 p-2 text-gray-500">
                    <Icon className="h-4 w-4" />
                  </div>
                  <div className="min-w-0 flex-1">
                    <p className="text-sm text-gray-900">
                      {activityDescription(entry)}
                    </p>
                    <p className="text-xs text-gray-500">
                      {entry.user.name} · {timeAgo(entry.createdAt)}
                    </p>
                  </div>
                </div>
              );
            })}
          </div>
        ) : (
          <p className="py-4 text-center text-sm text-gray-500">No recent activity</p>
        )}
      </Card>

      {/* Overdue Items */}
      <Card title="Overdue Items">
        {dashboardStats?.overdueItems && dashboardStats.overdueItems.totalCount > 0 ? (
          <div className="space-y-3">
            {dashboardStats.overdueItems.tasks.map((t) => (
              <Link
                key={t.id}
                to={`/projects/${t.projectId}/planning`}
                className="flex items-center justify-between rounded-lg p-2 hover:bg-gray-50"
              >
                <div>
                  <p className="text-sm font-medium text-gray-900">{t.name}</p>
                  <p className="text-xs text-gray-500">{t.projectName}</p>
                </div>
                <Badge status="delayed" />
              </Link>
            ))}
            {dashboardStats.overdueItems.defects.map((d) => (
              <Link
                key={d.id}
                to={`/projects/${d.projectId}/inspections`}
                className="flex items-center justify-between rounded-lg p-2 hover:bg-gray-50"
              >
                <div>
                  <p className="text-sm font-medium text-gray-900">{d.title}</p>
                  <p className="text-xs text-gray-500">{d.projectName}</p>
                </div>
                <Badge status="open" />
              </Link>
            ))}
          </div>
        ) : (
          <p className="py-4 text-center text-sm text-gray-500">No overdue items</p>
        )}
      </Card>
    </div>
  );
}

```

## web/src/pages/Profile.tsx

```tsx
import { useState, type FormEvent } from 'react';
import { ArrowLeft } from 'lucide-react';
import { Link } from 'react-router-dom';
import { useAuth } from '@/hooks/useAuth';
import { updateProfile } from '@/api/auth';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Card from '@/components/ui/Card';
import { formatDate } from '@/lib/utils';

export default function ProfilePage() {
  const { user, login, token } = useAuth();
  const [editing, setEditing] = useState(false);
  const [firstName, setFirstName] = useState(user?.firstName ?? '');
  const [lastName, setLastName] = useState(user?.lastName ?? '');
  const [email, setEmail] = useState(user?.email ?? '');
  const [phone, setPhone] = useState(user?.phone ?? '');
  const [error, setError] = useState('');
  const [success, setSuccess] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    setError('');
    setSuccess('');
    setLoading(true);
    try {
      const updated = await updateProfile({
        firstName,
        lastName,
        email,
        phone: phone || undefined,
      });
      if (token) login(token, updated);
      setSuccess('Profile updated successfully');
      setEditing(false);
    } catch {
      setError('Failed to update profile');
    } finally {
      setLoading(false);
    }
  };

  const handleCancel = () => {
    setFirstName(user?.firstName ?? '');
    setLastName(user?.lastName ?? '');
    setEmail(user?.email ?? '');
    setPhone(user?.phone ?? '');
    setError('');
    setSuccess('');
    setEditing(false);
  };

  return (
    <div className="space-y-6">
      <div>
        <Link to="/" className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700">
          <ArrowLeft className="h-4 w-4" /> Back to Dashboard
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Profile</h2>
      </div>

      {error && (
        <div className="rounded-lg bg-danger-50 px-4 py-3 text-sm text-danger">{error}</div>
      )}
      {success && (
        <div className="rounded-lg bg-success-50 px-4 py-3 text-sm text-success">{success}</div>
      )}

      <Card>
        {editing ? (
          <form onSubmit={handleSubmit} className="space-y-4">
            <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
              <Input
                id="firstName"
                label="First name"
                value={firstName}
                onChange={(e) => setFirstName(e.target.value)}
                required
              />
              <Input
                id="lastName"
                label="Last name"
                value={lastName}
                onChange={(e) => setLastName(e.target.value)}
                required
              />
            </div>
            <Input
              id="email"
              label="Email"
              type="email"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              required
            />
            <Input
              id="phone"
              label="Phone"
              type="tel"
              placeholder="04XX XXX XXX"
              value={phone}
              onChange={(e) => setPhone(e.target.value)}
            />
            <div className="flex justify-end gap-2 pt-2">
              <Button variant="secondary" type="button" onClick={handleCancel}>
                Cancel
              </Button>
              <Button type="submit" disabled={loading}>
                {loading ? 'Saving…' : 'Save changes'}
              </Button>
            </div>
          </form>
        ) : (
          <div className="space-y-4">
            <div className="flex items-center justify-between">
              <div className="flex items-center gap-4">
                <div className="flex h-14 w-14 items-center justify-center rounded-full bg-primary text-lg font-semibold text-white">
                  {user?.firstName?.[0]}
                  {user?.lastName?.[0]}
                </div>
                <div>
                  <p className="text-sm font-semibold text-gray-900">
                    {user?.firstName} {user?.lastName}
                  </p>
                  <p className="text-sm text-gray-500 capitalize">{user?.role}</p>
                </div>
              </div>
              <Button variant="secondary" size="sm" onClick={() => setEditing(true)}>
                Edit
              </Button>
            </div>

            <div className="grid grid-cols-1 gap-4 border-t border-gray-100 pt-4 sm:grid-cols-2">
              <div>
                <p className="text-xs text-gray-500">Email</p>
                <p className="text-sm text-gray-900">{user?.email}</p>
              </div>
              <div>
                <p className="text-xs text-gray-500">Phone</p>
                <p className="text-sm text-gray-900">{user?.phone || '—'}</p>
              </div>
              <div>
                <p className="text-xs text-gray-500">Member since</p>
                <p className="text-sm text-gray-900">
                  {user?.createdAt ? formatDate(user.createdAt) : '—'}
                </p>
              </div>
            </div>
          </div>
        )}
      </Card>
    </div>
  );
}

```

## web/src/pages/Settings.tsx

```tsx
import { useState, type FormEvent } from 'react';
import { ArrowLeft, Download } from 'lucide-react';
import { Link } from 'react-router-dom';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Card from '@/components/ui/Card';

export default function SettingsPage() {
  const [notifications, setNotifications] = useState({
    overdueTasks: true,
    expiringLicences: true,
    invoiceChanges: false,
  });

  const [company, setCompany] = useState({
    name: '',
    abn: '',
    phone: '',
    email: '',
    address: '',
  });

  const [success, setSuccess] = useState('');

  const handleToggle = (key: keyof typeof notifications) => {
    setNotifications((prev) => ({ ...prev, [key]: !prev[key] }));
  };

  const handleCompanySubmit = (e: FormEvent) => {
    e.preventDefault();
    setSuccess('Company details saved');
    setTimeout(() => setSuccess(''), 3000);
  };

  return (
    <div className="space-y-6">
      <div>
        <Link to="/" className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700">
          <ArrowLeft className="h-4 w-4" /> Back to Dashboard
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Settings</h2>
        <p className="text-sm text-gray-500">Manage your notification preferences, company details, and data exports.</p>
      </div>

      {success && (
        <div className="rounded-lg bg-success-50 px-4 py-3 text-sm text-success">{success}</div>
      )}

      {/* Notification Preferences */}
      <Card title="Notification Preferences">
        <div className="space-y-4">
          <ToggleRow
            label="Overdue task reminders"
            description="Receive email notifications when tasks are overdue"
            checked={notifications.overdueTasks}
            onChange={() => handleToggle('overdueTasks')}
          />
          <ToggleRow
            label="Expiring licence alerts"
            description="Receive email notifications when tradie licences are expiring"
            checked={notifications.expiringLicences}
            onChange={() => handleToggle('expiringLicences')}
          />
          <ToggleRow
            label="Invoice status changes"
            description="Receive email notifications when invoice statuses change"
            checked={notifications.invoiceChanges}
            onChange={() => handleToggle('invoiceChanges')}
          />
        </div>
      </Card>

      {/* Company Details */}
      <Card title="Company Details">
        <form onSubmit={handleCompanySubmit} className="space-y-4">
          <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
            <Input
              id="companyName"
              label="Company name"
              value={company.name}
              onChange={(e) => setCompany((p) => ({ ...p, name: e.target.value }))}
            />
            <Input
              id="abn"
              label="ABN"
              placeholder="XX XXX XXX XXX"
              value={company.abn}
              onChange={(e) => setCompany((p) => ({ ...p, abn: e.target.value }))}
            />
          </div>
          <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
            <Input
              id="companyPhone"
              label="Phone"
              type="tel"
              placeholder="04XX XXX XXX"
              value={company.phone}
              onChange={(e) => setCompany((p) => ({ ...p, phone: e.target.value }))}
            />
            <Input
              id="companyEmail"
              label="Email"
              type="email"
              value={company.email}
              onChange={(e) => setCompany((p) => ({ ...p, email: e.target.value }))}
            />
          </div>
          <Input
            id="companyAddress"
            label="Address"
            value={company.address}
            onChange={(e) => setCompany((p) => ({ ...p, address: e.target.value }))}
          />
          <div className="flex justify-end pt-2">
            <Button type="submit">Save</Button>
          </div>
        </form>
      </Card>

      {/* Data Export */}
      <Card title="Data Export">
        <p className="mb-4 text-sm text-gray-500">Download your data as CSV files.</p>
        <div className="flex flex-wrap gap-3">
          <Button variant="secondary" onClick={() => {}}>
            <Download className="h-4 w-4" /> Export Projects (CSV)
          </Button>
          <Button variant="secondary" onClick={() => {}}>
            <Download className="h-4 w-4" /> Export Transactions (CSV)
          </Button>
        </div>
      </Card>
    </div>
  );
}

function ToggleRow({
  label,
  description,
  checked,
  onChange,
}: {
  label: string;
  description: string;
  checked: boolean;
  onChange: () => void;
}) {
  return (
    <div className="flex items-center justify-between gap-4">
      <div>
        <p className="text-sm font-medium text-gray-900">{label}</p>
        <p className="text-xs text-gray-500">{description}</p>
      </div>
      <button
        type="button"
        role="switch"
        aria-checked={checked}
        onClick={onChange}
        className={`relative inline-flex h-6 w-11 shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 ${
          checked ? 'bg-primary' : 'bg-gray-200'
        }`}
      >
        <span
          className={`pointer-events-none inline-block h-5 w-5 rounded-full bg-white shadow ring-0 transition-transform ${
            checked ? 'translate-x-5' : 'translate-x-0'
          }`}
        />
      </button>
    </div>
  );
}

```

## web/src/pages/admin/UsersPage.tsx

```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { Link } from 'react-router-dom';
import { ArrowLeft, Shield, Users } from 'lucide-react';
import { getUsers, updateUserRole } from '@/api/users';
import Table from '@/components/ui/Table';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import EmptyState from '@/components/ui/EmptyState';
import { formatDate } from '@/lib/utils';

const ROLES = ['ADMIN', 'PROJECT_MANAGER', 'SITE_SUPERVISOR', 'ACCOUNTS', 'TRADIE'] as const;

function formatRole(role: string) {
  return role.replace(/_/g, ' ');
}

export default function UsersPage() {
  const queryClient = useQueryClient();

  const { data: users, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  });

  const roleMutation = useMutation({
    mutationFn: ({ id, role }: { id: string; role: string }) => updateUserRole(id, role),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });

  return (
    <div className="space-y-6">
      <div>
        <Link to="/" className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700">
          <ArrowLeft className="h-4 w-4" /> Back to Dashboard
        </Link>
        <div className="flex items-center gap-3">
          <Shield className="h-6 w-6 text-primary" />
          <h2 className="text-xl font-bold text-gray-900">User Management</h2>
        </div>
      </div>

      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : !users || users.length === 0 ? (
        <EmptyState
          icon={<Users className="h-12 w-12" />}
          title="No users found"
          description="There are no registered users yet."
        />
      ) : (
        <Table headers={['Name', 'Email', 'Role', 'Member Since']}>
          {users.map((user) => (
            <tr key={user.id}>
              <td className="whitespace-nowrap px-4 py-3 text-sm font-medium text-gray-900">
                {user.name}
              </td>
              <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                {user.email}
              </td>
              <td className="whitespace-nowrap px-4 py-3">
                <select
                  value={user.role}
                  onChange={(e) => roleMutation.mutate({ id: user.id, role: e.target.value })}
                  disabled={roleMutation.isPending}
                  className="rounded-lg border border-gray-300 px-2 py-1 text-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                >
                  {ROLES.map((r) => (
                    <option key={r} value={r}>{formatRole(r)}</option>
                  ))}
                </select>
              </td>
              <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                {formatDate(user.createdAt)}
              </td>
            </tr>
          ))}
        </Table>
      )}
    </div>
  );
}

```

## web/src/pages/projects/ProjectsPage.tsx

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

## web/src/pages/projects/ProjectDetailPage.tsx

```tsx
import { useState, useRef, type FormEvent } from 'react';
import { useParams, Link, useLocation } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import {
  ArrowLeft,
  DollarSign,
  TrendingUp,
  CalendarDays,
  Receipt,
  ClipboardCheck,
  FileText,
  Users,
  Pencil,
  Upload,
  Trash2,
  Image,
  Plus,
  UserMinus,
} from 'lucide-react';
import { getProject, updateProject, getProjectTeam, addProjectTeamMember, removeProjectTeamMember } from '@/api/projects';
import type { ProjectTeamMember } from '@/api/projects';
import { getUsers } from '@/api/users';
import { getDocuments, uploadDocument, deleteDocument } from '@/api/documents';
import type { Project } from '@/types';
import { useAuth } from '@/hooks/useAuth';
import { hasPermission } from '@/lib/permissions';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Card from '@/components/ui/Card';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import Table from '@/components/ui/Table';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const TABS = [
  { key: 'overview', label: 'Overview', path: '' },
  { key: 'budget', label: 'Budget', path: 'budget', icon: DollarSign },
  { key: 'cashflow', label: 'Cash Flow', path: 'cashflow', icon: TrendingUp },
  { key: 'planning', label: 'Planning', path: 'planning', icon: CalendarDays },
  { key: 'financials', label: 'Financials', path: 'financials', icon: Receipt },
  { key: 'inspections', label: 'Inspections', path: 'inspections', icon: ClipboardCheck },
  { key: 'documents', label: 'Documents', path: 'documents', icon: FileText },
  { key: 'team', label: 'Team', path: 'team', icon: Users },
] as const;

export default function ProjectDetailPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();
  const location = useLocation();
  const { user } = useAuth();
  const [showEdit, setShowEdit] = useState(false);
  const [showAddMember, setShowAddMember] = useState(false);
  const [previewPhoto, setPreviewPhoto] = useState<string | null>(null);
  const [notes, setNotes] = useState<string | null>(null);
  const [notesDirty, setNotesDirty] = useState(false);
  const photoInputRef = useRef<HTMLInputElement>(null);
  const canManageTeam = hasPermission(user?.role, 'manageTeam');

  const basePath = `/projects/${id}`;
  const activeTab = location.pathname === basePath
    ? 'overview'
    : location.pathname.endsWith('/team')
      ? 'team'
      : 'overview';

  const { data: project, isLoading } = useQuery({
    queryKey: ['project', id],
    queryFn: () => getProject(id!),
    enabled: !!id,
  });

  const { data: photos } = useQuery({
    queryKey: ['photos', id],
    queryFn: () => getDocuments(id!, { category: 'PHOTOS' }),
    enabled: !!id && activeTab === 'overview',
  });

  const { data: teamMembers, isLoading: teamLoading } = useQuery({
    queryKey: ['project-team', id],
    queryFn: () => getProjectTeam(id!),
    enabled: !!id && activeTab === 'team',
  });

  const removeMemberMutation = useMutation({
    mutationFn: (memberId: string) => removeProjectTeamMember(id!, memberId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['project-team', id] });
      queryClient.invalidateQueries({ queryKey: ['project', id] });
    },
  });

  const uploadMutation = useMutation({
    mutationFn: (file: File) => uploadDocument(id!, file, 'PHOTOS'),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['photos', id] }),
  });

  const deleteMutation = useMutation({
    mutationFn: (docId: string) => deleteDocument(id!, docId),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['photos', id] }),
  });

  const notesMutation = useMutation({
    mutationFn: (newNotes: string) => updateProject(id!, { notes: newNotes } as Partial<Project>),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['project', id] });
      setNotesDirty(false);
    },
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
            <Button variant="secondary" size="sm" onClick={() => setShowEdit(true)}>
              <Pencil className="h-4 w-4" /> Edit
            </Button>
          </div>
        </div>
      </div>

      {/* Tab Navigation */}
      <div className="-mx-4 overflow-x-auto px-4 sm:mx-0 sm:px-0">
        <div className="flex gap-1 border-b border-gray-200">
          {TABS.map((tab) => {
            const to = tab.path ? `${basePath}/${tab.path}` : basePath;
            const isActive = activeTab === tab.key;
            return (
              <Link
                key={tab.key}
                to={to}
                className={cn(
                  'whitespace-nowrap border-b-2 px-4 py-2.5 text-sm font-medium transition-colors',
                  isActive
                    ? 'border-primary text-primary'
                    : 'border-transparent text-gray-500 hover:text-gray-700',
                )}
              >
                {tab.label}
              </Link>
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
              {TABS.filter((t) => t.key !== 'overview').map((tab) => (
                <Link
                  key={tab.key}
                  to={`${basePath}/${tab.path}`}
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

          {/* Project Notes (3.2.0b) */}
          <Card
            title="Notes"
            action={
              notesDirty ? (
                <Button
                  size="sm"
                  onClick={() => notesMutation.mutate(notes ?? '')}
                  disabled={notesMutation.isPending}
                >
                  {notesMutation.isPending ? 'Saving…' : 'Save'}
                </Button>
              ) : undefined
            }
          >
            <textarea
              rows={4}
              value={notes ?? project.notes ?? ''}
              onChange={(e) => {
                setNotes(e.target.value);
                setNotesDirty(true);
              }}
              placeholder="Add project notes…"
              className="block w-full resize-y rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              style={{ minHeight: 80 }}
            />
          </Card>

          {/* Photo Gallery (3.2.0a) */}
          <Card
            title="Photos"
            action={
              <Button size="sm" variant="secondary" onClick={() => photoInputRef.current?.click()}>
                <Upload className="h-4 w-4" /> Upload
              </Button>
            }
          >
            <input
              ref={photoInputRef}
              type="file"
              accept="image/*"
              className="hidden"
              onChange={(e) => {
                const file = e.target.files?.[0];
                if (file) {
                  if (file.size > 50 * 1024 * 1024) {
                    alert('File exceeds the 50 MB limit. Please choose a smaller file.');
                  } else {
                    uploadMutation.mutate(file);
                  }
                }
                e.target.value = '';
              }}
            />
            {(!photos || photos.length === 0) ? (
              <div className="flex flex-col items-center gap-2 py-8 text-center">
                <Image className="h-12 w-12 text-gray-300" />
                <p className="text-sm text-gray-500">No photos yet</p>
              </div>
            ) : (
              <div className="grid grid-cols-2 gap-3 sm:grid-cols-3 lg:grid-cols-4">
                {photos.map((photo) => (
                  <div key={photo.id} className="group relative overflow-hidden rounded-lg border border-gray-200">
                    <img
                      src={photo.url || (photo as unknown as { fileUrl: string }).fileUrl}
                      alt={photo.name}
                      className="h-32 w-full cursor-pointer object-cover"
                      onClick={() => setPreviewPhoto(photo.url || (photo as unknown as { fileUrl: string }).fileUrl)}
                    />
                    <button
                      onClick={() => {
                        if (confirm('Delete this photo?')) deleteMutation.mutate(photo.id);
                      }}
                      className="absolute right-1 top-1 hidden rounded-full bg-red-500 p-1 text-white shadow group-hover:block"
                    >
                      <Trash2 className="h-3 w-3" />
                    </button>
                  </div>
                ))}
              </div>
            )}
          </Card>

          {/* Photo Preview Modal */}
          {previewPhoto && (
            <Modal open onClose={() => setPreviewPhoto(null)} title="Photo Preview">
              <div className="flex justify-center">
                <img src={previewPhoto} alt="Preview" className="max-h-[70vh] max-w-full rounded-lg" />
              </div>
            </Modal>
          )}
        </div>
      )}

      {activeTab === 'team' && (
        <Card
          title="Project Team"
          action={
            canManageTeam ? (
              <Button size="sm" onClick={() => setShowAddMember(true)}>
                <Plus className="h-4 w-4" /> Add Member
              </Button>
            ) : undefined
          }
        >
          {teamLoading ? (
            <LoadingSpinner className="py-8" />
          ) : !teamMembers || teamMembers.length === 0 ? (
            <EmptyState
              icon={<Users className="h-12 w-12" />}
              title="No team members"
              description="Add team members to collaborate on this project."
              action={
                canManageTeam ? (
                  <Button size="sm" onClick={() => setShowAddMember(true)}>
                    <Plus className="h-4 w-4" /> Add Member
                  </Button>
                ) : undefined
              }
            />
          ) : (
            <Table headers={['Name', 'Email', 'System Role', 'Project Role', canManageTeam ? '' : ''].filter(Boolean)}>
              {teamMembers.map((member) => (
                <tr key={member.id}>
                  <td className="whitespace-nowrap px-4 py-3 text-sm font-medium text-gray-900">
                    {member.user.name}
                  </td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                    {member.user.email}
                  </td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                    {member.user.role.replace(/_/g, ' ')}
                  </td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                    {member.role}
                  </td>
                  {canManageTeam && (
                    <td className="whitespace-nowrap px-4 py-3 text-right">
                      <button
                        onClick={() => {
                          if (confirm(`Remove ${member.user.name} from the team?`))
                            removeMemberMutation.mutate(member.id);
                        }}
                        className="text-gray-400 hover:text-danger"
                      >
                        <UserMinus className="h-4 w-4" />
                      </button>
                    </td>
                  )}
                </tr>
              ))}
            </Table>
          )}
        </Card>
      )}

      {showAddMember && (
        <AddTeamMemberModal
          projectId={id!}
          existingMembers={teamMembers ?? []}
          onClose={() => setShowAddMember(false)}
          onSaved={() => {
            setShowAddMember(false);
            queryClient.invalidateQueries({ queryKey: ['project-team', id] });
            queryClient.invalidateQueries({ queryKey: ['project', id] });
          }}
        />
      )}

      {showEdit && (
        <EditProjectModal
          project={project}
          onClose={() => setShowEdit(false)}
          onSaved={() => {
            setShowEdit(false);
            queryClient.invalidateQueries({ queryKey: ['project', id] });
          }}
        />
      )}
    </div>
  );
}

function EditProjectModal({
  project,
  onClose,
  onSaved,
}: {
  project: Project;
  onClose: () => void;
  onSaved: () => void;
}) {
  const [form, setForm] = useState({
    name: project.name,
    address: project.address ?? '',
    clientName: project.clientName ?? '',
    clientEmail: project.clientEmail ?? '',
    clientPhone: project.clientPhone ?? '',
    contractValue: project.contractValue,
    startDate: project.startDate?.slice(0, 10) ?? '',
    estimatedEndDate: project.estimatedEndDate?.slice(0, 10) ?? '',
    status: project.status,
  });

  const mutation = useMutation({
    mutationFn: (payload: Partial<Project>) => updateProject(project.id, payload),
    onSuccess: () => onSaved(),
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    mutation.mutate({
      ...form,
      contractValue: Number(form.contractValue),
      startDate: form.startDate || undefined,
      estimatedEndDate: form.estimatedEndDate || undefined,
    });
  };

  const set = (field: string, value: string | number) =>
    setForm((prev) => ({ ...prev, [field]: value }));

  return (
    <Modal open onClose={onClose} title="Edit Project">
      <form onSubmit={handleSubmit} className="space-y-4">
        <Input
          id="edit-name"
          label="Project name"
          required
          value={form.name}
          onChange={(e) => set('name', e.target.value)}
        />
        <Input
          id="edit-address"
          label="Address"
          value={form.address}
          onChange={(e) => set('address', e.target.value)}
        />
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
          <Input
            id="edit-clientName"
            label="Client name"
            value={form.clientName}
            onChange={(e) => set('clientName', e.target.value)}
          />
          <Input
            id="edit-clientEmail"
            label="Client email"
            type="email"
            value={form.clientEmail}
            onChange={(e) => set('clientEmail', e.target.value)}
          />
        </div>
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
          <Input
            id="edit-clientPhone"
            label="Client phone"
            value={form.clientPhone}
            onChange={(e) => set('clientPhone', e.target.value)}
          />
          <Input
            id="edit-contractValue"
            label="Contract value ($)"
            type="number"
            value={form.contractValue}
            onChange={(e) => set('contractValue', e.target.value)}
          />
        </div>
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
          <Input
            id="edit-startDate"
            label="Start date"
            type="date"
            value={form.startDate}
            onChange={(e) => set('startDate', e.target.value)}
          />
          <Input
            id="edit-estimatedEndDate"
            label="Est. completion"
            type="date"
            value={form.estimatedEndDate}
            onChange={(e) => set('estimatedEndDate', e.target.value)}
          />
        </div>
        <div>
          <label htmlFor="edit-status" className="mb-1 block text-sm font-medium text-gray-700">
            Status
          </label>
          <select
            id="edit-status"
            value={form.status}
            onChange={(e) => set('status', e.target.value)}
            className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
          >
            <option value="planning">Planning</option>
            <option value="active">Active</option>
            <option value="on_hold">On Hold</option>
            <option value="complete">Complete</option>
            <option value="cancelled">Cancelled</option>
          </select>
        </div>
        <div className="flex justify-end gap-2 pt-2">
          <Button variant="secondary" type="button" onClick={onClose}>Cancel</Button>
          <Button type="submit" disabled={mutation.isPending}>
            {mutation.isPending ? 'Saving…' : 'Save changes'}
          </Button>
        </div>
      </form>
    </Modal>
  );
}

const PROJECT_ROLES = ['Project Manager', 'Site Supervisor', 'Foreman', 'Accounts', 'Subcontractor', 'Other'];

function AddTeamMemberModal({
  projectId,
  existingMembers,
  onClose,
  onSaved,
}: {
  projectId: string;
  existingMembers: ProjectTeamMember[];
  onClose: () => void;
  onSaved: () => void;
}) {
  const [selectedUserId, setSelectedUserId] = useState('');
  const [projectRole, setProjectRole] = useState(PROJECT_ROLES[0]);

  const { data: users, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  });

  const existingUserIds = new Set(existingMembers.map((m) => m.userId));
  const availableUsers = users?.filter((u) => !existingUserIds.has(u.id)) ?? [];

  const mutation = useMutation({
    mutationFn: () => addProjectTeamMember(projectId, { userId: selectedUserId, role: projectRole }),
    onSuccess: () => onSaved(),
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    if (!selectedUserId) return;
    mutation.mutate();
  };

  return (
    <Modal open onClose={onClose} title="Add Team Member">
      <form onSubmit={handleSubmit} className="space-y-4">
        <div>
          <label htmlFor="add-user" className="mb-1 block text-sm font-medium text-gray-700">
            User
          </label>
          {isLoading ? (
            <LoadingSpinner className="py-2" />
          ) : (
            <select
              id="add-user"
              required
              value={selectedUserId}
              onChange={(e) => setSelectedUserId(e.target.value)}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="">Select a user…</option>
              {availableUsers.map((u) => (
                <option key={u.id} value={u.id}>
                  {u.name} ({u.email})
                </option>
              ))}
            </select>
          )}
        </div>
        <div>
          <label htmlFor="add-role" className="mb-1 block text-sm font-medium text-gray-700">
            Project Role
          </label>
          <select
            id="add-role"
            value={projectRole}
            onChange={(e) => setProjectRole(e.target.value)}
            className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
          >
            {PROJECT_ROLES.map((r) => (
              <option key={r} value={r}>{r}</option>
            ))}
          </select>
        </div>
        <div className="flex justify-end gap-2 pt-2">
          <Button variant="secondary" type="button" onClick={onClose}>Cancel</Button>
          <Button type="submit" disabled={mutation.isPending || !selectedUserId}>
            {mutation.isPending ? 'Adding…' : 'Add Member'}
          </Button>
        </div>
      </form>
    </Modal>
  );
}

```

## web/src/pages/financials/FinancialsPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, Receipt, Trash2, Download, Search, ChevronLeft, ChevronRight } from 'lucide-react';
import { getTransactions, createTransaction, updateTransaction, deleteTransaction } from '@/api/transactions';
import type { TransactionParams } from '@/api/transactions';
import { getTradies } from '@/api/tradies';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency, formatDate } from '@/lib/utils';
import type { Transaction } from '@/types';

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

type FormState = {
  type: 'income' | 'expense';
  category: (typeof CATEGORIES)[number];
  description: string;
  amount: number;
  gstAmount: number;
  totalAmount: number;
  date: string;
  status: (typeof STATUSES)[number];
  invoiceNumber: string;
  notes: string;
  tradieId: string;
};

const initialForm: FormState = {
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
  tradieId: '',
};

export default function FinancialsPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();

  // Filter state (2.1.3)
  const [typeFilter, setTypeFilter] = useState<'income' | 'expense'>('expense');
  const [searchQuery, setSearchQuery] = useState('');
  const [categoryFilter, setCategoryFilter] = useState('');
  const [dateFrom, setDateFrom] = useState('');
  const [dateTo, setDateTo] = useState('');

  // Pagination state (2.1.4)
  const [page, setPage] = useState(1);
  const limit = 20;

  // Modal state (2.1.1 / create)
  const [showModal, setShowModal] = useState(false);
  const [editingTransaction, setEditingTransaction] = useState<Transaction | null>(null);
  const [form, setForm] = useState<FormState>(initialForm);

  // Delete confirmation (2.1.2)
  const [deleteTarget, setDeleteTarget] = useState<Transaction | null>(null);

  // Bulk selection (2.1.6)
  const [selectedIds, setSelectedIds] = useState<Set<string>>(new Set());
  const [bulkStatus, setBulkStatus] = useState<(typeof STATUSES)[number]>('PAID');

  const resetForm = () => setForm(initialForm);

  const openCreateModal = () => {
    setEditingTransaction(null);
    resetForm();
    setShowModal(true);
  };

  const openEditModal = (t: Transaction) => {
    setEditingTransaction(t);
    setForm({
      type: t.type,
      category: (t.category as (typeof CATEGORIES)[number]) || 'OTHER',
      description: t.description,
      amount: t.amount,
      gstAmount: Math.round(t.amount * 0.1 * 100) / 100,
      totalAmount: t.amount + Math.round(t.amount * 0.1 * 100) / 100,
      date: t.date ? t.date.slice(0, 10) : new Date().toISOString().slice(0, 10),
      status: (t.status?.toUpperCase() as (typeof STATUSES)[number]) || 'DRAFT',
      invoiceNumber: t.invoiceNumber ?? '',
      notes: '',
      tradieId: t.tradieId ?? '',
    });
    setShowModal(true);
  };

  const closeModal = () => {
    setShowModal(false);
    setEditingTransaction(null);
    resetForm();
  };

  // Build query params
  const queryParams: TransactionParams = { type: typeFilter, page, limit };
  if (searchQuery) queryParams.search = searchQuery;
  if (categoryFilter) queryParams.category = categoryFilter;
  if (dateFrom) queryParams.dateFrom = dateFrom;
  if (dateTo) queryParams.dateTo = dateTo;

  const { data: response, isLoading } = useQuery({
    queryKey: ['transactions', id, typeFilter, page, searchQuery, categoryFilter, dateFrom, dateTo],
    queryFn: () => getTransactions(id!, queryParams),
    enabled: !!id,
  });

  // Tradies for linking (2.1.5)
  const { data: tradies } = useQuery({
    queryKey: ['tradies'],
    queryFn: () => getTradies(),
  });

  const createMutation = useMutation({
    mutationFn: (payload: FormState) =>
      createTransaction(id!, {
        ...payload,
        date: new Date(payload.date).toISOString(),
        tradieId: payload.tradieId || undefined,
      } as any),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['transactions', id] });
      closeModal();
    },
  });

  const updateMutation = useMutation({
    mutationFn: ({ txId, payload }: { txId: string; payload: FormState }) =>
      updateTransaction(id!, txId, {
        ...payload,
        date: new Date(payload.date).toISOString(),
        tradieId: payload.tradieId || undefined,
      } as any),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['transactions', id] });
      closeModal();
    },
  });

  const deleteMutation = useMutation({
    mutationFn: (txId: string) => deleteTransaction(id!, txId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['transactions', id] });
      setDeleteTarget(null);
    },
  });

  const bulkMutation = useMutation({
    mutationFn: (status: string) =>
      Promise.all(
        Array.from(selectedIds).map((txId) =>
          updateTransaction(id!, txId, { status } as any),
        ),
      ),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['transactions', id] });
      setSelectedIds(new Set());
    },
  });

  const handleAmountChange = (value: number) => {
    const gst = Math.round(value * 0.1 * 100) / 100;
    setForm((f) => ({ ...f, amount: value, gstAmount: gst, totalAmount: value + gst }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (editingTransaction) {
      updateMutation.mutate({ txId: editingTransaction.id, payload: form });
    } else {
      createMutation.mutate(form);
    }
  };

  // CSV export (2.1.7)
  const handleExportCsv = () => {
    const items = response?.data ?? [];
    const headers = ['Date', 'Description', 'Category', 'Type', 'Amount', 'GST', 'Total', 'Status'];
    const rows = items.map((t) => {
      const gst = Math.round(t.amount * 0.1 * 100) / 100;
      return [
        t.date ? t.date.slice(0, 10) : '',
        `"${t.description.replace(/"/g, '""')}"`,
        t.category,
        t.type,
        t.amount.toFixed(2),
        gst.toFixed(2),
        (t.amount + gst).toFixed(2),
        t.status,
      ].join(',');
    });
    const csv = [headers.join(','), ...rows].join('\n');
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `transactions-${typeFilter}-${new Date().toISOString().slice(0, 10)}.csv`;
    link.click();
    URL.revokeObjectURL(url);
  };

  const clearFilters = () => {
    setSearchQuery('');
    setCategoryFilter('');
    setDateFrom('');
    setDateTo('');
    setPage(1);
  };

  const toggleSelect = (txId: string) => {
    setSelectedIds((prev) => {
      const next = new Set(prev);
      if (next.has(txId)) next.delete(txId);
      else next.add(txId);
      return next;
    });
  };

  const toggleSelectAll = () => {
    const items = response?.data ?? [];
    if (selectedIds.size === items.length) {
      setSelectedIds(new Set());
    } else {
      setSelectedIds(new Set(items.map((t) => t.id)));
    }
  };

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const items = response?.data ?? [];
  const pagination = response?.pagination ?? { page: 1, limit: 20, total: 0, pages: 1 };
  const totalIncome = items.filter((t) => t.type === 'income').reduce((s, t) => s + t.amount, 0);
  const totalExpenses = items.filter((t) => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
  const outstanding = items
    .filter((t) => t.status === 'pending' || t.status === 'overdue')
    .reduce((s, t) => s + t.amount, 0);
  const isMutating = createMutation.isPending || updateMutation.isPending;
  const hasFilters = searchQuery || categoryFilter || dateFrom || dateTo;

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Financials</h2>
          <div className="flex gap-2">
            <Button size="sm" variant="secondary" onClick={handleExportCsv}>
              <Download className="h-4 w-4" /> Export CSV
            </Button>
            <Button size="sm" onClick={openCreateModal}>
              <Plus className="h-4 w-4" /> Add Transaction
            </Button>
          </div>
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

      {/* Filter Bar (2.1.3) */}
      <div className="flex flex-wrap items-end gap-3">
        <div className="relative">
          <Search className="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400" />
          <input
            type="text"
            placeholder="Search description…"
            value={searchQuery}
            onChange={(e) => { setSearchQuery(e.target.value); setPage(1); }}
            className="block w-56 rounded-lg border border-gray-300 py-2 pl-9 pr-3 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
          />
        </div>
        <select
          value={categoryFilter}
          onChange={(e) => { setCategoryFilter(e.target.value); setPage(1); }}
          className="block rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
        >
          <option value="">All Categories</option>
          {CATEGORIES.map((c) => (
            <option key={c} value={c}>{c.replace(/_/g, ' ')}</option>
          ))}
        </select>
        <input
          type="date"
          value={dateFrom}
          onChange={(e) => { setDateFrom(e.target.value); setPage(1); }}
          className="block rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
          placeholder="From"
        />
        <input
          type="date"
          value={dateTo}
          onChange={(e) => { setDateTo(e.target.value); setPage(1); }}
          className="block rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
          placeholder="To"
        />
        {hasFilters && (
          <Button size="sm" variant="ghost" onClick={clearFilters}>
            Clear
          </Button>
        )}
      </div>

      {/* Type Toggle */}
      <div className="flex gap-1 rounded-lg bg-gray-100 p-1 w-fit">
        {(['income', 'expense'] as const).map((t) => (
          <button
            key={t}
            onClick={() => { setTypeFilter(t); setPage(1); setSelectedIds(new Set()); }}
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

      {/* Bulk Actions Bar (2.1.6) */}
      {selectedIds.size > 0 && (
        <div className="flex items-center gap-3 rounded-lg border border-primary/20 bg-primary/5 px-4 py-2">
          <span className="text-sm font-medium text-gray-700">
            {selectedIds.size} selected
          </span>
          <select
            value={bulkStatus}
            onChange={(e) => setBulkStatus(e.target.value as (typeof STATUSES)[number])}
            className="block rounded-lg border border-gray-300 px-3 py-1.5 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
          >
            {STATUSES.map((s) => (
              <option key={s} value={s}>{s}</option>
            ))}
          </select>
          <Button
            size="sm"
            onClick={() => bulkMutation.mutate(bulkStatus)}
            disabled={bulkMutation.isPending}
          >
            {bulkMutation.isPending ? 'Updating…' : 'Apply'}
          </Button>
          <Button size="sm" variant="ghost" onClick={() => setSelectedIds(new Set())}>
            Cancel
          </Button>
        </div>
      )}

      {/* Transactions List */}
      {items.length === 0 ? (
        <EmptyState
          icon={<Receipt className="h-12 w-12" />}
          title="No transactions found"
          description={hasFilters ? 'Try adjusting your filters.' : `Add your first ${typeFilter} transaction.`}
          action={
            !hasFilters ? (
              <Button size="sm" onClick={openCreateModal}>
                <Plus className="h-4 w-4" /> Add Transaction
              </Button>
            ) : undefined
          }
        />
      ) : (
        <Card>
          <Table headers={['', 'Date', 'Description', 'Category', 'Tradie', 'Amount', 'Status', '']}>
            {items.map((t) => (
              <tr
                key={t.id}
                className="cursor-pointer hover:bg-gray-50"
                onClick={() => openEditModal(t)}
              >
                <td className="w-10 px-4 py-3" onClick={(e) => e.stopPropagation()}>
                  <input
                    type="checkbox"
                    checked={selectedIds.has(t.id)}
                    onChange={() => toggleSelect(t.id)}
                    className="h-4 w-4 rounded border-gray-300 text-primary focus:ring-primary"
                  />
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatDate(t.date)}
                </td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">{t.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {t.category?.replace(/_/g, ' ')}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {t.tradie?.company ?? '—'}
                </td>
                <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${t.type === 'income' ? 'text-success' : 'text-danger'}`}>
                  {t.type === 'income' ? '+' : '-'}{formatCurrency(t.amount)}
                </td>
                <td className="px-4 py-3">
                  <Badge status={t.status} />
                </td>
                <td className="px-4 py-3" onClick={(e) => e.stopPropagation()}>
                  <button
                    onClick={() => setDeleteTarget(t)}
                    className="rounded p-1 text-gray-400 hover:bg-red-50 hover:text-danger"
                  >
                    <Trash2 className="h-4 w-4" />
                  </button>
                </td>
              </tr>
            ))}
          </Table>

          {/* Select All */}
          <div className="flex items-center px-4 py-2 text-sm text-gray-500">
            <input
              type="checkbox"
              checked={items.length > 0 && selectedIds.size === items.length}
              onChange={toggleSelectAll}
              className="mr-2 h-4 w-4 rounded border-gray-300 text-primary focus:ring-primary"
            />
            Select all on this page
          </div>
        </Card>
      )}

      {/* Pagination Controls (2.1.4) */}
      {pagination.pages > 1 && (
        <div className="flex items-center justify-between">
          <p className="text-sm text-gray-500">
            Page {pagination.page} of {pagination.pages} ({pagination.total} total)
          </p>
          <div className="flex gap-2">
            <Button
              size="sm"
              variant="secondary"
              disabled={pagination.page <= 1}
              onClick={() => setPage((p) => p - 1)}
            >
              <ChevronLeft className="h-4 w-4" /> Previous
            </Button>
            <Button
              size="sm"
              variant="secondary"
              disabled={pagination.page >= pagination.pages}
              onClick={() => setPage((p) => p + 1)}
            >
              Next <ChevronRight className="h-4 w-4" />
            </Button>
          </div>
        </div>
      )}

      {/* Create / Edit Modal (2.1.1) */}
      <Modal
        open={showModal}
        onClose={closeModal}
        title={editingTransaction ? 'Edit Transaction' : 'Add Transaction'}
      >
        <form onSubmit={handleSubmit} className="space-y-4">
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

          {/* Tradie Linking (2.1.5) — only for expenses */}
          {form.type === 'expense' && (
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Tradie</label>
              <select
                value={form.tradieId}
                onChange={(e) => setForm((f) => ({ ...f, tradieId: e.target.value }))}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                <option value="">None</option>
                {(tradies ?? []).map((tr) => (
                  <option key={tr.id} value={tr.id}>
                    {tr.company ? `${tr.company} (${tr.firstName} ${tr.lastName})` : `${tr.firstName} ${tr.lastName}`}
                  </option>
                ))}
              </select>
            </div>
          )}

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
            <Button type="button" variant="secondary" size="sm" onClick={closeModal}>
              Cancel
            </Button>
            <Button type="submit" size="sm" disabled={isMutating}>
              {isMutating ? 'Saving…' : editingTransaction ? 'Update Transaction' : 'Save Transaction'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Delete Confirmation Modal (2.1.2) */}
      <Modal
        open={!!deleteTarget}
        onClose={() => setDeleteTarget(null)}
        title="Delete Transaction"
        className="max-w-sm"
      >
        <p className="text-sm text-gray-600">
          Are you sure you want to delete &ldquo;{deleteTarget?.description}&rdquo;? This action cannot be undone.
        </p>
        <div className="mt-4 flex justify-end gap-3">
          <Button size="sm" variant="secondary" onClick={() => setDeleteTarget(null)}>
            Cancel
          </Button>
          <Button
            size="sm"
            variant="danger"
            disabled={deleteMutation.isPending}
            onClick={() => deleteTarget && deleteMutation.mutate(deleteTarget.id)}
          >
            {deleteMutation.isPending ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>
    </div>
  );
}

```

## web/src/pages/budget/BudgetPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, Trash2, AlertTriangle } from 'lucide-react';
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
import {
  getBudgetSummary,
  getVariations,
  createBudgetCategory,
  updateBudgetCategory,
  deleteBudgetCategory,
  createVariation,
  updateVariation,
  deleteVariation,
  getContingency,
  createDrawdown,
} from '@/api/budget';
import type { BudgetCategory, Variation } from '@/types';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency, formatDate } from '@/lib/utils';

// Build a tree structure: parents first, then indented children
function buildCategoryTree(categories: BudgetCategory[]): (BudgetCategory & { depth: number })[] {
  const result: (BudgetCategory & { depth: number })[] = [];
  const roots = categories.filter((c) => !c.parentId);
  const childMap = new Map<string, BudgetCategory[]>();

  for (const cat of categories) {
    if (cat.parentId) {
      const siblings = childMap.get(cat.parentId) || [];
      siblings.push(cat);
      childMap.set(cat.parentId, siblings);
    }
  }

  for (const root of roots) {
    result.push({ ...root, depth: 0 });
    const children = childMap.get(root.id) || [];
    for (const child of children) {
      result.push({ ...child, depth: 1 });
    }
  }

  return result;
}

export default function BudgetPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();

  // Category create/edit
  const [catOpen, setCatOpen] = useState(false);
  const [editCat, setEditCat] = useState<BudgetCategory | null>(null);
  const [catForm, setCatForm] = useState({ costCode: '', name: '', budgetAmount: '', parentId: '', forecastToComplete: '' });

  // Category delete confirmation
  const [deleteCatId, setDeleteCatId] = useState<string | null>(null);

  // Variation create/edit
  const [varOpen, setVarOpen] = useState(false);
  const [editVar, setEditVar] = useState<Variation | null>(null);
  const [varForm, setVarForm] = useState({ variationNumber: '', title: '', description: '', amount: '', status: '', categoryId: '' });

  // Variation delete confirmation
  const [deleteVarId, setDeleteVarId] = useState<string | null>(null);

  // Contingency drawdown
  const [drawdownOpen, setDrawdownOpen] = useState(false);
  const [drawdownForm, setDrawdownForm] = useState({ description: '', amount: '', date: '', approvedBy: '' });

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

  const invalidateBudget = () => {
    queryClient.invalidateQueries({ queryKey: ['budget-summary', id] });
    queryClient.invalidateQueries({ queryKey: ['variations', id] });
    queryClient.invalidateQueries({ queryKey: ['contingency', id] });
  };

  const { data: contingency } = useQuery({
    queryKey: ['contingency', id],
    queryFn: () => getContingency(id!),
    enabled: !!id,
  });

  const drawdownMutation = useMutation({
    mutationFn: () =>
      createDrawdown(id!, {
        description: drawdownForm.description,
        amount: Number(drawdownForm.amount),
        date: drawdownForm.date,
        ...(drawdownForm.approvedBy && { approvedBy: drawdownForm.approvedBy }),
      }),
    onSuccess: () => {
      invalidateBudget();
      setDrawdownOpen(false);
      setDrawdownForm({ description: '', amount: '', date: '', approvedBy: '' });
    },
  });

  // Category mutations
  const categoryCreateMutation = useMutation({
    mutationFn: () =>
      createBudgetCategory(id!, {
        costCode: catForm.costCode,
        name: catForm.name,
        budgetAmount: Number(catForm.budgetAmount),
        ...(catForm.parentId && { parentId: catForm.parentId }),
      }),
    onSuccess: () => {
      invalidateBudget();
      closeCatModal();
    },
  });

  const categoryUpdateMutation = useMutation({
    mutationFn: () =>
      updateBudgetCategory(id!, editCat!.id, {
        costCode: catForm.costCode,
        name: catForm.name,
        budgetAmount: Number(catForm.budgetAmount),
        forecastToComplete: catForm.forecastToComplete ? Number(catForm.forecastToComplete) : undefined,
        ...(catForm.parentId ? { parentId: catForm.parentId } : {}),
      }),
    onSuccess: () => {
      invalidateBudget();
      closeCatModal();
    },
  });

  const categoryDeleteMutation = useMutation({
    mutationFn: (catId: string) => deleteBudgetCategory(id!, catId),
    onSuccess: () => {
      invalidateBudget();
      setDeleteCatId(null);
    },
  });

  // Variation mutations
  const variationCreateMutation = useMutation({
    mutationFn: () =>
      createVariation(id!, {
        variationNumber: Number(varForm.variationNumber),
        title: varForm.title,
        ...(varForm.description && { description: varForm.description }),
        amount: Number(varForm.amount),
        ...(varForm.status && { status: varForm.status as Variation['status'] }),
        ...(varForm.categoryId && { categoryId: varForm.categoryId }),
      }),
    onSuccess: () => {
      invalidateBudget();
      closeVarModal();
    },
  });

  const variationUpdateMutation = useMutation({
    mutationFn: () =>
      updateVariation(id!, editVar!.id, {
        title: varForm.title,
        ...(varForm.description && { description: varForm.description }),
        amount: Number(varForm.amount),
        ...(varForm.status && { status: varForm.status as Variation['status'] }),
        ...(varForm.categoryId ? { categoryId: varForm.categoryId } : {}),
      }),
    onSuccess: () => {
      invalidateBudget();
      closeVarModal();
    },
  });

  const variationDeleteMutation = useMutation({
    mutationFn: (varId: string) => deleteVariation(id!, varId),
    onSuccess: () => {
      invalidateBudget();
      setDeleteVarId(null);
    },
  });

  function closeCatModal() {
    setCatOpen(false);
    setEditCat(null);
    setCatForm({ costCode: '', name: '', budgetAmount: '', parentId: '', forecastToComplete: '' });
  }

  function openEditCat(cat: BudgetCategory) {
    setEditCat(cat);
    setCatForm({
      costCode: cat.costCode,
      name: cat.name,
      budgetAmount: String(cat.budgetAmount),
      parentId: cat.parentId || '',
      forecastToComplete: cat.forecastToComplete ? String(cat.forecastToComplete) : '',
    });
    setCatOpen(true);
  }

  function closeVarModal() {
    setVarOpen(false);
    setEditVar(null);
    setVarForm({ variationNumber: '', title: '', description: '', amount: '', status: '', categoryId: '' });
  }

  function openEditVar(v: Variation) {
    if (v.status !== 'DRAFT') return;
    setEditVar(v);
    setVarForm({
      variationNumber: String(v.variationNumber),
      title: v.title,
      description: v.description || '',
      amount: String(v.amount),
      status: v.status,
      categoryId: v.categoryId || '',
    });
    setVarOpen(true);
  }

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const categories = summary?.categories ?? [];
  const treeCategories = buildCategoryTree(categories);
  const parentCategories = categories.filter((c) => !c.parentId);

  // Budget alerts (2.3.8)
  const warningCategories = categories.filter((c) => {
    const pct = c.budgetAmount > 0 ? (c.spentAmount / c.budgetAmount) * 100 : 0;
    return pct > 80 && pct <= 100;
  });
  const dangerCategories = categories.filter((c) => {
    const pct = c.budgetAmount > 0 ? (c.spentAmount / c.budgetAmount) * 100 : 0;
    return pct > 100;
  });

  const summaryItems = [
    { label: 'Budget', value: summary?.totalBudget ?? 0, color: 'text-gray-900' },
    { label: 'Spent', value: summary?.totalSpent ?? 0, color: 'text-danger' },
    { label: 'Committed', value: summary?.totalCommitted ?? 0, color: 'text-warning' },
    { label: 'Forecast', value: summary?.totalForecast ?? 0, color: 'text-blue-600' },
    { label: 'Variance', value: summary?.totalVariance ?? 0, color: summary?.totalVariance && summary.totalVariance >= 0 ? 'text-success' : 'text-danger' },
  ];

  const chartData = categories.map((c) => ({
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

      {/* Budget Alerts (2.3.8) */}
      {dangerCategories.length > 0 && (
        <div className="flex items-start gap-3 rounded-lg bg-red-50 p-4">
          <AlertTriangle className="mt-0.5 h-5 w-5 shrink-0 text-danger" />
          <div>
            <p className="text-sm font-semibold text-danger">Over Budget</p>
            <p className="text-sm text-red-700">
              {dangerCategories.map((c) => c.name).join(', ')} — exceeded 100% of budget
            </p>
          </div>
        </div>
      )}
      {warningCategories.length > 0 && (
        <div className="flex items-start gap-3 rounded-lg bg-amber-50 p-4">
          <AlertTriangle className="mt-0.5 h-5 w-5 shrink-0 text-warning" />
          <div>
            <p className="text-sm font-semibold text-warning">Budget Warning</p>
            <p className="text-sm text-amber-700">
              {warningCategories.map((c) => c.name).join(', ')} — over 80% of budget spent
            </p>
          </div>
        </div>
      )}

      {/* Budget Categories Table */}
      <Card
        title="Budget Categories"
        action={
          <Button size="sm" variant="ghost" onClick={() => { setEditCat(null); setCatForm({ costCode: '', name: '', budgetAmount: '', parentId: '', forecastToComplete: '' }); setCatOpen(true); }}>
            <Plus className="h-4 w-4" /> Add Category
          </Button>
        }
      >
        <Table headers={['Cost Code', 'Category', 'Budget', 'Spent', 'Committed', 'Forecast', '% Spent', 'Variance', '']}>
          {treeCategories.map((cat) => {
            const pct = cat.budgetAmount > 0 ? (cat.spentAmount / cat.budgetAmount) * 100 : 0;
            return (
              <tr
                key={cat.id}
                className="cursor-pointer hover:bg-gray-50"
                onClick={() => openEditCat(cat)}
              >
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono text-gray-600">
                  {cat.costCode}
                </td>
                <td className={`px-4 py-3 text-sm font-medium text-gray-900 ${cat.depth > 0 ? 'pl-8' : ''}`}>
                  {cat.name}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.budgetAmount)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.spentAmount)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.committedAmount)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">
                  {formatCurrency(cat.forecastFinal)}
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
                <td className="px-4 py-3">
                  <button
                    className="rounded p-1 text-gray-400 hover:bg-gray-100 hover:text-danger"
                    onClick={(e) => { e.stopPropagation(); setDeleteCatId(cat.id); }}
                  >
                    <Trash2 className="h-4 w-4" />
                  </button>
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
          <Button size="sm" variant="ghost" onClick={() => { setEditVar(null); setVarForm({ variationNumber: '', title: '', description: '', amount: '', status: '', categoryId: '' }); setVarOpen(true); }}>
            <Plus className="h-4 w-4" /> Add Variation
          </Button>
        }
      >
        {(!variations || variations.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No variations</p>
        ) : (
          <Table headers={['#', 'Title', 'Category', 'Amount', 'Status', 'Date', '']}>
            {variations.map((v) => (
              <tr
                key={v.id}
                className={v.status === 'DRAFT' ? 'cursor-pointer hover:bg-gray-50' : ''}
                onClick={() => openEditVar(v)}
              >
                <td className="whitespace-nowrap px-4 py-3 text-sm font-mono">{v.variationNumber}</td>
                <td className="px-4 py-3 text-sm text-gray-900">{v.title}</td>
                <td className="px-4 py-3 text-sm text-gray-500">{v.category?.name || '—'}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(v.amount)}</td>
                <td className="px-4 py-3"><Badge status={v.status.toLowerCase()} /></td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{v.createdAt ? new Date(v.createdAt).toLocaleDateString('en-AU') : '—'}</td>
                <td className="px-4 py-3">
                  <button
                    className="rounded p-1 text-gray-400 hover:bg-gray-100 hover:text-danger"
                    onClick={(e) => { e.stopPropagation(); setDeleteVarId(v.id); }}
                  >
                    <Trash2 className="h-4 w-4" />
                  </button>
                </td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Category Create/Edit Modal */}
      <Modal open={catOpen} onClose={closeCatModal} title={editCat ? 'Edit Category' : 'Add Category'}>
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            if (editCat) categoryUpdateMutation.mutate();
            else categoryCreateMutation.mutate();
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
          {editCat && (
            <Input
              id="forecastToComplete"
              label="Forecast to Complete"
              type="number"
              value={catForm.forecastToComplete}
              onChange={(e) => setCatForm({ ...catForm, forecastToComplete: e.target.value })}
            />
          )}
          <div className="w-full">
            <label htmlFor="parentCategory" className="mb-1 block text-sm font-medium text-gray-700">Parent Category</label>
            <select
              id="parentCategory"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={catForm.parentId}
              onChange={(e) => setCatForm({ ...catForm, parentId: e.target.value })}
            >
              <option value="">None (top-level)</option>
              {parentCategories
                .filter((c) => c.id !== editCat?.id)
                .map((c) => (
                  <option key={c.id} value={c.id}>{c.costCode} — {c.name}</option>
                ))}
            </select>
          </div>
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={closeCatModal}>Cancel</Button>
            <Button type="submit" disabled={categoryCreateMutation.isPending || categoryUpdateMutation.isPending}>
              {(categoryCreateMutation.isPending || categoryUpdateMutation.isPending) ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Category Delete Confirmation Modal */}
      <Modal open={!!deleteCatId} onClose={() => setDeleteCatId(null)} title="Delete Category">
        <p className="text-sm text-gray-600">Are you sure? This cannot be undone.</p>
        <div className="flex justify-end gap-2 pt-4">
          <Button type="button" variant="secondary" onClick={() => setDeleteCatId(null)}>Cancel</Button>
          <Button
            variant="danger"
            disabled={categoryDeleteMutation.isPending}
            onClick={() => deleteCatId && categoryDeleteMutation.mutate(deleteCatId)}
          >
            {categoryDeleteMutation.isPending ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>

      {/* Variation Create/Edit Modal */}
      <Modal open={varOpen} onClose={closeVarModal} title={editVar ? 'Edit Variation' : 'Add Variation'}>
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            if (editVar) variationUpdateMutation.mutate();
            else variationCreateMutation.mutate();
          }}
        >
          <Input
            id="variationNumber"
            label="Variation #"
            type="number"
            value={varForm.variationNumber}
            onChange={(e) => setVarForm({ ...varForm, variationNumber: e.target.value })}
            required
            disabled={!!editVar}
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
          <div className="w-full">
            <label htmlFor="varCategory" className="mb-1 block text-sm font-medium text-gray-700">Budget Category</label>
            <select
              id="varCategory"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={varForm.categoryId}
              onChange={(e) => setVarForm({ ...varForm, categoryId: e.target.value })}
            >
              <option value="">None</option>
              {categories.map((c) => (
                <option key={c.id} value={c.id}>{c.costCode} — {c.name}</option>
              ))}
            </select>
          </div>
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={closeVarModal}>Cancel</Button>
            <Button type="submit" disabled={variationCreateMutation.isPending || variationUpdateMutation.isPending}>
              {(variationCreateMutation.isPending || variationUpdateMutation.isPending) ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Variation Delete Confirmation Modal */}
      <Modal open={!!deleteVarId} onClose={() => setDeleteVarId(null)} title="Delete Variation">
        <p className="text-sm text-gray-600">Are you sure? This cannot be undone.</p>
        <div className="flex justify-end gap-2 pt-4">
          <Button type="button" variant="secondary" onClick={() => setDeleteVarId(null)}>Cancel</Button>
          <Button
            variant="danger"
            disabled={variationDeleteMutation.isPending}
            onClick={() => deleteVarId && variationDeleteMutation.mutate(deleteVarId)}
          >
            {variationDeleteMutation.isPending ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>

      {/* Contingency Tracking (2.3.9) */}
      <Card
        title="Contingency"
        action={
          <Button size="sm" variant="ghost" onClick={() => setDrawdownOpen(true)}>
            <Plus className="h-4 w-4" /> Add Drawdown
          </Button>
        }
      >
        <div className="mb-4 grid grid-cols-3 gap-4">
          <div>
            <p className="text-xs text-gray-500">Contingency Amount</p>
            <p className="text-lg font-bold text-gray-900">{formatCurrency(contingency?.contingencyAmount ?? 0)}</p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Total Drawn</p>
            <p className="text-lg font-bold text-danger">{formatCurrency(contingency?.totalDrawn ?? 0)}</p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Remaining</p>
            <p className={`text-lg font-bold ${(contingency?.remaining ?? 0) >= 0 ? 'text-success' : 'text-danger'}`}>
              {formatCurrency(contingency?.remaining ?? 0)}
            </p>
          </div>
        </div>
        {(!contingency?.drawdowns || contingency.drawdowns.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No drawdowns recorded</p>
        ) : (
          <Table headers={['Date', 'Description', 'Amount', 'Approved By']}>
            {contingency.drawdowns.map((d) => (
              <tr key={d.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{formatDate(d.date)}</td>
                <td className="px-4 py-3 text-sm text-gray-900">{d.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium text-danger">{formatCurrency(d.amount)}</td>
                <td className="px-4 py-3 text-sm text-gray-500">{d.approvedBy || '—'}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Add Drawdown Modal */}
      <Modal open={drawdownOpen} onClose={() => setDrawdownOpen(false)} title="Add Drawdown">
        <form
          className="space-y-4"
          onSubmit={(e) => {
            e.preventDefault();
            drawdownMutation.mutate();
          }}
        >
          <Input
            id="drawdownDescription"
            label="Description"
            value={drawdownForm.description}
            onChange={(e) => setDrawdownForm({ ...drawdownForm, description: e.target.value })}
            required
          />
          <Input
            id="drawdownAmount"
            label="Amount"
            type="number"
            value={drawdownForm.amount}
            onChange={(e) => setDrawdownForm({ ...drawdownForm, amount: e.target.value })}
            required
          />
          <Input
            id="drawdownDate"
            label="Date"
            type="date"
            value={drawdownForm.date}
            onChange={(e) => setDrawdownForm({ ...drawdownForm, date: e.target.value })}
            required
          />
          <Input
            id="drawdownApprovedBy"
            label="Approved By"
            value={drawdownForm.approvedBy}
            onChange={(e) => setDrawdownForm({ ...drawdownForm, approvedBy: e.target.value })}
          />
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setDrawdownOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={drawdownMutation.isPending}>
              {drawdownMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

## web/src/pages/cashflow/CashFlowPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, Pencil, Trash2, AlertTriangle, TrendingUp } from 'lucide-react';
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
import {
  getCashFlow,
  createCashFlowEntry,
  updateCashFlowEntry,
  deleteCashFlowEntry,
  generateForecast,
  getClaimSchedule,
  createClaim,
  updateClaim,
  deleteClaim,
} from '@/api/cashflow';
import type { CashFlowEntry, ClaimSchedule } from '@/types';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency } from '@/lib/utils';

type FilterType = 'ALL' | 'ACTUAL' | 'FORECAST';

// Server returns incomeAmount/expenseAmount/type; frontend type uses income/expense
// Use `as any` access for server fields when needed
function getIncome(e: CashFlowEntry): number {
  return Number((e as any).incomeAmount ?? e.income ?? 0);
}
function getExpense(e: CashFlowEntry): number {
  return Number((e as any).expenseAmount ?? e.expense ?? 0);
}
function getEntryType(e: CashFlowEntry): string {
  return (e as any).type ?? '';
}
function getEntryMonth(e: CashFlowEntry): string {
  return e.month;
}
function getEntryNotes(e: CashFlowEntry): string {
  return (e as any).notes ?? '';
}

export default function CashFlowPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();

  // ─── State ──────────────────────────────────
  const [filter, setFilter] = useState<FilterType>('ALL');
  const [showEntryModal, setShowEntryModal] = useState(false);
  const [editingEntry, setEditingEntry] = useState<CashFlowEntry | null>(null);
  const [entryForm, setEntryForm] = useState({ month: '', incomeAmount: '', expenseAmount: '', type: 'ACTUAL', notes: '' });

  const [showClaimModal, setShowClaimModal] = useState(false);
  const [editingClaim, setEditingClaim] = useState<ClaimSchedule | null>(null);
  const [claimForm, setClaimForm] = useState({ claimNumber: '', description: '', amount: '', scheduledDate: '', percentComplete: '' });

  const [showDeleteConfirm, setShowDeleteConfirm] = useState(false);
  const [deleteTarget, setDeleteTarget] = useState<{ type: 'entry' | 'claim'; id: string } | null>(null);

  // ─── Queries ────────────────────────────────
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

  // ─── Entry Mutations ───────────────────────
  const createEntryMut = useMutation({
    mutationFn: (payload: Record<string, unknown>) => createCashFlowEntry(id!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['cashflow', id] });
      resetEntryModal();
    },
  });

  const updateEntryMut = useMutation({
    mutationFn: ({ entryId, payload }: { entryId: string; payload: Record<string, unknown> }) =>
      updateCashFlowEntry(id!, entryId, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['cashflow', id] });
      resetEntryModal();
    },
  });

  const deleteEntryMut = useMutation({
    mutationFn: (entryId: string) => deleteCashFlowEntry(entryId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['cashflow', id] });
      setShowDeleteConfirm(false);
      setDeleteTarget(null);
      resetEntryModal();
    },
  });

  // ─── Forecast Mutation ─────────────────────
  const forecastMut = useMutation({
    mutationFn: () => generateForecast(id!),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['cashflow', id] });
    },
  });

  // ─── Claim Mutations ──────────────────────
  const createClaimMut = useMutation({
    mutationFn: (payload: Record<string, unknown>) => createClaim(id!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['claims', id] });
      resetClaimModal();
    },
  });

  const updateClaimMut = useMutation({
    mutationFn: ({ claimId, payload }: { claimId: string; payload: Record<string, unknown> }) =>
      updateClaim(id!, claimId, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['claims', id] });
      resetClaimModal();
    },
  });

  const deleteClaimMut = useMutation({
    mutationFn: (claimId: string) => deleteClaim(claimId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['claims', id] });
      setShowDeleteConfirm(false);
      setDeleteTarget(null);
      resetClaimModal();
    },
  });

  // ─── Helpers ──────────────────────────────
  function resetEntryModal() {
    setShowEntryModal(false);
    setEditingEntry(null);
    setEntryForm({ month: '', incomeAmount: '', expenseAmount: '', type: 'ACTUAL', notes: '' });
  }

  function resetClaimModal() {
    setShowClaimModal(false);
    setEditingClaim(null);
    setClaimForm({ claimNumber: '', description: '', amount: '', scheduledDate: '', percentComplete: '' });
  }

  function openCreateEntry() {
    resetEntryModal();
    setShowEntryModal(true);
  }

  function openEditEntry(entry: CashFlowEntry) {
    const monthStr = getEntryMonth(entry);
    const d = new Date(monthStr);
    const formatted = `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}`;
    setEditingEntry(entry);
    setEntryForm({
      month: formatted,
      incomeAmount: String(getIncome(entry)),
      expenseAmount: String(getExpense(entry)),
      type: getEntryType(entry) || 'ACTUAL',
      notes: getEntryNotes(entry),
    });
    setShowEntryModal(true);
  }

  function handleEntrySubmit(e: React.FormEvent) {
    e.preventDefault();
    const payload = {
      month: new Date(entryForm.month + '-01').toISOString(),
      incomeAmount: Number(entryForm.incomeAmount),
      expenseAmount: Number(entryForm.expenseAmount),
      type: entryForm.type,
      notes: entryForm.notes || undefined,
    };
    if (editingEntry) {
      updateEntryMut.mutate({ entryId: editingEntry.id, payload });
    } else {
      createEntryMut.mutate(payload);
    }
  }

  function openCreateClaim() {
    resetClaimModal();
    setShowClaimModal(true);
  }

  function openEditClaim(claim: ClaimSchedule) {
    const dateStr = (claim as any).scheduledDate ?? claim.dueDate ?? '';
    const d = new Date(dateStr);
    const formatted = d.toISOString().slice(0, 10);
    setEditingClaim(claim);
    setClaimForm({
      claimNumber: String(claim.claimNumber),
      description: claim.description,
      amount: String(Number(claim.amount)),
      scheduledDate: formatted,
      percentComplete: String(Number((claim as any).percentComplete ?? 0)),
    });
    setShowClaimModal(true);
  }

  function handleClaimSubmit(e: React.FormEvent) {
    e.preventDefault();
    const payload = {
      claimNumber: Number(claimForm.claimNumber),
      description: claimForm.description,
      amount: Number(claimForm.amount),
      scheduledDate: new Date(claimForm.scheduledDate).toISOString(),
      percentComplete: Number(claimForm.percentComplete),
    };
    if (editingClaim) {
      updateClaimMut.mutate({ claimId: editingClaim.id, payload });
    } else {
      createClaimMut.mutate(payload);
    }
  }

  function confirmDelete(type: 'entry' | 'claim', targetId: string) {
    setDeleteTarget({ type, id: targetId });
    setShowDeleteConfirm(true);
  }

  function executeDelete() {
    if (!deleteTarget) return;
    if (deleteTarget.type === 'entry') {
      deleteEntryMut.mutate(deleteTarget.id);
    } else {
      deleteClaimMut.mutate(deleteTarget.id);
    }
  }

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  // ─── Data Processing ──────────────────────
  const allEntries = cashflow ?? [];
  const filteredEntries = filter === 'ALL'
    ? allEntries
    : allEntries.filter((e) => getEntryType(e) === filter);

  const totalIncome = filteredEntries.reduce((s, e) => s + getIncome(e), 0);
  const totalExpense = filteredEntries.reduce((s, e) => s + getExpense(e), 0);
  const netPosition = totalIncome - totalExpense;

  // Build chart data with cumulative computation
  let cumulative = 0;
  const chartData = filteredEntries.map((e) => {
    const income = getIncome(e);
    const expense = getExpense(e);
    cumulative += income - expense;
    const monthStr = getEntryMonth(e);
    const d = new Date(monthStr);
    const label = `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}`;
    return {
      month: label,
      Income: income,
      Expense: expense,
      Cumulative: cumulative,
    };
  });

  // Cash flow alert: check if cumulative goes negative in any future month
  let alertMonth: string | null = null;
  const today = new Date();
  let runningCumulative = 0;
  for (const e of allEntries) {
    const income = getIncome(e);
    const expense = getExpense(e);
    runningCumulative += income - expense;
    const monthDate = new Date(getEntryMonth(e));
    if (monthDate > today && runningCumulative < 0) {
      const d = monthDate;
      alertMonth = `${d.toLocaleString('en-AU', { month: 'long' })} ${d.getFullYear()}`;
      break;
    }
  }

  const entryPending = createEntryMut.isPending || updateEntryMut.isPending;
  const claimPending = createClaimMut.isPending || updateClaimMut.isPending;

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
          <h2 className="text-xl font-bold text-gray-900">Cash Flow</h2>
          <div className="flex gap-2">
            <Button size="sm" variant="secondary" onClick={() => forecastMut.mutate()} disabled={forecastMut.isPending}>
              <TrendingUp className="h-4 w-4" />
              {forecastMut.isPending ? 'Generating…' : 'Generate Forecast'}
            </Button>
            <Button size="sm" onClick={openCreateEntry}>
              <Plus className="h-4 w-4" /> Add Entry
            </Button>
          </div>
        </div>
      </div>

      {/* Cash Flow Alert */}
      {alertMonth && (
        <div className="flex items-center gap-3 rounded-lg border border-warning bg-warning-50 px-4 py-3">
          <AlertTriangle className="h-5 w-5 text-warning" />
          <p className="text-sm text-warning-600">
            Cash flow goes negative in {alertMonth}. Review upcoming expenses.
          </p>
        </div>
      )}

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

      {/* Filter Toggle */}
      <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
        {(['ALL', 'ACTUAL', 'FORECAST'] as FilterType[]).map((f) => (
          <button
            key={f}
            onClick={() => setFilter(f)}
            className={cn(
              'rounded-md px-4 py-2 text-sm font-medium transition-colors',
              filter === f
                ? 'bg-white text-gray-900 shadow-sm'
                : 'text-gray-500 hover:text-gray-700',
            )}
          >
            {f === 'ALL' ? 'All' : f === 'ACTUAL' ? 'Actual' : 'Forecast'}
          </button>
        ))}
      </div>

      {/* S-Curve Chart */}
      {chartData.length > 0 && (
        <Card title="S-Curve: Income vs Expense">
          <div className="h-72">
            <ResponsiveContainer width="100%" height="100%">
              <LineChart data={chartData}>
                <CartesianGrid strokeDasharray="3 3" />
                <XAxis dataKey="month" tick={{ fontSize: 12 }} />
                <YAxis tick={{ fontSize: 12 }} />
                <Tooltip formatter={(v: number) => formatCurrency(v)} />
                <Legend />
                <Line type="monotone" dataKey="Income" stroke="#16A34A" strokeWidth={2} />
                <Line type="monotone" dataKey="Expense" stroke="#DC2626" strokeWidth={2} />
                <Line type="monotone" dataKey="Cumulative" stroke="#1E40AF" strokeWidth={2} strokeDasharray="5 5" />
              </LineChart>
            </ResponsiveContainer>
          </div>
        </Card>
      )}

      {/* Monthly Cash Flow Table */}
      <Card title="Monthly Cash Flow">
        {filteredEntries.length === 0 ? (
          <p className="py-4 text-center text-sm text-gray-500">No cash flow data</p>
        ) : (
          <Table headers={['Month', 'Type', 'Income', 'Expense', 'Net', 'Actions']}>
            {filteredEntries.map((e) => {
              const income = getIncome(e);
              const expense = getExpense(e);
              const net = income - expense;
              const d = new Date(getEntryMonth(e));
              const label = `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}`;
              return (
                <tr
                  key={e.id}
                  className="cursor-pointer hover:bg-gray-50"
                  onClick={() => openEditEntry(e)}
                >
                  <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{label}</td>
                  <td className="px-4 py-3">
                    <Badge status={getEntryType(e) === 'FORECAST' ? 'upcoming' : 'active'} />
                  </td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-success">{formatCurrency(income)}</td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-danger">{formatCurrency(expense)}</td>
                  <td className={cn('whitespace-nowrap px-4 py-3 text-sm font-medium', net >= 0 ? 'text-success' : 'text-danger')}>
                    {formatCurrency(net)}
                  </td>
                  <td className="px-4 py-3">
                    <button
                      onClick={(ev) => { ev.stopPropagation(); openEditEntry(e); }}
                      className="rounded p-1 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                    >
                      <Pencil className="h-4 w-4" />
                    </button>
                  </td>
                </tr>
              );
            })}
          </Table>
        )}
      </Card>

      {/* Claim Schedule */}
      <Card title="Claim Schedule">
        <div className="mb-4 flex justify-end">
          <Button size="sm" onClick={openCreateClaim}>
            <Plus className="h-4 w-4" /> Add Claim
          </Button>
        </div>
        {(!claims || claims.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No claims scheduled</p>
        ) : (
          <Table headers={['Claim #', 'Description', 'Amount', 'Due Date', '% Complete', 'Status', 'Actions']}>
            {claims.map((c) => {
              const dateStr = (c as any).scheduledDate ?? c.dueDate ?? '';
              const d = new Date(dateStr);
              const dateLabel = d.toLocaleDateString('en-AU', { day: '2-digit', month: 'short', year: 'numeric' });
              return (
                <tr
                  key={c.id}
                  className="cursor-pointer hover:bg-gray-50"
                  onClick={() => openEditClaim(c)}
                >
                  <td className="whitespace-nowrap px-4 py-3 text-sm font-mono">#{c.claimNumber}</td>
                  <td className="px-4 py-3 text-sm text-gray-900">{c.description}</td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm font-medium">{formatCurrency(Number(c.amount))}</td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{dateLabel}</td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                    {Number((c as any).percentComplete ?? 0)}%
                  </td>
                  <td className="px-4 py-3">
                    <Badge status={((c as any).status ?? c.status ?? 'scheduled').toLowerCase()} />
                  </td>
                  <td className="px-4 py-3">
                    <button
                      onClick={(ev) => { ev.stopPropagation(); openEditClaim(c); }}
                      className="rounded p-1 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                    >
                      <Pencil className="h-4 w-4" />
                    </button>
                  </td>
                </tr>
              );
            })}
          </Table>
        )}
      </Card>

      {/* ─── Entry Modal (Create / Edit) ──────── */}
      <Modal
        open={showEntryModal}
        onClose={resetEntryModal}
        title={editingEntry ? 'Edit Cash Flow Entry' : 'Add Cash Flow Entry'}
      >
        <form onSubmit={handleEntrySubmit} className="space-y-4">
          <Input
            id="entry-month"
            label="Month"
            type="month"
            required
            value={entryForm.month}
            onChange={(e) => setEntryForm({ ...entryForm, month: e.target.value })}
          />
          <Input
            id="entry-income"
            label="Income Amount"
            type="number"
            required
            min={0}
            step="0.01"
            value={entryForm.incomeAmount}
            onChange={(e) => setEntryForm({ ...entryForm, incomeAmount: e.target.value })}
          />
          <Input
            id="entry-expense"
            label="Expense Amount"
            type="number"
            required
            min={0}
            step="0.01"
            value={entryForm.expenseAmount}
            onChange={(e) => setEntryForm({ ...entryForm, expenseAmount: e.target.value })}
          />
          <div className="w-full">
            <label htmlFor="entry-type" className="mb-1 block text-sm font-medium text-gray-700">
              Type
            </label>
            <select
              id="entry-type"
              value={entryForm.type}
              onChange={(e) => setEntryForm({ ...entryForm, type: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="ACTUAL">Actual</option>
              <option value="FORECAST">Forecast</option>
            </select>
          </div>
          <div className="w-full">
            <label htmlFor="entry-notes" className="mb-1 block text-sm font-medium text-gray-700">
              Notes
            </label>
            <textarea
              id="entry-notes"
              rows={3}
              value={entryForm.notes}
              onChange={(e) => setEntryForm({ ...entryForm, notes: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            />
          </div>
          <div className="flex items-center justify-between pt-2">
            <div>
              {editingEntry && (
                <Button
                  type="button"
                  variant="danger"
                  size="sm"
                  onClick={() => confirmDelete('entry', editingEntry.id)}
                >
                  <Trash2 className="h-4 w-4" /> Delete
                </Button>
              )}
            </div>
            <div className="flex gap-2">
              <Button variant="secondary" type="button" onClick={resetEntryModal}>
                Cancel
              </Button>
              <Button type="submit" disabled={entryPending}>
                {entryPending ? 'Saving…' : 'Save'}
              </Button>
            </div>
          </div>
        </form>
      </Modal>

      {/* ─── Claim Modal (Create / Edit) ──────── */}
      <Modal
        open={showClaimModal}
        onClose={resetClaimModal}
        title={editingClaim ? 'Edit Claim' : 'Add Claim'}
      >
        <form onSubmit={handleClaimSubmit} className="space-y-4">
          <Input
            id="claim-number"
            label="Claim Number"
            type="number"
            required
            min={1}
            value={claimForm.claimNumber}
            onChange={(e) => setClaimForm({ ...claimForm, claimNumber: e.target.value })}
          />
          <Input
            id="claim-description"
            label="Description"
            required
            value={claimForm.description}
            onChange={(e) => setClaimForm({ ...claimForm, description: e.target.value })}
          />
          <Input
            id="claim-amount"
            label="Amount"
            type="number"
            required
            min={0}
            step="0.01"
            value={claimForm.amount}
            onChange={(e) => setClaimForm({ ...claimForm, amount: e.target.value })}
          />
          <Input
            id="claim-date"
            label="Scheduled Date"
            type="date"
            required
            value={claimForm.scheduledDate}
            onChange={(e) => setClaimForm({ ...claimForm, scheduledDate: e.target.value })}
          />
          <Input
            id="claim-percent"
            label="Percent Complete"
            type="number"
            required
            min={0}
            max={100}
            value={claimForm.percentComplete}
            onChange={(e) => setClaimForm({ ...claimForm, percentComplete: e.target.value })}
          />
          <div className="flex items-center justify-between pt-2">
            <div>
              {editingClaim && (
                <Button
                  type="button"
                  variant="danger"
                  size="sm"
                  onClick={() => confirmDelete('claim', editingClaim.id)}
                >
                  <Trash2 className="h-4 w-4" /> Delete
                </Button>
              )}
            </div>
            <div className="flex gap-2">
              <Button variant="secondary" type="button" onClick={resetClaimModal}>
                Cancel
              </Button>
              <Button type="submit" disabled={claimPending}>
                {claimPending ? 'Saving…' : 'Save'}
              </Button>
            </div>
          </div>
        </form>
      </Modal>

      {/* ─── Delete Confirmation Modal ─────────── */}
      <Modal
        open={showDeleteConfirm}
        onClose={() => { setShowDeleteConfirm(false); setDeleteTarget(null); }}
        title="Confirm Delete"
      >
        <p className="text-sm text-gray-600">
          Are you sure you want to delete this {deleteTarget?.type === 'entry' ? 'cash flow entry' : 'claim'}? This action cannot be undone.
        </p>
        <div className="flex justify-end gap-2 pt-4">
          <Button variant="secondary" onClick={() => { setShowDeleteConfirm(false); setDeleteTarget(null); }}>
            Cancel
          </Button>
          <Button
            variant="danger"
            onClick={executeDelete}
            disabled={deleteEntryMut.isPending || deleteClaimMut.isPending}
          >
            {(deleteEntryMut.isPending || deleteClaimMut.isPending) ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>
    </div>
  );
}

```

## web/src/pages/planning/PlanningPage.tsx

```tsx
import { useState, useMemo, Component, type FormEvent, type ErrorInfo, type ReactNode } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import {
  ArrowLeft,
  Plus,
  CalendarDays,
  BarChart3,
  List,
  ZoomIn,
  ZoomOut,
  Calendar,
  Users,
  Clock,
  ChevronLeft,
  ChevronRight,
  Flag,
  Trash2,
  X,
  GripVertical,
} from 'lucide-react';
import { getTasks, createTask, updateTask, deleteTask, addDependency, removeDependency, getCriticalPath, setBaseline, logDelay, reorderTasks } from '@/api/tasks';
import type { Task } from '@/types';
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

const STATUS_FILTERS = ['all', 'NOT_STARTED', 'IN_PROGRESS', 'COMPLETE', 'ON_HOLD', 'DELAYED'] as const;

const STATUS_OPTIONS = ['NOT_STARTED', 'IN_PROGRESS', 'COMPLETE', 'ON_HOLD', 'DELAYED'] as const;

const PRIORITY_OPTIONS = ['LOW', 'MEDIUM', 'HIGH', 'CRITICAL'] as const;

const DEPENDENCY_TYPES = ['FINISH_TO_START', 'START_TO_START', 'FINISH_TO_FINISH', 'START_TO_FINISH'] as const;

const DELAY_REASONS = [
  'WEATHER',
  'MATERIAL_DELAY',
  'TRADIE_NO_SHOW',
  'INSPECTION_FAIL',
  'CLIENT_CHANGE',
  'PERMIT_DELAY',
  'OTHER',
] as const;

const STAGE_OPTIONS = [
  'INITIATION', 'DESIGN', 'APPROVALS', 'SITE_PREP', 'BASE', 'FRAME',
  'LOCK_UP', 'FIT_OFF', 'FIXING', 'COMPLETION', 'HANDOVER', 'DEFECT_LIABILITY',
] as const;

const INITIAL_FORM = {
  name: '',
  description: '',
  startDate: '',
  endDate: '',
  priority: '',
  isMilestone: false,
};

const INITIAL_DELAY_FORM = {
  taskId: '',
  reason: '' as string,
  description: '',
  delayDays: 1,
};

class PlanningErrorBoundary extends Component<{ children: ReactNode }, { error: Error | null }> {
  state = { error: null as Error | null };
  static getDerivedStateFromError(error: Error) { return { error }; }
  componentDidCatch(error: Error, info: ErrorInfo) { console.error('PlanningPage crash:', error, info); }
  render() {
    if (this.state.error) {
      return (
        <div className="p-8 text-center">
          <h2 className="text-xl font-bold text-red-600 mb-2">Planning Page Error</h2>
          <pre className="text-left bg-red-50 p-4 rounded-lg text-sm text-red-800 overflow-auto max-w-2xl mx-auto">
            {this.state.error.message}{'\n'}{this.state.error.stack}
          </pre>
        </div>
      );
    }
    return this.props.children;
  }
}

export default function PlanningPageWrapper() {
  return <PlanningErrorBoundary><PlanningPageInner /></PlanningErrorBoundary>;
}

function PlanningPageInner() {
  const { id } = useParams<{ id: string }>();
  const [statusFilter, setStatusFilter] = useState<string>('all');
  const [view, setView] = useState<'gantt' | 'list' | 'calendar' | 'resources'>('gantt');
  const ZOOM_LEVELS = [8, 14, 20, 28, 40, 56, 80];
  const [zoomIdx, setZoomIdx] = useState(3);
  const [showCreate, setShowCreate] = useState(false);
  const [showDelay, setShowDelay] = useState(false);
  const [form, setForm] = useState(INITIAL_FORM);
  const [delayForm, setDelayForm] = useState(INITIAL_DELAY_FORM);
  const [editingTask, setEditingTask] = useState<Task | null>(null);
  const [editForm, setEditForm] = useState({
    name: '',
    description: '',
    startDate: '',
    endDate: '',
    status: '',
    priority: '',
    stage: '',
    isMilestone: false,
    assignedTradieId: '',
    colour: '',
    progress: 0,
  });
  const [confirmDelete, setConfirmDelete] = useState(false);
  const [reorderDragId, setReorderDragId] = useState<string | null>(null);
  const [depForm, setDepForm] = useState({ predecessorId: '', type: 'FINISH_TO_START', lagDays: 0 });
  const [calMonth, setCalMonth] = useState(() => {
    const now = new Date();
    return new Date(now.getFullYear(), now.getMonth(), 1);
  });

  const queryClient = useQueryClient();

  const { mutate, isPending } = useMutation({
    mutationFn: (payload: Record<string, unknown>) => createTask(id!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setShowCreate(false);
      setForm(INITIAL_FORM);
    },
  });

  const baselineMutation = useMutation({
    mutationFn: () => setBaseline(id!),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
    },
  });

  const delayMutation = useMutation({
    mutationFn: (payload: typeof INITIAL_DELAY_FORM) =>
      logDelay(id!, {
        taskId: payload.taskId || undefined,
        reason: payload.reason,
        description: payload.description || undefined,
        delayDays: payload.delayDays,
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setShowDelay(false);
      setDelayForm(INITIAL_DELAY_FORM);
    },
  });

  const updateTaskMutation = useMutation({
    mutationFn: ({ taskId, ...updates }: { taskId: string; [key: string]: unknown }) =>
      updateTask(id!, taskId, updates),
    onMutate: async ({ taskId, ...updates }) => {
      await queryClient.cancelQueries({ queryKey: ['tasks', id] });
      const prevQueries = queryClient.getQueriesData<Task[]>({ queryKey: ['tasks', id] });
      queryClient.setQueriesData<Task[]>({ queryKey: ['tasks', id] }, (old) =>
        old?.map((t) => (t.id === taskId ? { ...t, ...updates } : t)),
      );
      return { prevQueries };
    },
    onError: (_err, _vars, ctx) => {
      ctx?.prevQueries?.forEach(([key, data]) => queryClient.setQueryData(key, data));
    },
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      queryClient.invalidateQueries({ queryKey: ['critical-path', id] });
    },
  });

  const reorderMutation = useMutation({
    mutationFn: (orderedIds: string[]) => reorderTasks(id!, orderedIds),
    onMutate: async (orderedIds) => {
      await queryClient.cancelQueries({ queryKey: ['tasks', id] });
      const prevQueries = queryClient.getQueriesData<Task[]>({ queryKey: ['tasks', id] });
      queryClient.setQueriesData<Task[]>({ queryKey: ['tasks', id] }, (old) => {
        if (!old) return old;
        const map = new Map(old.map((t) => [t.id, t]));
        return orderedIds.map((tid, i) => {
          const t = map.get(tid);
          return t ? { ...t, sortOrder: i } : t!;
        }).filter(Boolean);
      });
      return { prevQueries };
    },
    onError: (_err, _vars, ctx) => {
      ctx?.prevQueries?.forEach(([key, data]) => queryClient.setQueryData(key, data));
    },
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
    },
  });

  const editMutation = useMutation({
    mutationFn: (payload: Record<string, unknown>) => updateTask(id!, editingTask!.id, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setEditingTask(null);
    },
  });

  const deleteMutation = useMutation({
    mutationFn: () => deleteTask(id!, editingTask!.id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setEditingTask(null);
      setConfirmDelete(false);
    },
  });

  const addDepMutation = useMutation({
    mutationFn: (payload: { predecessorId: string; successorId: string; type: string; lagDays: number }) =>
      addDependency(payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
      setDepForm({ predecessorId: '', type: 'FINISH_TO_START', lagDays: 0 });
    },
  });

  const removeDepMutation = useMutation({
    mutationFn: (depId: string) => removeDependency(depId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks', id] });
    },
  });

  const openEditModal = (task: Task) => {
    setEditingTask(task);
    setEditForm({
      name: task.name,
      description: task.description ?? '',
      startDate: task.startDate ? task.startDate.slice(0, 10) : '',
      endDate: task.endDate ? task.endDate.slice(0, 10) : '',
      status: task.status?.toUpperCase() ?? 'NOT_STARTED',
      priority: task.priority?.toUpperCase() ?? 'MEDIUM',
      stage: task.stage ?? '',
      isMilestone: task.isMilestone,
      assignedTradieId: task.assignedTradieId ?? '',
      colour: task.colour ?? '#3b82f6',
      progress: task.progress ?? task.percentComplete ?? 0,
    });
    setConfirmDelete(false);
    setDepForm({ predecessorId: '', type: 'FINISH_TO_START', lagDays: 0 });
  };

  const handleEditSubmit = (e: FormEvent) => {
    e.preventDefault();
    const payload: Record<string, unknown> = {
      name: editForm.name,
      description: editForm.description || undefined,
      startDate: editForm.startDate ? new Date(editForm.startDate).toISOString() : undefined,
      endDate: editForm.endDate ? new Date(editForm.endDate).toISOString() : undefined,
      status: editForm.status,
      priority: editForm.priority,
      stage: editForm.stage || undefined,
      isMilestone: editForm.isMilestone,
      percentComplete: editForm.progress,
      colour: editForm.colour || undefined,
    };
    if (editForm.assignedTradieId) payload.assignedTradieId = editForm.assignedTradieId;
    editMutation.mutate(payload);
  };

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

  const handleDelaySubmit = (e: FormEvent) => {
    e.preventDefault();
    delayMutation.mutate(delayForm);
  };

  const { data: tasks, isLoading } = useQuery({
    queryKey: ['tasks', id, statusFilter !== 'all' ? statusFilter : undefined],
    queryFn: () => getTasks(id!, { status: statusFilter !== 'all' ? statusFilter : undefined }),
    enabled: !!id,
  });

  const { data: criticalPathIds } = useQuery({
    queryKey: ['critical-path', id],
    queryFn: () => getCriticalPath(id!),
    enabled: !!id && view === 'gantt',
  });

  const allDependencies = useMemo(() => {
    if (!tasks) return [];
    return tasks.flatMap(t => t.predecessors ?? []);
  }, [tasks]);

  // Calendar view data
  const calendarDays = useMemo(() => {
    const year = calMonth.getFullYear();
    const month = calMonth.getMonth();
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);

    // Start from Monday
    let startDow = firstDay.getDay();
    if (startDow === 0) startDow = 7;
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - (startDow - 1));

    const days: { date: Date; inMonth: boolean; tasks: Task[] }[] = [];
    const cur = new Date(startDate);

    const totalCells = Math.max(35, Math.ceil((lastDay.getDate() + startDow - 1) / 7) * 7);
    for (let i = 0; i < totalCells; i++) {
      const d = new Date(cur);
      const inMonth = d.getMonth() === month;
      const dayTasks = (tasks ?? []).filter((t) => {
        if (!t.startDate) return false;
        const ts = new Date(t.startDate);
        return ts.getFullYear() === d.getFullYear() &&
          ts.getMonth() === d.getMonth() &&
          ts.getDate() === d.getDate();
      });
      days.push({ date: d, inMonth, tasks: dayTasks });
      cur.setDate(cur.getDate() + 1);
    }
    return days;
  }, [calMonth, tasks]);

  // Resource allocation data
  const resourceGroups = useMemo(() => {
    if (!tasks) return [];
    const groups: Record<string, { name: string; tasks: Task[] }> = {};
    for (const t of tasks) {
      const assignee = t.assignedTo ?? 'Unassigned';
      if (!groups[assignee]) groups[assignee] = { name: assignee, tasks: [] };
      groups[assignee].tasks.push(t);
    }
    return Object.values(groups).sort((a, b) => a.name.localeCompare(b.name));
  }, [tasks]);

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
          <div className="flex flex-wrap items-center gap-2">
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
              <button
                onClick={() => setView('calendar')}
                className={cn('rounded-md px-2.5 py-1 text-xs font-medium transition-colors', view === 'calendar' ? 'bg-primary text-white' : 'text-gray-500 hover:text-gray-700')}
              >
                <Calendar className="inline h-3.5 w-3.5 mr-1" />Calendar
              </button>
              <button
                onClick={() => setView('resources')}
                className={cn('rounded-md px-2.5 py-1 text-xs font-medium transition-colors', view === 'resources' ? 'bg-primary text-white' : 'text-gray-500 hover:text-gray-700')}
              >
                <Users className="inline h-3.5 w-3.5 mr-1" />Resources
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
            {view === 'gantt' && (
              <Button
                size="sm"
                variant="secondary"
                onClick={() => baselineMutation.mutate()}
                disabled={baselineMutation.isPending}
              >
                <Flag className="h-4 w-4" />
                {baselineMutation.isPending ? 'Setting…' : 'Set Baseline'}
              </Button>
            )}
            <Button size="sm" variant="secondary" onClick={() => setShowDelay(true)}>
              <Clock className="h-4 w-4" /> Log Delay
            </Button>
            <Button size="sm" onClick={() => setShowCreate(true)}>
              <Plus className="h-4 w-4" /> Add Task
            </Button>
          </div>
        </div>
      </div>

      {/* Gantt Chart */}
      {view === 'gantt' && tasks && tasks.length > 0 && (
        <GanttChart
          tasks={tasks}
          dependencies={allDependencies}
          dayWidth={ZOOM_LEVELS[zoomIdx]}
          criticalPathIds={criticalPathIds ?? []}
          canReorder={statusFilter === 'all'}
          onTaskClick={(t) => {
            const found = tasks?.find(task => task.id === t.id);
            if (found) openEditModal(found);
          }}
          onTaskUpdate={(taskId, updates) => updateTaskMutation.mutate({ taskId, ...updates })}
          onTaskReorder={(draggedId, targetId) => {
            if (!tasks) return;
            const ids = tasks.map((t) => t.id);
            const fromIdx = ids.indexOf(draggedId);
            const toIdx = ids.indexOf(targetId);
            if (fromIdx === -1 || toIdx === -1) return;
            ids.splice(fromIdx, 1);
            ids.splice(toIdx, 0, draggedId);
            reorderMutation.mutate(ids);
          }}
        />
      )}

      {/* Calendar View */}
      {view === 'calendar' && (
        <Card>
          <div className="flex items-center justify-between pb-4">
            <button
              onClick={() => setCalMonth((m) => new Date(m.getFullYear(), m.getMonth() - 1, 1))}
              className="flex h-8 w-8 items-center justify-center rounded-lg border border-gray-300 bg-white text-gray-600 hover:bg-gray-100"
            >
              <ChevronLeft className="h-4 w-4" />
            </button>
            <h3 className="text-sm font-semibold text-gray-900">
              {calMonth.toLocaleDateString('en-AU', { month: 'long', year: 'numeric' })}
            </h3>
            <button
              onClick={() => setCalMonth((m) => new Date(m.getFullYear(), m.getMonth() + 1, 1))}
              className="flex h-8 w-8 items-center justify-center rounded-lg border border-gray-300 bg-white text-gray-600 hover:bg-gray-100"
            >
              <ChevronRight className="h-4 w-4" />
            </button>
          </div>
          <div className="grid grid-cols-7 gap-px bg-gray-200 rounded-lg overflow-hidden">
            {['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'].map((d) => (
              <div key={d} className="bg-gray-50 px-2 py-1.5 text-center text-xs font-semibold text-gray-600">
                {d}
              </div>
            ))}
            {calendarDays.map((day, i) => {
              const isToday = day.date.toDateString() === new Date().toDateString();
              return (
                <div
                  key={i}
                  className={cn(
                    'min-h-[80px] bg-white p-1.5',
                    !day.inMonth && 'bg-gray-50',
                  )}
                >
                  <span
                    className={cn(
                      'inline-flex h-6 w-6 items-center justify-center rounded-full text-xs',
                      isToday ? 'bg-primary text-white font-bold' : day.inMonth ? 'text-gray-900' : 'text-gray-400',
                    )}
                  >
                    {day.date.getDate()}
                  </span>
                  <div className="mt-1 space-y-0.5">
                    {day.tasks.slice(0, 3).map((t) => (
                      <div
                        key={t.id}
                        className={cn(
                          'truncate rounded px-1 py-0.5 text-[10px] font-medium',
                          t.isMilestone
                            ? 'bg-purple-100 text-purple-700'
                            : 'bg-blue-100 text-blue-700',
                        )}
                        title={t.name}
                      >
                        {t.isMilestone && '◆ '}{t.name}
                      </div>
                    ))}
                    {day.tasks.length > 3 && (
                      <span className="text-[10px] text-gray-500">+{day.tasks.length - 3} more</span>
                    )}
                  </div>
                </div>
              );
            })}
          </div>
        </Card>
      )}

      {/* Resource Allocation View */}
      {view === 'resources' && (
        <>
          {resourceGroups.length === 0 ? (
            <EmptyState
              icon={<Users className="h-12 w-12" />}
              title="No task assignments"
              description="Assign tasks to team members to see resource allocation."
            />
          ) : (
            <div className="space-y-4">
              {resourceGroups.map((group) => (
                <Card key={group.name} title={group.name}>
                  <Table headers={['Task', 'Start', 'End', 'Status']}>
                    {group.tasks.map((t) => (
                      <tr key={t.id}>
                        <td className="px-4 py-2.5">
                          <span className="text-sm font-medium text-gray-900">{t.name}</span>
                        </td>
                        <td className="whitespace-nowrap px-4 py-2.5 text-sm text-gray-500">
                          {t.startDate ? formatDate(t.startDate) : '—'}
                        </td>
                        <td className="whitespace-nowrap px-4 py-2.5 text-sm text-gray-500">
                          {t.endDate ? formatDate(t.endDate) : '—'}
                        </td>
                        <td className="px-4 py-2.5">
                          <Badge status={t.status} />
                        </td>
                      </tr>
                    ))}
                  </Table>
                </Card>
              ))}
            </div>
          )}
        </>
      )}

      {/* Status Filters (for gantt + list) */}
      {(view === 'gantt' || view === 'list') && (
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
      )}

      {/* Task List (for gantt + list views) */}
      {(view === 'gantt' || view === 'list') && (
        <>
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
              <Table headers={['', 'Task', 'Stage', 'Start', 'End', 'Duration', 'Progress', 'Status', 'Assigned To']}>
                {tasks.map((task) => (
                  <tr
                    key={task.id}
                    onClick={() => openEditModal(task)}
                    className={cn(
                      'cursor-pointer hover:bg-gray-50',
                      task.isMilestone && 'bg-blue-50',
                      reorderDragId && reorderDragId !== task.id && 'transition-colors',
                    )}
                    onDragOver={(e) => { if (reorderDragId) e.preventDefault(); }}
                    onDrop={(e) => {
                      e.preventDefault();
                      if (reorderDragId && reorderDragId !== task.id && tasks) {
                        const ids = tasks.map((t) => t.id);
                        const fromIdx = ids.indexOf(reorderDragId);
                        const toIdx = ids.indexOf(task.id);
                        if (fromIdx !== -1 && toIdx !== -1) {
                          ids.splice(fromIdx, 1);
                          ids.splice(toIdx, 0, reorderDragId);
                          reorderMutation.mutate(ids);
                        }
                      }
                      setReorderDragId(null);
                    }}
                  >
                    <td className="w-8 px-1 py-3">
                      {statusFilter === 'all' && (
                        <div
                          draggable
                          onDragStart={(e) => { e.stopPropagation(); setReorderDragId(task.id); e.dataTransfer.effectAllowed = 'move'; }}
                          onDragEnd={() => setReorderDragId(null)}
                          className="cursor-grab text-gray-300 hover:text-gray-500 active:cursor-grabbing"
                          onClick={(e) => e.stopPropagation()}
                        >
                          <GripVertical className="h-4 w-4" />
                        </div>
                      )}
                    </td>
                    <td className="px-4 py-3">
                      <div className="flex items-center gap-2">
                        {task.isMilestone && (
                          <span className="text-xs font-medium text-blue-600">◆</span>
                        )}
                        <span className="text-sm font-medium text-gray-900">{task.name}</span>
                      </div>
                    </td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                      {task.stage ? task.stage.replace(/_/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase()) : '—'}
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
        </>
      )}

      {/* Create Task Modal */}
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

      {/* Log Delay Modal */}
      <Modal open={showDelay} onClose={() => setShowDelay(false)} title="Log Delay">
        <form onSubmit={handleDelaySubmit} className="space-y-4">
          <div className="w-full">
            <label htmlFor="delay-reason" className="mb-1 block text-sm font-medium text-gray-700">
              Reason
            </label>
            <select
              id="delay-reason"
              required
              value={delayForm.reason}
              onChange={(e) => setDelayForm({ ...delayForm, reason: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="">Select reason…</option>
              {DELAY_REASONS.map((r) => (
                <option key={r} value={r}>
                  {r.replace(/_/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase())}
                </option>
              ))}
            </select>
          </div>

          <div className="w-full">
            <label htmlFor="delay-task" className="mb-1 block text-sm font-medium text-gray-700">
              Affected Task
            </label>
            <select
              id="delay-task"
              value={delayForm.taskId}
              onChange={(e) => setDelayForm({ ...delayForm, taskId: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="">None (project-level)</option>
              {(tasks ?? []).map((t) => (
                <option key={t.id} value={t.id}>{t.name}</option>
              ))}
            </select>
          </div>

          <Input
            id="delay-days"
            label="Delay Days"
            type="number"
            required
            min={1}
            value={delayForm.delayDays}
            onChange={(e) => setDelayForm({ ...delayForm, delayDays: parseInt(e.target.value) || 1 })}
          />

          <div className="w-full">
            <label htmlFor="delay-description" className="mb-1 block text-sm font-medium text-gray-700">
              Description
            </label>
            <textarea
              id="delay-description"
              rows={3}
              value={delayForm.description}
              onChange={(e) => setDelayForm({ ...delayForm, description: e.target.value })}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            />
          </div>

          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" size="sm" onClick={() => setShowDelay(false)}>
              Cancel
            </Button>
            <Button type="submit" size="sm" disabled={!delayForm.reason || delayMutation.isPending}>
              {delayMutation.isPending ? 'Logging…' : 'Log Delay'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Edit Task Modal */}
      <Modal open={!!editingTask} onClose={() => setEditingTask(null)} title="Edit Task">
        {editingTask && (
          <form onSubmit={handleEditSubmit} className="space-y-4">
            <Input
              id="edit-name"
              label="Name"
              required
              value={editForm.name}
              onChange={(e) => setEditForm({ ...editForm, name: e.target.value })}
            />

            <div className="w-full">
              <label htmlFor="edit-description" className="mb-1 block text-sm font-medium text-gray-700">
                Description
              </label>
              <textarea
                id="edit-description"
                rows={3}
                value={editForm.description}
                onChange={(e) => setEditForm({ ...editForm, description: e.target.value })}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              />
            </div>

            <div className="grid grid-cols-2 gap-4">
              <Input
                id="edit-startDate"
                label="Start Date"
                type="date"
                value={editForm.startDate}
                onChange={(e) => setEditForm({ ...editForm, startDate: e.target.value })}
              />
              <Input
                id="edit-endDate"
                label="End Date"
                type="date"
                value={editForm.endDate}
                onChange={(e) => setEditForm({ ...editForm, endDate: e.target.value })}
              />
            </div>

            <div className="grid grid-cols-2 gap-4">
              <div className="w-full">
                <label htmlFor="edit-status" className="mb-1 block text-sm font-medium text-gray-700">
                  Status
                </label>
                <select
                  id="edit-status"
                  value={editForm.status}
                  onChange={(e) => setEditForm({ ...editForm, status: e.target.value })}
                  className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                >
                  {STATUS_OPTIONS.map((s) => (
                    <option key={s} value={s}>
                      {s.replace(/_/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase())}
                    </option>
                  ))}
                </select>
              </div>
              <div className="w-full">
                <label htmlFor="edit-priority" className="mb-1 block text-sm font-medium text-gray-700">
                  Priority
                </label>
                <select
                  id="edit-priority"
                  value={editForm.priority}
                  onChange={(e) => setEditForm({ ...editForm, priority: e.target.value })}
                  className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                >
                  {PRIORITY_OPTIONS.map((p) => (
                    <option key={p} value={p}>
                      {p.charAt(0) + p.slice(1).toLowerCase()}
                    </option>
                  ))}
                </select>
              </div>
            </div>

            <div className="w-full">
              <label htmlFor="edit-stage" className="mb-1 block text-sm font-medium text-gray-700">
                Stage
              </label>
              <select
                id="edit-stage"
                value={editForm.stage}
                onChange={(e) => setEditForm({ ...editForm, stage: e.target.value })}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                <option value="">None</option>
                {STAGE_OPTIONS.map((s) => (
                  <option key={s} value={s}>
                    {s.replace(/_/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase())}
                  </option>
                ))}
              </select>
            </div>

            <div className="w-full">
              <label htmlFor="edit-progress" className="mb-1 block text-sm font-medium text-gray-700">
                Progress: {editForm.progress}%
              </label>
              <input
                id="edit-progress"
                type="range"
                min={0}
                max={100}
                value={editForm.progress}
                onChange={(e) => setEditForm({ ...editForm, progress: parseInt(e.target.value) })}
                className="w-full accent-primary"
              />
            </div>

            <div className="grid grid-cols-2 gap-4">
              <Input
                id="edit-assignedTradieId"
                label="Assigned Tradie ID"
                value={editForm.assignedTradieId}
                onChange={(e) => setEditForm({ ...editForm, assignedTradieId: e.target.value })}
              />
              <div className="w-full">
                <label htmlFor="edit-colour" className="mb-1 block text-sm font-medium text-gray-700">
                  Colour
                </label>
                <input
                  id="edit-colour"
                  type="color"
                  value={editForm.colour}
                  onChange={(e) => setEditForm({ ...editForm, colour: e.target.value })}
                  className="h-9 w-full cursor-pointer rounded-lg border border-gray-300"
                />
              </div>
            </div>

            <label className="flex items-center gap-2 text-sm font-medium text-gray-700">
              <input
                type="checkbox"
                checked={editForm.isMilestone}
                onChange={(e) => setEditForm({ ...editForm, isMilestone: e.target.checked })}
                className="h-4 w-4 rounded border-gray-300 text-primary focus:ring-primary"
              />
              Milestone
            </label>

            {/* Dependencies Section */}
            <div className="border-t border-gray-200 pt-4">
              <h3 className="text-sm font-semibold text-gray-900 mb-2">Dependencies (Predecessors)</h3>
              {(editingTask.predecessors ?? []).length > 0 ? (
                <div className="space-y-2 mb-3">
                  {editingTask.predecessors!.map((dep) => (
                    <div key={dep.id} className="flex items-center justify-between rounded-lg border border-gray-200 px-3 py-2">
                      <div className="text-sm text-gray-900">
                        <span className="font-medium">{dep.predecessor?.name ?? dep.predecessorId}</span>
                        <span className="mx-2 text-gray-400">·</span>
                        <span className="text-xs text-gray-500">{dep.type.replace(/_/g, ' ')}</span>
                        {dep.lagDays !== 0 && (
                          <span className="ml-2 text-xs text-gray-500">({dep.lagDays > 0 ? '+' : ''}{dep.lagDays}d lag)</span>
                        )}
                      </div>
                      <button
                        type="button"
                        onClick={() => removeDepMutation.mutate(dep.id)}
                        className="text-gray-400 hover:text-red-500"
                        title="Remove dependency"
                      >
                        <X className="h-4 w-4" />
                      </button>
                    </div>
                  ))}
                </div>
              ) : (
                <p className="text-sm text-gray-500 mb-3">No predecessors.</p>
              )}

              <div className="flex items-end gap-2">
                <div className="flex-1">
                  <label htmlFor="dep-predecessor" className="mb-1 block text-sm font-medium text-gray-700">
                    Predecessor
                  </label>
                  <select
                    id="dep-predecessor"
                    value={depForm.predecessorId}
                    onChange={(e) => setDepForm({ ...depForm, predecessorId: e.target.value })}
                    className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                  >
                    <option value="">Select task…</option>
                    {(tasks ?? [])
                      .filter((t) => t.id !== editingTask.id)
                      .map((t) => (
                        <option key={t.id} value={t.id}>{t.name}</option>
                      ))}
                  </select>
                </div>
                <div className="w-36">
                  <label htmlFor="dep-type" className="mb-1 block text-sm font-medium text-gray-700">
                    Type
                  </label>
                  <select
                    id="dep-type"
                    value={depForm.type}
                    onChange={(e) => setDepForm({ ...depForm, type: e.target.value })}
                    className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                  >
                    {DEPENDENCY_TYPES.map((dt) => (
                      <option key={dt} value={dt}>
                        {dt.replace(/_/g, ' ')}
                      </option>
                    ))}
                  </select>
                </div>
                <div className="w-20">
                  <label htmlFor="dep-lag" className="mb-1 block text-sm font-medium text-gray-700">
                    Lag
                  </label>
                  <input
                    id="dep-lag"
                    type="number"
                    value={depForm.lagDays}
                    onChange={(e) => setDepForm({ ...depForm, lagDays: parseInt(e.target.value) || 0 })}
                    className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm transition-colors focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                  />
                </div>
                <Button
                  type="button"
                  size="sm"
                  disabled={!depForm.predecessorId || addDepMutation.isPending}
                  onClick={() =>
                    addDepMutation.mutate({
                      predecessorId: depForm.predecessorId,
                      successorId: editingTask.id,
                      type: depForm.type,
                      lagDays: depForm.lagDays,
                    })
                  }
                >
                  Add
                </Button>
              </div>
            </div>

            <div className="flex items-center justify-between border-t border-gray-200 pt-4">
              {confirmDelete ? (
                <div className="flex items-center gap-2">
                  <span className="text-sm text-red-600">Are you sure?</span>
                  <Button
                    type="button"
                    size="sm"
                    variant="secondary"
                    onClick={() => setConfirmDelete(false)}
                  >
                    No
                  </Button>
                  <Button
                    type="button"
                    size="sm"
                    className="bg-red-600 hover:bg-red-700 text-white"
                    onClick={() => deleteMutation.mutate()}
                    disabled={deleteMutation.isPending}
                  >
                    {deleteMutation.isPending ? 'Deleting…' : 'Yes, Delete'}
                  </Button>
                </div>
              ) : (
                <Button
                  type="button"
                  size="sm"
                  className="bg-red-600 hover:bg-red-700 text-white"
                  onClick={() => setConfirmDelete(true)}
                >
                  <Trash2 className="h-4 w-4" /> Delete Task
                </Button>
              )}

              <div className="flex gap-2">
                <Button type="button" variant="secondary" size="sm" onClick={() => setEditingTask(null)}>
                  Cancel
                </Button>
                <Button type="submit" size="sm" disabled={!editForm.name.trim() || editMutation.isPending}>
                  {editMutation.isPending ? 'Saving…' : 'Save Changes'}
                </Button>
              </div>
            </div>
          </form>
        )}
      </Modal>
    </div>
  );
}

```

## web/src/pages/inspections/InspectionsPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link, useNavigate } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, ClipboardCheck } from 'lucide-react';
import { getInspections, getDefects, createInspection, createDefect } from '@/api/inspections';
import type { Defect } from '@/types';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatDate } from '@/lib/utils';

const SEVERITY_FILTERS = ['all', 'MINOR', 'MODERATE', 'MAJOR', 'CRITICAL'] as const;
const STATUS_FILTERS = ['all', 'OPEN', 'ASSIGNED', 'IN_PROGRESS', 'RECTIFIED', 'VERIFIED', 'CLOSED'] as const;

const INITIAL_INSPECTION = { type: '', title: '', scheduledDate: '', inspectorName: '', notes: '' };
const INITIAL_DEFECT = { title: '', description: '', location: '', severity: '', dueDate: '' };

export default function InspectionsPage() {
  const { id } = useParams<{ id: string }>();
  const navigate = useNavigate();
  const queryClient = useQueryClient();

  const [inspOpen, setInspOpen] = useState(false);
  const [inspForm, setInspForm] = useState(INITIAL_INSPECTION);

  const [defectOpen, setDefectOpen] = useState(false);
  const [defectForm, setDefectForm] = useState(INITIAL_DEFECT);

  const [severityFilter, setSeverityFilter] = useState<string>('all');
  const [statusFilter, setStatusFilter] = useState<string>('all');

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
        severity: defectForm.severity as Defect['severity'],
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
          <Table headers={['Type', 'Title', 'Date', 'Inspector', 'Status', 'Result']}>
            {inspections.map((insp) => (
              <tr
                key={insp.id}
                className="cursor-pointer hover:bg-gray-50"
                onClick={() => navigate(`/projects/${id}/inspections/${insp.id}`)}
              >
                <td className="px-4 py-3">
                  <Badge status={insp.type} />
                </td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">
                  {insp.title ?? '—'}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatDate(insp.inspectionDate ?? insp.scheduledDate ?? insp.createdAt)}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {insp.inspectorName ?? insp.inspector ?? '—'}
                </td>
                <td className="px-4 py-3">
                  <Badge status={insp.status} />
                </td>
                <td className="px-4 py-3 text-sm text-gray-500">{insp.overallResult ?? insp.result ?? '—'}</td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Defects */}
      <Card title="Defects">
        {/* Defect Filters (Task 5.2.7) */}
        {defects && defects.length > 0 && (
          <div className="mb-4 space-y-2">
            <div>
              <p className="mb-1 text-xs font-medium text-gray-500">Severity</p>
              <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
                {SEVERITY_FILTERS.map((s) => (
                  <button
                    key={s}
                    onClick={() => setSeverityFilter(s)}
                    className={cn(
                      'whitespace-nowrap rounded-md px-3 py-1.5 text-xs font-medium capitalize transition-colors',
                      severityFilter === s
                        ? 'bg-white text-gray-900 shadow-sm'
                        : 'text-gray-500 hover:text-gray-700',
                    )}
                  >
                    {s === 'all' ? 'All' : s.replace(/_/g, ' ')}
                  </button>
                ))}
              </div>
            </div>
            <div>
              <p className="mb-1 text-xs font-medium text-gray-500">Status</p>
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
                    {s === 'all' ? 'All' : s.replace(/_/g, ' ')}
                  </button>
                ))}
              </div>
            </div>
          </div>
        )}

        {(!defects || defects.length === 0) ? (
          <p className="py-4 text-center text-sm text-gray-500">No defects recorded</p>
        ) : (() => {
          const filtered = defects.filter((d) => {
            if (severityFilter !== 'all' && d.severity?.toUpperCase() !== severityFilter) return false;
            if (statusFilter !== 'all' && d.status?.toUpperCase() !== statusFilter) return false;
            return true;
          });
          if (filtered.length === 0) {
            return <p className="py-4 text-center text-sm text-gray-500">No defects match filters</p>;
          }
          return (
            <Table headers={['Title', 'Severity', 'Status', 'Assigned To', 'Due Date']}>
              {filtered.map((d) => (
                <tr
                  key={d.id}
                  className="cursor-pointer hover:bg-gray-50"
                  onClick={() => navigate(`/projects/${id}/defects/${d.id}`)}
                >
                  <td className="px-4 py-3">
                    <p className="text-sm font-medium text-gray-900">{d.title}</p>
                    <p className="text-xs text-gray-500">{d.description}</p>
                  </td>
                  <td className="px-4 py-3"><Badge status={d.severity} /></td>
                  <td className="px-4 py-3"><Badge status={d.status} /></td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                    {d.assignedTradie?.companyName ?? d.assignedTo ?? '—'}
                  </td>
                  <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                    {d.dueDate ? formatDate(d.dueDate) : '—'}
                  </td>
                </tr>
              ))}
            </Table>
          );
        })()}
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

## web/src/pages/inspections/InspectionDetailPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link, useNavigate } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import {
  ArrowLeft,
  Plus,
  Pencil,
  Trash2,
  CheckCircle2,
  XCircle,
  MinusCircle,
  Circle,
  ClipboardCheck,
  AlertTriangle,
  FileText,
  Printer,
} from 'lucide-react';
import {
  getInspection,
  updateInspection,
  deleteInspection,
  createInspectionItem,
  createInspectionItemsBatch,
  updateInspectionItem,
} from '@/api/inspections';
import type { InspectionItem } from '@/types';
import Card from '@/components/ui/Card';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import Table from '@/components/ui/Table';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatDate } from '@/lib/utils';

// ─── NCC Standard Checklists (Task 5.1.6) ──────────────────
const NCC_TEMPLATES: Record<string, { category: string; description: string }[]> = {
  PRE_SLAB: [
    { category: 'Setout', description: 'Building setout matches approved plans' },
    { category: 'Setout', description: 'Boundary setbacks comply with DA conditions' },
    { category: 'Excavation', description: 'Excavation depth per engineering specifications' },
    { category: 'Excavation', description: 'Soil bearing capacity adequate' },
    { category: 'Reinforcement', description: 'Steel reinforcement size and spacing per engineer\'s design' },
    { category: 'Reinforcement', description: 'Reinforcement chairs and bar supports in place' },
    { category: 'Reinforcement', description: 'Lapped and tied reinforcement as specified' },
    { category: 'Moisture Barrier', description: 'Vapour barrier installed and sealed' },
    { category: 'Moisture Barrier', description: 'Termite protection system installed' },
    { category: 'Services', description: 'Plumbing penetrations correctly positioned' },
    { category: 'Services', description: 'Electrical conduits in place' },
    { category: 'Formwork', description: 'Edge formwork level and secure' },
    { category: 'Formwork', description: 'Step-downs and rebates as per plan' },
    { category: 'Drainage', description: 'Stormwater and sewer connections compliant' },
    { category: 'General', description: 'Site clean and safe for pour' },
  ],
  FRAME: [
    { category: 'Wall Frames', description: 'Wall frames plumb and straight' },
    { category: 'Wall Frames', description: 'Stud spacing per engineering specification' },
    { category: 'Wall Frames', description: 'Top and bottom plates correctly fixed' },
    { category: 'Wall Frames', description: 'Noggings installed at correct centres' },
    { category: 'Wall Frames', description: 'Lintels and headers over openings per engineer' },
    { category: 'Roof Trusses', description: 'Roof trusses installed per manufacturer\'s layout' },
    { category: 'Roof Trusses', description: 'Truss spacings as specified' },
    { category: 'Roof Trusses', description: 'Truss tie-downs and brackets installed' },
    { category: 'Bracing', description: 'Temporary bracing in place during construction' },
    { category: 'Bracing', description: 'Permanent bracing per engineer\'s bracing plan' },
    { category: 'Bracing', description: 'Cross-bracing in roof space installed' },
    { category: 'Connections', description: 'Hold-down bolts and connectors installed' },
    { category: 'Connections', description: 'Wall-to-floor connections per engineer' },
    { category: 'Openings', description: 'Window and door openings correct dimensions' },
    { category: 'Openings', description: 'Wet area frames have moisture barriers' },
    { category: 'Services', description: 'Plumbing rough-in inspected and approved' },
    { category: 'Services', description: 'Electrical rough-in inspected and approved' },
    { category: 'Services', description: 'HVAC ductwork rough-in complete' },
    { category: 'Insulation', description: 'Wall insulation installed per NCC energy provisions' },
    { category: 'General', description: 'All frame members free from damage and defects' },
  ],
  LOCK_UP: [
    { category: 'Roof Cladding', description: 'Roof sheeting/tiles installed per manufacturer specs' },
    { category: 'Roof Cladding', description: 'Ridge capping and flashings sealed' },
    { category: 'Roof Cladding', description: 'Gutters and downpipes installed and connected' },
    { category: 'Roof Cladding', description: 'Sarking/roof underlay installed' },
    { category: 'Windows', description: 'All windows installed and sealed' },
    { category: 'Windows', description: 'Window flashings installed correctly' },
    { category: 'Windows', description: 'Windows operate correctly (open, close, lock)' },
    { category: 'Doors', description: 'External doors installed, sealed, and lockable' },
    { category: 'Doors', description: 'Door hardware and weatherstripping installed' },
    { category: 'External Cladding', description: 'Wall cladding/brickwork complete and sealed' },
    { category: 'External Cladding', description: 'Weep holes and cavity flashings in place' },
    { category: 'External Cladding', description: 'External render/paint complete' },
    { category: 'Waterproofing', description: 'Wet area waterproofing complete with certificates' },
    { category: 'Waterproofing', description: 'Shower recesses waterproofed and tested' },
    { category: 'General', description: 'Building weather-tight and secure' },
  ],
};

const RESULT_CYCLE: InspectionItem['result'][] = ['NOT_INSPECTED', 'PASS', 'FAIL', 'NA'];

function resultIcon(result: string) {
  switch (result) {
    case 'PASS':
      return <CheckCircle2 className="h-5 w-5 text-success" />;
    case 'FAIL':
      return <XCircle className="h-5 w-5 text-danger" />;
    case 'NA':
      return <MinusCircle className="h-5 w-5 text-gray-400" />;
    default:
      return <Circle className="h-5 w-5 text-gray-300" />;
  }
}

export default function InspectionDetailPage() {
  const { id, inspectionId } = useParams<{ id: string; inspectionId: string }>();
  const navigate = useNavigate();
  const queryClient = useQueryClient();

  const [editOpen, setEditOpen] = useState(false);
  const [deleteOpen, setDeleteOpen] = useState(false);
  const [itemOpen, setItemOpen] = useState(false);
  const [templateOpen, setTemplateOpen] = useState(false);
  const [editForm, setEditForm] = useState({
    scheduledDate: '',
    inspectorName: '',
    status: '',
    notes: '',
    overallResult: '',
  });
  const [itemForm, setItemForm] = useState({ category: '', description: '' });

  const { data: inspection, isLoading } = useQuery({
    queryKey: ['inspection', inspectionId],
    queryFn: () => getInspection(inspectionId!),
    enabled: !!inspectionId,
  });

  const editMutation = useMutation({
    mutationFn: () =>
      updateInspection(id!, inspectionId!, {
        scheduledDate: editForm.scheduledDate ? new Date(editForm.scheduledDate).toISOString() : undefined,
        ...(editForm.inspectorName && { inspectorName: editForm.inspectorName }),
        ...(editForm.status && { status: editForm.status }),
        ...(editForm.notes !== undefined && { notes: editForm.notes }),
        ...(editForm.overallResult && { overallResult: editForm.overallResult }),
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['inspection', inspectionId] });
      setEditOpen(false);
    },
  });

  const deleteMutation = useMutation({
    mutationFn: () => deleteInspection(id!, inspectionId!),
    onSuccess: () => navigate(`/projects/${id}/inspections`),
  });

  const addItemMutation = useMutation({
    mutationFn: () => createInspectionItem(inspectionId!, itemForm),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['inspection', inspectionId] });
      setItemOpen(false);
      setItemForm({ category: '', description: '' });
    },
  });

  const templateMutation = useMutation({
    mutationFn: (type: string) =>
      createInspectionItemsBatch(inspectionId!, NCC_TEMPLATES[type]),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['inspection', inspectionId] });
      setTemplateOpen(false);
    },
  });

  const toggleItemMutation = useMutation({
    mutationFn: ({ itemId, result }: { itemId: string; result: string }) =>
      updateInspectionItem(itemId, { result }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['inspection', inspectionId] });
    },
  });

  const handleToggleResult = (item: InspectionItem) => {
    const currentIdx = RESULT_CYCLE.indexOf(item.result);
    const nextResult = RESULT_CYCLE[(currentIdx + 1) % RESULT_CYCLE.length];
    toggleItemMutation.mutate({ itemId: item.id, result: nextResult });
  };

  const openEdit = () => {
    if (!inspection) return;
    setEditForm({
      scheduledDate: inspection.scheduledDate
        ? new Date(inspection.scheduledDate).toISOString().split('T')[0]
        : '',
      inspectorName: inspection.inspectorName ?? inspection.inspector ?? '',
      status: inspection.status ?? '',
      notes: inspection.notes ?? '',
      overallResult: inspection.overallResult ?? '',
    });
    setEditOpen(true);
  };

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;
  if (!inspection) return <p className="py-20 text-center text-gray-500">Inspection not found</p>;

  const items = inspection.items ?? [];
  const defects = inspection.defects ?? [];

  // Group items by category
  const grouped = items.reduce<Record<string, InspectionItem[]>>((acc, item) => {
    const cat = item.category || 'General';
    if (!acc[cat]) acc[cat] = [];
    acc[cat].push(item);
    return acc;
  }, {});

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link
          to={`/projects/${id}/inspections`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Inspections
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <div>
            <h2 className="text-xl font-bold text-gray-900">{inspection.title || 'Inspection'}</h2>
            <div className="mt-1 flex items-center gap-2">
              <Badge status={inspection.type.toLowerCase()} />
              <Badge status={inspection.status?.toLowerCase() ?? 'scheduled'} />
              {inspection.overallResult && (
                <Badge status={inspection.overallResult.toLowerCase()} />
              )}
            </div>
          </div>
          <div className="flex gap-2">
            <Button size="sm" variant="secondary" onClick={() => window.print()}>
              <Printer className="h-4 w-4" /> Print Report
            </Button>
            <Button size="sm" variant="secondary" onClick={openEdit}>
              <Pencil className="h-4 w-4" /> Edit
            </Button>
            <Button size="sm" variant="danger" onClick={() => setDeleteOpen(true)}>
              <Trash2 className="h-4 w-4" /> Delete
            </Button>
          </div>
        </div>
      </div>

      {/* Info Card */}
      <Card title="Details">
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div>
            <p className="text-xs text-gray-500">Type</p>
            <p className="text-sm text-gray-900">{inspection.type.replace(/_/g, ' ')}</p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Scheduled Date</p>
            <p className="text-sm text-gray-900">
              {inspection.scheduledDate ? formatDate(inspection.scheduledDate) : '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Completed Date</p>
            <p className="text-sm text-gray-900">
              {inspection.completedDate ? formatDate(inspection.completedDate) : '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Inspector</p>
            <p className="text-sm text-gray-900">
              {inspection.inspectorName ?? inspection.inspector ?? '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Status</p>
            <Badge status={inspection.status?.toLowerCase() ?? 'scheduled'} />
          </div>
          <div>
            <p className="text-xs text-gray-500">Overall Result</p>
            <p className="text-sm text-gray-900">
              {inspection.overallResult ?? '—'}
            </p>
          </div>
          {inspection.notes && (
            <div className="sm:col-span-2 lg:col-span-3">
              <p className="text-xs text-gray-500">Notes</p>
              <p className="text-sm text-gray-900 whitespace-pre-wrap">{inspection.notes}</p>
            </div>
          )}
        </div>
      </Card>

      {/* Checklist Items */}
      <Card
        title={`Checklist Items (${items.length})`}
        action={
          <div className="flex gap-2">
            {Object.keys(NCC_TEMPLATES).includes(inspection.type) && (
              <Button size="sm" variant="secondary" onClick={() => setTemplateOpen(true)}>
                <FileText className="h-4 w-4" /> Use Template
              </Button>
            )}
            <Button size="sm" onClick={() => setItemOpen(true)}>
              <Plus className="h-4 w-4" /> Add Item
            </Button>
          </div>
        }
      >
        {items.length === 0 ? (
          <EmptyState
            icon={<ClipboardCheck className="h-12 w-12" />}
            title="No checklist items"
            description="Add items manually or use a template."
          />
        ) : (
          <div className="space-y-4">
            {Object.entries(grouped).map(([category, catItems]) => (
              <div key={category}>
                <h4 className="mb-2 text-xs font-semibold uppercase tracking-wider text-gray-500">
                  {category}
                </h4>
                <div className="space-y-1">
                  {catItems.map((item) => (
                    <div
                      key={item.id}
                      className="flex items-center gap-3 rounded-lg px-3 py-2 hover:bg-gray-50"
                    >
                      <button
                        onClick={() => handleToggleResult(item)}
                        className="shrink-0"
                        title={`Status: ${item.result} — click to cycle`}
                      >
                        {resultIcon(item.result)}
                      </button>
                      <div className="min-w-0 flex-1">
                        <p className={cn(
                          'text-sm',
                          item.result === 'PASS' && 'text-gray-500',
                          item.result === 'FAIL' && 'text-danger font-medium',
                          item.result === 'NOT_INSPECTED' && 'text-gray-900',
                          item.result === 'NA' && 'text-gray-400',
                        )}>
                          {item.description}
                        </p>
                        {item.notes && (
                          <p className="text-xs text-gray-500">{item.notes}</p>
                        )}
                      </div>
                      <span className="shrink-0 text-xs text-gray-400">{item.result.replace(/_/g, ' ')}</span>
                    </div>
                  ))}
                </div>
              </div>
            ))}
          </div>
        )}
      </Card>

      {/* Linked Defects */}
      <Card title={`Linked Defects (${defects.length})`}>
        {defects.length === 0 ? (
          <p className="py-4 text-center text-sm text-gray-500">No defects linked to this inspection</p>
        ) : (
          <Table headers={['Title', 'Severity', 'Status', 'Assigned To']}>
            {defects.map((d) => (
              <tr
                key={d.id}
                className="cursor-pointer hover:bg-gray-50"
                onClick={() => navigate(`/projects/${id}/defects/${d.id}`)}
              >
                <td className="px-4 py-3">
                  <p className="text-sm font-medium text-gray-900">{d.title}</p>
                </td>
                <td className="px-4 py-3"><Badge status={d.severity?.toLowerCase() ?? 'minor'} /></td>
                <td className="px-4 py-3"><Badge status={d.status?.toLowerCase() ?? 'open'} /></td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {d.assignedTradie?.companyName ?? d.assignedTo ?? '—'}
                </td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Edit Modal */}
      <Modal open={editOpen} onClose={() => setEditOpen(false)} title="Edit Inspection">
        <form
          className="space-y-4"
          onSubmit={(e) => { e.preventDefault(); editMutation.mutate(); }}
        >
          <Input
            id="editScheduledDate"
            label="Scheduled Date"
            type="date"
            value={editForm.scheduledDate}
            onChange={(e) => setEditForm({ ...editForm, scheduledDate: e.target.value })}
          />
          <Input
            id="editInspectorName"
            label="Inspector Name"
            value={editForm.inspectorName}
            onChange={(e) => setEditForm({ ...editForm, inspectorName: e.target.value })}
          />
          <div className="w-full">
            <label htmlFor="editStatus" className="mb-1 block text-sm font-medium text-gray-700">Status</label>
            <select
              id="editStatus"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={editForm.status}
              onChange={(e) => setEditForm({ ...editForm, status: e.target.value })}
            >
              <option value="">Select status</option>
              <option value="SCHEDULED">Scheduled</option>
              <option value="IN_PROGRESS">In Progress</option>
              <option value="COMPLETE">Complete</option>
              <option value="CANCELLED">Cancelled</option>
            </select>
          </div>
          <div className="w-full">
            <label htmlFor="editResult" className="mb-1 block text-sm font-medium text-gray-700">Overall Result</label>
            <select
              id="editResult"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={editForm.overallResult}
              onChange={(e) => setEditForm({ ...editForm, overallResult: e.target.value })}
            >
              <option value="">Select result</option>
              <option value="PASS">Pass</option>
              <option value="FAIL">Fail</option>
              <option value="CONDITIONAL">Conditional</option>
            </select>
          </div>
          <Input
            id="editNotes"
            label="Notes"
            value={editForm.notes}
            onChange={(e) => setEditForm({ ...editForm, notes: e.target.value })}
          />
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setEditOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={editMutation.isPending}>
              {editMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Delete Confirmation */}
      <Modal open={deleteOpen} onClose={() => setDeleteOpen(false)} title="Delete Inspection">
        <div className="space-y-4">
          <div className="flex items-start gap-3">
            <AlertTriangle className="h-6 w-6 shrink-0 text-danger" />
            <p className="text-sm text-gray-700">
              Are you sure you want to delete this inspection? This will also remove all checklist items. This action cannot be undone.
            </p>
          </div>
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setDeleteOpen(false)}>Cancel</Button>
            <Button variant="danger" onClick={() => deleteMutation.mutate()} disabled={deleteMutation.isPending}>
              {deleteMutation.isPending ? 'Deleting…' : 'Delete'}
            </Button>
          </div>
        </div>
      </Modal>

      {/* Add Item Modal */}
      <Modal open={itemOpen} onClose={() => setItemOpen(false)} title="Add Checklist Item">
        <form
          className="space-y-4"
          onSubmit={(e) => { e.preventDefault(); addItemMutation.mutate(); }}
        >
          <Input
            id="itemCategory"
            label="Category"
            value={itemForm.category}
            onChange={(e) => setItemForm({ ...itemForm, category: e.target.value })}
            required
          />
          <Input
            id="itemDescription"
            label="Description"
            value={itemForm.description}
            onChange={(e) => setItemForm({ ...itemForm, description: e.target.value })}
            required
          />
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setItemOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={addItemMutation.isPending}>
              {addItemMutation.isPending ? 'Adding…' : 'Add Item'}
            </Button>
          </div>
        </form>
      </Modal>

      {/* Print styles */}
      <style>{`
        @media print {
          body * { visibility: hidden; }
          .space-y-6, .space-y-6 * { visibility: visible; }
          .space-y-6 { position: absolute; left: 0; top: 0; width: 100%; }
          button, a { display: none !important; }
        }
      `}</style>

      {/* Use Template Modal */}
      <Modal open={templateOpen} onClose={() => setTemplateOpen(false)} title="Use NCC Standard Checklist">
        <div className="space-y-4">
          <p className="text-sm text-gray-500">
            Select a template to auto-populate checklist items based on NCC standards. Items will be added to the existing checklist.
          </p>
          <div className="space-y-2">
            {Object.entries(NCC_TEMPLATES).map(([type, templateItems]) => (
              <button
                key={type}
                onClick={() => templateMutation.mutate(type)}
                disabled={templateMutation.isPending}
                className="flex w-full items-center justify-between rounded-lg border border-gray-200 px-4 py-3 text-left hover:bg-gray-50"
              >
                <div>
                  <p className="text-sm font-medium text-gray-900">{type.replace(/_/g, ' ')}</p>
                  <p className="text-xs text-gray-500">{templateItems.length} items</p>
                </div>
                <Badge status={type.toLowerCase()} />
              </button>
            ))}
          </div>
          <div className="flex justify-end pt-2">
            <Button type="button" variant="secondary" onClick={() => setTemplateOpen(false)}>Cancel</Button>
          </div>
        </div>
      </Modal>
    </div>
  );
}

```

## web/src/pages/inspections/DefectDetailPage.tsx

```tsx
import { useState, useRef } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Pencil, CheckCircle2, Circle, Camera, Upload } from 'lucide-react';
import { getDefect, updateDefect } from '@/api/inspections';
import { uploadDocument } from '@/api/documents';
import { getTradies } from '@/api/tradies';
import Card from '@/components/ui/Card';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatDate } from '@/lib/utils';

const DEFECT_STATUSES = ['OPEN', 'ASSIGNED', 'IN_PROGRESS', 'RECTIFIED', 'VERIFIED', 'CLOSED'] as const;

const STATUS_BADGE_MAP: Record<string, string> = {
  OPEN: 'open',
  ASSIGNED: 'scheduled',
  IN_PROGRESS: 'in_progress',
  RECTIFIED: 'approved',
  VERIFIED: 'passed',
  CLOSED: 'closed',
};

export default function DefectDetailPage() {
  const { id, defectId } = useParams<{ id: string; defectId: string }>();
  const queryClient = useQueryClient();

  const photoInputRef = useRef<HTMLInputElement>(null);
  const rectPhotoInputRef = useRef<HTMLInputElement>(null);

  const [editOpen, setEditOpen] = useState(false);
  const [editForm, setEditForm] = useState({
    title: '',
    description: '',
    severity: '',
    status: '',
    assignedTradieId: '',
    dueDate: '',
  });

  const { data: defect, isLoading } = useQuery({
    queryKey: ['defect', defectId],
    queryFn: () => getDefect(defectId!),
    enabled: !!defectId,
  });

  const { data: tradies } = useQuery({
    queryKey: ['tradies'],
    queryFn: () => getTradies(),
  });

  const editMutation = useMutation({
    mutationFn: () => {
      const payload: Record<string, unknown> = {};
      if (editForm.title) payload.title = editForm.title;
      if (editForm.description !== undefined) payload.description = editForm.description;
      if (editForm.severity) payload.severity = editForm.severity;
      if (editForm.status) payload.status = editForm.status;
      if (editForm.assignedTradieId) payload.assignedTradieId = editForm.assignedTradieId;
      else payload.assignedTradieId = null;
      if (editForm.dueDate) payload.dueDate = new Date(editForm.dueDate).toISOString();
      return updateDefect(id!, defectId!, payload);
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['defect', defectId] });
      setEditOpen(false);
    },
  });

  // Photo upload for defect (Task 5.2.3)
  const photoUploadMutation = useMutation({
    mutationFn: async (file: File) => {
      const doc = await uploadDocument(id!, file, 'PHOTOS');
      const currentUrls: string[] = (() => { try { return JSON.parse(defect?.photoUrls || '[]'); } catch { return []; } })();
      const url = doc.fileUrl || doc.url;
      await updateDefect(id!, defectId!, { photoUrls: [...currentUrls, url] });
    },
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['defect', defectId] }),
  });

  // Photo upload for rectification (Task 5.2.4)
  const rectPhotoUploadMutation = useMutation({
    mutationFn: async (file: File) => {
      const doc = await uploadDocument(id!, file, 'PHOTOS');
      const currentUrls: string[] = (() => { try { return JSON.parse(defect?.rectificationPhotoUrls || '[]'); } catch { return []; } })();
      const url = doc.fileUrl || doc.url;
      await updateDefect(id!, defectId!, { rectificationPhotoUrls: [...currentUrls, url] });
    },
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['defect', defectId] }),
  });

  const handlePhotoUpload = (files: FileList | null, type: 'defect' | 'rectification') => {
    if (!files) return;
    Array.from(files).forEach((file) => {
      if (type === 'defect') photoUploadMutation.mutate(file);
      else rectPhotoUploadMutation.mutate(file);
    });
  };

  const openEdit = () => {
    if (!defect) return;
    setEditForm({
      title: defect.title ?? '',
      description: defect.description ?? '',
      severity: defect.severity?.toUpperCase() ?? '',
      status: defect.status?.toUpperCase() ?? '',
      assignedTradieId: defect.assignedTradieId ?? defect.tradieId ?? '',
      dueDate: defect.dueDate ? new Date(defect.dueDate).toISOString().split('T')[0] : '',
    });
    setEditOpen(true);
  };

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;
  if (!defect) return <p className="py-20 text-center text-gray-500">Defect not found</p>;

  const currentStatusIdx = DEFECT_STATUSES.indexOf(
    (defect.status?.toUpperCase() ?? 'OPEN') as typeof DEFECT_STATUSES[number],
  );

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link
          to={`/projects/${id}/inspections`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Inspections
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <div>
            <h2 className="text-xl font-bold text-gray-900">{defect.title}</h2>
            <div className="mt-1 flex items-center gap-2">
              <Badge status={defect.severity?.toLowerCase() ?? 'minor'} />
              <Badge status={STATUS_BADGE_MAP[defect.status?.toUpperCase() ?? 'OPEN'] ?? 'open'} />
            </div>
          </div>
          <Button size="sm" variant="secondary" onClick={openEdit}>
            <Pencil className="h-4 w-4" /> Edit
          </Button>
        </div>
      </div>

      {/* Status Timeline (Task 5.2.5) */}
      <Card title="Status Lifecycle">
        <div className="flex items-center gap-0 overflow-x-auto py-2">
          {DEFECT_STATUSES.map((s, i) => {
            const isComplete = i <= currentStatusIdx;
            const isCurrent = i === currentStatusIdx;
            return (
              <div key={s} className="flex items-center">
                <div className="flex flex-col items-center">
                  <div
                    className={cn(
                      'flex h-8 w-8 items-center justify-center rounded-full',
                      isCurrent
                        ? 'bg-primary text-white'
                        : isComplete
                          ? 'bg-success-50 text-success'
                          : 'bg-gray-100 text-gray-400',
                    )}
                  >
                    {isComplete ? (
                      <CheckCircle2 className="h-5 w-5" />
                    ) : (
                      <Circle className="h-5 w-5" />
                    )}
                  </div>
                  <span
                    className={cn(
                      'mt-1 whitespace-nowrap text-xs',
                      isCurrent ? 'font-semibold text-gray-900' : 'text-gray-500',
                    )}
                  >
                    {s.replace(/_/g, ' ')}
                  </span>
                </div>
                {i < DEFECT_STATUSES.length - 1 && (
                  <div
                    className={cn(
                      'mx-1 h-0.5 w-8 sm:w-12',
                      i < currentStatusIdx ? 'bg-success' : 'bg-gray-200',
                    )}
                  />
                )}
              </div>
            );
          })}
        </div>
      </Card>

      {/* Info Card */}
      <Card title="Details">
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div>
            <p className="text-xs text-gray-500">Severity</p>
            <Badge status={defect.severity?.toLowerCase() ?? 'minor'} />
          </div>
          <div>
            <p className="text-xs text-gray-500">Status</p>
            <Badge status={STATUS_BADGE_MAP[defect.status?.toUpperCase() ?? 'OPEN'] ?? 'open'} />
          </div>
          <div>
            <p className="text-xs text-gray-500">Assigned To</p>
            <p className="text-sm text-gray-900">
              {defect.assignedTradie?.companyName ?? defect.assignedTo ?? '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Due Date</p>
            <p className="text-sm text-gray-900">
              {defect.dueDate ? formatDate(defect.dueDate) : '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Location</p>
            <p className="text-sm text-gray-900">{defect.location ?? '—'}</p>
          </div>
          {defect.inspection && (
            <div>
              <p className="text-xs text-gray-500">Linked Inspection</p>
              <Link
                to={`/projects/${id}/inspections/${defect.inspectionId}`}
                className="text-sm text-primary hover:underline"
              >
                {defect.inspection.title ?? defect.inspection.type}
              </Link>
            </div>
          )}
          <div>
            <p className="text-xs text-gray-500">Rectified Date</p>
            <p className="text-sm text-gray-900">
              {defect.rectifiedDate ? formatDate(defect.rectifiedDate) : '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Verified Date</p>
            <p className="text-sm text-gray-900">
              {defect.verifiedDate ? formatDate(defect.verifiedDate) : '—'}
            </p>
          </div>
          <div>
            <p className="text-xs text-gray-500">Created</p>
            <p className="text-sm text-gray-900">{formatDate(defect.createdAt)}</p>
          </div>
          {defect.description && (
            <div className="sm:col-span-2 lg:col-span-3">
              <p className="text-xs text-gray-500">Description</p>
              <p className="text-sm text-gray-900 whitespace-pre-wrap">{defect.description}</p>
            </div>
          )}
        </div>
      </Card>

      {/* Defect Photos (Task 5.2.3) */}
      <Card title="Defect Photos">
        {(() => {
          const urls: string[] = (() => { try { return JSON.parse(defect.photoUrls || '[]'); } catch { return []; } })();
          return (
            <div className="space-y-3">
              {urls.length > 0 && (
                <div className="flex flex-wrap gap-3">
                  {urls.map((url, i) => (
                    <a key={i} href={url} target="_blank" rel="noopener noreferrer">
                      <img src={url} alt={`Defect photo ${i + 1}`} className="h-24 w-24 rounded-lg border object-cover hover:opacity-80" />
                    </a>
                  ))}
                </div>
              )}
              <div>
                <Button size="sm" variant="secondary" onClick={() => photoInputRef.current?.click()}>
                  <Camera className="h-4 w-4" /> Upload Photo
                </Button>
                <input
                  ref={photoInputRef}
                  type="file"
                  accept="image/*"
                  multiple
                  className="hidden"
                  onChange={(e) => handlePhotoUpload(e.target.files, 'defect')}
                />
              </div>
              {urls.length === 0 && (
                <p className="text-sm text-gray-500">No photos uploaded yet.</p>
              )}
            </div>
          );
        })()}
      </Card>

      {/* Rectification Photos (Task 5.2.4) */}
      <Card title="Rectification Photos">
        {(() => {
          const urls: string[] = (() => { try { return JSON.parse(defect.rectificationPhotoUrls || '[]'); } catch { return []; } })();
          return (
            <div className="space-y-3">
              {urls.length > 0 && (
                <div className="flex flex-wrap gap-3">
                  {urls.map((url, i) => (
                    <a key={i} href={url} target="_blank" rel="noopener noreferrer">
                      <img src={url} alt={`Rectification photo ${i + 1}`} className="h-24 w-24 rounded-lg border object-cover hover:opacity-80" />
                    </a>
                  ))}
                </div>
              )}
              <div>
                <Button size="sm" variant="secondary" onClick={() => rectPhotoInputRef.current?.click()}>
                  <Upload className="h-4 w-4" /> Upload Rectification Photo
                </Button>
                <input
                  ref={rectPhotoInputRef}
                  type="file"
                  accept="image/*"
                  multiple
                  className="hidden"
                  onChange={(e) => handlePhotoUpload(e.target.files, 'rectification')}
                />
              </div>
              {urls.length === 0 && (
                <p className="text-sm text-gray-500">No rectification photos uploaded yet.</p>
              )}
            </div>
          );
        })()}
      </Card>

      {/* Edit Modal */}
      <Modal open={editOpen} onClose={() => setEditOpen(false)} title="Edit Defect">
        <form
          className="space-y-4"
          onSubmit={(e) => { e.preventDefault(); editMutation.mutate(); }}
        >
          <Input
            id="editTitle"
            label="Title"
            value={editForm.title}
            onChange={(e) => setEditForm({ ...editForm, title: e.target.value })}
            required
          />
          <Input
            id="editDescription"
            label="Description"
            value={editForm.description}
            onChange={(e) => setEditForm({ ...editForm, description: e.target.value })}
          />
          <div className="w-full">
            <label htmlFor="editSeverity" className="mb-1 block text-sm font-medium text-gray-700">Severity</label>
            <select
              id="editSeverity"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={editForm.severity}
              onChange={(e) => setEditForm({ ...editForm, severity: e.target.value })}
            >
              <option value="MINOR">Minor</option>
              <option value="MODERATE">Moderate</option>
              <option value="MAJOR">Major</option>
              <option value="CRITICAL">Critical</option>
            </select>
          </div>
          <div className="w-full">
            <label htmlFor="editDefectStatus" className="mb-1 block text-sm font-medium text-gray-700">Status</label>
            <select
              id="editDefectStatus"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={editForm.status}
              onChange={(e) => setEditForm({ ...editForm, status: e.target.value })}
            >
              {DEFECT_STATUSES.map((s) => (
                <option key={s} value={s}>{s.replace(/_/g, ' ')}</option>
              ))}
            </select>
          </div>
          <div className="w-full">
            <label htmlFor="editTradie" className="mb-1 block text-sm font-medium text-gray-700">Assign to Tradie</label>
            <select
              id="editTradie"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={editForm.assignedTradieId}
              onChange={(e) => setEditForm({ ...editForm, assignedTradieId: e.target.value })}
            >
              <option value="">Unassigned</option>
              {tradies?.map((t) => (
                <option key={t.id} value={t.id}>
                  {t.companyName ?? t.company ?? `${t.firstName} ${t.lastName}`} — {t.trade}
                </option>
              ))}
            </select>
          </div>
          <Input
            id="editDueDate"
            label="Due Date"
            type="date"
            value={editForm.dueDate}
            onChange={(e) => setEditForm({ ...editForm, dueDate: e.target.value })}
          />
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setEditOpen(false)}>Cancel</Button>
            <Button type="submit" disabled={editMutation.isPending}>
              {editMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

## web/src/pages/invoices/InvoicesPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link, useNavigate } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Plus, FileText, X, Trash2 } from 'lucide-react';
import { getInvoices, createInvoice, deleteInvoice } from '@/api/invoices';
import type { InvoiceLineItem } from '@/api/invoices';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency, formatDate } from '@/lib/utils';

const UNITS = ['m', 'm²', 'hrs', 'ea', 'lot'] as const;

const emptyLineItem = (): InvoiceLineItem => ({
  description: '',
  quantity: 1,
  unit: 'ea',
  unitPrice: 0,
  amount: 0,
});

type InvoiceForm = {
  type: 'TAX_INVOICE' | 'PROGRESS_CLAIM';
  toName: string;
  toEmail: string;
  toAddress: string;
  dueDate: string;
  retentionPercent: number;
  lineItems: InvoiceLineItem[];
};

const initialForm: InvoiceForm = {
  type: 'TAX_INVOICE',
  toName: '',
  toEmail: '',
  toAddress: '',
  dueDate: new Date(Date.now() + 30 * 86400000).toISOString().slice(0, 10),
  retentionPercent: 0,
  lineItems: [emptyLineItem()],
};

export default function InvoicesPage() {
  const { id } = useParams<{ id: string }>();
  const navigate = useNavigate();
  const queryClient = useQueryClient();

  const [showCreate, setShowCreate] = useState(false);
  const [form, setForm] = useState<InvoiceForm>(initialForm);
  const [deleteTarget, setDeleteTarget] = useState<{ id: string; number: string } | null>(null);

  const { data: invoices, isLoading } = useQuery({
    queryKey: ['invoices', id],
    queryFn: () => getInvoices(id!),
    enabled: !!id,
  });

  const createMutation = useMutation({
    mutationFn: (payload: any) => createInvoice(id!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['invoices', id] });
      setShowCreate(false);
      setForm(initialForm);
    },
  });

  const deleteMutation = useMutation({
    mutationFn: (invoiceId: string) => deleteInvoice(invoiceId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['invoices', id] });
      setDeleteTarget(null);
    },
  });

  // Line item calculations
  const subtotal = form.lineItems.reduce((sum, li) => sum + li.amount, 0);
  const gstTotal = Math.round(subtotal * 0.1 * 100) / 100;
  const retentionAmount = form.retentionPercent > 0
    ? Math.round(subtotal * (form.retentionPercent / 100) * 100) / 100
    : 0;
  const total = subtotal + gstTotal - retentionAmount;

  const updateLineItem = (index: number, field: keyof InvoiceLineItem, value: any) => {
    setForm((f) => {
      const items = [...f.lineItems];
      items[index] = { ...items[index], [field]: value };
      if (field === 'quantity' || field === 'unitPrice') {
        items[index].amount = Math.round(items[index].quantity * items[index].unitPrice * 100) / 100;
      }
      return { ...f, lineItems: items };
    });
  };

  const addLineItem = () => {
    setForm((f) => ({ ...f, lineItems: [...f.lineItems, emptyLineItem()] }));
  };

  const removeLineItem = (index: number) => {
    setForm((f) => ({
      ...f,
      lineItems: f.lineItems.length > 1 ? f.lineItems.filter((_, i) => i !== index) : f.lineItems,
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    const now = new Date().toISOString();
    const invoiceNumber = `INV-${(id ?? '').slice(0, 4).toUpperCase()}-${Date.now().toString().slice(-6)}`;
    createMutation.mutate({
      invoiceNumber,
      type: form.type,
      toName: form.toName,
      toEmail: form.toEmail || undefined,
      toAddress: form.toAddress || undefined,
      subtotal,
      gstTotal,
      total,
      retentionPercent: form.retentionPercent || undefined,
      retentionAmount: retentionAmount || undefined,
      amountDue: total,
      issueDate: now,
      dueDate: new Date(form.dueDate).toISOString(),
      status: 'DRAFT',
      lineItems: form.lineItems,
    });
  };

  if (isLoading) {
    return (
      <div className="flex min-h-[400px] items-center justify-center">
        <LoadingSpinner size="lg" />
      </div>
    );
  }

  const items = invoices ?? [];

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link
          to={`/projects/${id}`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Project
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Invoices</h2>
          <Button size="sm" onClick={() => setShowCreate(true)}>
            <Plus className="h-4 w-4" /> Create Invoice
          </Button>
        </div>
      </div>

      {/* Invoice list */}
      {items.length === 0 ? (
        <Card>
          <EmptyState
            icon={<FileText className="h-12 w-12" />}
            title="No invoices yet"
            description="Create your first invoice to get started."
            action={
              <Button size="sm" onClick={() => setShowCreate(true)}>
                <Plus className="h-4 w-4" /> Create Invoice
              </Button>
            }
          />
        </Card>
      ) : (
        <Card>
          <Table headers={['Invoice #', 'Type', 'To', 'Amount Due', 'Due Date', 'Status', '']}>
            {items.map((inv) => (
              <tr
                key={inv.id}
                className="cursor-pointer hover:bg-gray-50"
                onClick={() => navigate(`/projects/${id}/invoices/${inv.id}`)}
              >
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium text-gray-900">
                  {inv.invoiceNumber}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {inv.type.replace(/_/g, ' ')}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {inv.toName}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium text-gray-900">
                  {formatCurrency(Number(inv.amountDue))}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatDate(inv.dueDate)}
                </td>
                <td className="px-4 py-3">
                  <Badge status={inv.status.toLowerCase()} />
                </td>
                <td className="px-4 py-3">
                  {inv.status === 'DRAFT' && (
                    <button
                      onClick={(e) => {
                        e.stopPropagation();
                        setDeleteTarget({ id: inv.id, number: inv.invoiceNumber });
                      }}
                      className="rounded p-1 text-gray-400 hover:bg-red-50 hover:text-danger"
                    >
                      <Trash2 className="h-4 w-4" />
                    </button>
                  )}
                </td>
              </tr>
            ))}
          </Table>
        </Card>
      )}

      {/* Delete confirmation modal */}
      <Modal
        open={!!deleteTarget}
        onClose={() => setDeleteTarget(null)}
        title="Delete Invoice"
      >
        <p className="text-sm text-gray-500">
          Are you sure you want to delete invoice <strong>{deleteTarget?.number}</strong>? This action cannot be undone.
        </p>
        <div className="flex justify-end gap-2 pt-4">
          <Button variant="secondary" onClick={() => setDeleteTarget(null)}>
            Cancel
          </Button>
          <Button
            variant="danger"
            onClick={() => deleteTarget && deleteMutation.mutate(deleteTarget.id)}
            disabled={deleteMutation.isPending}
          >
            {deleteMutation.isPending ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>

      {/* Create Invoice Modal (2.2.2 & 2.2.3) */}
      <Modal
        open={showCreate}
        onClose={() => setShowCreate(false)}
        title="Create Invoice"
        className="max-w-2xl"
      >
        <form onSubmit={handleSubmit} className="space-y-4">
          {/* Type */}
          <div>
            <label className="mb-1 block text-sm font-medium text-gray-700">Type</label>
            <select
              value={form.type}
              onChange={(e) => setForm((f) => ({ ...f, type: e.target.value as InvoiceForm['type'] }))}
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
            >
              <option value="TAX_INVOICE">Tax Invoice</option>
              <option value="PROGRESS_CLAIM">Progress Claim</option>
            </select>
          </div>

          {/* To fields */}
          <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
            <Input
              label="To (Name)"
              id="toName"
              required
              value={form.toName}
              onChange={(e) => setForm((f) => ({ ...f, toName: e.target.value }))}
            />
            <Input
              label="Email"
              id="toEmail"
              type="email"
              value={form.toEmail}
              onChange={(e) => setForm((f) => ({ ...f, toEmail: e.target.value }))}
            />
          </div>
          <Input
            label="Address"
            id="toAddress"
            value={form.toAddress}
            onChange={(e) => setForm((f) => ({ ...f, toAddress: e.target.value }))}
          />

          {/* Line items (2.2.3) */}
          <div>
            <label className="mb-2 block text-sm font-medium text-gray-700">Line Items</label>
            <div className="space-y-2">
              {form.lineItems.map((li, idx) => (
                <div key={idx} className="flex items-end gap-2">
                  <div className="flex-1">
                    {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Description</span>}
                    <input
                      required
                      value={li.description}
                      onChange={(e) => updateLineItem(idx, 'description', e.target.value)}
                      className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                    />
                  </div>
                  <div className="w-20">
                    {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Qty</span>}
                    <input
                      type="number"
                      min={0}
                      step="any"
                      required
                      value={li.quantity}
                      onChange={(e) => updateLineItem(idx, 'quantity', parseFloat(e.target.value) || 0)}
                      className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                    />
                  </div>
                  <div className="w-20">
                    {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Unit</span>}
                    <select
                      value={li.unit}
                      onChange={(e) => updateLineItem(idx, 'unit', e.target.value)}
                      className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                    >
                      {UNITS.map((u) => (
                        <option key={u} value={u}>{u}</option>
                      ))}
                    </select>
                  </div>
                  <div className="w-28">
                    {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Unit Price</span>}
                    <input
                      type="number"
                      min={0}
                      step="0.01"
                      required
                      value={li.unitPrice}
                      onChange={(e) => updateLineItem(idx, 'unitPrice', parseFloat(e.target.value) || 0)}
                      className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                    />
                  </div>
                  <div className="w-28">
                    {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Amount</span>}
                    <input
                      type="number"
                      readOnly
                      value={li.amount}
                      className="block w-full rounded-lg border border-gray-200 bg-gray-50 px-3 py-2 text-sm"
                    />
                  </div>
                  <button
                    type="button"
                    onClick={() => removeLineItem(idx)}
                    className="mb-0.5 rounded p-1.5 text-gray-400 hover:bg-red-50 hover:text-danger"
                  >
                    <X className="h-4 w-4" />
                  </button>
                </div>
              ))}
            </div>
            <Button type="button" variant="ghost" size="sm" className="mt-2" onClick={addLineItem}>
              <Plus className="h-4 w-4" /> Add Line Item
            </Button>
          </div>

          {/* Totals */}
          <div className="rounded-lg border border-gray-200 bg-gray-50 p-4">
            <div className="space-y-1 text-sm">
              <div className="flex justify-between">
                <span className="text-gray-500">Subtotal</span>
                <span className="font-medium text-gray-900">{formatCurrency(subtotal)}</span>
              </div>
              <div className="flex justify-between">
                <span className="text-gray-500">GST (10%)</span>
                <span className="font-medium text-gray-900">{formatCurrency(gstTotal)}</span>
              </div>
              <div className="flex items-center justify-between">
                <div className="flex items-center gap-2">
                  <span className="text-gray-500">Retention</span>
                  <input
                    type="number"
                    min={0}
                    max={100}
                    step="0.5"
                    value={form.retentionPercent}
                    onChange={(e) => setForm((f) => ({ ...f, retentionPercent: parseFloat(e.target.value) || 0 }))}
                    className="w-16 rounded border border-gray-300 px-2 py-1 text-sm"
                  />
                  <span className="text-gray-500">%</span>
                </div>
                <span className="font-medium text-gray-900">−{formatCurrency(retentionAmount)}</span>
              </div>
              <div className="flex justify-between border-t border-gray-200 pt-2">
                <span className="font-semibold text-gray-900">Total / Amount Due</span>
                <span className="font-semibold text-gray-900">{formatCurrency(total)}</span>
              </div>
            </div>
          </div>

          {/* Due date */}
          <Input
            label="Due Date"
            id="dueDate"
            type="date"
            required
            value={form.dueDate}
            onChange={(e) => setForm((f) => ({ ...f, dueDate: e.target.value }))}
          />

          <div className="flex justify-end gap-2 pt-2">
            <Button variant="secondary" type="button" onClick={() => setShowCreate(false)}>
              Cancel
            </Button>
            <Button type="submit" disabled={createMutation.isPending}>
              {createMutation.isPending ? 'Creating…' : 'Create Invoice'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

## web/src/pages/invoices/InvoiceDetailPage.tsx

```tsx
import { useState } from 'react';
import { useParams, Link, useNavigate } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { ArrowLeft, Download, Pencil, Trash2, Send, DollarSign, X, Plus } from 'lucide-react';
import { getInvoice, updateInvoice, deleteInvoice, updateInvoiceStatus } from '@/api/invoices';
import type { InvoiceLineItem } from '@/api/invoices';
import Card from '@/components/ui/Card';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import Table from '@/components/ui/Table';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency, formatDate } from '@/lib/utils';

const UNITS = ['m', 'm²', 'hrs', 'ea', 'lot'] as const;

const emptyLineItem = (): InvoiceLineItem => ({
  description: '',
  quantity: 1,
  unit: 'ea',
  unitPrice: 0,
  amount: 0,
});

type EditForm = {
  type: 'TAX_INVOICE' | 'PROGRESS_CLAIM';
  toName: string;
  toEmail: string;
  toAddress: string;
  dueDate: string;
  retentionPercent: number;
  lineItems: InvoiceLineItem[];
};

export default function InvoiceDetailPage() {
  const { id, invoiceId } = useParams<{ id: string; invoiceId: string }>();
  const navigate = useNavigate();
  const queryClient = useQueryClient();

  const [showEdit, setShowEdit] = useState(false);
  const [showDeleteConfirm, setShowDeleteConfirm] = useState(false);
  const [editForm, setEditForm] = useState<EditForm | null>(null);

  const { data: invoice, isLoading } = useQuery({
    queryKey: ['invoice', invoiceId],
    queryFn: () => getInvoice(invoiceId!),
    enabled: !!invoiceId,
  });

  const statusMutation = useMutation({
    mutationFn: (status: string) => updateInvoiceStatus(invoiceId!, status),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['invoice', invoiceId] });
      queryClient.invalidateQueries({ queryKey: ['invoices', id] });
    },
  });

  const deleteMutation = useMutation({
    mutationFn: () => deleteInvoice(invoiceId!),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['invoices', id] });
      navigate(`/projects/${id}/invoices`);
    },
  });

  const updateMutation = useMutation({
    mutationFn: (payload: any) => updateInvoice(invoiceId!, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['invoice', invoiceId] });
      queryClient.invalidateQueries({ queryKey: ['invoices', id] });
      setShowEdit(false);
      setEditForm(null);
    },
  });

  const openEdit = () => {
    if (!invoice) return;
    setEditForm({
      type: invoice.type as EditForm['type'],
      toName: invoice.toName,
      toEmail: invoice.toEmail ?? '',
      toAddress: invoice.toAddress ?? '',
      dueDate: invoice.dueDate.slice(0, 10),
      retentionPercent: Number(invoice.retentionPercent ?? 0),
      lineItems: (invoice.lineItems ?? []).map((li) => ({
        description: li.description,
        quantity: Number(li.quantity),
        unit: li.unit ?? 'ea',
        unitPrice: Number(li.unitPrice),
        amount: Number(li.amount),
      })),
    });
    setShowEdit(true);
  };

  // Edit form helpers
  const editSubtotal = editForm?.lineItems.reduce((sum, li) => sum + li.amount, 0) ?? 0;
  const editGst = Math.round(editSubtotal * 0.1 * 100) / 100;
  const editRetention = editForm && editForm.retentionPercent > 0
    ? Math.round(editSubtotal * (editForm.retentionPercent / 100) * 100) / 100
    : 0;
  const editTotal = editSubtotal + editGst - editRetention;

  const updateLineItem = (index: number, field: keyof InvoiceLineItem, value: any) => {
    setEditForm((f) => {
      if (!f) return f;
      const items = [...f.lineItems];
      items[index] = { ...items[index], [field]: value };
      if (field === 'quantity' || field === 'unitPrice') {
        items[index].amount = Math.round(items[index].quantity * items[index].unitPrice * 100) / 100;
      }
      return { ...f, lineItems: items };
    });
  };

  const handleEditSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!editForm) return;
    updateMutation.mutate({
      type: editForm.type,
      toName: editForm.toName,
      toEmail: editForm.toEmail || undefined,
      toAddress: editForm.toAddress || undefined,
      subtotal: editSubtotal,
      gstTotal: editGst,
      total: editTotal,
      retentionPercent: editForm.retentionPercent || undefined,
      retentionAmount: editRetention || undefined,
      amountDue: editTotal,
      dueDate: new Date(editForm.dueDate).toISOString(),
      lineItems: editForm.lineItems,
    });
  };

  if (isLoading) {
    return (
      <div className="flex min-h-[400px] items-center justify-center">
        <LoadingSpinner size="lg" />
      </div>
    );
  }

  if (!invoice) {
    return (
      <div className="space-y-6">
        <Link
          to={`/projects/${id}/invoices`}
          className="inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Invoices
        </Link>
        <p className="text-sm text-gray-500">Invoice not found.</p>
      </div>
    );
  }

  const subtotal = Number(invoice.subtotal);
  const gstTotal = Number(invoice.gstTotal);
  const retentionAmount = Number(invoice.retentionAmount ?? 0);
  const retentionPercent = Number(invoice.retentionPercent ?? 0);
  const amountDue = Number(invoice.amountDue);
  const isDraft = invoice.status === 'DRAFT';
  const isSent = invoice.status === 'SENT';
  const isViewed = invoice.status === 'VIEWED';
  const isOverdue = invoice.status === 'OVERDUE';
  const isPaid = invoice.status === 'PAID';
  const pastDue = !isPaid && new Date(invoice.dueDate) < new Date();

  return (
    <div className="space-y-6">
      {/* Header — hidden when printing */}
      <div className="print:hidden">
        <Link
          to={`/projects/${id}/invoices`}
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Invoices
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Invoice {invoice.invoiceNumber}</h2>
          <div className="flex flex-wrap items-center gap-2">
            {/* 2.2.5 — Download PDF via print */}
            <Button size="sm" variant="secondary" onClick={() => window.print()}>
              <Download className="h-4 w-4" /> Download PDF
            </Button>

            {/* 2.2.7 — Status transition buttons */}
            {isDraft && (
              <Button
                size="sm"
                onClick={() => statusMutation.mutate('SENT')}
                disabled={statusMutation.isPending}
              >
                <Send className="h-4 w-4" /> Mark as Sent
              </Button>
            )}
            {(isSent || isViewed || isOverdue) && (
              <Button
                size="sm"
                onClick={() => statusMutation.mutate('PAID')}
                disabled={statusMutation.isPending}
              >
                <DollarSign className="h-4 w-4" /> Mark as Paid
              </Button>
            )}

            {/* 2.2.8 — Edit & Delete for drafts */}
            {isDraft && (
              <>
                <Button size="sm" variant="secondary" onClick={openEdit}>
                  <Pencil className="h-4 w-4" /> Edit
                </Button>
                <Button
                  size="sm"
                  variant="danger"
                  onClick={() => setShowDeleteConfirm(true)}
                >
                  <Trash2 className="h-4 w-4" /> Delete
                </Button>
              </>
            )}
          </div>
        </div>
      </div>

      {/* Invoice preview — visible in print */}
      <Card>
        <div className="space-y-6">
          {/* Invoice meta */}
          <div className="flex flex-col justify-between gap-4 sm:flex-row">
            <div>
              <h3 className="text-lg font-bold text-gray-900">{invoice.invoiceNumber}</h3>
              <p className="text-sm text-gray-500">{invoice.type.replace(/_/g, ' ')}</p>
            </div>
            <div className="flex items-center gap-2 text-right">
              <Badge status={invoice.status.toLowerCase()} />
              {pastDue && !isOverdue && (
                <Badge status="overdue" />
              )}
            </div>
          </div>

          {/* Dates */}
          <div className="grid grid-cols-2 gap-4 sm:grid-cols-4">
            <div>
              <p className="text-xs text-gray-500">Issue Date</p>
              <p className="text-sm font-medium text-gray-900">{formatDate(invoice.issueDate)}</p>
            </div>
            <div>
              <p className="text-xs text-gray-500">Due Date</p>
              <p className="text-sm font-medium text-gray-900">{formatDate(invoice.dueDate)}</p>
            </div>
            {isPaid && invoice.paidDate && (
              <div>
                <p className="text-xs text-gray-500">Paid Date</p>
                <p className="text-sm font-medium text-success">{formatDate(invoice.paidDate)}</p>
              </div>
            )}
          </div>

          {/* From / To */}
          <div className="grid grid-cols-1 gap-6 sm:grid-cols-2">
            <div>
              <p className="mb-1 text-xs font-medium uppercase text-gray-500">From</p>
              <p className="text-sm font-medium text-gray-900">BuildMate Pty Ltd</p>
              <p className="text-sm text-gray-500">ABN: 00 000 000 000</p>
              <p className="text-sm text-gray-500">accounts@buildmate.com.au</p>
            </div>
            <div>
              <p className="mb-1 text-xs font-medium uppercase text-gray-500">To</p>
              <p className="text-sm font-medium text-gray-900">{invoice.toName}</p>
              {invoice.toEmail && <p className="text-sm text-gray-500">{invoice.toEmail}</p>}
              {invoice.toAddress && <p className="text-sm text-gray-500">{invoice.toAddress}</p>}
            </div>
          </div>

          {/* Line items table */}
          <Table headers={['Description', 'Qty', 'Unit', 'Unit Price', 'Amount']}>
            {(invoice.lineItems ?? []).map((li) => (
              <tr key={li.id}>
                <td className="px-4 py-3 text-sm text-gray-900">{li.description}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{Number(li.quantity)}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{li.unit ?? '—'}</td>
                <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">
                  {formatCurrency(Number(li.unitPrice))}
                </td>
                <td className="whitespace-nowrap px-4 py-3 text-sm font-medium text-gray-900">
                  {formatCurrency(Number(li.amount))}
                </td>
              </tr>
            ))}
          </Table>

          {/* Totals */}
          <div className="flex justify-end">
            <div className="w-72 space-y-1 text-sm">
              <div className="flex justify-between">
                <span className="text-gray-500">Subtotal</span>
                <span className="font-medium text-gray-900">{formatCurrency(subtotal)}</span>
              </div>
              <div className="flex justify-between">
                <span className="text-gray-500">GST (10%)</span>
                <span className="font-medium text-gray-900">{formatCurrency(gstTotal)}</span>
              </div>
              {retentionPercent > 0 && (
                <div className="flex justify-between">
                  <span className="text-gray-500">Retention ({retentionPercent}%)</span>
                  <span className="font-medium text-gray-900">−{formatCurrency(retentionAmount)}</span>
                </div>
              )}
              <div className="flex justify-between border-t border-gray-200 pt-2">
                <span className="font-semibold text-gray-900">Amount Due</span>
                <span className="font-semibold text-gray-900">{formatCurrency(amountDue)}</span>
              </div>
            </div>
          </div>

          {/* 2.2.9 — Retention info section */}
          {retentionAmount > 0 && (
            <div className="rounded-lg border border-amber-200 bg-amber-50 p-4">
              <h4 className="text-sm font-semibold text-gray-900">Retention</h4>
              <div className="mt-2 space-y-1 text-sm">
                <div className="flex justify-between">
                  <span className="text-gray-500">Retention Rate</span>
                  <span className="font-medium text-gray-900">{retentionPercent}%</span>
                </div>
                <div className="flex justify-between">
                  <span className="text-gray-500">Retention Held</span>
                  <span className="font-medium text-gray-900">{formatCurrency(retentionAmount)}</span>
                </div>
              </div>
            </div>
          )}
        </div>
      </Card>

      {/* Print styles */}
      <style>{`
        @media print {
          body * { visibility: hidden; }
          .space-y-6, .space-y-6 * { visibility: visible; }
          .print\\:hidden { display: none !important; }
          .space-y-6 { position: absolute; left: 0; top: 0; width: 100%; }
        }
      `}</style>

      {/* Delete confirmation modal */}
      <Modal
        open={showDeleteConfirm}
        onClose={() => setShowDeleteConfirm(false)}
        title="Delete Invoice"
      >
        <p className="text-sm text-gray-500">
          Are you sure you want to delete invoice <strong>{invoice.invoiceNumber}</strong>? This action cannot be undone.
        </p>
        <div className="flex justify-end gap-2 pt-4">
          <Button variant="secondary" onClick={() => setShowDeleteConfirm(false)}>
            Cancel
          </Button>
          <Button
            variant="danger"
            onClick={() => deleteMutation.mutate()}
            disabled={deleteMutation.isPending}
          >
            {deleteMutation.isPending ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>

      {/* Edit Invoice Modal (2.2.8) */}
      {editForm && (
        <Modal
          open={showEdit}
          onClose={() => { setShowEdit(false); setEditForm(null); }}
          title="Edit Invoice"
          className="max-w-2xl"
        >
          <form onSubmit={handleEditSubmit} className="space-y-4">
            {/* Type */}
            <div>
              <label className="mb-1 block text-sm font-medium text-gray-700">Type</label>
              <select
                value={editForm.type}
                onChange={(e) => setEditForm((f) => f ? { ...f, type: e.target.value as EditForm['type'] } : f)}
                className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              >
                <option value="TAX_INVOICE">Tax Invoice</option>
                <option value="PROGRESS_CLAIM">Progress Claim</option>
              </select>
            </div>

            {/* To fields */}
            <div className="grid grid-cols-1 gap-4 sm:grid-cols-2">
              <Input
                label="To (Name)"
                id="editToName"
                required
                value={editForm.toName}
                onChange={(e) => setEditForm((f) => f ? { ...f, toName: e.target.value } : f)}
              />
              <Input
                label="Email"
                id="editToEmail"
                type="email"
                value={editForm.toEmail}
                onChange={(e) => setEditForm((f) => f ? { ...f, toEmail: e.target.value } : f)}
              />
            </div>
            <Input
              label="Address"
              id="editToAddress"
              value={editForm.toAddress}
              onChange={(e) => setEditForm((f) => f ? { ...f, toAddress: e.target.value } : f)}
            />

            {/* Line items */}
            <div>
              <label className="mb-2 block text-sm font-medium text-gray-700">Line Items</label>
              <div className="space-y-2">
                {editForm.lineItems.map((li, idx) => (
                  <div key={idx} className="flex items-end gap-2">
                    <div className="flex-1">
                      {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Description</span>}
                      <input
                        required
                        value={li.description}
                        onChange={(e) => updateLineItem(idx, 'description', e.target.value)}
                        className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                      />
                    </div>
                    <div className="w-20">
                      {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Qty</span>}
                      <input
                        type="number"
                        min={0}
                        step="any"
                        required
                        value={li.quantity}
                        onChange={(e) => updateLineItem(idx, 'quantity', parseFloat(e.target.value) || 0)}
                        className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                      />
                    </div>
                    <div className="w-20">
                      {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Unit</span>}
                      <select
                        value={li.unit}
                        onChange={(e) => updateLineItem(idx, 'unit', e.target.value)}
                        className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                      >
                        {UNITS.map((u) => (
                          <option key={u} value={u}>{u}</option>
                        ))}
                      </select>
                    </div>
                    <div className="w-28">
                      {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Unit Price</span>}
                      <input
                        type="number"
                        min={0}
                        step="0.01"
                        required
                        value={li.unitPrice}
                        onChange={(e) => updateLineItem(idx, 'unitPrice', parseFloat(e.target.value) || 0)}
                        className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
                      />
                    </div>
                    <div className="w-28">
                      {idx === 0 && <span className="mb-1 block text-xs text-gray-500">Amount</span>}
                      <input
                        type="number"
                        readOnly
                        value={li.amount}
                        className="block w-full rounded-lg border border-gray-200 bg-gray-50 px-3 py-2 text-sm"
                      />
                    </div>
                    <button
                      type="button"
                      onClick={() => setEditForm((f) => f ? {
                        ...f,
                        lineItems: f.lineItems.length > 1 ? f.lineItems.filter((_, i) => i !== idx) : f.lineItems,
                      } : f)}
                      className="mb-0.5 rounded p-1.5 text-gray-400 hover:bg-red-50 hover:text-danger"
                    >
                      <X className="h-4 w-4" />
                    </button>
                  </div>
                ))}
              </div>
              <Button
                type="button"
                variant="ghost"
                size="sm"
                className="mt-2"
                onClick={() => setEditForm((f) => f ? { ...f, lineItems: [...f.lineItems, emptyLineItem()] } : f)}
              >
                <Plus className="h-4 w-4" /> Add Line Item
              </Button>
            </div>

            {/* Totals */}
            <div className="rounded-lg border border-gray-200 bg-gray-50 p-4">
              <div className="space-y-1 text-sm">
                <div className="flex justify-between">
                  <span className="text-gray-500">Subtotal</span>
                  <span className="font-medium text-gray-900">{formatCurrency(editSubtotal)}</span>
                </div>
                <div className="flex justify-between">
                  <span className="text-gray-500">GST (10%)</span>
                  <span className="font-medium text-gray-900">{formatCurrency(editGst)}</span>
                </div>
                <div className="flex items-center justify-between">
                  <div className="flex items-center gap-2">
                    <span className="text-gray-500">Retention</span>
                    <input
                      type="number"
                      min={0}
                      max={100}
                      step="0.5"
                      value={editForm.retentionPercent}
                      onChange={(e) => setEditForm((f) => f ? { ...f, retentionPercent: parseFloat(e.target.value) || 0 } : f)}
                      className="w-16 rounded border border-gray-300 px-2 py-1 text-sm"
                    />
                    <span className="text-gray-500">%</span>
                  </div>
                  <span className="font-medium text-gray-900">−{formatCurrency(editRetention)}</span>
                </div>
                <div className="flex justify-between border-t border-gray-200 pt-2">
                  <span className="font-semibold text-gray-900">Total / Amount Due</span>
                  <span className="font-semibold text-gray-900">{formatCurrency(editTotal)}</span>
                </div>
              </div>
            </div>

            {/* Due date */}
            <Input
              label="Due Date"
              id="editDueDate"
              type="date"
              required
              value={editForm.dueDate}
              onChange={(e) => setEditForm((f) => f ? { ...f, dueDate: e.target.value } : f)}
            />

            <div className="flex justify-end gap-2 pt-2">
              <Button variant="secondary" type="button" onClick={() => { setShowEdit(false); setEditForm(null); }}>
                Cancel
              </Button>
              <Button type="submit" disabled={updateMutation.isPending}>
                {updateMutation.isPending ? 'Saving…' : 'Save Changes'}
              </Button>
            </div>
          </form>
        </Modal>
      )}
    </div>
  );
}

```

## web/src/pages/documents/DocumentsPage.tsx

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
  Search,
  Eye,
  Pencil,
  History,
  CheckCircle2,
  XCircle,
  ClipboardList,
} from 'lucide-react';
import { getDocuments, uploadDocument, deleteDocument, updateDocument, uploadDocumentVersion, getDocumentVersions } from '@/api/documents';
import type { Document } from '@/types';
import Card from '@/components/ui/Card';
import Button from '@/components/ui/Button';
import Modal from '@/components/ui/Modal';
import Input from '@/components/ui/Input';
import EmptyState from '@/components/ui/EmptyState';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatDate } from '@/lib/utils';

const CATEGORIES = ['all', 'plans', 'contracts', 'permits', 'swms', 'photos', 'reports', 'other'] as const;
const FOLDER_VALUES = ['PLANS', 'CONTRACTS', 'PERMITS', 'SWMS', 'CERTIFICATES', 'INSURANCE', 'PHOTOS', 'REPORTS', 'CORRESPONDENCE', 'OTHER'] as const;

function fileIcon(mimeType?: string | null) {
  if (mimeType?.startsWith('image/')) return FileImage;
  if (mimeType?.includes('pdf')) return FileText;
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
  const [search, setSearch] = useState('');
  const [previewDoc, setPreviewDoc] = useState<Document | null>(null);
  const [editDoc, setEditDoc] = useState<Document | null>(null);
  const [editForm, setEditForm] = useState({ name: '', folder: '', description: '' });
  const [versionDoc, setVersionDoc] = useState<Document | null>(null);

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

  const MAX_FILE_SIZE = 50 * 1024 * 1024; // 50MB

  const handleFiles = useCallback(
    (files: FileList) => {
      Array.from(files).forEach((file) => {
        if (file.size > MAX_FILE_SIZE) {
          alert(`"${file.name}" exceeds the 50 MB limit (${formatFileSize(file.size)}). Please choose a smaller file.`);
          return;
        }
        uploadMutation.mutate({ file, cat: category !== 'all' ? category : 'other' });
      });
    },
    [category, uploadMutation],
  );

  const versionUploadMutation = useMutation({
    mutationFn: ({ docId, file }: { docId: string; file: File }) => uploadDocumentVersion(docId, file),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['documents', id] });
      queryClient.invalidateQueries({ queryKey: ['document-versions'] });
    },
  });

  const { data: versions } = useQuery({
    queryKey: ['document-versions', versionDoc?.id],
    queryFn: () => getDocumentVersions(versionDoc!.id),
    enabled: !!versionDoc,
  });

  const editMutation = useMutation({
    mutationFn: () => updateDocument(editDoc!.id, editForm),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['documents', id] });
      setEditDoc(null);
    },
  });

  const handleDrop = useCallback(
    (e: React.DragEvent) => {
      e.preventDefault();
      setDragOver(false);
      if (e.dataTransfer.files.length > 0) handleFiles(e.dataTransfer.files);
    },
    [handleFiles],
  );

  const openEdit = (doc: Document) => {
    setEditForm({
      name: doc.name,
      folder: doc.folder ?? doc.category?.toUpperCase() ?? 'OTHER',
      description: doc.description ?? '',
    });
    setEditDoc(doc);
  };

  // Filter documents by search term (Task 5.3.4)
  const filteredDocs = search
    ? documents?.filter((d) => d.name.toLowerCase().includes(search.toLowerCase()))
    : documents;

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

      {/* Search (Task 5.3.4) */}
      <div className="relative">
        <Search className="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400" />
        <input
          type="text"
          placeholder="Search documents by name…"
          value={search}
          onChange={(e) => setSearch(e.target.value)}
          className="block w-full rounded-lg border border-gray-300 py-2 pl-10 pr-3 text-sm shadow-sm placeholder:text-gray-400 focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
        />
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
      {(!filteredDocs || filteredDocs.length === 0) ? (
        <EmptyState
          icon={<FileText className="h-12 w-12" />}
          title={search ? 'No matching documents' : 'No documents'}
          description={search ? 'Try a different search term.' : 'Upload your first document.'}
        />
      ) : (
        <Card>
          <div className="divide-y divide-gray-100">
            {filteredDocs.map((doc) => {
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
                    <button
                      onClick={() => setPreviewDoc(doc)}
                      className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                      title="Preview"
                    >
                      <Eye className="h-4 w-4" />
                    </button>
                    <button
                      onClick={() => openEdit(doc)}
                      className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                      title="Edit"
                    >
                      <Pencil className="h-4 w-4" />
                    </button>
                    <button
                      onClick={() => setVersionDoc(doc)}
                      className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                      title="Version History"
                    >
                      <History className="h-4 w-4" />
                    </button>
                    <a
                      href={doc.url || doc.fileUrl}
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

      {/* Preview Modal (Task 5.3.1) */}
      <Modal
        open={!!previewDoc}
        onClose={() => setPreviewDoc(null)}
        title={previewDoc?.name ?? 'Preview'}
        className="max-w-3xl"
      >
        {previewDoc && (() => {
          const url = previewDoc.url || previewDoc.fileUrl || '';
          const mime = previewDoc.mimeType ?? '';
          if (!url) {
            return <p className="py-8 text-center text-sm text-gray-500">File URL unavailable.</p>;
          }
          if (mime.startsWith('image/')) {
            return <img src={url} alt={previewDoc.name} className="max-h-[70vh] w-full object-contain" />;
          }
          if (mime.includes('pdf')) {
            return <iframe src={url} title={previewDoc.name} className="h-[70vh] w-full rounded border" />;
          }
          return (
            <div className="flex flex-col items-center gap-4 py-12">
              <FileText className="h-16 w-16 text-gray-300" />
              <p className="text-sm text-gray-500">Preview not available for this file type.</p>
              <a
                href={url}
                target="_blank"
                rel="noopener noreferrer"
              >
                <Button size="sm"><Download className="h-4 w-4" /> Download</Button>
              </a>
            </div>
          );
        })()}
      </Modal>

      {/* Mandatory Document Checklist (Task 5.3.5) */}
      <Card
        title="Required Documents"
        action={<ClipboardList className="h-4 w-4 text-gray-400" />}
      >
        <div className="space-y-2">
          {[
            { label: 'Building Permit', folder: 'PERMITS' },
            { label: 'DA Approval', folder: 'PERMITS' },
            { label: 'Engineers Certificate', folder: 'CERTIFICATES' },
            { label: 'Insurance Certificate', folder: 'INSURANCE' },
            { label: 'SWMS', folder: 'SWMS' },
            { label: 'Site Plan', folder: 'PLANS' },
          ].map((req) => {
            const exists = documents?.some(
              (d) =>
                (d.folder ?? (d.category as string)?.toUpperCase()) === req.folder &&
                d.name.toLowerCase().includes(req.label.toLowerCase().split(' ')[0]),
            ) || documents?.some(
              (d) => d.name.toLowerCase().includes(req.label.toLowerCase()),
            );
            return (
              <div key={req.label} className="flex items-center justify-between rounded-lg p-2 hover:bg-gray-50">
                <div className="flex items-center gap-2">
                  {exists ? (
                    <CheckCircle2 className="h-5 w-5 text-success" />
                  ) : (
                    <XCircle className="h-5 w-5 text-danger" />
                  )}
                  <span className="text-sm text-gray-900">{req.label}</span>
                </div>
                {!exists && (
                  <label>
                    <Button size="sm" variant="secondary" className="cursor-pointer">
                      <Upload className="h-3 w-3" /> Upload
                    </Button>
                    <input
                      type="file"
                      className="hidden"
                      onChange={(e) => {
                        if (e.target.files?.[0]) {
                          uploadMutation.mutate({ file: e.target.files[0], cat: req.folder.toLowerCase() });
                        }
                      }}
                    />
                  </label>
                )}
              </div>
            );
          })}
        </div>
      </Card>

      {/* Version History Modal (Task 5.3.2) */}
      <Modal open={!!versionDoc} onClose={() => setVersionDoc(null)} title={`Version History — ${versionDoc?.name ?? ''}`}>
        <div className="space-y-4">
          <label>
            <Button size="sm" className="cursor-pointer">
              <Upload className="h-4 w-4" /> Upload New Version
            </Button>
            <input
              type="file"
              className="hidden"
              onChange={(e) => {
                if (e.target.files?.[0] && versionDoc) {
                  versionUploadMutation.mutate({ docId: versionDoc.id, file: e.target.files[0] });
                }
              }}
            />
          </label>
          {versions && versions.length > 0 ? (
            <div className="divide-y divide-gray-100">
              {versions.map((v) => (
                <div key={v.id} className="flex items-center justify-between py-2">
                  <div>
                    <p className="text-sm font-medium text-gray-900">v{(v as Document & { version?: number }).version ?? 1}</p>
                    <p className="text-xs text-gray-500">
                      {formatFileSize(v.fileSize)} · {formatDate(v.createdAt)} · {v.uploadedBy}
                    </p>
                  </div>
                  <a
                    href={v.url || v.fileUrl}
                    target="_blank"
                    rel="noopener noreferrer"
                    className="rounded-lg p-2 text-gray-400 hover:bg-gray-100 hover:text-gray-600"
                  >
                    <Download className="h-4 w-4" />
                  </a>
                </div>
              ))}
            </div>
          ) : (
            <p className="py-4 text-center text-sm text-gray-500">No version history available.</p>
          )}
        </div>
      </Modal>

      {/* Edit Metadata Modal (Task 5.3.3) */}
      <Modal open={!!editDoc} onClose={() => setEditDoc(null)} title="Edit Document">
        <form
          className="space-y-4"
          onSubmit={(e) => { e.preventDefault(); editMutation.mutate(); }}
        >
          <Input
            id="editDocName"
            label="Name"
            value={editForm.name}
            onChange={(e) => setEditForm({ ...editForm, name: e.target.value })}
            required
          />
          <div className="w-full">
            <label htmlFor="editDocFolder" className="mb-1 block text-sm font-medium text-gray-700">Folder / Category</label>
            <select
              id="editDocFolder"
              className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={editForm.folder}
              onChange={(e) => setEditForm({ ...editForm, folder: e.target.value })}
            >
              {FOLDER_VALUES.map((f) => (
                <option key={f} value={f}>{f.replace(/_/g, ' ')}</option>
              ))}
            </select>
          </div>
          <Input
            id="editDocDescription"
            label="Description"
            value={editForm.description}
            onChange={(e) => setEditForm({ ...editForm, description: e.target.value })}
          />
          <div className="flex justify-end gap-2 pt-2">
            <Button type="button" variant="secondary" onClick={() => setEditDoc(null)}>Cancel</Button>
            <Button type="submit" disabled={editMutation.isPending}>
              {editMutation.isPending ? 'Saving…' : 'Save'}
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

```

## web/src/pages/tradies/TradiesPage.tsx

```tsx
import { useState, type FormEvent } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { Plus, Search, Users, Star, AlertTriangle, Phone, Mail, ShieldCheck } from 'lucide-react';
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
  const navigate = useNavigate();
  const [search, setSearch] = useState('');
  const [tradeFilter, setTradeFilter] = useState('all');
  const [showArchived, setShowArchived] = useState(false);
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
    queryKey: ['tradies', tradeFilter !== 'all' ? tradeFilter : undefined, search, showArchived ? undefined : 'ACTIVE'],
    queryFn: () =>
      getTradies({
        trade: tradeFilter !== 'all' ? tradeFilter : undefined,
        search: search || undefined,
        status: showArchived ? undefined : 'ACTIVE',
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
        <div className="flex items-center gap-2">
          <Link to="/tradies/compliance">
            <Button variant="secondary" size="sm">
              <ShieldCheck className="h-4 w-4" /> Compliance
            </Button>
          </Link>
          <Button size="sm" onClick={() => setShowCreate(true)}>
            <Plus className="h-4 w-4" /> Add Tradie
          </Button>
        </div>
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

      {/* Trade Filter + Archive Toggle */}
      <div className="flex items-center justify-between gap-4">
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
        <label className="flex items-center gap-2 whitespace-nowrap text-sm text-gray-600">
          <input
            type="checkbox"
            checked={showArchived}
            onChange={(e) => setShowArchived(e.target.checked)}
            className="rounded border-gray-300"
          />
          Show Archived
        </label>
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
                onClick={() => navigate(`/tradies/${tradie.id}`)}
                className="cursor-pointer rounded-xl border border-gray-200 bg-white p-5 shadow-sm transition-shadow hover:shadow-md"
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

## web/src/pages/tradies/TradieDetailPage.tsx

```tsx
import { useState, type FormEvent } from 'react';
import { useParams, Link } from 'react-router-dom';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import {
  ArrowLeft,
  Pencil,
  Archive,
  Plus,
  Trash2,
  Star,
  Phone,
  Mail,
  Building2,
  FileText,
  Briefcase,
  DollarSign,
  AlertTriangle,
  Upload,
} from 'lucide-react';
import {
  getTradie,
  updateTradie,
  getTradieDocuments,
  uploadTradieDocument,
  deleteTradieDocument,
  getTradieAssignments,
  assignTradieToProject,
  updateAssignmentRating,
  getTradiePayments,
} from '@/api/tradies';
import { getProjects } from '@/api/projects';
import type { Tradie } from '@/types';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import Card from '@/components/ui/Card';
import Input from '@/components/ui/Input';
import Modal from '@/components/ui/Modal';
import Table from '@/components/ui/Table';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { cn, formatCurrency, formatDate } from '@/lib/utils';

const TRADES = [
  'BUILDER','CARPENTER','ELECTRICIAN','PLUMBER','PLASTERER','PAINTER','TILER',
  'BRICKLAYER','CONCRETER','ROOFER','LANDSCAPER','DEMOLITION','EXCAVATION',
  'STEEL_FIXER','CABINET_MAKER','GLAZIER','RENDERER','WATERPROOFER','INSULATION',
  'FENCER','SCAFFOLDER','CLEANER','HVAC','FIRE_PROTECTION','SECURITY_SYSTEMS',
  'FLOORING','STONEMASON','POOL_BUILDER','SURVEYOR','ENGINEER','ARCHITECT',
  'DRAFTSPERSON','OTHER',
] as const;

const DOC_TYPES = ['LICENCE', 'PUBLIC_LIABILITY', 'WORKERS_COMP', 'ABN_LOOKUP', 'SWMS', 'OTHER'] as const;
const RATE_TYPES = ['HOURLY', 'DAILY', 'FIXED', 'SQM'] as const;

function getExpiryStatus(expiryDate?: string): 'valid' | 'expiring' | 'expired' {
  if (!expiryDate) return 'valid';
  const exp = new Date(expiryDate);
  const now = new Date();
  if (exp < now) return 'expired';
  const in30 = new Date();
  in30.setDate(in30.getDate() + 30);
  if (exp <= in30) return 'expiring';
  return 'valid';
}

function ExpiryBadge({ date }: { date?: string }) {
  const status = getExpiryStatus(date);
  const colors = {
    valid: 'bg-success-50 text-success-500',
    expiring: 'bg-warning-50 text-warning-500',
    expired: 'bg-danger-50 text-danger-500',
  };
  return (
    <span className={cn('inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium capitalize', colors[status])}>
      {status === 'expiring' && <AlertTriangle className="mr-1 h-3 w-3" />}
      {status}
    </span>
  );
}

// ─── Edit Modal ─────────────────────────────────

function EditModal({ tradie, open, onClose }: { tradie: Tradie; open: boolean; onClose: () => void }) {
  const queryClient = useQueryClient();
  const [form, setForm] = useState({
    companyName: tradie.companyName || tradie.company || '',
    contactName: tradie.contactName || `${tradie.firstName} ${tradie.lastName}`,
    trade: tradie.trade,
    email: tradie.email || '',
    phone: tradie.phone || '',
    abn: tradie.abn || '',
    licenceNumber: tradie.licenceNumber || '',
    licenceExpiry: tradie.licenceExpiry ? tradie.licenceExpiry.slice(0, 10) : '',
    insuranceExpiry: tradie.insuranceExpiry ? tradie.insuranceExpiry.slice(0, 10) : '',
    notes: tradie.notes || '',
  });

  const mutation = useMutation({
    mutationFn: (payload: Partial<Tradie>) => updateTradie(tradie.id, payload),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradie', tradie.id] });
      queryClient.invalidateQueries({ queryKey: ['tradies'] });
      onClose();
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    const payload: Record<string, unknown> = { ...form };
    if (form.licenceExpiry) payload.licenceExpiry = new Date(form.licenceExpiry).toISOString();
    else delete payload.licenceExpiry;
    if (form.insuranceExpiry) payload.insuranceExpiry = new Date(form.insuranceExpiry).toISOString();
    else delete payload.insuranceExpiry;
    mutation.mutate(payload as Partial<Tradie>);
  };

  return (
    <Modal open={open} onClose={onClose} title="Edit Tradie">
      <form onSubmit={handleSubmit} className="space-y-4">
        <Input id="editCompanyName" label="Company Name" required value={form.companyName} onChange={(e) => setForm({ ...form, companyName: e.target.value })} />
        <Input id="editContactName" label="Contact Name" required value={form.contactName} onChange={(e) => setForm({ ...form, contactName: e.target.value })} />
        <div>
          <label htmlFor="editTrade" className="mb-1 block text-sm font-medium text-gray-700">Trade</label>
          <select id="editTrade" value={form.trade} onChange={(e) => setForm({ ...form, trade: e.target.value })} className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm">
            {TRADES.map((t) => <option key={t} value={t}>{t.replace(/_/g, ' ')}</option>)}
          </select>
        </div>
        <div className="grid grid-cols-2 gap-3">
          <Input id="editEmail" label="Email" type="email" value={form.email} onChange={(e) => setForm({ ...form, email: e.target.value })} />
          <Input id="editPhone" label="Phone" value={form.phone} onChange={(e) => setForm({ ...form, phone: e.target.value })} />
        </div>
        <div className="grid grid-cols-2 gap-3">
          <Input id="editAbn" label="ABN" value={form.abn} onChange={(e) => setForm({ ...form, abn: e.target.value })} />
          <Input id="editLicenceNumber" label="Licence #" value={form.licenceNumber} onChange={(e) => setForm({ ...form, licenceNumber: e.target.value })} />
        </div>
        <div className="grid grid-cols-2 gap-3">
          <Input id="editLicenceExpiry" label="Licence Expiry" type="date" value={form.licenceExpiry} onChange={(e) => setForm({ ...form, licenceExpiry: e.target.value })} />
          <Input id="editInsuranceExpiry" label="Insurance Expiry" type="date" value={form.insuranceExpiry} onChange={(e) => setForm({ ...form, insuranceExpiry: e.target.value })} />
        </div>
        <div>
          <label htmlFor="editNotes" className="mb-1 block text-sm font-medium text-gray-700">Notes</label>
          <textarea id="editNotes" rows={3} value={form.notes} onChange={(e) => setForm({ ...form, notes: e.target.value })} className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm" />
        </div>
        <div className="flex justify-end gap-2 pt-2">
          <Button type="button" variant="secondary" onClick={onClose}>Cancel</Button>
          <Button type="submit" disabled={mutation.isPending}>{mutation.isPending ? 'Saving…' : 'Save'}</Button>
        </div>
      </form>
    </Modal>
  );
}

// ─── Upload Document Modal ──────────────────────

function UploadDocModal({ tradieId, open, onClose }: { tradieId: string; open: boolean; onClose: () => void }) {
  const queryClient = useQueryClient();
  const [form, setForm] = useState({ type: 'LICENCE' as string, name: '', expiryDate: '' });

  const mutation = useMutation({
    mutationFn: () => uploadTradieDocument(tradieId, {
      type: form.type,
      name: form.name,
      expiryDate: form.expiryDate ? new Date(form.expiryDate).toISOString() : undefined,
    }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradie-documents', tradieId] });
      queryClient.invalidateQueries({ queryKey: ['tradie', tradieId] });
      onClose();
      setForm({ type: 'LICENCE', name: '', expiryDate: '' });
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    mutation.mutate();
  };

  return (
    <Modal open={open} onClose={onClose} title="Upload Document">
      <form onSubmit={handleSubmit} className="space-y-4">
        <div>
          <label htmlFor="docType" className="mb-1 block text-sm font-medium text-gray-700">Document Type</label>
          <select id="docType" value={form.type} onChange={(e) => setForm({ ...form, type: e.target.value })} className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm">
            {DOC_TYPES.map((t) => <option key={t} value={t}>{t.replace(/_/g, ' ')}</option>)}
          </select>
        </div>
        <Input id="docName" label="Document Name" required value={form.name} onChange={(e) => setForm({ ...form, name: e.target.value })} />
        <Input id="docExpiry" label="Expiry Date" type="date" value={form.expiryDate} onChange={(e) => setForm({ ...form, expiryDate: e.target.value })} />
        <div className="flex justify-end gap-2 pt-2">
          <Button type="button" variant="secondary" onClick={onClose}>Cancel</Button>
          <Button type="submit" disabled={mutation.isPending}>{mutation.isPending ? 'Uploading…' : 'Upload'}</Button>
        </div>
      </form>
    </Modal>
  );
}

// ─── Assign to Project Modal ────────────────────

function AssignProjectModal({ tradieId, open, onClose }: { tradieId: string; open: boolean; onClose: () => void }) {
  const queryClient = useQueryClient();
  const [form, setForm] = useState({ projectId: '', role: '', agreedRate: '', rateType: 'HOURLY' as string });

  const { data: projects } = useQuery({
    queryKey: ['projects'],
    queryFn: () => getProjects(),
    enabled: open,
  });

  const mutation = useMutation({
    mutationFn: () => assignTradieToProject({
      tradieId,
      projectId: form.projectId,
      role: form.role || undefined,
      agreedRate: form.agreedRate ? parseFloat(form.agreedRate) : undefined,
      rateType: form.rateType || undefined,
    }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradie-assignments', tradieId] });
      queryClient.invalidateQueries({ queryKey: ['tradie', tradieId] });
      onClose();
      setForm({ projectId: '', role: '', agreedRate: '', rateType: 'HOURLY' });
    },
  });

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    mutation.mutate();
  };

  return (
    <Modal open={open} onClose={onClose} title="Assign to Project">
      <form onSubmit={handleSubmit} className="space-y-4">
        <div>
          <label htmlFor="assignProject" className="mb-1 block text-sm font-medium text-gray-700">Project</label>
          <select id="assignProject" required value={form.projectId} onChange={(e) => setForm({ ...form, projectId: e.target.value })} className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm">
            <option value="">Select a project…</option>
            {(projects ?? []).map((p) => <option key={p.id} value={p.id}>{p.name}</option>)}
          </select>
        </div>
        <Input id="assignRole" label="Role" value={form.role} onChange={(e) => setForm({ ...form, role: e.target.value })} placeholder="e.g. Lead Electrician" />
        <div className="grid grid-cols-2 gap-3">
          <Input id="assignRate" label="Agreed Rate ($)" type="number" value={form.agreedRate} onChange={(e) => setForm({ ...form, agreedRate: e.target.value })} />
          <div>
            <label htmlFor="assignRateType" className="mb-1 block text-sm font-medium text-gray-700">Rate Type</label>
            <select id="assignRateType" value={form.rateType} onChange={(e) => setForm({ ...form, rateType: e.target.value })} className="block w-full rounded-lg border border-gray-300 px-3 py-2 text-sm">
              {RATE_TYPES.map((t) => <option key={t} value={t}>{t.toLowerCase()}</option>)}
            </select>
          </div>
        </div>
        <div className="flex justify-end gap-2 pt-2">
          <Button type="button" variant="secondary" onClick={onClose}>Cancel</Button>
          <Button type="submit" disabled={mutation.isPending}>{mutation.isPending ? 'Assigning…' : 'Assign'}</Button>
        </div>
      </form>
    </Modal>
  );
}

// ─── Star Rating Component ──────────────────────

function StarRating({ value, onChange }: { value: number; onChange: (v: number) => void }) {
  return (
    <div className="flex items-center gap-0.5">
      {Array.from({ length: 5 }).map((_, i) => (
        <button
          key={i}
          type="button"
          onClick={() => onChange(i + 1)}
          className="p-0"
        >
          <Star className={cn('h-4 w-4', i < value ? 'fill-warning text-warning' : 'text-gray-200 hover:text-warning')} />
        </button>
      ))}
    </div>
  );
}

// ─── Main Detail Page ───────────────────────────

export default function TradieDetailPage() {
  const { id } = useParams<{ id: string }>();
  const queryClient = useQueryClient();
  const [showEdit, setShowEdit] = useState(false);
  const [showArchive, setShowArchive] = useState(false);
  const [showUpload, setShowUpload] = useState(false);
  const [showAssign, setShowAssign] = useState(false);
  const [deleteDocId, setDeleteDocId] = useState<string | null>(null);

  const { data: tradie, isLoading } = useQuery({
    queryKey: ['tradie', id],
    queryFn: () => getTradie(id!),
    enabled: !!id,
  });

  const { data: documents } = useQuery({
    queryKey: ['tradie-documents', id],
    queryFn: () => getTradieDocuments(id!),
    enabled: !!id,
  });

  const { data: assignments } = useQuery({
    queryKey: ['tradie-assignments', id],
    queryFn: () => getTradieAssignments(id!),
    enabled: !!id,
  });

  const { data: payments } = useQuery({
    queryKey: ['tradie-payments', id],
    queryFn: () => getTradiePayments(id!),
    enabled: !!id,
  });

  const archiveMutation = useMutation({
    mutationFn: () => updateTradie(id!, { status: 'INACTIVE' } as Partial<Tradie>),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradie', id] });
      queryClient.invalidateQueries({ queryKey: ['tradies'] });
      setShowArchive(false);
    },
  });

  const deleteDocMutation = useMutation({
    mutationFn: (docId: string) => deleteTradieDocument(docId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradie-documents', id] });
      queryClient.invalidateQueries({ queryKey: ['tradie', id] });
      setDeleteDocId(null);
    },
  });

  const ratingMutation = useMutation({
    mutationFn: ({ assignmentId, rating }: { assignmentId: string; rating: number }) =>
      updateAssignmentRating(assignmentId, rating),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tradie-assignments', id] });
    },
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;
  if (!tradie) return <p className="py-20 text-center text-gray-500">Tradie not found</p>;

  const displayName = tradie.contactName || `${tradie.firstName} ${tradie.lastName}`;
  const companyName = tradie.companyName || tradie.company;

  return (
    <div className="space-y-6">
      {/* Header */}
      <div>
        <Link to="/tradies" className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700">
          <ArrowLeft className="h-4 w-4" /> Back to Tradies
        </Link>
        <div className="flex flex-col gap-2 sm:flex-row sm:items-center sm:justify-between">
          <div>
            <h2 className="text-xl font-bold text-gray-900">{displayName}</h2>
            {companyName && <p className="text-sm text-gray-500">{companyName}</p>}
          </div>
          <div className="flex items-center gap-2">
            <Badge status={tradie.trade} />
            {tradie.status === 'INACTIVE' && <Badge status="closed" className="!bg-gray-200 !text-gray-600" />}
            <Button variant="secondary" size="sm" onClick={() => setShowEdit(true)}>
              <Pencil className="h-4 w-4" /> Edit
            </Button>
            {tradie.status !== 'INACTIVE' && (
              <Button variant="secondary" size="sm" onClick={() => setShowArchive(true)}>
                <Archive className="h-4 w-4" /> Archive
              </Button>
            )}
          </div>
        </div>
      </div>

      {/* Profile Card */}
      <Card title="Profile">
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
          {tradie.email && (
            <div className="flex items-center gap-2 text-sm text-gray-600">
              <Mail className="h-4 w-4 text-gray-400" /> {tradie.email}
            </div>
          )}
          {tradie.phone && (
            <div className="flex items-center gap-2 text-sm text-gray-600">
              <Phone className="h-4 w-4 text-gray-400" /> {tradie.phone}
            </div>
          )}
          {companyName && (
            <div className="flex items-center gap-2 text-sm text-gray-600">
              <Building2 className="h-4 w-4 text-gray-400" /> {companyName}
            </div>
          )}
          {tradie.abn && (
            <div className="text-sm text-gray-600">
              <span className="font-medium text-gray-700">ABN:</span> {tradie.abn}
            </div>
          )}
          {tradie.licenceNumber && (
            <div className="text-sm text-gray-600">
              <span className="font-medium text-gray-700">Licence:</span> {tradie.licenceNumber}
            </div>
          )}
          {tradie.licenceExpiry && (
            <div className="flex items-center gap-2 text-sm text-gray-600">
              <span className="font-medium text-gray-700">Licence Expiry:</span> {formatDate(tradie.licenceExpiry)}
              <ExpiryBadge date={tradie.licenceExpiry} />
            </div>
          )}
          {tradie.insuranceExpiry && (
            <div className="flex items-center gap-2 text-sm text-gray-600">
              <span className="font-medium text-gray-700">Insurance Expiry:</span> {formatDate(tradie.insuranceExpiry)}
              <ExpiryBadge date={tradie.insuranceExpiry} />
            </div>
          )}
          {tradie.rating != null && (
            <div className="flex items-center gap-2 text-sm text-gray-600">
              <span className="font-medium text-gray-700">Rating:</span>
              <div className="flex items-center gap-0.5">
                {Array.from({ length: 5 }).map((_, i) => (
                  <Star key={i} className={cn('h-4 w-4', i < tradie.rating! ? 'fill-warning text-warning' : 'text-gray-200')} />
                ))}
                <span className="ml-1 text-xs text-gray-500">{tradie.rating.toFixed(1)}</span>
              </div>
            </div>
          )}
        </div>
      </Card>

      {/* Documents */}
      <Card
        title="Documents"
        action={
          <Button size="sm" onClick={() => setShowUpload(true)}>
            <Upload className="h-4 w-4" /> Upload Document
          </Button>
        }
      >
        {!documents || documents.length === 0 ? (
          <p className="text-sm text-gray-500">No documents uploaded.</p>
        ) : (
          <Table headers={['Type', 'Name', 'Expiry', 'Status', '']}>
            {documents.map((doc) => (
              <tr key={doc.id}>
                <td className="whitespace-nowrap px-4 py-3 text-sm">
                  <div className="flex items-center gap-2">
                    <FileText className="h-4 w-4 text-gray-400" />
                    {doc.type.replace(/_/g, ' ')}
                  </div>
                </td>
                <td className="px-4 py-3 text-sm text-gray-700">{doc.name}</td>
                <td className="px-4 py-3 text-sm text-gray-500">{doc.expiryDate ? formatDate(doc.expiryDate) : '—'}</td>
                <td className="px-4 py-3"><ExpiryBadge date={doc.expiryDate} /></td>
                <td className="px-4 py-3 text-right">
                  <button onClick={() => setDeleteDocId(doc.id)} className="rounded p-1 text-gray-400 hover:text-danger">
                    <Trash2 className="h-4 w-4" />
                  </button>
                </td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Project Assignments */}
      <Card
        title="Project Assignments"
        action={
          <Button size="sm" onClick={() => setShowAssign(true)}>
            <Plus className="h-4 w-4" /> Assign to Project
          </Button>
        }
      >
        {!assignments || assignments.length === 0 ? (
          <p className="text-sm text-gray-500">Not assigned to any projects.</p>
        ) : (
          <Table headers={['Project', 'Role', 'Rate', 'Rating', 'Status']}>
            {assignments.map((a) => (
              <tr key={a.id}>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">
                  <Link to={`/projects/${a.projectId}`} className="hover:text-primary">
                    <div className="flex items-center gap-2">
                      <Briefcase className="h-4 w-4 text-gray-400" />
                      {a.project?.name ?? a.projectId}
                    </div>
                  </Link>
                </td>
                <td className="px-4 py-3 text-sm text-gray-600">{a.role || '—'}</td>
                <td className="px-4 py-3 text-sm text-gray-600">
                  {a.agreedRate ? `${formatCurrency(Number(a.agreedRate))} / ${(a.rateType || 'FIXED').toLowerCase()}` : '—'}
                </td>
                <td className="px-4 py-3">
                  <StarRating
                    value={a.performanceRating ?? 0}
                    onChange={(rating) => ratingMutation.mutate({ assignmentId: a.id, rating })}
                  />
                </td>
                <td className="px-4 py-3">
                  {a.project?.status && <Badge status={a.project.status} />}
                </td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Payment History */}
      <Card title="Payment History">
        {!payments || payments.length === 0 ? (
          <p className="text-sm text-gray-500">No payment history.</p>
        ) : (
          <Table headers={['Date', 'Project', 'Description', 'Amount', 'Status']}>
            {payments.map((p) => (
              <tr key={p.id}>
                <td className="px-4 py-3 text-sm text-gray-500">{formatDate(p.date)}</td>
                <td className="px-4 py-3 text-sm font-medium text-gray-900">
                  <div className="flex items-center gap-2">
                    <DollarSign className="h-4 w-4 text-gray-400" />
                    {p.project?.name ?? '—'}
                  </div>
                </td>
                <td className="px-4 py-3 text-sm text-gray-600">{p.description}</td>
                <td className="px-4 py-3 text-sm font-medium">{formatCurrency(p.amount)}</td>
                <td className="px-4 py-3"><Badge status={p.status} /></td>
              </tr>
            ))}
          </Table>
        )}
      </Card>

      {/* Edit Modal */}
      {showEdit && <EditModal tradie={tradie} open={showEdit} onClose={() => setShowEdit(false)} />}

      {/* Upload Document Modal */}
      <UploadDocModal tradieId={id!} open={showUpload} onClose={() => setShowUpload(false)} />

      {/* Assign to Project Modal */}
      <AssignProjectModal tradieId={id!} open={showAssign} onClose={() => setShowAssign(false)} />

      {/* Archive Confirmation */}
      <Modal open={showArchive} onClose={() => setShowArchive(false)} title="Archive Tradie">
        <p className="mb-4 text-sm text-gray-600">
          Archive this tradie? They will be hidden from the active list.
        </p>
        <div className="flex justify-end gap-2">
          <Button variant="secondary" onClick={() => setShowArchive(false)}>Cancel</Button>
          <Button variant="danger" disabled={archiveMutation.isPending} onClick={() => archiveMutation.mutate()}>
            {archiveMutation.isPending ? 'Archiving…' : 'Archive'}
          </Button>
        </div>
      </Modal>

      {/* Delete Document Confirmation */}
      <Modal open={!!deleteDocId} onClose={() => setDeleteDocId(null)} title="Delete Document">
        <p className="mb-4 text-sm text-gray-600">
          Are you sure you want to delete this document? This cannot be undone.
        </p>
        <div className="flex justify-end gap-2">
          <Button variant="secondary" onClick={() => setDeleteDocId(null)}>Cancel</Button>
          <Button variant="danger" disabled={deleteDocMutation.isPending} onClick={() => deleteDocId && deleteDocMutation.mutate(deleteDocId)}>
            {deleteDocMutation.isPending ? 'Deleting…' : 'Delete'}
          </Button>
        </div>
      </Modal>
    </div>
  );
}

```

## web/src/pages/tradies/CompliancePage.tsx

```tsx
import { Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft, AlertTriangle, CheckCircle, XCircle, FileText } from 'lucide-react';
import { getCompliance } from '@/api/tradies';
import Badge from '@/components/ui/Badge';
import Card from '@/components/ui/Card';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatDate } from '@/lib/utils';

export default function CompliancePage() {
  const { data, isLoading } = useQuery({
    queryKey: ['compliance'],
    queryFn: () => getCompliance(30),
  });

  if (isLoading) return <LoadingSpinner className="py-20" size="lg" />;

  const now = new Date();
  const in30 = new Date();
  in30.setDate(in30.getDate() + 30);

  const expiredTradies = (data?.expiringTradies ?? []).filter((t) => {
    const dates = [t.licenceExpiry, t.insuranceExpiry, t.workersCompExpiry].filter(Boolean).map((d) => new Date(d!));
    return dates.some((d) => d < now);
  });

  const expiringTradies = (data?.expiringTradies ?? []).filter((t) => {
    const dates = [t.licenceExpiry, t.insuranceExpiry, t.workersCompExpiry].filter(Boolean).map((d) => new Date(d!));
    const hasExpired = dates.some((d) => d < now);
    const hasExpiring = dates.some((d) => d >= now && d <= in30);
    return !hasExpired && hasExpiring;
  });

  const expiredDocs = (data?.expiringDocuments ?? []).filter((d) => d.expiryDate && new Date(d.expiryDate) < now);
  const expiringDocs = (data?.expiringDocuments ?? []).filter((d) => d.expiryDate && new Date(d.expiryDate) >= now && new Date(d.expiryDate) <= in30);

  return (
    <div className="space-y-6">
      <div>
        <Link to="/tradies" className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700">
          <ArrowLeft className="h-4 w-4" /> Back to Tradies
        </Link>
        <h2 className="text-xl font-bold text-gray-900">Compliance Dashboard</h2>
        <p className="text-sm text-gray-500">Overview of tradie licence, insurance, and document compliance</p>
      </div>

      {/* Summary */}
      <div className="grid grid-cols-1 gap-4 sm:grid-cols-3">
        <div className="rounded-xl border border-danger-200 bg-danger-50 p-4">
          <div className="flex items-center gap-2">
            <XCircle className="h-5 w-5 text-danger" />
            <span className="text-sm font-semibold text-danger">Expired</span>
          </div>
          <p className="mt-1 text-2xl font-bold text-danger">{expiredTradies.length + expiredDocs.length}</p>
        </div>
        <div className="rounded-xl border border-warning-200 bg-warning-50 p-4">
          <div className="flex items-center gap-2">
            <AlertTriangle className="h-5 w-5 text-warning" />
            <span className="text-sm font-semibold text-warning-600">Expiring (30 days)</span>
          </div>
          <p className="mt-1 text-2xl font-bold text-warning-600">{expiringTradies.length + expiringDocs.length}</p>
        </div>
        <div className="rounded-xl border border-success-200 bg-success-50 p-4">
          <div className="flex items-center gap-2">
            <CheckCircle className="h-5 w-5 text-success" />
            <span className="text-sm font-semibold text-success">All Clear</span>
          </div>
          <p className="mt-2 text-sm text-success-600">Tradies not flagged are compliant</p>
        </div>
      </div>

      {/* Expired Section */}
      {(expiredTradies.length > 0 || expiredDocs.length > 0) && (
        <Card title="🔴 Expired">
          <div className="space-y-3">
            {expiredTradies.map((t) => (
              <Link key={t.id} to={`/tradies/${t.id}`} className="flex items-center justify-between rounded-lg border border-danger-100 bg-danger-50 p-3 hover:bg-danger-100">
                <div>
                  <p className="text-sm font-medium text-gray-900">{t.contactName || `${t.firstName} ${t.lastName}`}</p>
                  <p className="text-xs text-gray-500">{(t.companyName || t.company) ?? ''} · <Badge status={t.trade} /></p>
                </div>
                <div className="text-right text-xs text-danger">
                  {t.licenceExpiry && new Date(t.licenceExpiry) < now && <p>Licence: {formatDate(t.licenceExpiry)}</p>}
                  {t.insuranceExpiry && new Date(t.insuranceExpiry) < now && <p>Insurance: {formatDate(t.insuranceExpiry)}</p>}
                  {t.workersCompExpiry && new Date(t.workersCompExpiry) < now && <p>Workers Comp: {formatDate(t.workersCompExpiry)}</p>}
                </div>
              </Link>
            ))}
            {expiredDocs.map((d) => (
              <Link key={d.id} to={`/tradies/${d.tradie.id}`} className="flex items-center justify-between rounded-lg border border-danger-100 bg-danger-50 p-3 hover:bg-danger-100">
                <div className="flex items-center gap-2">
                  <FileText className="h-4 w-4 text-danger" />
                  <div>
                    <p className="text-sm font-medium text-gray-900">{d.tradie.contactName}</p>
                    <p className="text-xs text-gray-500">{d.name} · {d.type.replace(/_/g, ' ')}</p>
                  </div>
                </div>
                <span className="text-xs text-danger">{d.expiryDate ? formatDate(d.expiryDate) : ''}</span>
              </Link>
            ))}
          </div>
        </Card>
      )}

      {/* Expiring Section */}
      {(expiringTradies.length > 0 || expiringDocs.length > 0) && (
        <Card title="🟡 Expiring Within 30 Days">
          <div className="space-y-3">
            {expiringTradies.map((t) => (
              <Link key={t.id} to={`/tradies/${t.id}`} className="flex items-center justify-between rounded-lg border border-warning-100 bg-warning-50 p-3 hover:bg-warning-100">
                <div>
                  <p className="text-sm font-medium text-gray-900">{t.contactName || `${t.firstName} ${t.lastName}`}</p>
                  <p className="text-xs text-gray-500">{(t.companyName || t.company) ?? ''} · <Badge status={t.trade} /></p>
                </div>
                <div className="text-right text-xs text-warning-600">
                  {t.licenceExpiry && new Date(t.licenceExpiry) >= now && new Date(t.licenceExpiry) <= in30 && <p>Licence: {formatDate(t.licenceExpiry)}</p>}
                  {t.insuranceExpiry && new Date(t.insuranceExpiry) >= now && new Date(t.insuranceExpiry) <= in30 && <p>Insurance: {formatDate(t.insuranceExpiry)}</p>}
                  {t.workersCompExpiry && new Date(t.workersCompExpiry) >= now && new Date(t.workersCompExpiry) <= in30 && <p>Workers Comp: {formatDate(t.workersCompExpiry)}</p>}
                </div>
              </Link>
            ))}
            {expiringDocs.map((d) => (
              <Link key={d.id} to={`/tradies/${d.tradie.id}`} className="flex items-center justify-between rounded-lg border border-warning-100 bg-warning-50 p-3 hover:bg-warning-100">
                <div className="flex items-center gap-2">
                  <FileText className="h-4 w-4 text-warning" />
                  <div>
                    <p className="text-sm font-medium text-gray-900">{d.tradie.contactName}</p>
                    <p className="text-xs text-gray-500">{d.name} · {d.type.replace(/_/g, ' ')}</p>
                  </div>
                </div>
                <span className="text-xs text-warning-600">{d.expiryDate ? formatDate(d.expiryDate) : ''}</span>
              </Link>
            ))}
          </div>
        </Card>
      )}

      {expiredTradies.length === 0 && expiringTradies.length === 0 && expiredDocs.length === 0 && expiringDocs.length === 0 && (
        <Card>
          <div className="flex flex-col items-center py-8">
            <CheckCircle className="mb-3 h-12 w-12 text-success" />
            <p className="text-sm font-medium text-gray-900">All tradies are compliant</p>
            <p className="text-sm text-gray-500">No expiring or expired documents or licences found.</p>
          </div>
        </Card>
      )}
    </div>
  );
}

```

## web/src/pages/reports/ReportsPage.tsx

```tsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import {
  DollarSign,
  TrendingUp,
  Users,
  FolderKanban,
  Calendar,
  ArrowRight,
  Receipt,
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
    path: '/reports/budget',
  },
  {
    key: 'cashflow',
    title: 'Cash Flow',
    description: 'Monthly cash flow analysis with S-curve projections.',
    icon: TrendingUp,
    color: 'text-success bg-success-50',
    path: '/reports/cashflow',
  },
  {
    key: 'compliance',
    title: 'Tradie Compliance',
    description: 'Licence and insurance expiry status for all tradies.',
    icon: Users,
    color: 'text-warning bg-warning-50',
    path: '/reports/compliance',
  },
  {
    key: 'status',
    title: 'Project Status',
    description: 'Status summary across all active projects.',
    icon: FolderKanban,
    color: 'text-blue-600 bg-blue-50',
    path: '/reports/status',
  },
  {
    key: 'gst',
    title: 'GST / BAS Report',
    description: 'BAS-ready GST reporting by quarter with CSV export.',
    icon: Receipt,
    color: 'text-purple-600 bg-purple-50',
    path: '/reports/gst',
  },
];

export default function ReportsPage() {
  const navigate = useNavigate();
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
            onClick={() => {
              const params = new URLSearchParams();
              if (startDate) params.set('startDate', startDate);
              if (endDate) params.set('endDate', endDate);
              const qs = params.toString();
              navigate(report.path + (qs ? `?${qs}` : ''));
            }}
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

## web/src/pages/reports/GstReportPage.tsx

```tsx
import { useState } from 'react';
import { Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft, Download } from 'lucide-react';
import { getGstReport } from '@/api/reports';
import type { GstReportData } from '@/api/reports';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Button from '@/components/ui/Button';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency, formatDate } from '@/lib/utils';

const BAS_QUARTERS = [
  { label: 'Q1 (Jul–Sep)', quarter: 1, startMonth: 6, endMonth: 8 },
  { label: 'Q2 (Oct–Dec)', quarter: 2, startMonth: 9, endMonth: 11 },
  { label: 'Q3 (Jan–Mar)', quarter: 3, startMonth: 0, endMonth: 2 },
  { label: 'Q4 (Apr–Jun)', quarter: 4, startMonth: 3, endMonth: 5 },
];

function getQuarterDates(quarter: number, year: number) {
  const q = BAS_QUARTERS.find((b) => b.quarter === quarter)!;
  // Q1 Jul-Sep and Q2 Oct-Dec belong to the financial year starting July of `year`
  // Q3 Jan-Mar and Q4 Apr-Jun belong to the next calendar year
  const calendarYear = quarter >= 3 ? year + 1 : year;
  const start = new Date(calendarYear, q.startMonth, 1);
  const end = new Date(calendarYear, q.endMonth + 1, 0); // last day of end month
  return {
    startDate: start.toISOString().split('T')[0],
    endDate: end.toISOString().split('T')[0],
  };
}

function exportCsv(data: GstReportData) {
  const rows = [
    ['Date', 'Description', 'Type', 'Amount', 'GST Amount'],
    ...data.transactions.map((t) => [
      new Date(t.date).toLocaleDateString('en-AU'),
      `"${t.description}"`,
      t.type,
      t.amount.toFixed(2),
      t.gstAmount.toFixed(2),
    ]),
    [],
    ['', '', '', 'GST Collected', data.gstCollected.toFixed(2)],
    ['', '', '', 'GST Paid', data.gstPaid.toFixed(2)],
    ['', '', '', 'Net GST Payable', data.netGst.toFixed(2)],
  ];
  const csv = rows.map((r) => r.join(',')).join('\n');
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'gst-report.csv';
  a.click();
  URL.revokeObjectURL(url);
}

export default function GstReportPage() {
  const currentYear = new Date().getFullYear();
  const [year, setYear] = useState(currentYear - 1); // financial year starting July
  const [quarter, setQuarter] = useState(1);

  const { startDate, endDate } = getQuarterDates(quarter, year);

  const { data, isLoading } = useQuery({
    queryKey: ['gst-report', startDate, endDate],
    queryFn: () => getGstReport(startDate, endDate),
  });

  const years = Array.from({ length: 5 }, (_, i) => currentYear - 3 + i);

  return (
    <div className="space-y-6">
      <div>
        <Link
          to="/reports"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Reports
        </Link>
        <h2 className="text-xl font-bold text-gray-900">GST / BAS Report</h2>
      </div>

      {/* Filters */}
      <Card title="BAS Period">
        <div className="flex flex-wrap items-end gap-4">
          <div>
            <label htmlFor="fy-year" className="mb-1 block text-sm font-medium text-gray-700">Financial Year</label>
            <select
              id="fy-year"
              className="block rounded-lg border border-gray-300 px-3 py-2 text-sm shadow-sm focus:border-primary focus:outline-none focus:ring-1 focus:ring-primary"
              value={year}
              onChange={(e) => setYear(Number(e.target.value))}
            >
              {years.map((y) => (
                <option key={y} value={y}>FY {y}/{y + 1}</option>
              ))}
            </select>
          </div>
          <div className="flex gap-1 rounded-lg bg-gray-100 p-1">
            {BAS_QUARTERS.map((q) => (
              <button
                key={q.quarter}
                className={`rounded-md px-3 py-1.5 text-sm font-medium transition-colors ${
                  quarter === q.quarter
                    ? 'bg-white text-gray-900 shadow-sm'
                    : 'text-gray-500 hover:text-gray-700'
                }`}
                onClick={() => setQuarter(q.quarter)}
              >
                {q.label}
              </button>
            ))}
          </div>
          {data && (
            <Button size="sm" variant="secondary" onClick={() => exportCsv(data)}>
              <Download className="h-4 w-4" /> Export CSV
            </Button>
          )}
        </div>
      </Card>

      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : (
        <>
          {/* Summary */}
          <div className="grid grid-cols-3 gap-4">
            <Card>
              <p className="text-xs text-gray-500">GST Collected</p>
              <p className="text-lg font-bold text-success">{formatCurrency(data?.gstCollected ?? 0)}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">GST Paid</p>
              <p className="text-lg font-bold text-danger">{formatCurrency(data?.gstPaid ?? 0)}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Net GST Payable</p>
              <p className={`text-lg font-bold ${(data?.netGst ?? 0) >= 0 ? 'text-danger' : 'text-success'}`}>
                {formatCurrency(data?.netGst ?? 0)}
              </p>
            </Card>
          </div>

          {/* Transactions */}
          <Card title="Transactions">
            {(!data?.transactions || data.transactions.length === 0) ? (
              <p className="py-4 text-center text-sm text-gray-500">No transactions in this period</p>
            ) : (
              <Table headers={['Date', 'Description', 'Type', 'Amount', 'GST Amount']}>
                {data.transactions.map((t, i) => (
                  <tr key={i}>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{formatDate(t.date)}</td>
                    <td className="px-4 py-3 text-sm text-gray-900">{t.description}</td>
                    <td className="px-4 py-3 text-sm text-gray-500">{t.type}</td>
                    <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${t.type === 'INCOME' ? 'text-success' : 'text-danger'}`}>
                      {formatCurrency(t.amount)}
                    </td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">{formatCurrency(t.gstAmount)}</td>
                  </tr>
                ))}
              </Table>
            )}
          </Card>
        </>
      )}
    </div>
  );
}

```

## web/src/pages/reports/BudgetReportPage.tsx

```tsx
import { Link, useSearchParams } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft, Download, Printer } from 'lucide-react';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { getBudgetReport } from '@/api/reports';
import type { BudgetReportData } from '@/api/reports';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

function exportCsv(data: BudgetReportData) {
  const rows = [
    ['Project', 'Status', 'Budget', 'Spent', 'Variance'],
    ...data.projects.map((p) => [
      `"${p.name}"`,
      p.status,
      p.totalBudget.toFixed(2),
      p.totalSpent.toFixed(2),
      p.variance.toFixed(2),
    ]),
  ];
  const csv = rows.map((r) => r.join(',')).join('\n');
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'budget-report.csv';
  a.click();
  URL.revokeObjectURL(url);
}

export default function BudgetReportPage() {
  const [searchParams] = useSearchParams();
  const startDate = searchParams.get('startDate') || undefined;
  const endDate = searchParams.get('endDate') || undefined;

  const { data, isLoading } = useQuery({
    queryKey: ['budget-report', startDate, endDate],
    queryFn: () => getBudgetReport(startDate, endDate),
  });

  const totalBudget = data?.projects.reduce((s, p) => s + p.totalBudget, 0) ?? 0;
  const totalSpent = data?.projects.reduce((s, p) => s + p.totalSpent, 0) ?? 0;
  const totalVariance = totalBudget - totalSpent;

  return (
    <div className="space-y-6">
      <div>
        <Link
          to="/reports"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Reports
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Budget Summary</h2>
          {data && (
            <div className="flex gap-2">
              <Button size="sm" variant="secondary" onClick={() => window.print()}>
                <Printer className="h-4 w-4" /> Print
              </Button>
              <Button size="sm" variant="secondary" onClick={() => exportCsv(data)}>
                <Download className="h-4 w-4" /> Export CSV
              </Button>
            </div>
          )}
        </div>
      </div>

      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : (
        <>
          {/* Summary Cards */}
          <div className="grid grid-cols-3 gap-4">
            <Card>
              <p className="text-xs text-gray-500">Total Budget</p>
              <p className="text-lg font-bold text-gray-900">{formatCurrency(totalBudget)}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Total Spent</p>
              <p className="text-lg font-bold text-danger">{formatCurrency(totalSpent)}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Variance</p>
              <p className={`text-lg font-bold ${totalVariance >= 0 ? 'text-success' : 'text-danger'}`}>
                {formatCurrency(totalVariance)}
              </p>
            </Card>
          </div>

          {/* Bar Chart */}
          {data && data.projects.length > 0 && (
            <Card title="Budget vs Actual">
              <ResponsiveContainer width="100%" height={300}>
                <BarChart data={data.projects}>
                  <CartesianGrid strokeDasharray="3 3" />
                  <XAxis dataKey="name" tick={{ fontSize: 12 }} />
                  <YAxis tickFormatter={(v) => `$${(v / 1000).toFixed(0)}k`} />
                  <Tooltip formatter={(value: number) => formatCurrency(value)} />
                  <Legend />
                  <Bar dataKey="totalBudget" name="Budget" fill="#3b82f6" />
                  <Bar dataKey="totalSpent" name="Spent" fill="#ef4444" />
                </BarChart>
              </ResponsiveContainer>
            </Card>
          )}

          {/* Table */}
          <Card title="Projects">
            {(!data?.projects || data.projects.length === 0) ? (
              <p className="py-4 text-center text-sm text-gray-500">No projects found</p>
            ) : (
              <Table headers={['Project', 'Status', 'Budget', 'Spent', 'Variance']}>
                {data.projects.map((p) => (
                  <tr key={p.id}>
                    <td className="px-4 py-3 text-sm font-medium text-gray-900">{p.name}</td>
                    <td className="px-4 py-3"><Badge status={p.status.toLowerCase()} /></td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">{formatCurrency(p.totalBudget)}</td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-danger">{formatCurrency(p.totalSpent)}</td>
                    <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${p.variance >= 0 ? 'text-success' : 'text-danger'}`}>
                      {formatCurrency(p.variance)}
                    </td>
                  </tr>
                ))}
              </Table>
            )}
          </Card>
        </>
      )}

      <style>{`
        @media print {
          body * { visibility: hidden; }
          .space-y-6, .space-y-6 * { visibility: visible; }
          .space-y-6 { position: absolute; left: 0; top: 0; width: 100%; }
          button, a { display: none !important; }
        }
      `}</style>
    </div>
  );
}

```

## web/src/pages/reports/CashFlowReportPage.tsx

```tsx
import { useState } from 'react';
import { Link, useSearchParams } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft, Download, Calendar, Printer } from 'lucide-react';
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { getCashFlowReport } from '@/api/reports';
import type { CashFlowReportData } from '@/api/reports';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Input from '@/components/ui/Input';
import Button from '@/components/ui/Button';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import { formatCurrency } from '@/lib/utils';

function exportCsv(data: CashFlowReportData) {
  const rows = [
    ['Month', 'Income', 'Expense', 'Cumulative'],
    ...data.entries.map((e) => [
      e.month,
      e.totalIncome.toFixed(2),
      e.totalExpense.toFixed(2),
      e.cumulative.toFixed(2),
    ]),
  ];
  const csv = rows.map((r) => r.join(',')).join('\n');
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'cashflow-report.csv';
  a.click();
  URL.revokeObjectURL(url);
}

function formatMonth(month: string) {
  const [y, m] = month.split('-');
  const date = new Date(Number(y), Number(m) - 1);
  return date.toLocaleDateString('en-AU', { month: 'short', year: 'numeric' });
}

export default function CashFlowReportPage() {
  const [searchParams] = useSearchParams();
  const [startDate, setStartDate] = useState(searchParams.get('startDate') || '');
  const [endDate, setEndDate] = useState(searchParams.get('endDate') || '');

  const { data, isLoading } = useQuery({
    queryKey: ['cashflow-report', startDate, endDate],
    queryFn: () => getCashFlowReport(startDate || undefined, endDate || undefined),
  });

  const totalIncome = data?.entries.reduce((s, e) => s + e.totalIncome, 0) ?? 0;
  const totalExpense = data?.entries.reduce((s, e) => s + e.totalExpense, 0) ?? 0;

  return (
    <div className="space-y-6">
      <div>
        <Link
          to="/reports"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Reports
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Cash Flow Report</h2>
          {data && (
            <div className="flex gap-2">
              <Button size="sm" variant="secondary" onClick={() => window.print()}>
                <Printer className="h-4 w-4" /> Print
              </Button>
              <Button size="sm" variant="secondary" onClick={() => exportCsv(data)}>
                <Download className="h-4 w-4" /> Export CSV
              </Button>
            </div>
          )}
        </div>
      </div>

      {/* Date Range Filter */}
      <Card title="Date Range">
        <div className="flex flex-col gap-3 sm:flex-row sm:items-end">
          <div className="flex items-center gap-2">
            <Calendar className="h-4 w-4 text-gray-400" />
            <Input id="cf-start" type="date" label="From" value={startDate} onChange={(e) => setStartDate(e.target.value)} />
          </div>
          <div className="flex items-center gap-2">
            <Calendar className="h-4 w-4 text-gray-400" />
            <Input id="cf-end" type="date" label="To" value={endDate} onChange={(e) => setEndDate(e.target.value)} />
          </div>
        </div>
      </Card>

      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : (
        <>
          {/* Summary Cards */}
          <div className="grid grid-cols-3 gap-4">
            <Card>
              <p className="text-xs text-gray-500">Total Income</p>
              <p className="text-lg font-bold text-success">{formatCurrency(totalIncome)}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Total Expense</p>
              <p className="text-lg font-bold text-danger">{formatCurrency(totalExpense)}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Net Position</p>
              <p className={`text-lg font-bold ${totalIncome - totalExpense >= 0 ? 'text-success' : 'text-danger'}`}>
                {formatCurrency(totalIncome - totalExpense)}
              </p>
            </Card>
          </div>

          {/* S-Curve Chart */}
          {data && data.entries.length > 0 && (
            <Card title="Cash Flow S-Curve">
              <ResponsiveContainer width="100%" height={300}>
                <LineChart data={data.entries}>
                  <CartesianGrid strokeDasharray="3 3" />
                  <XAxis dataKey="month" tickFormatter={formatMonth} tick={{ fontSize: 12 }} />
                  <YAxis tickFormatter={(v) => `$${(v / 1000).toFixed(0)}k`} />
                  <Tooltip
                    labelFormatter={formatMonth}
                    formatter={(value: number) => formatCurrency(value)}
                  />
                  <Legend />
                  <Line type="monotone" dataKey="totalIncome" name="Income" stroke="#22c55e" strokeWidth={2} />
                  <Line type="monotone" dataKey="totalExpense" name="Expense" stroke="#ef4444" strokeWidth={2} />
                  <Line type="monotone" dataKey="cumulative" name="Cumulative" stroke="#3b82f6" strokeWidth={2} strokeDasharray="5 5" />
                </LineChart>
              </ResponsiveContainer>
            </Card>
          )}

          {/* Table */}
          <Card title="Monthly Breakdown">
            {(!data?.entries || data.entries.length === 0) ? (
              <p className="py-4 text-center text-sm text-gray-500">No cash flow data found</p>
            ) : (
              <Table headers={['Month', 'Income', 'Expense', 'Net', 'Cumulative']}>
                {data.entries.map((e) => (
                  <tr key={e.month}>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">{formatMonth(e.month)}</td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-success">{formatCurrency(e.totalIncome)}</td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-danger">{formatCurrency(e.totalExpense)}</td>
                    <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${e.totalIncome - e.totalExpense >= 0 ? 'text-success' : 'text-danger'}`}>
                      {formatCurrency(e.totalIncome - e.totalExpense)}
                    </td>
                    <td className={`whitespace-nowrap px-4 py-3 text-sm font-medium ${e.cumulative >= 0 ? 'text-success' : 'text-danger'}`}>
                      {formatCurrency(e.cumulative)}
                    </td>
                  </tr>
                ))}
              </Table>
            )}
          </Card>
        </>
      )}

      <style>{`
        @media print {
          body * { visibility: hidden; }
          .space-y-6, .space-y-6 * { visibility: visible; }
          .space-y-6 { position: absolute; left: 0; top: 0; width: 100%; }
          button, a { display: none !important; }
        }
      `}</style>
    </div>
  );
}

```

## web/src/pages/reports/ComplianceReportPage.tsx

```tsx
import { useState } from 'react';
import { Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft, Download, ShieldCheck, Printer } from 'lucide-react';
import { getTradies } from '@/api/tradies';
import type { Tradie } from '@/types';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Button from '@/components/ui/Button';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import EmptyState from '@/components/ui/EmptyState';
import { cn, formatDate } from '@/lib/utils';

type ComplianceStatus = 'valid' | 'expiring' | 'expired';

function getExpiryStatus(date?: string): ComplianceStatus {
  if (!date) return 'expired';
  const expiry = new Date(date);
  const now = new Date();
  const thirtyDays = new Date();
  thirtyDays.setDate(thirtyDays.getDate() + 30);
  if (expiry < now) return 'expired';
  if (expiry < thirtyDays) return 'expiring';
  return 'valid';
}

function getWorstStatus(...statuses: ComplianceStatus[]): ComplianceStatus {
  if (statuses.includes('expired')) return 'expired';
  if (statuses.includes('expiring')) return 'expiring';
  return 'valid';
}

const statusColors: Record<ComplianceStatus, string> = {
  valid: 'text-success',
  expiring: 'text-warning-500',
  expired: 'text-danger',
};

const statusBg: Record<ComplianceStatus, string> = {
  valid: 'bg-success-50 text-success-500',
  expiring: 'bg-warning-50 text-warning-500',
  expired: 'bg-danger-50 text-danger-500',
};

type FilterTab = 'all' | 'valid' | 'expiring' | 'expired';

function exportCsv(tradies: Tradie[]) {
  const rows = [
    ['Company', 'Contact', 'Trade', 'Licence Expiry', 'Insurance Expiry', 'Workers Comp Expiry'],
    ...tradies.map((t) => [
      `"${t.companyName || t.company || ''}"`,
      `"${t.contactName || `${t.firstName} ${t.lastName}`}"`,
      t.trade,
      t.licenceExpiry ? new Date(t.licenceExpiry).toLocaleDateString('en-AU') : 'N/A',
      t.insuranceExpiry ? new Date(t.insuranceExpiry).toLocaleDateString('en-AU') : 'N/A',
      t.workersCompExpiry ? new Date(t.workersCompExpiry).toLocaleDateString('en-AU') : 'N/A',
    ]),
  ];
  const csv = rows.map((r) => r.join(',')).join('\n');
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'compliance-report.csv';
  a.click();
  URL.revokeObjectURL(url);
}

export default function ComplianceReportPage() {
  const [tab, setTab] = useState<FilterTab>('all');

  const { data: tradies, isLoading } = useQuery({
    queryKey: ['tradies-compliance-report'],
    queryFn: () => getTradies({ status: 'ACTIVE' }),
  });

  const enriched = (tradies ?? []).map((t) => ({
    ...t,
    licenceStatus: getExpiryStatus(t.licenceExpiry),
    insuranceStatus: getExpiryStatus(t.insuranceExpiry),
    workersCompStatus: getExpiryStatus(t.workersCompExpiry),
    overallStatus: getWorstStatus(
      getExpiryStatus(t.licenceExpiry),
      getExpiryStatus(t.insuranceExpiry),
      getExpiryStatus(t.workersCompExpiry),
    ),
  }));

  const filtered = tab === 'all' ? enriched : enriched.filter((t) => t.overallStatus === tab);

  const counts = {
    all: enriched.length,
    valid: enriched.filter((t) => t.overallStatus === 'valid').length,
    expiring: enriched.filter((t) => t.overallStatus === 'expiring').length,
    expired: enriched.filter((t) => t.overallStatus === 'expired').length,
  };

  const tabs: { key: FilterTab; label: string }[] = [
    { key: 'all', label: `All (${counts.all})` },
    { key: 'valid', label: `Valid (${counts.valid})` },
    { key: 'expiring', label: `Expiring (${counts.expiring})` },
    { key: 'expired', label: `Expired (${counts.expired})` },
  ];

  return (
    <div className="space-y-6">
      <div>
        <Link
          to="/reports"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Reports
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Tradie Compliance</h2>
          {tradies && (
            <div className="flex gap-2">
              <Button size="sm" variant="secondary" onClick={() => window.print()}>
                <Printer className="h-4 w-4" /> Print
              </Button>
              <Button size="sm" variant="secondary" onClick={() => exportCsv(filtered)}>
                <Download className="h-4 w-4" /> Export CSV
              </Button>
            </div>
          )}
        </div>
      </div>

      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : (
        <>
          {/* Summary Cards */}
          <div className="grid grid-cols-3 gap-4">
            <Card>
              <p className="text-xs text-gray-500">Valid</p>
              <p className="text-lg font-bold text-success">{counts.valid}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Expiring (30 days)</p>
              <p className="text-lg font-bold text-warning-500">{counts.expiring}</p>
            </Card>
            <Card>
              <p className="text-xs text-gray-500">Expired</p>
              <p className="text-lg font-bold text-danger">{counts.expired}</p>
            </Card>
          </div>

          {/* Filter Tabs */}
          <div className="flex gap-1 overflow-x-auto rounded-lg bg-gray-100 p-1">
            {tabs.map((t) => (
              <button
                key={t.key}
                className={cn(
                  'rounded-md px-3 py-1.5 text-sm font-medium transition-colors',
                  tab === t.key
                    ? 'bg-white text-gray-900 shadow-sm'
                    : 'text-gray-500 hover:text-gray-700',
                )}
                onClick={() => setTab(t.key)}
              >
                {t.label}
              </button>
            ))}
          </div>

          {/* Table */}
          <Card>
            {filtered.length === 0 ? (
              <EmptyState
                icon={<ShieldCheck className="h-12 w-12" />}
                title="No tradies found"
                description={`No tradies with ${tab} compliance status.`}
              />
            ) : (
              <Table headers={['Company', 'Contact', 'Trade', 'Licence Expiry', 'Insurance Expiry', 'Workers Comp Expiry', 'Status']}>
                {filtered.map((t) => (
                  <tr key={t.id}>
                    <td className="px-4 py-3 text-sm font-medium text-gray-900">{t.companyName || t.company || '—'}</td>
                    <td className="px-4 py-3 text-sm text-gray-500">{t.contactName || `${t.firstName} ${t.lastName}`}</td>
                    <td className="px-4 py-3 text-sm text-gray-500 capitalize">{t.trade.toLowerCase().replace(/_/g, ' ')}</td>
                    <td className={cn('whitespace-nowrap px-4 py-3 text-sm', statusColors[t.licenceStatus])}>
                      {t.licenceExpiry ? formatDate(t.licenceExpiry) : 'N/A'}
                    </td>
                    <td className={cn('whitespace-nowrap px-4 py-3 text-sm', statusColors[t.insuranceStatus])}>
                      {t.insuranceExpiry ? formatDate(t.insuranceExpiry) : 'N/A'}
                    </td>
                    <td className={cn('whitespace-nowrap px-4 py-3 text-sm', statusColors[t.workersCompStatus])}>
                      {t.workersCompExpiry ? formatDate(t.workersCompExpiry) : 'N/A'}
                    </td>
                    <td className="px-4 py-3">
                      <span className={cn('inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium capitalize', statusBg[t.overallStatus])}>
                        {t.overallStatus}
                      </span>
                    </td>
                  </tr>
                ))}
              </Table>
            )}
          </Card>
        </>
      )}

      <style>{`
        @media print {
          body * { visibility: hidden; }
          .space-y-6, .space-y-6 * { visibility: visible; }
          .space-y-6 { position: absolute; left: 0; top: 0; width: 100%; }
          button, a { display: none !important; }
        }
      `}</style>
    </div>
  );
}

```

## web/src/pages/reports/ProjectStatusReportPage.tsx

```tsx
import { Link } from 'react-router-dom';
import { useQuery } from '@tanstack/react-query';
import { ArrowLeft, Download, FolderKanban, Printer } from 'lucide-react';
import { PieChart, Pie, Cell, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { getProjectStatusReport } from '@/api/reports';
import type { ProjectStatusReportData } from '@/api/reports';
import Card from '@/components/ui/Card';
import Table from '@/components/ui/Table';
import Badge from '@/components/ui/Badge';
import Button from '@/components/ui/Button';
import LoadingSpinner from '@/components/ui/LoadingSpinner';
import EmptyState from '@/components/ui/EmptyState';
import { formatCurrency, formatDate } from '@/lib/utils';

const STATUS_COLORS: Record<string, string> = {
  NOT_STARTED: '#9ca3af',
  IN_PROGRESS: '#f59e0b',
  ON_HOLD: '#ef4444',
  COMPLETE: '#22c55e',
};

function exportCsv(data: ProjectStatusReportData) {
  const rows = [
    ['Project', 'Status', 'Progress %', 'Contract Value', 'Start Date', 'Est End Date'],
    ...data.projects.map((p) => [
      `"${p.name}"`,
      p.status,
      p.progress.toString(),
      Number(p.contractValue).toFixed(2),
      p.startDate ? new Date(p.startDate).toLocaleDateString('en-AU') : '',
      p.estimatedEndDate ? new Date(p.estimatedEndDate).toLocaleDateString('en-AU') : '',
    ]),
  ];
  const csv = rows.map((r) => r.join(',')).join('\n');
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'project-status-report.csv';
  a.click();
  URL.revokeObjectURL(url);
}

export default function ProjectStatusReportPage() {
  const { data, isLoading } = useQuery({
    queryKey: ['project-status-report'],
    queryFn: getProjectStatusReport,
  });

  // Build pie chart data from status counts
  const statusCounts: Record<string, number> = {};
  (data?.projects ?? []).forEach((p) => {
    statusCounts[p.status] = (statusCounts[p.status] || 0) + 1;
  });
  const pieData = Object.entries(statusCounts).map(([name, value]) => ({ name, value }));

  return (
    <div className="space-y-6">
      <div>
        <Link
          to="/reports"
          className="mb-3 inline-flex items-center gap-1 text-sm text-gray-500 hover:text-gray-700"
        >
          <ArrowLeft className="h-4 w-4" /> Back to Reports
        </Link>
        <div className="flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
          <h2 className="text-xl font-bold text-gray-900">Project Status Report</h2>
          {data && (
            <div className="flex gap-2">
              <Button size="sm" variant="secondary" onClick={() => window.print()}>
                <Printer className="h-4 w-4" /> Print
              </Button>
              <Button size="sm" variant="secondary" onClick={() => exportCsv(data)}>
                <Download className="h-4 w-4" /> Export CSV
              </Button>
            </div>
          )}
        </div>
      </div>

      {isLoading ? (
        <LoadingSpinner className="py-20" size="lg" />
      ) : (
        <>
          {/* Summary Cards */}
          <div className="grid grid-cols-4 gap-4">
            {(['NOT_STARTED', 'IN_PROGRESS', 'ON_HOLD', 'COMPLETE'] as const).map((status) => (
              <Card key={status}>
                <p className="text-xs text-gray-500">{status.replace(/_/g, ' ')}</p>
                <p className="text-lg font-bold text-gray-900">{statusCounts[status] ?? 0}</p>
              </Card>
            ))}
          </div>

          {/* Pie Chart */}
          {pieData.length > 0 && (
            <Card title="Status Distribution">
              <ResponsiveContainer width="100%" height={300}>
                <PieChart>
                  <Pie
                    data={pieData}
                    cx="50%"
                    cy="50%"
                    outerRadius={100}
                    dataKey="value"
                    nameKey="name"
                    label={({ name, percent }) => `${name.replace(/_/g, ' ')} ${(percent * 100).toFixed(0)}%`}
                  >
                    {pieData.map((entry) => (
                      <Cell key={entry.name} fill={STATUS_COLORS[entry.name] || '#6b7280'} />
                    ))}
                  </Pie>
                  <Tooltip />
                  <Legend formatter={(value: string) => value.replace(/_/g, ' ')} />
                </PieChart>
              </ResponsiveContainer>
            </Card>
          )}

          {/* Table */}
          <Card title="Projects">
            {(!data?.projects || data.projects.length === 0) ? (
              <EmptyState
                icon={<FolderKanban className="h-12 w-12" />}
                title="No projects"
                description="No projects found."
              />
            ) : (
              <Table headers={['Project', 'Status', 'Progress', 'Contract Value', 'Start Date', 'Est End Date']}>
                {data.projects.map((p) => (
                  <tr key={p.id}>
                    <td className="px-4 py-3 text-sm font-medium text-gray-900">{p.name}</td>
                    <td className="px-4 py-3"><Badge status={p.status.toLowerCase()} /></td>
                    <td className="px-4 py-3">
                      <div className="flex items-center gap-2">
                        <div className="h-2 w-20 overflow-hidden rounded-full bg-gray-200">
                          <div
                            className="h-full rounded-full bg-primary"
                            style={{ width: `${p.progress}%` }}
                          />
                        </div>
                        <span className="text-xs text-gray-500">{p.progress}%</span>
                      </div>
                    </td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-900">{formatCurrency(p.contractValue)}</td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{p.startDate ? formatDate(p.startDate) : '—'}</td>
                    <td className="whitespace-nowrap px-4 py-3 text-sm text-gray-500">{p.estimatedEndDate ? formatDate(p.estimatedEndDate) : '—'}</td>
                  </tr>
                ))}
              </Table>
            )}
          </Card>
        </>
      )}

      <style>{`
        @media print {
          body * { visibility: hidden; }
          .space-y-6, .space-y-6 * { visibility: visible; }
          .space-y-6 { position: absolute; left: 0; top: 0; width: 100%; }
          button, a { display: none !important; }
        }
      `}</style>
    </div>
  );
}

```
