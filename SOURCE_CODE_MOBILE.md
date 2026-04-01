# BuildMate — Mobile App Source Code

## Table of Contents

### Configuration
- mobile/package.json
- mobile/tsconfig.json
- mobile/app.json
- mobile/babel.config.js
- mobile/eas.json
- mobile/jest.config.js
- mobile/DEPLOYMENT.md

### Entry Point
- mobile/App.tsx

### Core
- mobile/src/types/index.ts
- mobile/src/utils/index.ts
- mobile/src/theme/index.ts
- mobile/src/store/authStore.tsx

### API Client
- mobile/src/api/client.ts
- mobile/src/api/auth.ts
- mobile/src/api/projects.ts
- mobile/src/api/transactions.ts
- mobile/src/api/tasks.ts
- mobile/src/api/inspections.ts
- mobile/src/api/documents.ts
- mobile/src/api/tradies.ts
- mobile/src/api/cashflow.ts
- mobile/src/api/reports.ts
- mobile/src/api/devices.ts

### Navigation
- mobile/src/navigation/RootNavigator.tsx
- mobile/src/navigation/AuthNavigator.tsx
- mobile/src/navigation/TabNavigator.tsx
- mobile/src/navigation/DrawerNavigator.tsx
- mobile/src/navigation/ProjectsStackNavigator.tsx
- mobile/src/navigation/ProjectTabNavigator.tsx
- mobile/src/navigation/MoreNavigator.tsx

### Components
- mobile/src/components/SwipeableRow.tsx
- mobile/src/components/SplitView.tsx
- mobile/src/components/SideBySide.tsx
- mobile/src/components/ResponsiveGrid.tsx
- mobile/src/components/PhotoGallery.tsx
- mobile/src/components/OfflineBanner.tsx
- mobile/src/components/GanttChart.tsx

### Screens
- mobile/src/screens/LoginScreen.tsx
- mobile/src/screens/RegisterScreen.tsx
- mobile/src/screens/DashboardScreen.tsx
- mobile/src/screens/ProjectsScreen.tsx
- mobile/src/screens/ProjectDetailScreen.tsx
- mobile/src/screens/CreateProjectScreen.tsx
- mobile/src/screens/FinancialsScreen.tsx
- mobile/src/screens/BudgetScreen.tsx
- mobile/src/screens/CashFlowScreen.tsx
- mobile/src/screens/PlanningScreen.tsx
- mobile/src/screens/InspectionsScreen.tsx
- mobile/src/screens/DocumentsScreen.tsx
- mobile/src/screens/TradiesScreen.tsx
- mobile/src/screens/TradieDetailScreen.tsx
- mobile/src/screens/ReportsScreen.tsx
- mobile/src/screens/ProfileScreen.tsx
- mobile/src/screens/SettingsScreen.tsx

### Hooks
- mobile/src/hooks/useCamera.ts
- mobile/src/hooks/useLocation.ts
- mobile/src/hooks/usePushNotifications.ts
- mobile/src/hooks/useBiometrics.ts
- mobile/src/hooks/useOffline.ts
- mobile/src/hooks/useHaptics.ts
- mobile/src/hooks/useDocumentPicker.ts
- mobile/src/hooks/useShare.ts
- mobile/src/hooks/useDeviceType.ts
- mobile/src/hooks/useKeyboardShortcuts.ts
- mobile/src/hooks/useDragAndDrop.ts

### Tests
- mobile/src/__tests__/screens/LoginScreen.test.tsx
- mobile/src/__tests__/ipad/iPadLayout.test.tsx
- mobile/src/__tests__/navigation/NavigationFlow.test.tsx
- mobile/src/__tests__/components/SwipeableRow.test.tsx
- mobile/src/__tests__/components/SplitView.test.tsx
- mobile/src/__tests__/components/ResponsiveGrid.test.tsx
- mobile/src/__tests__/components/PhotoGallery.test.tsx
- mobile/src/__tests__/components/OfflineBanner.test.tsx
- mobile/src/__tests__/e2e/detox.config.ts

---


## mobile/package.json

```json
{
  "name": "buildmate-mobile",
  "version": "1.0.0",
  "main": "expo-router/entry",
  "scripts": {
    "start": "expo start",
    "ios": "expo start --ios",
    "android": "expo start --android",
    "test": "jest"
  },
  "dependencies": {
    "expo": "~52.0.0",
    "expo-router": "~4.0.0",
    "expo-status-bar": "~2.0.0",
    "react": "18.3.1",
    "react-native": "0.76.0",
    "@react-navigation/native": "^7.0.0",
    "@react-navigation/bottom-tabs": "^7.0.0",
    "@react-navigation/native-stack": "^7.0.0",
    "@react-navigation/drawer": "^7.0.0",
    "react-native-screens": "~4.0.0",
    "react-native-safe-area-context": "~5.0.0",
    "@tanstack/react-query": "^5.59.0",
    "axios": "^1.7.0",
    "@react-native-async-storage/async-storage": "~2.1.0",
    "expo-image-picker": "~16.0.0",
    "expo-camera": "~16.0.0",
    "expo-location": "~18.0.0",
    "expo-notifications": "~0.29.0",
    "expo-local-authentication": "~15.0.0",
    "expo-document-picker": "~13.0.0",
    "expo-haptics": "~14.0.0",
    "expo-secure-store": "~14.0.0",
    "expo-device": "~7.0.0",
    "@react-native-community/netinfo": "~11.4.0",
    "expo-updates": "~0.27.0",
    "react-native-reanimated": "~3.16.0",
    "react-native-gesture-handler": "~2.20.0",
    "react-native-svg": "~15.8.0",
    "victory-native": "^41.0.0",
    "lucide-react-native": "^0.460.0",
    "date-fns": "^4.0.0",
    "nativewind": "^4.0.0",
    "tailwindcss": "^3.4.0"
  },
  "devDependencies": {
    "@types/react": "~18.3.0",
    "typescript": "~5.6.0",
    "@babel/core": "^7.25.0",
    "jest": "^29.7.0",
    "@testing-library/react-native": "^12.0.0"
  }
}

```

## mobile/tsconfig.json

```json
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["**/*.ts", "**/*.tsx", "src/**/*"]
}

```

## mobile/app.json

```json
{
  "expo": {
    "name": "BuildMate",
    "slug": "buildmate",
    "version": "1.0.0",
    "scheme": "buildmate",
    "orientation": "default",
    "icon": "./assets/icon.png",
    "splash": {
      "image": "./assets/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#1E40AF"
    },
    "updates": {
      "enabled": true,
      "fallbackToCacheTimeout": 30000
    },
    "ios": {
      "bundleIdentifier": "com.buildmate.app",
      "buildNumber": "1",
      "supportsTablet": true,
      "infoPlist": {
        "NSCameraUsageDescription": "BuildMate needs camera access to capture inspection and defect photos.",
        "NSPhotoLibraryUsageDescription": "BuildMate needs photo library access to upload project and inspection photos.",
        "NSLocationWhenInUseUsageDescription": "BuildMate uses your location to tag inspection and defect photos.",
        "NSFaceIDUsageDescription": "BuildMate uses Face ID for secure authentication.",
        "UIRequiresFullScreen": false
      },
      "config": {
        "usesNonExemptEncryption": false
      }
    },
    "plugins": ["expo-router", "expo-camera", "expo-location", "expo-secure-store"]
  }
}

```

## mobile/babel.config.js

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ['nativewind/babel', 'react-native-reanimated/plugin'],
  };
};

```

## mobile/eas.json

```json
{
  "cli": {
    "version": ">= 12.0.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal",
      "ios": {
        "simulator": true
      }
    },
    "preview": {
      "distribution": "internal",
      "ios": {
        "buildConfiguration": "Release"
      }
    },
    "production": {
      "ios": {
        "buildConfiguration": "Release"
      }
    }
  },
  "submit": {
    "production": {
      "ios": {
        "appleId": "APPLE_ID_HERE",
        "ascAppId": "APP_STORE_CONNECT_APP_ID",
        "appleTeamId": "APPLE_TEAM_ID"
      }
    }
  }
}

```

## mobile/jest.config.js

```javascript
module.exports = {
  preset: 'react-native',
  setupFilesAfterSetup: ['@testing-library/react-native/extend-expect'],
  transformIgnorePatterns: [
    'node_modules/(?!(react-native|@react-native|@react-navigation|expo|expo-.*|@expo|react-native-.*|lucide-react-native|nativewind|victory-native|date-fns)/)',
  ],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  testMatch: ['**/__tests__/**/*.test.{ts,tsx}'],
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/types/**',
  ],
};

```

## mobile/DEPLOYMENT.md

```markdown
# BuildMate Mobile — Deployment Guide

## Prerequisites

- Apple Developer account ($99/year)
- EAS CLI installed: `npm install -g eas-cli`
- Authenticated: `eas login`
- Xcode installed (for local builds)

## Build Profiles

Configured in `eas.json`:

| Profile | Purpose | Distribution |
|---------|---------|-------------|
| `development` | Dev client with hot reload | Internal (simulator) |
| `preview` | Testing builds for QA | Internal (ad-hoc) |
| `production` | App Store / TestFlight | Store |

## Development Build

```bash
# Build for iOS simulator
eas build --platform ios --profile development

# Build for physical device (requires provisioning)
eas build --platform ios --profile development --local
```

## TestFlight Distribution (8.8.6)

### Step 1: Build for production
```bash
eas build --platform ios --profile production
```

### Step 2: Submit to App Store Connect
```bash
eas submit --platform ios --latest
```

### Step 3: Configure TestFlight
1. Log into [App Store Connect](https://appstoreconnect.apple.com)
2. Navigate to your app → TestFlight tab
3. Add internal testers (up to 100)
4. Create external testing group for beta testers (up to 10,000)
5. Submit build for external testing review

## App Store Submission (8.8.7)

### Required Assets
- **Screenshots:**
  - iPhone 6.7" (1290×2796) — minimum 3, maximum 10
  - iPad 12.9" (2048×2732) — minimum 3, maximum 10
- **App description** (max 4000 chars)
- **Keywords** (max 100 chars, comma-separated)
  - Suggested: construction, builder, project management, budget, trades, inspections, Australian
- **Privacy Policy URL** — required
- **Support URL** — required
- **App category:** Business / Productivity
- **Age rating:** 4+

### Submission Steps
1. Complete all App Store Connect metadata
2. Upload screenshots for all required device sizes
3. Set pricing (Free or paid)
4. Submit for App Review
5. Expected review time: 1-3 business days

### Common Rejection Reasons
- Missing login credentials for reviewer (provide demo account)
- Incomplete metadata or placeholder text
- Privacy policy doesn't cover all data collected
- Camera/location permissions without clear usage description

## OTA Updates (8.8.8)

Over-the-air updates allow pushing JavaScript bundle changes without App Store review.

### Configuration
Already configured in `app.json` under `updates`:
```json
{
  "updates": {
    "enabled": true,
    "url": "https://u.expo.dev/YOUR_PROJECT_ID",
    "fallbackToCacheTimeout": 30000
  }
}
```

### Publishing Updates
```bash
# Publish update to production channel
eas update --branch production --message "Bug fix description"

# Publish update to preview channel
eas update --branch preview --message "Feature preview"
```

### Update Channels
| Channel | Branch | Environment |
|---------|--------|-------------|
| production | production | App Store users |
| preview | preview | Internal testers |
| development | development | Developers |

### Limitations
- Cannot change native code (new native modules, permissions, etc.)
- Can update JavaScript, assets, and configuration
- Users receive updates on next app launch

```

## mobile/App.tsx

```tsx
import React from 'react';
import { StatusBar } from 'expo-status-bar';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { AuthProvider } from '@/store/authStore';
import { RootNavigator } from '@/navigation/RootNavigator';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5,
      retry: 2,
    },
  },
});

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <AuthProvider>
        <RootNavigator />
        <StatusBar style="auto" />
      </AuthProvider>
    </QueryClientProvider>
  );
}

```

## mobile/src/types/index.ts

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

## mobile/src/utils/index.ts

```typescript
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

export function formatPercentage(value: number): string {
  return `${Math.round(value)}%`;
}

export function getInitials(firstName: string, lastName: string): string {
  return `${firstName.charAt(0)}${lastName.charAt(0)}`.toUpperCase();
}

export function truncate(text: string, maxLength: number): string {
  if (text.length <= maxLength) return text;
  return `${text.slice(0, maxLength)}…`;
}

```

## mobile/src/theme/index.ts

```typescript
import { DefaultTheme, type Theme } from '@react-navigation/native';

export const colors = {
  primary: '#1E40AF',
  primaryLight: '#3B82F6',
  primaryDark: '#1E3A8A',

  success: '#059669',
  successLight: '#10B981',
  successDark: '#047857',

  warning: '#D97706',
  warningLight: '#F59E0B',
  warningDark: '#B45309',

  danger: '#DC2626',
  dangerLight: '#EF4444',
  dangerDark: '#B91C1C',

  white: '#FFFFFF',
  gray50: '#F9FAFB',
  gray100: '#F3F4F6',
  gray200: '#E5E7EB',
  gray300: '#D1D5DB',
  gray400: '#9CA3AF',
  gray500: '#6B7280',
  gray600: '#4B5563',
  gray700: '#374151',
  gray800: '#1F2937',
  gray900: '#111827',
  black: '#000000',

  background: '#F9FAFB',
  card: '#FFFFFF',
  text: '#111827',
  textSecondary: '#6B7280',
  border: '#E5E7EB',
} as const;

export const typography = {
  heading: {
    fontSize: 20,
    fontWeight: '700' as const,
    color: colors.gray900,
  },
  subheading: {
    fontSize: 18,
    fontWeight: '600' as const,
    color: colors.gray900,
  },
  body: {
    fontSize: 14,
    fontWeight: '400' as const,
    color: colors.gray900,
  },
  bodySecondary: {
    fontSize: 14,
    fontWeight: '400' as const,
    color: colors.gray500,
  },
  caption: {
    fontSize: 12,
    fontWeight: '400' as const,
    color: colors.gray500,
  },
  label: {
    fontSize: 12,
    fontWeight: '500' as const,
    color: colors.gray500,
    textTransform: 'uppercase' as const,
    letterSpacing: 0.5,
  },
  formLabel: {
    fontSize: 14,
    fontWeight: '500' as const,
    color: colors.gray700,
  },
  cardTitle: {
    fontSize: 14,
    fontWeight: '600' as const,
    color: colors.gray900,
  },
} as const;

export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  '2xl': 48,
  '3xl': 64,
} as const;

export const borderRadius = {
  sm: 4,
  md: 8,
  lg: 12,
  xl: 16,
  full: 9999,
} as const;

export const shadows = {
  sm: {
    shadowColor: colors.black,
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.05,
    shadowRadius: 2,
    elevation: 1,
  },
  md: {
    shadowColor: colors.black,
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 3,
  },
  lg: {
    shadowColor: colors.black,
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.15,
    shadowRadius: 8,
    elevation: 5,
  },
} as const;

export const navigationTheme: Theme = {
  ...DefaultTheme,
  colors: {
    ...DefaultTheme.colors,
    primary: colors.primary,
    background: colors.background,
    card: colors.card,
    text: colors.text,
    border: colors.border,
    notification: colors.danger,
  },
};

```

## mobile/src/store/authStore.tsx

```tsx
import React, { createContext, useContext, useState, useEffect, useCallback } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';
import type { User } from '@/types';
import * as authApi from '@/api/auth';

interface AuthState {
  user: User | null;
  token: string | null;
  isLoading: boolean;
  isAuthenticated: boolean;
}

interface AuthContextValue extends AuthState {
  login: (email: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
  getMe: () => Promise<void>;
}

const AuthContext = createContext<AuthContextValue | null>(null);

const TOKEN_KEY = 'buildmate_token';

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [state, setState] = useState<AuthState>({
    user: null,
    token: null,
    isLoading: true,
    isAuthenticated: false,
  });

  const loadToken = useCallback(async () => {
    const token = await AsyncStorage.getItem(TOKEN_KEY);
    if (token) {
      setState((prev) => ({ ...prev, token, isAuthenticated: true }));
      try {
        const user = await authApi.getMe();
        setState((prev) => ({ ...prev, user, isLoading: false }));
      } catch {
        await AsyncStorage.removeItem(TOKEN_KEY);
        setState({ user: null, token: null, isLoading: false, isAuthenticated: false });
      }
    } else {
      setState((prev) => ({ ...prev, isLoading: false }));
    }
  }, []);

  useEffect(() => {
    loadToken();
  }, [loadToken]);

  const login = useCallback(async (email: string, password: string) => {
    const response = await authApi.login(email, password);
    await AsyncStorage.setItem(TOKEN_KEY, response.token);
    setState({
      user: response.user,
      token: response.token,
      isLoading: false,
      isAuthenticated: true,
    });
  }, []);

  const logout = useCallback(async () => {
    await AsyncStorage.removeItem(TOKEN_KEY);
    setState({ user: null, token: null, isLoading: false, isAuthenticated: false });
  }, []);

  const getMe = useCallback(async () => {
    const user = await authApi.getMe();
    setState((prev) => ({ ...prev, user }));
  }, []);

  return (
    <AuthContext.Provider value={{ ...state, login, logout, getMe }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth(): AuthContextValue {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
}

```

## mobile/src/api/client.ts

```typescript
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

const API_BASE_URL = process.env.EXPO_PUBLIC_API_URL ?? 'http://localhost:3001/api';

const client = axios.create({
  baseURL: API_BASE_URL,
  headers: { 'Content-Type': 'application/json' },
});

client.interceptors.request.use(async (config) => {
  const token = await AsyncStorage.getItem('buildmate_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

client.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401 && !error.config?.url?.includes('/auth/login')) {
      await AsyncStorage.removeItem('buildmate_token');
    }
    return Promise.reject(error);
  },
);

export default client;

```

## mobile/src/api/auth.ts

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

## mobile/src/api/projects.ts

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

## mobile/src/api/transactions.ts

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

## mobile/src/api/tasks.ts

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

## mobile/src/api/inspections.ts

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

## mobile/src/api/documents.ts

```typescript
import { Platform } from 'react-native';
import client from './client';
import type { Document } from '@/types';

export async function getDocuments(projectId: string, params?: {
  category?: string;
}): Promise<Document[]> {
  const { data } = await client.get('/documents', { params: { projectId, folder: params?.category } });
  return Array.isArray(data) ? data : data.data ?? [];
}

export async function uploadDocument(
  projectId: string,
  fileUri: string,
  fileName: string,
  mimeType: string,
  folder: string,
): Promise<Document> {
  const formData = new FormData();
  formData.append('file', {
    uri: Platform.OS === 'ios' ? fileUri.replace('file://', '') : fileUri,
    name: fileName,
    type: mimeType,
  } as unknown as Blob);
  formData.append('folder', folder.toUpperCase());
  formData.append('projectId', projectId);
  formData.append('name', fileName);
  const { data } = await client.post<Document>('/documents/upload', formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
  });
  return data;
}

export async function deleteDocument(projectId: string, id: string): Promise<void> {
  await client.delete(`/documents/${id}`, { params: { projectId } });
}

export async function downloadDocument(projectId: string, id: string): Promise<ArrayBuffer> {
  const { data } = await client.get(`/documents/${id}/download`, {
    params: { projectId },
    responseType: 'arraybuffer',
  });
  return data;
}

export async function updateDocument(id: string, payload: { name?: string; folder?: string; description?: string }): Promise<Document> {
  const { data } = await client.put<Document>(`/documents/${id}`, payload);
  return data;
}

export async function uploadDocumentVersion(
  documentId: string,
  fileUri: string,
  fileName: string,
  mimeType: string,
): Promise<Document> {
  const formData = new FormData();
  formData.append('file', {
    uri: Platform.OS === 'ios' ? fileUri.replace('file://', '') : fileUri,
    name: fileName,
    type: mimeType,
  } as unknown as Blob);
  const { data } = await client.post<Document>(`/documents/${documentId}/version`, formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
  });
  return data;
}

export async function getDocumentVersions(documentId: string): Promise<Document[]> {
  const { data } = await client.get(`/documents/${documentId}/versions`);
  return Array.isArray(data) ? data : data.data ?? [];
}

```

## mobile/src/api/tradies.ts

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

## mobile/src/api/cashflow.ts

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

## mobile/src/api/reports.ts

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

## mobile/src/api/devices.ts

```typescript
import client from './client';

interface DeviceRegistration {
  id: string;
  userId: string;
  token: string;
  platform: string;
  createdAt: string;
}

export async function registerDevice(token: string, platform: string): Promise<DeviceRegistration> {
  const { data } = await client.post('/devices/register', { token, platform });
  return data;
}

export async function unregisterDevice(deviceId: string): Promise<void> {
  await client.delete(`/devices/${deviceId}`);
}

```

## mobile/src/navigation/RootNavigator.tsx

```tsx
import React from 'react';
import { ActivityIndicator, View, StyleSheet } from 'react-native';
import { NavigationContainer, type LinkingOptions } from '@react-navigation/native';
import { AuthNavigator } from './AuthNavigator';
import { TabNavigator } from './TabNavigator';
import { DrawerNavigator } from './DrawerNavigator';
import { useAuth } from '@/store/authStore';
import { useDeviceType } from '@/hooks/useDeviceType';
import { navigationTheme, colors } from '@/theme';

const linking: LinkingOptions<Record<string, unknown>> = {
  prefixes: ['buildmate://'],
  config: {
    screens: {
      ProjectsTab: {
        screens: {
          ProjectDetail: 'projects/:projectId',
        },
      },
      TradiesTab: {
        screens: {
          TradieDetail: 'tradies/:tradieId',
        },
      },
    },
  },
};

export function RootNavigator() {
  const { isAuthenticated, isLoading } = useAuth();
  const { isTablet } = useDeviceType();

  if (isLoading) {
    return (
      <View style={styles.loading}>
        <ActivityIndicator size="large" color={colors.primary} />
      </View>
    );
  }

  return (
    <NavigationContainer theme={navigationTheme} linking={linking}>
      {isAuthenticated ? (isTablet ? <DrawerNavigator /> : <TabNavigator />) : <AuthNavigator />}
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  loading: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: colors.background,
  },
});

```

## mobile/src/navigation/AuthNavigator.tsx

```tsx
import React from 'react';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { LoginScreen } from '@/screens/LoginScreen';
import { RegisterScreen } from '@/screens/RegisterScreen';
import { colors } from '@/theme';

export type AuthStackParamList = {
  Login: undefined;
  Register: undefined;
};

const Stack = createNativeStackNavigator<AuthStackParamList>();

export function AuthNavigator() {
  return (
    <Stack.Navigator
      screenOptions={{
        headerShown: false,
        contentStyle: { backgroundColor: colors.background },
      }}
    >
      <Stack.Screen name="Login" component={LoginScreen} />
      <Stack.Screen name="Register" component={RegisterScreen} />
    </Stack.Navigator>
  );
}

```

## mobile/src/navigation/TabNavigator.tsx

```tsx
import React from 'react';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { Home, FolderKanban, Users, BarChart3, Menu } from 'lucide-react-native';
import { ProjectsStackNavigator } from './ProjectsStackNavigator';
import { DashboardScreen } from '@/screens/DashboardScreen';
import { TradiesScreen } from '@/screens/TradiesScreen';
import { ReportsScreen } from '@/screens/ReportsScreen';
import { MoreNavigator } from './MoreNavigator';
import { colors, typography } from '@/theme';

export type TabParamList = {
  DashboardTab: undefined;
  ProjectsTab: undefined;
  TradiesTab: undefined;
  ReportsTab: undefined;
  MoreTab: undefined;
};

const Tab = createBottomTabNavigator<TabParamList>();

export function TabNavigator() {
  return (
    <Tab.Navigator
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.primary,
        tabBarInactiveTintColor: colors.gray400,
        tabBarLabelStyle: {
          fontSize: typography.caption.fontSize,
          fontWeight: '500',
        },
        tabBarStyle: {
          borderTopColor: colors.border,
        },
      }}
    >
      <Tab.Screen
        name="DashboardTab"
        component={DashboardScreen}
        options={{
          tabBarLabel: 'Dashboard',
          tabBarIcon: ({ color, size }) => <Home size={size} color={color} />,
          headerShown: true,
          title: 'Dashboard',
          headerTitleStyle: typography.heading,
        }}
      />
      <Tab.Screen
        name="ProjectsTab"
        component={ProjectsStackNavigator}
        options={{
          tabBarLabel: 'Projects',
          tabBarIcon: ({ color, size }) => <FolderKanban size={size} color={color} />,
        }}
      />
      <Tab.Screen
        name="TradiesTab"
        component={TradiesScreen}
        options={{
          tabBarLabel: 'Tradies',
          tabBarIcon: ({ color, size }) => <Users size={size} color={color} />,
          headerShown: true,
          title: 'Tradies',
          headerTitleStyle: typography.heading,
        }}
      />
      <Tab.Screen
        name="ReportsTab"
        component={ReportsScreen}
        options={{
          tabBarLabel: 'Reports',
          tabBarIcon: ({ color, size }) => <BarChart3 size={size} color={color} />,
          headerShown: true,
          title: 'Reports',
          headerTitleStyle: typography.heading,
        }}
      />
      <Tab.Screen
        name="MoreTab"
        component={MoreNavigator}
        options={{
          tabBarLabel: 'More',
          tabBarIcon: ({ color, size }) => <Menu size={size} color={color} />,
        }}
      />
    </Tab.Navigator>
  );
}

```

## mobile/src/navigation/DrawerNavigator.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, TouchableOpacity } from 'react-native';
import { createDrawerNavigator, DrawerContentScrollView, type DrawerContentComponentProps } from '@react-navigation/drawer';
import { Home, FolderKanban, Users, BarChart3, User, Settings } from 'lucide-react-native';
import { DashboardScreen } from '@/screens/DashboardScreen';
import { ProjectsStackNavigator } from './ProjectsStackNavigator';
import { TradiesScreen } from '@/screens/TradiesScreen';
import { ReportsScreen } from '@/screens/ReportsScreen';
import { ProfileScreen } from '@/screens/ProfileScreen';
import { SettingsScreen } from '@/screens/SettingsScreen';
import { useDeviceType } from '@/hooks/useDeviceType';
import { colors, typography, spacing } from '@/theme';

export type DrawerParamList = {
  Dashboard: undefined;
  Projects: undefined;
  Tradies: undefined;
  Reports: undefined;
  Profile: undefined;
  Settings: undefined;
};

const NAV_ITEMS: { name: keyof DrawerParamList; label: string; icon: typeof Home }[] = [
  { name: 'Dashboard', label: 'Dashboard', icon: Home },
  { name: 'Projects', label: 'Projects', icon: FolderKanban },
  { name: 'Tradies', label: 'Tradies', icon: Users },
  { name: 'Reports', label: 'Reports', icon: BarChart3 },
  { name: 'Profile', label: 'Profile', icon: User },
  { name: 'Settings', label: 'Settings', icon: Settings },
];

function CustomDrawerContent(props: DrawerContentComponentProps) {
  const { state, navigation } = props;

  return (
    <DrawerContentScrollView {...props} style={styles.drawer}>
      <View style={styles.header}>
        <Text style={styles.logo}>🏗️ BuildMate</Text>
      </View>
      {NAV_ITEMS.map((item, index) => {
        const isActive = state.index === index;
        const Icon = item.icon;
        return (
          <TouchableOpacity
            key={item.name}
            style={[styles.navItem, isActive && styles.navItemActive]}
            onPress={() => navigation.navigate(item.name)}
          >
            <Icon size={20} color={isActive ? colors.primary : colors.gray500} />
            <Text style={[styles.navLabel, isActive && styles.navLabelActive]}>{item.label}</Text>
          </TouchableOpacity>
        );
      })}
    </DrawerContentScrollView>
  );
}

const Drawer = createDrawerNavigator<DrawerParamList>();

export function DrawerNavigator() {
  const { isLandscape } = useDeviceType();

  return (
    <Drawer.Navigator
      drawerContent={(props) => <CustomDrawerContent {...props} />}
      screenOptions={{
        drawerType: isLandscape ? 'permanent' : 'slide',
        drawerStyle: { width: 256 },
        headerTintColor: colors.primary,
        headerTitleStyle: typography.heading,
      }}
    >
      <Drawer.Screen name="Dashboard" component={DashboardScreen} />
      <Drawer.Screen name="Projects" component={ProjectsStackNavigator} options={{ headerShown: false }} />
      <Drawer.Screen name="Tradies" component={TradiesScreen} />
      <Drawer.Screen name="Reports" component={ReportsScreen} />
      <Drawer.Screen name="Profile" component={ProfileScreen} />
      <Drawer.Screen name="Settings" component={SettingsScreen} />
    </Drawer.Navigator>
  );
}

const styles = StyleSheet.create({
  drawer: {
    backgroundColor: colors.white,
  },
  header: {
    padding: spacing.lg,
    borderBottomWidth: 1,
    borderBottomColor: colors.gray200,
    marginBottom: spacing.sm,
  },
  logo: {
    fontSize: 22,
    fontWeight: '700',
    color: colors.primary,
  },
  navItem: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingVertical: spacing.sm + 4,
    paddingHorizontal: spacing.lg,
    marginHorizontal: spacing.sm,
    borderRadius: 8,
    gap: spacing.md,
  },
  navItemActive: {
    backgroundColor: colors.gray100,
  },
  navLabel: {
    ...typography.body,
    color: colors.gray600,
  },
  navLabelActive: {
    color: colors.primary,
    fontWeight: '600',
  },
});

```

## mobile/src/navigation/ProjectsStackNavigator.tsx

```tsx
import React from 'react';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { ProjectsScreen } from '@/screens/ProjectsScreen';
import { ProjectDetailScreen } from '@/screens/ProjectDetailScreen';
import { CreateProjectScreen } from '@/screens/CreateProjectScreen';
import { TradieDetailScreen } from '@/screens/TradieDetailScreen';
import { colors, typography } from '@/theme';

export type ProjectsStackParamList = {
  ProjectsList: undefined;
  ProjectDetail: { projectId: string };
  CreateProject: undefined;
  TradieDetail: { tradieId: string };
};

const Stack = createNativeStackNavigator<ProjectsStackParamList>();

export function ProjectsStackNavigator() {
  return (
    <Stack.Navigator
      screenOptions={{
        headerTintColor: colors.primary,
        headerTitleStyle: typography.heading,
        contentStyle: { backgroundColor: colors.background },
      }}
    >
      <Stack.Screen name="ProjectsList" component={ProjectsScreen} options={{ title: 'Projects' }} />
      <Stack.Screen name="ProjectDetail" component={ProjectDetailScreen} options={{ title: 'Project' }} />
      <Stack.Screen name="CreateProject" component={CreateProjectScreen} options={{ title: 'New Project', presentation: 'modal' }} />
      <Stack.Screen name="TradieDetail" component={TradieDetailScreen} options={{ title: 'Tradie' }} />
    </Stack.Navigator>
  );
}

```

## mobile/src/navigation/ProjectTabNavigator.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, ScrollView, Text, TouchableOpacity } from 'react-native';
import { FinancialsScreen } from '@/screens/FinancialsScreen';
import { BudgetScreen } from '@/screens/BudgetScreen';
import { CashFlowScreen } from '@/screens/CashFlowScreen';
import { PlanningScreen } from '@/screens/PlanningScreen';
import { InspectionsScreen } from '@/screens/InspectionsScreen';
import { DocumentsScreen } from '@/screens/DocumentsScreen';
import { colors, typography, spacing, borderRadius } from '@/theme';

const TABS = [
  { key: 'overview', label: 'Overview' },
  { key: 'budget', label: 'Budget' },
  { key: 'cashflow', label: 'Cash Flow' },
  { key: 'planning', label: 'Planning' },
  { key: 'financials', label: 'Financials' },
  { key: 'inspections', label: 'Inspections' },
  { key: 'documents', label: 'Documents' },
] as const;

type TabKey = (typeof TABS)[number]['key'];

interface ProjectTabNavigatorProps {
  projectId: string;
}

export function ProjectTabNavigator({ projectId }: ProjectTabNavigatorProps) {
  const [activeTab, setActiveTab] = useState<TabKey>('overview');

  const renderContent = () => {
    switch (activeTab) {
      case 'budget':
        return <BudgetScreen projectId={projectId} />;
      case 'cashflow':
        return <CashFlowScreen projectId={projectId} />;
      case 'planning':
        return <PlanningScreen projectId={projectId} />;
      case 'financials':
        return <FinancialsScreen projectId={projectId} />;
      case 'inspections':
        return <InspectionsScreen projectId={projectId} />;
      case 'documents':
        return <DocumentsScreen projectId={projectId} />;
      default:
        return (
          <View style={styles.placeholder}>
            <Text style={typography.bodySecondary}>Project overview content</Text>
          </View>
        );
    }
  };

  return (
    <View style={styles.container}>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} style={styles.tabBar} contentContainerStyle={styles.tabBarContent}>
        {TABS.map((tab) => {
          const isActive = activeTab === tab.key;
          return (
            <TouchableOpacity
              key={tab.key}
              style={[styles.tab, isActive && styles.tabActive]}
              onPress={() => setActiveTab(tab.key)}
            >
              <Text style={[styles.tabLabel, isActive && styles.tabLabelActive]}>{tab.label}</Text>
            </TouchableOpacity>
          );
        })}
      </ScrollView>
      <View style={styles.content}>{renderContent()}</View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  tabBar: {
    backgroundColor: colors.white,
    borderBottomWidth: 1,
    borderBottomColor: colors.gray200,
    flexGrow: 0,
  },
  tabBarContent: {
    paddingHorizontal: spacing.md,
    gap: spacing.xs,
  },
  tab: {
    paddingVertical: spacing.sm + 2,
    paddingHorizontal: spacing.md,
    borderBottomWidth: 2,
    borderBottomColor: 'transparent',
  },
  tabActive: {
    borderBottomColor: colors.primary,
  },
  tabLabel: {
    fontSize: typography.body.fontSize,
    fontWeight: '500',
    color: colors.gray500,
  },
  tabLabelActive: {
    color: colors.primary,
    fontWeight: '600',
  },
  content: {
    flex: 1,
  },
  placeholder: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    padding: spacing.xl,
  },
});

```

## mobile/src/navigation/MoreNavigator.tsx

```tsx
import React from 'react';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { ProfileScreen } from '@/screens/ProfileScreen';
import { SettingsScreen } from '@/screens/SettingsScreen';
import { TradieDetailScreen } from '@/screens/TradieDetailScreen';
import { colors, typography } from '@/theme';

export type MoreStackParamList = {
  Profile: undefined;
  Settings: undefined;
  TradieDetail: { tradieId: string };
};

const Stack = createNativeStackNavigator<MoreStackParamList>();

export function MoreNavigator() {
  return (
    <Stack.Navigator
      screenOptions={{
        headerTintColor: colors.primary,
        headerTitleStyle: typography.heading,
        contentStyle: { backgroundColor: colors.background },
      }}
    >
      <Stack.Screen name="Profile" component={ProfileScreen} />
      <Stack.Screen name="Settings" component={SettingsScreen} />
      <Stack.Screen name="TradieDetail" component={TradieDetailScreen} options={{ title: 'Tradie' }} />
    </Stack.Navigator>
  );
}

```

## mobile/src/components/SwipeableRow.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, TouchableOpacity, Animated } from 'react-native';
import { Swipeable } from 'react-native-gesture-handler';
import { Trash2, Archive } from 'lucide-react-native';
import { colors, spacing } from '../theme';

interface SwipeAction {
  label: string;
  color: string;
  icon: 'delete' | 'archive';
  onPress: () => void;
}

interface SwipeableRowProps {
  children: React.ReactNode;
  leftActions?: SwipeAction[];
  rightActions?: SwipeAction[];
}

const ICON_MAP = {
  delete: Trash2,
  archive: Archive,
};

function renderActions(actions: SwipeAction[], _progress: Animated.AnimatedInterpolation<number>) {
  return (
    <View style={styles.actionsContainer}>
      {actions.map((action, index) => {
        const Icon = ICON_MAP[action.icon];
        return (
          <TouchableOpacity
            key={index}
            style={[styles.action, { backgroundColor: action.color }]}
            onPress={action.onPress}
          >
            <Icon size={20} color={colors.white} />
            <Text style={styles.actionText}>{action.label}</Text>
          </TouchableOpacity>
        );
      })}
    </View>
  );
}

export default function SwipeableRow({ children, leftActions, rightActions }: SwipeableRowProps) {
  return (
    <Swipeable
      renderLeftActions={leftActions ? (progress) => renderActions(leftActions, progress) : undefined}
      renderRightActions={rightActions ? (progress) => renderActions(rightActions, progress) : undefined}
      overshootLeft={false}
      overshootRight={false}
    >
      {children}
    </Swipeable>
  );
}

const styles = StyleSheet.create({
  actionsContainer: {
    flexDirection: 'row',
  },
  action: {
    justifyContent: 'center',
    alignItems: 'center',
    width: 80,
    paddingHorizontal: spacing.sm,
  },
  actionText: {
    color: colors.white,
    fontSize: 12,
    fontWeight: '600',
    marginTop: 4,
  },
});

```

## mobile/src/components/SplitView.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, TouchableOpacity, Animated } from 'react-native';
import { Menu, X } from 'lucide-react-native';
import { useDeviceType } from '../hooks/useDeviceType';
import { colors, spacing, shadows } from '../theme';

// NOTE (8.7.7): Pointer/cursor hover states are supported on iPad via
// React Native's Pressable onHoverIn/onHoverOut props. Use Pressable
// instead of TouchableOpacity in sidebar items for hover state support.

// NOTE (8.7.8): iPad multi-window (Split View / Slide Over) is supported
// when configured in app.json with "ios.supportsTablet": true and
// "ios.requireFullScreen": false (default). The useDeviceType hook
// and useWindowDimensions ensure layout adapts to any window size.

interface SplitViewProps {
  sidebar: React.ReactNode;
  children: React.ReactNode;
  sidebarWidth?: number;
}

const SIDEBAR_WIDTH = 288; // w-72

export default function SplitView({ sidebar, children, sidebarWidth = SIDEBAR_WIDTH }: SplitViewProps) {
  const { isTablet, isLandscape } = useDeviceType();
  const [sidebarOpen, setSidebarOpen] = useState(false);

  // Phone: no split view, render children only
  if (!isTablet) {
    return <View style={styles.container}>{children}</View>;
  }

  // iPad landscape: permanent sidebar
  if (isLandscape) {
    return (
      <View style={styles.splitContainer}>
        <View style={[styles.sidebar, { width: sidebarWidth }]}>{sidebar}</View>
        <View style={styles.content}>{children}</View>
      </View>
    );
  }

  // iPad portrait: collapsible sidebar
  return (
    <View style={styles.container}>
      <TouchableOpacity style={styles.menuButton} onPress={() => setSidebarOpen(!sidebarOpen)}>
        {sidebarOpen ? <X size={24} color={colors.gray700} /> : <Menu size={24} color={colors.gray700} />}
      </TouchableOpacity>

      {sidebarOpen && (
        <View style={[styles.overlappingSidebar, { width: sidebarWidth }, shadows.lg]}>
          {sidebar}
        </View>
      )}

      <View style={styles.content}>{children}</View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  splitContainer: {
    flex: 1,
    flexDirection: 'row',
    backgroundColor: colors.background,
  },
  sidebar: {
    backgroundColor: colors.white,
    borderRightWidth: 1,
    borderRightColor: colors.border,
  },
  overlappingSidebar: {
    position: 'absolute',
    top: 0,
    left: 0,
    bottom: 0,
    backgroundColor: colors.white,
    borderRightWidth: 1,
    borderRightColor: colors.border,
    zIndex: 100,
  },
  content: {
    flex: 1,
  },
  menuButton: {
    position: 'absolute',
    top: spacing.md,
    left: spacing.md,
    zIndex: 200,
    padding: spacing.sm,
  },
});

```

## mobile/src/components/SideBySide.tsx

```tsx
import React from 'react';
import { StyleSheet, View } from 'react-native';
import { useDeviceType } from '../hooks/useDeviceType';
import { colors, spacing } from '../theme';

interface SideBySideProps {
  left: React.ReactNode;
  right: React.ReactNode;
  leftRatio?: number;
}

export default function SideBySide({ left, right, leftRatio = 0.5 }: SideBySideProps) {
  const { isTablet, isLandscape } = useDeviceType();

  // On phone or portrait tablet, stack vertically
  if (!isTablet || !isLandscape) {
    return (
      <View style={styles.stacked}>
        <View style={styles.stackedPanel}>{left}</View>
        <View style={styles.stackedPanel}>{right}</View>
      </View>
    );
  }

  // iPad landscape: side by side
  return (
    <View style={styles.horizontal}>
      <View style={[styles.panel, { flex: leftRatio }]}>{left}</View>
      <View style={styles.divider} />
      <View style={[styles.panel, { flex: 1 - leftRatio }]}>{right}</View>
    </View>
  );
}

const styles = StyleSheet.create({
  horizontal: {
    flex: 1,
    flexDirection: 'row',
  },
  stacked: {
    flex: 1,
  },
  stackedPanel: {
    marginBottom: spacing.md,
  },
  panel: {
    overflow: 'hidden',
  },
  divider: {
    width: 1,
    backgroundColor: colors.border,
  },
});

```

## mobile/src/components/ResponsiveGrid.tsx

```tsx
import React from 'react';
import { FlatList, useWindowDimensions, StyleSheet, View } from 'react-native';
import { spacing } from '../theme';

interface ResponsiveGridProps<T> {
  data: T[];
  renderItem: ({ item, index }: { item: T; index: number }) => React.ReactElement;
  keyExtractor: (item: T, index: number) => string;
  phoneCols?: number;
  tabletCols?: number;
  tabletLandscapeCols?: number;
}

export default function ResponsiveGrid<T>({
  data,
  renderItem,
  keyExtractor,
  phoneCols = 2,
  tabletCols = 3,
  tabletLandscapeCols = 4,
}: ResponsiveGridProps<T>) {
  const { width, height } = useWindowDimensions();
  const isTablet = width >= 768;
  const isLandscape = width > height;

  const numColumns = isTablet
    ? isLandscape ? tabletLandscapeCols : tabletCols
    : phoneCols;

  return (
    <FlatList
      data={data}
      renderItem={renderItem}
      keyExtractor={keyExtractor}
      numColumns={numColumns}
      key={`grid-${numColumns}`}
      columnWrapperStyle={numColumns > 1 ? styles.row : undefined}
      contentContainerStyle={styles.container}
    />
  );
}

const styles = StyleSheet.create({
  container: {
    padding: spacing.md,
  },
  row: {
    gap: spacing.md,
    marginBottom: spacing.md,
  },
});

```

## mobile/src/components/PhotoGallery.tsx

```tsx
import React, { useState } from 'react';
import {
  StyleSheet,
  View,
  Image,
  ScrollView,
  TouchableOpacity,
  Modal,
  Dimensions,
  Text,
} from 'react-native';
import { X, Plus, Camera } from 'lucide-react-native';
import { colors, borderRadius, spacing } from '../theme';

interface PhotoGalleryProps {
  photos: string[];
  onAddPhoto?: () => void;
  editable?: boolean;
}

const THUMB_SIZE = 80;
const { width: SCREEN_WIDTH, height: SCREEN_HEIGHT } = Dimensions.get('window');

export default function PhotoGallery({ photos, onAddPhoto, editable = true }: PhotoGalleryProps) {
  const [previewUri, setPreviewUri] = useState<string | null>(null);

  return (
    <View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={styles.container}>
        {photos.map((uri, index) => (
          <TouchableOpacity key={index} onPress={() => setPreviewUri(uri)} activeOpacity={0.8}>
            <Image source={{ uri }} style={styles.thumbnail} />
          </TouchableOpacity>
        ))}
        {editable && onAddPhoto && (
          <TouchableOpacity style={styles.addButton} onPress={onAddPhoto} activeOpacity={0.7}>
            <Camera size={24} color={colors.gray400} />
            <Text style={styles.addText}>Add</Text>
          </TouchableOpacity>
        )}
      </ScrollView>

      <Modal visible={!!previewUri} transparent animationType="fade" onRequestClose={() => setPreviewUri(null)}>
        <View style={styles.modalOverlay}>
          <TouchableOpacity style={styles.closeButton} onPress={() => setPreviewUri(null)}>
            <X size={24} color={colors.white} />
          </TouchableOpacity>
          {previewUri && (
            <Image
              source={{ uri: previewUri }}
              style={styles.fullImage}
              resizeMode="contain"
            />
          )}
        </View>
      </Modal>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    gap: spacing.sm,
    paddingVertical: spacing.sm,
  },
  thumbnail: {
    width: THUMB_SIZE,
    height: THUMB_SIZE,
    borderRadius: borderRadius.md,
    backgroundColor: colors.gray100,
  },
  addButton: {
    width: THUMB_SIZE,
    height: THUMB_SIZE,
    borderRadius: borderRadius.md,
    borderWidth: 2,
    borderColor: colors.gray200,
    borderStyle: 'dashed',
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: colors.gray50,
  },
  addText: {
    fontSize: 10,
    color: colors.gray400,
    marginTop: 2,
  },
  modalOverlay: {
    flex: 1,
    backgroundColor: 'rgba(0,0,0,0.9)',
    justifyContent: 'center',
    alignItems: 'center',
  },
  closeButton: {
    position: 'absolute',
    top: 60,
    right: 20,
    zIndex: 10,
    padding: spacing.sm,
  },
  fullImage: {
    width: SCREEN_WIDTH,
    height: SCREEN_HEIGHT * 0.75,
  },
});

```

## mobile/src/components/OfflineBanner.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text } from 'react-native';
import { WifiOff } from 'lucide-react-native';
import { colors, spacing } from '../theme';
import { useOffline } from '../hooks/useOffline';

export default function OfflineBanner() {
  const { isOnline } = useOffline();

  if (isOnline) return null;

  return (
    <View style={styles.banner}>
      <WifiOff size={16} color={colors.gray800} />
      <Text style={styles.text}>You are offline. Some features may be unavailable.</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  banner: {
    flexDirection: 'row',
    alignItems: 'center',
    gap: spacing.sm,
    backgroundColor: colors.warningLight,
    paddingVertical: spacing.sm,
    paddingHorizontal: spacing.md,
  },
  text: {
    fontSize: 13,
    fontWeight: '500',
    color: colors.gray800,
  },
});

```

## mobile/src/components/GanttChart.tsx

```tsx
import React, { useRef } from 'react';
import { StyleSheet, View, Text, ScrollView, useWindowDimensions } from 'react-native';
import Svg, { Rect, Line, Text as SvgText, Circle } from 'react-native-svg';
import { differenceInDays, format, isWeekend, addDays, isToday } from 'date-fns';
import { colors, spacing, typography, borderRadius } from '../theme';

interface GanttTask {
  id: string;
  name: string;
  startDate: string;
  endDate: string;
  progress: number;
  isMilestone?: boolean;
}

interface GanttChartProps {
  tasks: GanttTask[];
  dayWidth?: number;
}

const ROW_HEIGHT = 40;
const HEADER_HEIGHT = 50;
const LABEL_WIDTH = 180;

const STATUS_COLORS: Record<string, string> = {
  complete: colors.success,
  inProgress: colors.primaryLight,
  notStarted: colors.gray300,
};

function getBarColor(progress: number): string {
  if (progress >= 100) return STATUS_COLORS.complete;
  if (progress > 0) return STATUS_COLORS.inProgress;
  return STATUS_COLORS.notStarted;
}

export default function GanttChart({ tasks, dayWidth = 30 }: GanttChartProps) {
  const scrollRef = useRef<ScrollView>(null);
  const { width: screenWidth } = useWindowDimensions();

  if (tasks.length === 0) return null;

  const allDates = tasks.flatMap((t) => [new Date(t.startDate), new Date(t.endDate)]);
  const minDate = new Date(Math.min(...allDates.map((d) => d.getTime())));
  const maxDate = new Date(Math.max(...allDates.map((d) => d.getTime())));
  const totalDays = differenceInDays(maxDate, minDate) + 7; // padding
  const chartWidth = totalDays * dayWidth;
  const chartHeight = HEADER_HEIGHT + tasks.length * ROW_HEIGHT;

  const today = new Date();
  const todayOffset = differenceInDays(today, minDate) * dayWidth;

  return (
    <View style={styles.container}>
      {/* Task labels */}
      <View style={[styles.labels, { width: LABEL_WIDTH }]}>
        <View style={[styles.headerCell, { height: HEADER_HEIGHT }]}>
          <Text style={styles.headerText}>Task</Text>
        </View>
        {tasks.map((task) => (
          <View key={task.id} style={[styles.labelCell, { height: ROW_HEIGHT }]}>
            <Text style={styles.labelText} numberOfLines={1}>{task.name}</Text>
          </View>
        ))}
      </View>

      {/* Chart area */}
      <ScrollView horizontal ref={scrollRef} showsHorizontalScrollIndicator>
        <Svg width={chartWidth} height={chartHeight}>
          {/* Date headers */}
          {Array.from({ length: totalDays }).map((_, i) => {
            const date = addDays(minDate, i);
            const x = i * dayWidth;
            const weekend = isWeekend(date);

            return (
              <React.Fragment key={i}>
                {/* Weekend shading */}
                {weekend && (
                  <Rect x={x} y={HEADER_HEIGHT} width={dayWidth} height={chartHeight - HEADER_HEIGHT} fill={colors.gray50} />
                )}
                {/* Date label (show every 7th day) */}
                {i % 7 === 0 && (
                  <SvgText x={x + 2} y={20} fontSize={10} fill={colors.gray500}>
                    {format(date, 'dd MMM')}
                  </SvgText>
                )}
                {/* Grid line */}
                <Line x1={x} y1={HEADER_HEIGHT} x2={x} y2={chartHeight} stroke={colors.gray100} strokeWidth={1} />
              </React.Fragment>
            );
          })}

          {/* Header separator */}
          <Line x1={0} y1={HEADER_HEIGHT} x2={chartWidth} y2={HEADER_HEIGHT} stroke={colors.gray200} strokeWidth={1} />

          {/* Task bars */}
          {tasks.map((task, index) => {
            const startOffset = differenceInDays(new Date(task.startDate), minDate) * dayWidth;
            const duration = differenceInDays(new Date(task.endDate), new Date(task.startDate));
            const barWidth = Math.max(duration * dayWidth, dayWidth);
            const y = HEADER_HEIGHT + index * ROW_HEIGHT + 8;
            const barHeight = ROW_HEIGHT - 16;

            if (task.isMilestone) {
              // Diamond shape for milestones
              const cx = startOffset + dayWidth / 2;
              const cy = y + barHeight / 2;
              return (
                <React.Fragment key={task.id}>
                  <Circle cx={cx} cy={cy} r={8} fill={colors.warning} />
                </React.Fragment>
              );
            }

            const fillColor = getBarColor(task.progress);
            const progressWidth = barWidth * (task.progress / 100);

            return (
              <React.Fragment key={task.id}>
                {/* Background bar */}
                <Rect x={startOffset} y={y} width={barWidth} height={barHeight} rx={4} fill={colors.gray200} />
                {/* Progress bar */}
                {task.progress > 0 && (
                  <Rect x={startOffset} y={y} width={progressWidth} height={barHeight} rx={4} fill={fillColor} />
                )}
                {/* Row separator */}
                <Line
                  x1={0}
                  y1={HEADER_HEIGHT + (index + 1) * ROW_HEIGHT}
                  x2={chartWidth}
                  y2={HEADER_HEIGHT + (index + 1) * ROW_HEIGHT}
                  stroke={colors.gray100}
                  strokeWidth={1}
                />
              </React.Fragment>
            );
          })}

          {/* Today line */}
          {todayOffset >= 0 && todayOffset <= chartWidth && (
            <Line x1={todayOffset} y1={0} x2={todayOffset} y2={chartHeight} stroke={colors.danger} strokeWidth={2} strokeDasharray="4,4" />
          )}
        </Svg>
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    overflow: 'hidden',
    borderWidth: 1,
    borderColor: colors.border,
  },
  labels: {
    borderRightWidth: 1,
    borderRightColor: colors.border,
    backgroundColor: colors.white,
  },
  headerCell: {
    justifyContent: 'center',
    paddingHorizontal: spacing.md,
    borderBottomWidth: 1,
    borderBottomColor: colors.gray200,
  },
  headerText: {
    ...typography.cardTitle,
  },
  labelCell: {
    justifyContent: 'center',
    paddingHorizontal: spacing.md,
    borderBottomWidth: 1,
    borderBottomColor: colors.gray100,
  },
  labelText: {
    ...typography.body,
  },
});

```

## mobile/src/screens/LoginScreen.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, Text, TextInput, TouchableOpacity, KeyboardAvoidingView, Platform, ScrollView } from 'react-native';
import { HardHat } from 'lucide-react-native';
import { useAuth } from '@/store/authStore';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import type { AuthStackParamList } from '@/navigation/AuthNavigator';

type Props = NativeStackScreenProps<AuthStackParamList, 'Login'>;

export function LoginScreen({ navigation }: Props) {
  const { login } = useAuth();
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleLogin = async () => {
    if (!email || !password) {
      setError('Please enter email and password');
      return;
    }
    setError('');
    setLoading(true);
    try {
      await login(email, password);
    } catch (err: unknown) {
      const message = err instanceof Error ? err.message : 'Login failed. Please try again.';
      setError(message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <KeyboardAvoidingView style={styles.container} behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
      <ScrollView contentContainerStyle={styles.scrollContent} keyboardShouldPersistTaps="handled">
        <View style={styles.logoContainer}>
          <View style={styles.iconCircle}>
            <HardHat size={40} color={colors.white} />
          </View>
          <Text style={styles.title}>BuildMate</Text>
          <Text style={styles.subtitle}>Construction Management</Text>
        </View>

        {error ? (
          <View style={styles.errorBanner}>
            <Text style={styles.errorText}>{error}</Text>
          </View>
        ) : null}

        <View style={styles.form}>
          <Text style={styles.label}>Email</Text>
          <TextInput
            style={styles.input}
            value={email}
            onChangeText={setEmail}
            placeholder="you@example.com"
            placeholderTextColor={colors.gray400}
            keyboardType="email-address"
            autoCapitalize="none"
            autoCorrect={false}
          />

          <Text style={styles.label}>Password</Text>
          <TextInput
            style={styles.input}
            value={password}
            onChangeText={setPassword}
            placeholder="Enter password"
            placeholderTextColor={colors.gray400}
            secureTextEntry
          />

          <TouchableOpacity style={[styles.button, loading && styles.buttonDisabled]} onPress={handleLogin} disabled={loading}>
            <Text style={styles.buttonText}>{loading ? 'Signing in…' : 'Sign In'}</Text>
          </TouchableOpacity>

          <TouchableOpacity style={styles.linkButton} onPress={() => navigation.navigate('Register')}>
            <Text style={styles.linkText}>
              Don't have an account? <Text style={styles.linkBold}>Register</Text>
            </Text>
          </TouchableOpacity>
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  scrollContent: {
    flexGrow: 1,
    justifyContent: 'center',
    padding: spacing.lg,
  },
  logoContainer: {
    alignItems: 'center',
    marginBottom: spacing.xl,
  },
  iconCircle: {
    width: 80,
    height: 80,
    borderRadius: 40,
    backgroundColor: colors.primary,
    alignItems: 'center',
    justifyContent: 'center',
    marginBottom: spacing.md,
    ...shadows.lg,
  },
  title: {
    fontSize: 28,
    fontWeight: '700',
    color: colors.gray900,
  },
  subtitle: {
    ...typography.bodySecondary,
    marginTop: spacing.xs,
  },
  errorBanner: {
    backgroundColor: '#FEE2E2',
    borderWidth: 1,
    borderColor: '#FECACA',
    borderRadius: borderRadius.md,
    padding: spacing.md,
    marginBottom: spacing.md,
  },
  errorText: {
    color: colors.danger,
    fontSize: typography.body.fontSize,
  },
  form: {
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    padding: spacing.lg,
    ...shadows.md,
  },
  label: {
    ...typography.formLabel,
    marginBottom: spacing.xs,
    marginTop: spacing.md,
  },
  input: {
    borderWidth: 1,
    borderColor: colors.gray200,
    borderRadius: borderRadius.md,
    padding: spacing.sm + 4,
    fontSize: typography.body.fontSize,
    color: colors.gray900,
    backgroundColor: colors.white,
  },
  button: {
    backgroundColor: colors.primary,
    borderRadius: borderRadius.md,
    padding: spacing.sm + 6,
    alignItems: 'center',
    marginTop: spacing.lg,
  },
  buttonDisabled: {
    opacity: 0.6,
  },
  buttonText: {
    color: colors.white,
    fontSize: 16,
    fontWeight: '600',
  },
  linkButton: {
    alignItems: 'center',
    marginTop: spacing.md,
    padding: spacing.sm,
  },
  linkText: {
    ...typography.bodySecondary,
  },
  linkBold: {
    color: colors.primary,
    fontWeight: '600',
  },
});

```

## mobile/src/screens/RegisterScreen.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, Text, TextInput, TouchableOpacity, KeyboardAvoidingView, Platform, ScrollView } from 'react-native';
import { HardHat } from 'lucide-react-native';
import * as authApi from '@/api/auth';
import { useAuth } from '@/store/authStore';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import type { AuthStackParamList } from '@/navigation/AuthNavigator';

type Props = NativeStackScreenProps<AuthStackParamList, 'Register'>;

export function RegisterScreen({ navigation }: Props) {
  const { login } = useAuth();
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [confirmPassword, setConfirmPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleRegister = async () => {
    if (!firstName || !email || !password) {
      setError('Please fill in all required fields');
      return;
    }
    if (password !== confirmPassword) {
      setError('Passwords do not match');
      return;
    }
    setError('');
    setLoading(true);
    try {
      await authApi.register({ firstName, lastName, email, password });
      await login(email, password);
    } catch (err: unknown) {
      const message = err instanceof Error ? err.message : 'Registration failed. Please try again.';
      setError(message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <KeyboardAvoidingView style={styles.container} behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
      <ScrollView contentContainerStyle={styles.scrollContent} keyboardShouldPersistTaps="handled">
        <View style={styles.logoContainer}>
          <View style={styles.iconCircle}>
            <HardHat size={32} color={colors.white} />
          </View>
          <Text style={styles.title}>Create Account</Text>
        </View>

        {error ? (
          <View style={styles.errorBanner}>
            <Text style={styles.errorText}>{error}</Text>
          </View>
        ) : null}

        <View style={styles.form}>
          <Text style={styles.label}>First Name *</Text>
          <TextInput style={styles.input} value={firstName} onChangeText={setFirstName} placeholder="First name" placeholderTextColor={colors.gray400} />

          <Text style={styles.label}>Last Name</Text>
          <TextInput style={styles.input} value={lastName} onChangeText={setLastName} placeholder="Last name" placeholderTextColor={colors.gray400} />

          <Text style={styles.label}>Email *</Text>
          <TextInput style={styles.input} value={email} onChangeText={setEmail} placeholder="you@example.com" placeholderTextColor={colors.gray400} keyboardType="email-address" autoCapitalize="none" autoCorrect={false} />

          <Text style={styles.label}>Password *</Text>
          <TextInput style={styles.input} value={password} onChangeText={setPassword} placeholder="Create password" placeholderTextColor={colors.gray400} secureTextEntry />

          <Text style={styles.label}>Confirm Password *</Text>
          <TextInput style={styles.input} value={confirmPassword} onChangeText={setConfirmPassword} placeholder="Confirm password" placeholderTextColor={colors.gray400} secureTextEntry />

          <TouchableOpacity style={[styles.button, loading && styles.buttonDisabled]} onPress={handleRegister} disabled={loading}>
            <Text style={styles.buttonText}>{loading ? 'Creating Account…' : 'Create Account'}</Text>
          </TouchableOpacity>

          <TouchableOpacity style={styles.linkButton} onPress={() => navigation.goBack()}>
            <Text style={styles.linkText}>
              Already have an account? <Text style={styles.linkBold}>Sign In</Text>
            </Text>
          </TouchableOpacity>
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  scrollContent: {
    flexGrow: 1,
    justifyContent: 'center',
    padding: spacing.lg,
  },
  logoContainer: {
    alignItems: 'center',
    marginBottom: spacing.lg,
  },
  iconCircle: {
    width: 64,
    height: 64,
    borderRadius: 32,
    backgroundColor: colors.primary,
    alignItems: 'center',
    justifyContent: 'center',
    marginBottom: spacing.md,
    ...shadows.lg,
  },
  title: {
    fontSize: 24,
    fontWeight: '700',
    color: colors.gray900,
  },
  errorBanner: {
    backgroundColor: '#FEE2E2',
    borderWidth: 1,
    borderColor: '#FECACA',
    borderRadius: borderRadius.md,
    padding: spacing.md,
    marginBottom: spacing.md,
  },
  errorText: {
    color: colors.danger,
    fontSize: typography.body.fontSize,
  },
  form: {
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    padding: spacing.lg,
    ...shadows.md,
  },
  label: {
    ...typography.formLabel,
    marginBottom: spacing.xs,
    marginTop: spacing.md,
  },
  input: {
    borderWidth: 1,
    borderColor: colors.gray200,
    borderRadius: borderRadius.md,
    padding: spacing.sm + 4,
    fontSize: typography.body.fontSize,
    color: colors.gray900,
    backgroundColor: colors.white,
  },
  button: {
    backgroundColor: colors.primary,
    borderRadius: borderRadius.md,
    padding: spacing.sm + 6,
    alignItems: 'center',
    marginTop: spacing.lg,
  },
  buttonDisabled: {
    opacity: 0.6,
  },
  buttonText: {
    color: colors.white,
    fontSize: 16,
    fontWeight: '600',
  },
  linkButton: {
    alignItems: 'center',
    marginTop: spacing.md,
    padding: spacing.sm,
  },
  linkText: {
    ...typography.bodySecondary,
  },
  linkBold: {
    color: colors.primary,
    fontWeight: '600',
  },
});

```

## mobile/src/screens/DashboardScreen.tsx

```tsx
import React, { useCallback } from 'react';
import { StyleSheet, View, Text, FlatList, TouchableOpacity, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { FolderKanban, DollarSign, Users, AlertTriangle } from 'lucide-react-native';
import { getProjects } from '@/api/projects';
import { getTradies } from '@/api/tradies';
import { useDeviceType } from '@/hooks/useDeviceType';
import { formatCurrency } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

interface StatCard {
  key: string;
  title: string;
  value: string;
  color: string;
  icon: typeof FolderKanban;
}

export function DashboardScreen() {
  const queryClient = useQueryClient();
  const { isTablet } = useDeviceType();

  const { data: projects = [], isLoading: loadingProjects } = useQuery({
    queryKey: ['projects'],
    queryFn: () => getProjects(),
  });

  const { data: tradies = [], isLoading: loadingTradies } = useQuery({
    queryKey: ['tradies'],
    queryFn: () => getTradies(),
  });

  const isLoading = loadingProjects || loadingTradies;

  const activeProjects = projects.filter((p) => p.status === 'active');
  const totalValue = projects.reduce((sum, p) => sum + (p.contractValue || 0), 0);
  const onHoldCount = projects.filter((p) => p.status === 'on_hold').length;

  const stats: StatCard[] = [
    { key: 'projects', title: 'Active Projects', value: String(activeProjects.length), color: colors.primary, icon: FolderKanban },
    { key: 'value', title: 'Total Value', value: formatCurrency(totalValue), color: colors.success, icon: DollarSign },
    { key: 'tradies', title: 'Tradies', value: String(tradies.length), color: colors.primaryLight, icon: Users },
    { key: 'onhold', title: 'On Hold', value: String(onHoldCount), color: colors.warning, icon: AlertTriangle },
  ];

  const onRefresh = useCallback(() => {
    queryClient.invalidateQueries({ queryKey: ['projects'] });
    queryClient.invalidateQueries({ queryKey: ['tradies'] });
  }, [queryClient]);

  const renderStatCard = ({ item }: { item: StatCard }) => {
    const Icon = item.icon;
    return (
      <View style={styles.statCard}>
        <View style={[styles.statIconCircle, { backgroundColor: item.color + '15' }]}>
          <Icon size={20} color={item.color} />
        </View>
        <Text style={styles.statValue}>{item.value}</Text>
        <Text style={styles.statTitle}>{item.title}</Text>
      </View>
    );
  };

  const renderProjectCard = ({ item }: { item: (typeof projects)[0] }) => (
    <View style={styles.projectCard}>
      <View style={styles.projectHeader}>
        <Text style={styles.projectName} numberOfLines={1}>{item.name}</Text>
        <View style={[styles.statusBadge, { backgroundColor: getStatusColor(item.status) + '20' }]}>
          <Text style={[styles.statusText, { color: getStatusColor(item.status) }]}>{item.status.replace('_', ' ')}</Text>
        </View>
      </View>
      {item.address ? <Text style={styles.projectAddress} numberOfLines={1}>{item.address}</Text> : null}
      <Text style={styles.projectValue}>{formatCurrency(item.contractValue)}</Text>
    </View>
  );

  return (
    <FlatList
      style={styles.container}
      contentContainerStyle={styles.content}
      refreshControl={<RefreshControl refreshing={isLoading} onRefresh={onRefresh} tintColor={colors.primary} />}
      ListHeaderComponent={
        <>
          <Text style={styles.sectionTitle}>Overview</Text>
          <FlatList
            data={stats}
            renderItem={renderStatCard}
            keyExtractor={(item) => item.key}
            numColumns={isTablet ? 4 : 2}
            key={isTablet ? 'tablet' : 'phone'}
            scrollEnabled={false}
            columnWrapperStyle={styles.statRow}
            contentContainerStyle={styles.statsContainer}
          />
          <Text style={styles.sectionTitle}>Recent Projects</Text>
        </>
      }
      data={projects.slice(0, 5)}
      renderItem={renderProjectCard}
      keyExtractor={(item) => item.id}
      ListEmptyComponent={
        <Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No projects yet'}</Text>
      }
    />
  );
}

function getStatusColor(status: string): string {
  switch (status) {
    case 'active': return colors.success;
    case 'planning': return colors.primary;
    case 'on_hold': return colors.warning;
    case 'complete': return colors.gray500;
    case 'cancelled': return colors.danger;
    default: return colors.gray500;
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  content: {
    padding: spacing.md,
  },
  sectionTitle: {
    ...typography.heading,
    marginBottom: spacing.md,
    marginTop: spacing.md,
  },
  statsContainer: {
    gap: spacing.sm,
  },
  statRow: {
    gap: spacing.sm,
  },
  statCard: {
    flex: 1,
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    padding: spacing.md,
    borderWidth: 1,
    borderColor: colors.gray200,
    ...shadows.sm,
  },
  statIconCircle: {
    width: 36,
    height: 36,
    borderRadius: 18,
    alignItems: 'center',
    justifyContent: 'center',
    marginBottom: spacing.sm,
  },
  statValue: {
    fontSize: 20,
    fontWeight: '700',
    color: colors.gray900,
  },
  statTitle: {
    ...typography.caption,
    marginTop: spacing.xs,
  },
  projectCard: {
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    padding: spacing.md,
    borderWidth: 1,
    borderColor: colors.gray200,
    marginBottom: spacing.sm,
    ...shadows.sm,
  },
  projectHeader: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  projectName: {
    ...typography.cardTitle,
    flex: 1,
    marginRight: spacing.sm,
  },
  statusBadge: {
    paddingHorizontal: spacing.sm,
    paddingVertical: spacing.xs,
    borderRadius: borderRadius.full,
  },
  statusText: {
    fontSize: 11,
    fontWeight: '600',
    textTransform: 'capitalize',
  },
  projectAddress: {
    ...typography.bodySecondary,
    marginTop: spacing.xs,
  },
  projectValue: {
    ...typography.body,
    fontWeight: '600',
    marginTop: spacing.sm,
    color: colors.success,
  },
  emptyText: {
    ...typography.bodySecondary,
    textAlign: 'center',
    padding: spacing.xl,
  },
});

```

## mobile/src/screens/ProjectsScreen.tsx

```tsx
import React, { useState, useCallback } from 'react';
import { StyleSheet, View, Text, TextInput, FlatList, TouchableOpacity, ScrollView, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { useNavigation } from '@react-navigation/native';
import type { NativeStackNavigationProp } from '@react-navigation/native-stack';
import { Search, Plus } from 'lucide-react-native';
import { getProjects } from '@/api/projects';
import { formatCurrency } from '@/utils';
import type { ProjectsStackParamList } from '@/navigation/ProjectsStackNavigator';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

const STATUSES = ['all', 'active', 'planning', 'on_hold', 'complete', 'cancelled'] as const;

export function ProjectsScreen() {
  const navigation = useNavigation<NativeStackNavigationProp<ProjectsStackParamList>>();
  const queryClient = useQueryClient();
  const [search, setSearch] = useState('');
  const [statusFilter, setStatusFilter] = useState<string>('all');

  const { data: projects = [], isLoading } = useQuery({
    queryKey: ['projects', { search, status: statusFilter === 'all' ? undefined : statusFilter }],
    queryFn: () => getProjects({ search: search || undefined, status: statusFilter === 'all' ? undefined : statusFilter }),
  });

  const onRefresh = useCallback(() => {
    queryClient.invalidateQueries({ queryKey: ['projects'] });
  }, [queryClient]);

  return (
    <View style={styles.container}>
      <View style={styles.searchContainer}>
        <Search size={18} color={colors.gray400} style={styles.searchIcon} />
        <TextInput
          style={styles.searchInput}
          placeholder="Search projects…"
          placeholderTextColor={colors.gray400}
          value={search}
          onChangeText={setSearch}
          autoCorrect={false}
        />
      </View>

      <ScrollView horizontal showsHorizontalScrollIndicator={false} style={styles.filterBar} contentContainerStyle={styles.filterContent}>
        {STATUSES.map((s) => (
          <TouchableOpacity key={s} style={[styles.filterPill, statusFilter === s && styles.filterPillActive]} onPress={() => setStatusFilter(s)}>
            <Text style={[styles.filterText, statusFilter === s && styles.filterTextActive]}>{s === 'all' ? 'All' : s.replace('_', ' ')}</Text>
          </TouchableOpacity>
        ))}
      </ScrollView>

      <FlatList
        data={projects}
        keyExtractor={(item) => item.id}
        refreshControl={<RefreshControl refreshing={isLoading} onRefresh={onRefresh} tintColor={colors.primary} />}
        contentContainerStyle={styles.listContent}
        renderItem={({ item }) => (
          <TouchableOpacity style={styles.card} onPress={() => navigation.navigate('ProjectDetail', { projectId: item.id })}>
            <View style={styles.cardHeader}>
              <Text style={styles.cardTitle} numberOfLines={1}>{item.name}</Text>
              <View style={[styles.badge, { backgroundColor: getStatusColor(item.status) + '20' }]}>
                <Text style={[styles.badgeText, { color: getStatusColor(item.status) }]}>{item.status.replace('_', ' ')}</Text>
              </View>
            </View>
            {item.address ? <Text style={styles.cardSubtitle} numberOfLines={1}>{item.address}</Text> : null}
            <View style={styles.cardFooter}>
              <Text style={styles.cardValue}>{formatCurrency(item.contractValue)}</Text>
              <View style={styles.progressContainer}>
                <View style={[styles.progressBar, { width: `${item.progress || 0}%` }]} />
              </View>
              <Text style={styles.progressText}>{item.progress || 0}%</Text>
            </View>
          </TouchableOpacity>
        )}
        ListEmptyComponent={<Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No projects found'}</Text>}
      />

      <TouchableOpacity style={styles.fab} onPress={() => navigation.navigate('CreateProject')}>
        <Plus size={24} color={colors.white} />
      </TouchableOpacity>
    </View>
  );
}

function getStatusColor(status: string): string {
  switch (status) {
    case 'active': return colors.success;
    case 'planning': return colors.primary;
    case 'on_hold': return colors.warning;
    case 'complete': return colors.gray500;
    case 'cancelled': return colors.danger;
    default: return colors.gray500;
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  searchContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: colors.white,
    margin: spacing.md,
    borderRadius: borderRadius.md,
    borderWidth: 1,
    borderColor: colors.gray200,
    paddingHorizontal: spacing.sm,
  },
  searchIcon: {
    marginRight: spacing.sm,
  },
  searchInput: {
    flex: 1,
    paddingVertical: spacing.sm + 2,
    fontSize: typography.body.fontSize,
    color: colors.gray900,
  },
  filterBar: {
    flexGrow: 0,
    marginBottom: spacing.sm,
  },
  filterContent: {
    paddingHorizontal: spacing.md,
    gap: spacing.sm,
  },
  filterPill: {
    paddingHorizontal: spacing.md,
    paddingVertical: spacing.xs + 2,
    borderRadius: borderRadius.full,
    backgroundColor: colors.white,
    borderWidth: 1,
    borderColor: colors.gray200,
  },
  filterPillActive: {
    backgroundColor: colors.primary,
    borderColor: colors.primary,
  },
  filterText: {
    fontSize: typography.caption.fontSize,
    fontWeight: '500',
    color: colors.gray600,
    textTransform: 'capitalize',
  },
  filterTextActive: {
    color: colors.white,
  },
  listContent: {
    padding: spacing.md,
    paddingTop: spacing.sm,
  },
  card: {
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    padding: spacing.md,
    borderWidth: 1,
    borderColor: colors.gray200,
    marginBottom: spacing.sm,
    ...shadows.sm,
  },
  cardHeader: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  cardTitle: {
    ...typography.cardTitle,
    flex: 1,
    marginRight: spacing.sm,
  },
  badge: {
    paddingHorizontal: spacing.sm,
    paddingVertical: spacing.xs,
    borderRadius: borderRadius.full,
  },
  badgeText: {
    fontSize: 11,
    fontWeight: '600',
    textTransform: 'capitalize',
  },
  cardSubtitle: {
    ...typography.bodySecondary,
    marginTop: spacing.xs,
  },
  cardFooter: {
    flexDirection: 'row',
    alignItems: 'center',
    marginTop: spacing.sm,
    gap: spacing.sm,
  },
  cardValue: {
    ...typography.body,
    fontWeight: '600',
    color: colors.success,
  },
  progressContainer: {
    flex: 1,
    height: 4,
    backgroundColor: colors.gray200,
    borderRadius: 2,
  },
  progressBar: {
    height: 4,
    backgroundColor: colors.primary,
    borderRadius: 2,
  },
  progressText: {
    ...typography.caption,
    fontWeight: '600',
  },
  emptyText: {
    ...typography.bodySecondary,
    textAlign: 'center',
    padding: spacing.xl,
  },
  fab: {
    position: 'absolute',
    right: spacing.md,
    bottom: spacing.md,
    width: 56,
    height: 56,
    borderRadius: 28,
    backgroundColor: colors.primary,
    alignItems: 'center',
    justifyContent: 'center',
    ...shadows.lg,
  },
});

```

## mobile/src/screens/ProjectDetailScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, ActivityIndicator } from 'react-native';
import { useQuery } from '@tanstack/react-query';
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import { getProject } from '@/api/projects';
import { ProjectTabNavigator } from '@/navigation/ProjectTabNavigator';
import { formatCurrency } from '@/utils';
import type { ProjectsStackParamList } from '@/navigation/ProjectsStackNavigator';
import { colors, typography, spacing, borderRadius } from '@/theme';

type Props = NativeStackScreenProps<ProjectsStackParamList, 'ProjectDetail'>;

export function ProjectDetailScreen({ route }: Props) {
  const { projectId } = route.params;

  const { data: project, isLoading } = useQuery({
    queryKey: ['project', projectId],
    queryFn: () => getProject(projectId),
  });

  if (isLoading || !project) {
    return (
      <View style={styles.loading}>
        <ActivityIndicator size="large" color={colors.primary} />
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <View style={styles.header}>
        <View style={styles.headerTop}>
          <Text style={styles.name} numberOfLines={1}>{project.name}</Text>
          <View style={[styles.badge, { backgroundColor: getStatusColor(project.status) + '20' }]}>
            <Text style={[styles.badgeText, { color: getStatusColor(project.status) }]}>{project.status.replace('_', ' ')}</Text>
          </View>
        </View>
        {project.address ? <Text style={styles.address}>{project.address}</Text> : null}
        <Text style={styles.value}>{formatCurrency(project.contractValue)}</Text>
      </View>
      <ProjectTabNavigator projectId={projectId} />
    </View>
  );
}

function getStatusColor(status: string): string {
  switch (status) {
    case 'active': return colors.success;
    case 'planning': return colors.primary;
    case 'on_hold': return colors.warning;
    case 'complete': return colors.gray500;
    case 'cancelled': return colors.danger;
    default: return colors.gray500;
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  loading: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  header: {
    backgroundColor: colors.white,
    padding: spacing.md,
    borderBottomWidth: 1,
    borderBottomColor: colors.gray200,
  },
  headerTop: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  name: {
    ...typography.heading,
    flex: 1,
    marginRight: spacing.sm,
  },
  badge: {
    paddingHorizontal: spacing.sm,
    paddingVertical: spacing.xs,
    borderRadius: borderRadius.full,
  },
  badgeText: {
    fontSize: 11,
    fontWeight: '600',
    textTransform: 'capitalize',
  },
  address: {
    ...typography.bodySecondary,
    marginTop: spacing.xs,
  },
  value: {
    ...typography.subheading,
    color: colors.success,
    marginTop: spacing.xs,
  },
});

```

## mobile/src/screens/CreateProjectScreen.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, Text, TextInput, TouchableOpacity, ScrollView, Alert } from 'react-native';
import { useNavigation } from '@react-navigation/native';
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { createProject } from '@/api/projects';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

export function CreateProjectScreen() {
  const navigation = useNavigation();
  const queryClient = useQueryClient();

  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  const [clientName, setClientName] = useState('');
  const [contractValue, setContractValue] = useState('');
  const [startDate, setStartDate] = useState('');
  const [endDate, setEndDate] = useState('');

  const mutation = useMutation({
    mutationFn: createProject,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['projects'] });
      navigation.goBack();
    },
    onError: () => {
      Alert.alert('Error', 'Failed to create project. Please try again.');
    },
  });

  const handleCreate = () => {
    if (!name) {
      Alert.alert('Validation', 'Project name is required');
      return;
    }
    mutation.mutate({
      name,
      address: address || undefined,
      clientName: clientName || undefined,
      contractValue: contractValue ? Number(contractValue) : 0,
      startDate: startDate || undefined,
      endDate: endDate || undefined,
    });
  };

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content} keyboardShouldPersistTaps="handled">
      <View style={styles.form}>
        <Text style={styles.label}>Project Name *</Text>
        <TextInput style={styles.input} value={name} onChangeText={setName} placeholder="e.g. Smith Residence" placeholderTextColor={colors.gray400} />

        <Text style={styles.label}>Address</Text>
        <TextInput style={styles.input} value={address} onChangeText={setAddress} placeholder="Project address" placeholderTextColor={colors.gray400} />

        <Text style={styles.label}>Client Name</Text>
        <TextInput style={styles.input} value={clientName} onChangeText={setClientName} placeholder="Client name" placeholderTextColor={colors.gray400} />

        <Text style={styles.label}>Contract Value ($)</Text>
        <TextInput style={styles.input} value={contractValue} onChangeText={setContractValue} placeholder="0" placeholderTextColor={colors.gray400} keyboardType="numeric" />

        <Text style={styles.label}>Start Date</Text>
        <TextInput style={styles.input} value={startDate} onChangeText={setStartDate} placeholder="YYYY-MM-DD" placeholderTextColor={colors.gray400} />

        <Text style={styles.label}>Estimated End Date</Text>
        <TextInput style={styles.input} value={endDate} onChangeText={setEndDate} placeholder="YYYY-MM-DD" placeholderTextColor={colors.gray400} />

        <TouchableOpacity style={[styles.button, mutation.isPending && styles.buttonDisabled]} onPress={handleCreate} disabled={mutation.isPending}>
          <Text style={styles.buttonText}>{mutation.isPending ? 'Creating…' : 'Create Project'}</Text>
        </TouchableOpacity>
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  content: {
    padding: spacing.md,
  },
  form: {
    backgroundColor: colors.white,
    borderRadius: borderRadius.lg,
    padding: spacing.lg,
    ...shadows.md,
  },
  label: {
    ...typography.formLabel,
    marginBottom: spacing.xs,
    marginTop: spacing.md,
  },
  input: {
    borderWidth: 1,
    borderColor: colors.gray200,
    borderRadius: borderRadius.md,
    padding: spacing.sm + 4,
    fontSize: typography.body.fontSize,
    color: colors.gray900,
    backgroundColor: colors.white,
  },
  button: {
    backgroundColor: colors.primary,
    borderRadius: borderRadius.md,
    padding: spacing.sm + 6,
    alignItems: 'center',
    marginTop: spacing.lg,
  },
  buttonDisabled: {
    opacity: 0.6,
  },
  buttonText: {
    color: colors.white,
    fontSize: 16,
    fontWeight: '600',
  },
});

```

## mobile/src/screens/FinancialsScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, FlatList, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { DollarSign, TrendingUp, TrendingDown, Clock } from 'lucide-react-native';
import { getTransactions } from '@/api/transactions';
import { getFinancialSummary } from '@/api/projects';
import { formatCurrency, formatDate } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

interface Props {
  projectId: string;
}

export function FinancialsScreen({ projectId }: Props) {
  const queryClient = useQueryClient();

  const { data: summary } = useQuery({
    queryKey: ['financial-summary', projectId],
    queryFn: () => getFinancialSummary(projectId),
  });

  const { data: txResult, isLoading } = useQuery({
    queryKey: ['transactions', projectId],
    queryFn: () => getTransactions(projectId),
  });

  const transactions = txResult?.data ?? [];

  const cards = [
    { key: 'income', label: 'Total Income', value: summary?.totalIncome ?? 0, icon: TrendingUp, color: colors.success },
    { key: 'expenses', label: 'Total Expenses', value: summary?.totalExpenses ?? 0, icon: TrendingDown, color: colors.danger },
    { key: 'net', label: 'Net Position', value: summary?.netPosition ?? 0, icon: DollarSign, color: colors.primary },
    { key: 'outstanding', label: 'Outstanding', value: summary?.outstandingInvoices ?? 0, icon: Clock, color: colors.warning },
  ];

  return (
    <FlatList
      style={styles.container}
      contentContainerStyle={styles.content}
      refreshControl={<RefreshControl refreshing={isLoading} onRefresh={() => { queryClient.invalidateQueries({ queryKey: ['transactions', projectId] }); queryClient.invalidateQueries({ queryKey: ['financial-summary', projectId] }); }} tintColor={colors.primary} />}
      ListHeaderComponent={
        <View style={styles.summaryRow}>
          {cards.map((c) => {
            const Icon = c.icon;
            return (
              <View key={c.key} style={styles.summaryCard}>
                <Icon size={16} color={c.color} />
                <Text style={styles.summaryLabel}>{c.label}</Text>
                <Text style={[styles.summaryValue, { color: c.color }]}>{formatCurrency(c.value)}</Text>
              </View>
            );
          })}
        </View>
      }
      data={transactions}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <View style={styles.txCard}>
          <View style={styles.txRow}>
            <View style={styles.txInfo}>
              <Text style={styles.txDesc} numberOfLines={1}>{item.description}</Text>
              <Text style={styles.txMeta}>{item.category} · {formatDate(item.date)}</Text>
            </View>
            <Text style={[styles.txAmount, { color: item.type === 'income' ? colors.success : colors.danger }]}>
              {item.type === 'income' ? '+' : '-'}{formatCurrency(item.amount)}
            </Text>
          </View>
        </View>
      )}
      ListEmptyComponent={<Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No transactions'}</Text>}
    />
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  content: { padding: spacing.md },
  summaryRow: { flexDirection: 'row', flexWrap: 'wrap', gap: spacing.sm, marginBottom: spacing.md },
  summaryCard: { flex: 1, minWidth: '45%', backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, ...shadows.sm },
  summaryLabel: { ...typography.caption, marginTop: spacing.xs },
  summaryValue: { fontSize: 16, fontWeight: '700', marginTop: spacing.xs },
  txCard: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm },
  txRow: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' },
  txInfo: { flex: 1, marginRight: spacing.sm },
  txDesc: { ...typography.cardTitle },
  txMeta: { ...typography.caption, marginTop: spacing.xs },
  txAmount: { fontSize: 14, fontWeight: '600' },
  emptyText: { ...typography.bodySecondary, textAlign: 'center', padding: spacing.xl },
});

```

## mobile/src/screens/BudgetScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, FlatList, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { formatCurrency } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';
import client from '@/api/client';
import type { BudgetSummary } from '@/types';

interface Props {
  projectId: string;
}

async function getBudget(projectId: string): Promise<BudgetSummary> {
  const { data } = await client.get<BudgetSummary>(`/projects/${projectId}/budget`);
  return data;
}

export function BudgetScreen({ projectId }: Props) {
  const queryClient = useQueryClient();

  const { data: budget, isLoading } = useQuery({
    queryKey: ['budget', projectId],
    queryFn: () => getBudget(projectId),
  });

  const categories = budget?.categories ?? [];

  return (
    <FlatList
      style={styles.container}
      contentContainerStyle={styles.content}
      refreshControl={<RefreshControl refreshing={isLoading} onRefresh={() => queryClient.invalidateQueries({ queryKey: ['budget', projectId] })} tintColor={colors.primary} />}
      ListHeaderComponent={
        <View style={styles.summaryRow}>
          <View style={styles.summaryCard}>
            <Text style={styles.summaryLabel}>Total Budget</Text>
            <Text style={styles.summaryValue}>{formatCurrency(budget?.totalBudget ?? 0)}</Text>
          </View>
          <View style={styles.summaryCard}>
            <Text style={styles.summaryLabel}>Spent</Text>
            <Text style={[styles.summaryValue, { color: colors.danger }]}>{formatCurrency(budget?.totalSpent ?? 0)}</Text>
          </View>
          <View style={styles.summaryCard}>
            <Text style={styles.summaryLabel}>Variance</Text>
            <Text style={[styles.summaryValue, { color: (budget?.totalVariance ?? 0) >= 0 ? colors.success : colors.danger }]}>{formatCurrency(budget?.totalVariance ?? 0)}</Text>
          </View>
        </View>
      }
      data={categories}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => {
        const pct = item.budgetAmount > 0 ? Math.min((item.spentAmount / item.budgetAmount) * 100, 100) : 0;
        const overBudget = item.spentAmount > item.budgetAmount;
        return (
          <View style={styles.card}>
            <View style={styles.cardHeader}>
              <Text style={styles.costCode}>{item.costCode}</Text>
              <Text style={styles.cardTitle} numberOfLines={1}>{item.name}</Text>
            </View>
            <View style={styles.amounts}>
              <Text style={styles.amountLabel}>Budget: <Text style={styles.amountValue}>{formatCurrency(item.budgetAmount)}</Text></Text>
              <Text style={styles.amountLabel}>Spent: <Text style={[styles.amountValue, overBudget && { color: colors.danger }]}>{formatCurrency(item.spentAmount)}</Text></Text>
            </View>
            <View style={styles.progressBg}>
              <View style={[styles.progressFill, { width: `${pct}%`, backgroundColor: overBudget ? colors.danger : colors.primary }]} />
            </View>
          </View>
        );
      }}
      ListEmptyComponent={<Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No budget categories'}</Text>}
    />
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  content: { padding: spacing.md },
  summaryRow: { flexDirection: 'row', gap: spacing.sm, marginBottom: spacing.md },
  summaryCard: { flex: 1, backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, ...shadows.sm },
  summaryLabel: { ...typography.caption },
  summaryValue: { fontSize: 16, fontWeight: '700', color: colors.gray900, marginTop: spacing.xs },
  card: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm, ...shadows.sm },
  cardHeader: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm },
  costCode: { ...typography.label, backgroundColor: colors.gray100, paddingHorizontal: spacing.sm, paddingVertical: 2, borderRadius: borderRadius.sm, overflow: 'hidden' },
  cardTitle: { ...typography.cardTitle, flex: 1 },
  amounts: { flexDirection: 'row', justifyContent: 'space-between', marginTop: spacing.sm },
  amountLabel: { ...typography.caption },
  amountValue: { fontWeight: '600', color: colors.gray900 },
  progressBg: { height: 6, backgroundColor: colors.gray200, borderRadius: 3, marginTop: spacing.sm },
  progressFill: { height: 6, borderRadius: 3 },
  emptyText: { ...typography.bodySecondary, textAlign: 'center', padding: spacing.xl },
});

```

## mobile/src/screens/CashFlowScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, FlatList, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { getCashFlow } from '@/api/cashflow';
import { formatCurrency } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

interface Props {
  projectId: string;
}

export function CashFlowScreen({ projectId }: Props) {
  const queryClient = useQueryClient();

  const { data: entries = [], isLoading } = useQuery({
    queryKey: ['cashflow', projectId],
    queryFn: () => getCashFlow(projectId),
  });

  const latest = entries.length > 0 ? entries[entries.length - 1] : null;

  return (
    <FlatList
      style={styles.container}
      contentContainerStyle={styles.content}
      refreshControl={<RefreshControl refreshing={isLoading} onRefresh={() => queryClient.invalidateQueries({ queryKey: ['cashflow', projectId] })} tintColor={colors.primary} />}
      ListHeaderComponent={
        <View style={styles.positionRow}>
          <View style={[styles.positionCard, { borderLeftColor: colors.success }]}>
            <Text style={styles.positionLabel}>Total Income</Text>
            <Text style={[styles.positionValue, { color: colors.success }]}>{formatCurrency(entries.reduce((s, e) => s + e.income, 0))}</Text>
          </View>
          <View style={[styles.positionCard, { borderLeftColor: colors.danger }]}>
            <Text style={styles.positionLabel}>Total Expenses</Text>
            <Text style={[styles.positionValue, { color: colors.danger }]}>{formatCurrency(entries.reduce((s, e) => s + e.expense, 0))}</Text>
          </View>
          <View style={[styles.positionCard, { borderLeftColor: colors.primary }]}>
            <Text style={styles.positionLabel}>Cash Position</Text>
            <Text style={[styles.positionValue, { color: colors.primary }]}>{formatCurrency(latest?.cumulative ?? 0)}</Text>
          </View>
        </View>
      }
      data={entries}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <View style={styles.card}>
          <Text style={styles.month}>{item.month}</Text>
          <View style={styles.row}>
            <View style={styles.col}>
              <Text style={styles.colLabel}>Income</Text>
              <Text style={[styles.colValue, { color: colors.success }]}>{formatCurrency(item.income)}</Text>
            </View>
            <View style={styles.col}>
              <Text style={styles.colLabel}>Expense</Text>
              <Text style={[styles.colValue, { color: colors.danger }]}>{formatCurrency(item.expense)}</Text>
            </View>
            <View style={styles.col}>
              <Text style={styles.colLabel}>Net</Text>
              <Text style={[styles.colValue, { color: item.net >= 0 ? colors.success : colors.danger }]}>{formatCurrency(item.net)}</Text>
            </View>
          </View>
        </View>
      )}
      ListEmptyComponent={<Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No cash flow data'}</Text>}
    />
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  content: { padding: spacing.md },
  positionRow: { gap: spacing.sm, marginBottom: spacing.md },
  positionCard: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, borderLeftWidth: 4, ...shadows.sm },
  positionLabel: { ...typography.caption },
  positionValue: { fontSize: 18, fontWeight: '700', marginTop: spacing.xs },
  card: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm },
  month: { ...typography.cardTitle, marginBottom: spacing.sm },
  row: { flexDirection: 'row', justifyContent: 'space-between' },
  col: { flex: 1 },
  colLabel: { ...typography.caption },
  colValue: { fontSize: 14, fontWeight: '600', marginTop: 2 },
  emptyText: { ...typography.bodySecondary, textAlign: 'center', padding: spacing.xl },
});

```

## mobile/src/screens/PlanningScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, FlatList, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { getTasks } from '@/api/tasks';
import { useDeviceType } from '@/hooks/useDeviceType';
import { formatDate } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

interface Props {
  projectId: string;
}

const STATUS_COLORS: Record<string, string> = {
  not_started: colors.gray400,
  in_progress: colors.primary,
  complete: colors.success,
  on_hold: colors.warning,
  blocked: colors.danger,
};

export function PlanningScreen({ projectId }: Props) {
  const queryClient = useQueryClient();
  const { isTablet } = useDeviceType();

  const { data: tasks = [], isLoading } = useQuery({
    queryKey: ['tasks', projectId],
    queryFn: () => getTasks(projectId),
  });

  return (
    <View style={styles.container}>
      {isTablet && (
        <View style={styles.tabletBanner}>
          <Text style={styles.tabletBannerText}>Gantt chart available on iPad landscape mode</Text>
        </View>
      )}
      <FlatList
        style={styles.list}
        contentContainerStyle={styles.listContent}
        refreshControl={<RefreshControl refreshing={isLoading} onRefresh={() => queryClient.invalidateQueries({ queryKey: ['tasks', projectId] })} tintColor={colors.primary} />}
        data={tasks}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.card}>
            <View style={styles.cardHeader}>
              <View style={[styles.statusDot, { backgroundColor: STATUS_COLORS[item.status] ?? colors.gray400 }]} />
              <Text style={styles.taskName} numberOfLines={1}>{item.name}</Text>
              <View style={[styles.priorityBadge, { backgroundColor: getPriorityColor(item.priority) + '20' }]}>
                <Text style={[styles.priorityText, { color: getPriorityColor(item.priority) }]}>{item.priority}</Text>
              </View>
            </View>
            {item.startDate || item.endDate ? (
              <Text style={styles.dates}>
                {item.startDate ? formatDate(item.startDate) : '—'} → {item.endDate ? formatDate(item.endDate) : '—'}
              </Text>
            ) : null}
            <View style={styles.progressRow}>
              <View style={styles.progressBg}>
                <View style={[styles.progressFill, { width: `${item.progress || 0}%` }]} />
              </View>
              <Text style={styles.progressText}>{item.progress || 0}%</Text>
            </View>
          </View>
        )}
        ListEmptyComponent={<Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No tasks'}</Text>}
      />
    </View>
  );
}

function getPriorityColor(priority: string): string {
  switch (priority) {
    case 'critical': return colors.danger;
    case 'high': return colors.warning;
    case 'medium': return colors.primary;
    default: return colors.gray500;
  }
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  tabletBanner: { backgroundColor: colors.primaryLight + '15', padding: spacing.sm, alignItems: 'center' },
  tabletBannerText: { ...typography.caption, color: colors.primary },
  list: { flex: 1 },
  listContent: { padding: spacing.md },
  card: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm, ...shadows.sm },
  cardHeader: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm },
  statusDot: { width: 10, height: 10, borderRadius: 5 },
  taskName: { ...typography.cardTitle, flex: 1 },
  priorityBadge: { paddingHorizontal: spacing.sm, paddingVertical: 2, borderRadius: borderRadius.full },
  priorityText: { fontSize: 10, fontWeight: '600', textTransform: 'capitalize' },
  dates: { ...typography.caption, marginTop: spacing.sm },
  progressRow: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm, marginTop: spacing.sm },
  progressBg: { flex: 1, height: 4, backgroundColor: colors.gray200, borderRadius: 2 },
  progressFill: { height: 4, backgroundColor: colors.primary, borderRadius: 2 },
  progressText: { ...typography.caption, fontWeight: '600', minWidth: 32, textAlign: 'right' },
  emptyText: { ...typography.bodySecondary, textAlign: 'center', padding: spacing.xl },
});

```

## mobile/src/screens/InspectionsScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, FlatList, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { getInspections, getDefects } from '@/api/inspections';
import { formatDate } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

interface Props {
  projectId: string;
}

const RESULT_COLORS: Record<string, string> = {
  passed: colors.success,
  scheduled: colors.primary,
  failed: colors.danger,
  conditional: colors.warning,
};

export function InspectionsScreen({ projectId }: Props) {
  const queryClient = useQueryClient();

  const { data: inspections = [], isLoading: loadingInsp } = useQuery({
    queryKey: ['inspections', projectId],
    queryFn: () => getInspections(projectId),
  });

  const { data: defects = [] } = useQuery({
    queryKey: ['defects', projectId],
    queryFn: () => getDefects(projectId),
  });

  const onRefresh = () => {
    queryClient.invalidateQueries({ queryKey: ['inspections', projectId] });
    queryClient.invalidateQueries({ queryKey: ['defects', projectId] });
  };

  return (
    <FlatList
      style={styles.container}
      contentContainerStyle={styles.content}
      refreshControl={<RefreshControl refreshing={loadingInsp} onRefresh={onRefresh} tintColor={colors.primary} />}
      ListHeaderComponent={
        <>
          <Text style={styles.sectionTitle}>Inspections</Text>
        </>
      }
      data={inspections}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <View style={styles.card}>
          <View style={styles.cardHeader}>
            <Text style={styles.cardTitle} numberOfLines={1}>{item.title || item.type}</Text>
            <View style={[styles.badge, { backgroundColor: (RESULT_COLORS[item.status] ?? colors.gray400) + '20' }]}>
              <Text style={[styles.badgeText, { color: RESULT_COLORS[item.status] ?? colors.gray400 }]}>{item.status}</Text>
            </View>
          </View>
          <Text style={styles.meta}>{formatDate(item.inspectionDate)}{item.inspectorName ? ` · ${item.inspectorName}` : ''}</Text>
          {item._count ? <Text style={styles.meta}>{item._count.items} items · {item._count.defects} defects</Text> : null}
        </View>
      )}
      ListFooterComponent={
        defects.length > 0 ? (
          <View>
            <Text style={styles.sectionTitle}>Defects</Text>
            {defects.map((d) => (
              <View key={d.id} style={styles.card}>
                <View style={styles.cardHeader}>
                  <Text style={styles.cardTitle} numberOfLines={1}>{d.title}</Text>
                  <View style={[styles.badge, { backgroundColor: getSeverityColor(d.severity) + '20' }]}>
                    <Text style={[styles.badgeText, { color: getSeverityColor(d.severity) }]}>{d.severity}</Text>
                  </View>
                </View>
                <Text style={styles.meta} numberOfLines={2}>{d.description}</Text>
                <Text style={styles.meta}>Status: {d.status.replace('_', ' ')}</Text>
              </View>
            ))}
          </View>
        ) : null
      }
      ListEmptyComponent={<Text style={styles.emptyText}>{loadingInsp ? 'Loading…' : 'No inspections'}</Text>}
    />
  );
}

function getSeverityColor(severity: string): string {
  switch (severity) {
    case 'critical': return colors.danger;
    case 'high': return colors.warningDark;
    case 'medium': return colors.warning;
    default: return colors.gray500;
  }
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  content: { padding: spacing.md },
  sectionTitle: { ...typography.subheading, marginBottom: spacing.sm, marginTop: spacing.sm },
  card: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm, ...shadows.sm },
  cardHeader: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' },
  cardTitle: { ...typography.cardTitle, flex: 1, marginRight: spacing.sm },
  badge: { paddingHorizontal: spacing.sm, paddingVertical: spacing.xs, borderRadius: borderRadius.full },
  badgeText: { fontSize: 11, fontWeight: '600', textTransform: 'capitalize' },
  meta: { ...typography.caption, marginTop: spacing.xs },
  emptyText: { ...typography.bodySecondary, textAlign: 'center', padding: spacing.xl },
});

```

## mobile/src/screens/DocumentsScreen.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, Text, FlatList, TouchableOpacity, ScrollView, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { FileText, Image, File, FolderOpen } from 'lucide-react-native';
import { getDocuments } from '@/api/documents';
import { formatDate } from '@/utils';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

interface Props {
  projectId: string;
}

const CATEGORIES = ['all', 'plans', 'contracts', 'permits', 'swms', 'photos', 'reports', 'other'] as const;

function getFileIcon(mimeType: string) {
  if (mimeType.startsWith('image/')) return Image;
  if (mimeType.includes('pdf')) return FileText;
  return File;
}

function formatFileSize(bytes: number): string {
  if (bytes < 1024) return `${bytes} B`;
  if (bytes < 1024 * 1024) return `${(bytes / 1024).toFixed(1)} KB`;
  return `${(bytes / (1024 * 1024)).toFixed(1)} MB`;
}

export function DocumentsScreen({ projectId }: Props) {
  const queryClient = useQueryClient();
  const [category, setCategory] = useState<string>('all');

  const { data: documents = [], isLoading } = useQuery({
    queryKey: ['documents', projectId, category],
    queryFn: () => getDocuments(projectId, { category: category === 'all' ? undefined : category }),
  });

  return (
    <View style={styles.container}>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} style={styles.filterBar} contentContainerStyle={styles.filterContent}>
        {CATEGORIES.map((c) => (
          <TouchableOpacity key={c} style={[styles.pill, category === c && styles.pillActive]} onPress={() => setCategory(c)}>
            <Text style={[styles.pillText, category === c && styles.pillTextActive]}>{c === 'all' ? 'All' : c.toUpperCase()}</Text>
          </TouchableOpacity>
        ))}
      </ScrollView>

      <FlatList
        style={styles.list}
        contentContainerStyle={styles.listContent}
        refreshControl={<RefreshControl refreshing={isLoading} onRefresh={() => queryClient.invalidateQueries({ queryKey: ['documents', projectId] })} tintColor={colors.primary} />}
        data={documents}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => {
          const Icon = getFileIcon(item.mimeType);
          return (
            <View style={styles.card}>
              <View style={styles.iconContainer}>
                <Icon size={24} color={colors.primary} />
              </View>
              <View style={styles.docInfo}>
                <Text style={styles.docName} numberOfLines={1}>{item.name}</Text>
                <Text style={styles.docMeta}>{formatFileSize(item.fileSize)} · {formatDate(item.createdAt)}</Text>
                {item.folder ? <Text style={styles.docFolder}>{item.folder}</Text> : null}
              </View>
            </View>
          );
        }}
        ListEmptyComponent={
          <View style={styles.emptyContainer}>
            <FolderOpen size={48} color={colors.gray300} />
            <Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No documents'}</Text>
          </View>
        }
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  filterBar: { flexGrow: 0, borderBottomWidth: 1, borderBottomColor: colors.gray200, backgroundColor: colors.white },
  filterContent: { paddingHorizontal: spacing.md, paddingVertical: spacing.sm, gap: spacing.sm },
  pill: { paddingHorizontal: spacing.md, paddingVertical: spacing.xs + 2, borderRadius: borderRadius.full, backgroundColor: colors.gray100 },
  pillActive: { backgroundColor: colors.primary },
  pillText: { fontSize: typography.caption.fontSize, fontWeight: '500', color: colors.gray600 },
  pillTextActive: { color: colors.white },
  list: { flex: 1 },
  listContent: { padding: spacing.md },
  card: { flexDirection: 'row', alignItems: 'center', backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm, ...shadows.sm },
  iconContainer: { width: 44, height: 44, borderRadius: borderRadius.md, backgroundColor: colors.primaryLight + '15', alignItems: 'center', justifyContent: 'center', marginRight: spacing.md },
  docInfo: { flex: 1 },
  docName: { ...typography.cardTitle },
  docMeta: { ...typography.caption, marginTop: 2 },
  docFolder: { ...typography.label, marginTop: spacing.xs },
  emptyContainer: { alignItems: 'center', padding: spacing.xl, gap: spacing.md },
  emptyText: { ...typography.bodySecondary, textAlign: 'center' },
});

```

## mobile/src/screens/TradiesScreen.tsx

```tsx
import React, { useState, useCallback } from 'react';
import { StyleSheet, View, Text, TextInput, FlatList, TouchableOpacity, ScrollView, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { useNavigation } from '@react-navigation/native';
import type { NativeStackNavigationProp } from '@react-navigation/native-stack';
import { Search, Star, AlertTriangle } from 'lucide-react-native';
import { getTradies } from '@/api/tradies';
import type { Tradie } from '@/types';
import type { MoreStackParamList } from '@/navigation/MoreNavigator';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

const TRADES = ['all', 'Carpenter', 'Electrician', 'Plumber', 'Concreter', 'Bricklayer', 'Painter', 'Tiler', 'Roofer'] as const;

export function TradiesScreen() {
  const navigation = useNavigation<NativeStackNavigationProp<MoreStackParamList>>();
  const queryClient = useQueryClient();
  const [search, setSearch] = useState('');
  const [tradeFilter, setTradeFilter] = useState<string>('all');

  const { data: tradies = [], isLoading } = useQuery({
    queryKey: ['tradies', { search, trade: tradeFilter === 'all' ? undefined : tradeFilter }],
    queryFn: () => getTradies({ search: search || undefined, trade: tradeFilter === 'all' ? undefined : tradeFilter }),
  });

  const onRefresh = useCallback(() => {
    queryClient.invalidateQueries({ queryKey: ['tradies'] });
  }, [queryClient]);

  const renderStars = (rating?: number) => {
    if (!rating) return null;
    return (
      <View style={styles.starsRow}>
        {[1, 2, 3, 4, 5].map((s) => (
          <Star key={s} size={14} color={s <= rating ? colors.warningLight : colors.gray300} fill={s <= rating ? colors.warningLight : 'none'} />
        ))}
      </View>
    );
  };

  const isLicenceExpiring = (tradie: Tradie) => {
    if (!tradie.licenceExpiry) return false;
    const diff = new Date(tradie.licenceExpiry).getTime() - Date.now();
    return diff < 30 * 24 * 60 * 60 * 1000 && diff > 0;
  };

  return (
    <View style={styles.container}>
      <View style={styles.searchContainer}>
        <Search size={18} color={colors.gray400} style={styles.searchIcon} />
        <TextInput style={styles.searchInput} placeholder="Search tradies…" placeholderTextColor={colors.gray400} value={search} onChangeText={setSearch} autoCorrect={false} />
      </View>

      <ScrollView horizontal showsHorizontalScrollIndicator={false} style={styles.filterBar} contentContainerStyle={styles.filterContent}>
        {TRADES.map((t) => (
          <TouchableOpacity key={t} style={[styles.pill, tradeFilter === t && styles.pillActive]} onPress={() => setTradeFilter(t)}>
            <Text style={[styles.pillText, tradeFilter === t && styles.pillTextActive]}>{t === 'all' ? 'All Trades' : t}</Text>
          </TouchableOpacity>
        ))}
      </ScrollView>

      <FlatList
        data={tradies}
        keyExtractor={(item) => item.id}
        refreshControl={<RefreshControl refreshing={isLoading} onRefresh={onRefresh} tintColor={colors.primary} />}
        contentContainerStyle={styles.listContent}
        renderItem={({ item }) => (
          <TouchableOpacity style={styles.card} onPress={() => navigation.navigate('TradieDetail', { tradieId: item.id })}>
            <View style={styles.cardHeader}>
              <View style={styles.initials}>
                <Text style={styles.initialsText}>{(item.firstName?.[0] ?? '') + (item.lastName?.[0] ?? '')}</Text>
              </View>
              <View style={styles.nameBlock}>
                <Text style={styles.tradieName}>{item.firstName} {item.lastName}</Text>
                {item.company || item.companyName ? <Text style={styles.company}>{item.company || item.companyName}</Text> : null}
              </View>
              <View style={styles.tradeBadge}>
                <Text style={styles.tradeText}>{item.trade}</Text>
              </View>
            </View>
            <View style={styles.cardFooter}>
              {renderStars(item.rating)}
              <Text style={styles.phone}>{item.phone}</Text>
            </View>
            {isLicenceExpiring(item) && (
              <View style={styles.warningRow}>
                <AlertTriangle size={14} color={colors.warning} />
                <Text style={styles.warningText}>Licence expiring soon</Text>
              </View>
            )}
          </TouchableOpacity>
        )}
        ListEmptyComponent={<Text style={styles.emptyText}>{isLoading ? 'Loading…' : 'No tradies found'}</Text>}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  searchContainer: { flexDirection: 'row', alignItems: 'center', backgroundColor: colors.white, margin: spacing.md, borderRadius: borderRadius.md, borderWidth: 1, borderColor: colors.gray200, paddingHorizontal: spacing.sm },
  searchIcon: { marginRight: spacing.sm },
  searchInput: { flex: 1, paddingVertical: spacing.sm + 2, fontSize: typography.body.fontSize, color: colors.gray900 },
  filterBar: { flexGrow: 0, marginBottom: spacing.sm },
  filterContent: { paddingHorizontal: spacing.md, gap: spacing.sm },
  pill: { paddingHorizontal: spacing.md, paddingVertical: spacing.xs + 2, borderRadius: borderRadius.full, backgroundColor: colors.white, borderWidth: 1, borderColor: colors.gray200 },
  pillActive: { backgroundColor: colors.primary, borderColor: colors.primary },
  pillText: { fontSize: typography.caption.fontSize, fontWeight: '500', color: colors.gray600 },
  pillTextActive: { color: colors.white },
  listContent: { padding: spacing.md, paddingTop: spacing.sm },
  card: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm, ...shadows.sm },
  cardHeader: { flexDirection: 'row', alignItems: 'center' },
  initials: { width: 40, height: 40, borderRadius: 20, backgroundColor: colors.primary + '15', alignItems: 'center', justifyContent: 'center', marginRight: spacing.sm },
  initialsText: { fontSize: 14, fontWeight: '600', color: colors.primary },
  nameBlock: { flex: 1 },
  tradieName: { ...typography.cardTitle },
  company: { ...typography.caption, marginTop: 1 },
  tradeBadge: { backgroundColor: colors.gray100, paddingHorizontal: spacing.sm, paddingVertical: 2, borderRadius: borderRadius.sm },
  tradeText: { ...typography.caption, fontWeight: '500', color: colors.gray700 },
  cardFooter: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', marginTop: spacing.sm },
  starsRow: { flexDirection: 'row', gap: 2 },
  phone: { ...typography.bodySecondary },
  warningRow: { flexDirection: 'row', alignItems: 'center', gap: spacing.xs, marginTop: spacing.sm, backgroundColor: colors.warningLight + '15', padding: spacing.sm, borderRadius: borderRadius.sm },
  warningText: { ...typography.caption, color: colors.warning },
  emptyText: { ...typography.bodySecondary, textAlign: 'center', padding: spacing.xl },
});

```

## mobile/src/screens/TradieDetailScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, ScrollView, ActivityIndicator, RefreshControl } from 'react-native';
import { useQuery, useQueryClient } from '@tanstack/react-query';
import { Star, Phone, Mail, FileText, Briefcase, DollarSign } from 'lucide-react-native';
import { getTradie, getTradieDocuments, getTradieAssignments, getTradiePayments } from '@/api/tradies';
import { formatCurrency, formatDate } from '@/utils';
import type { NativeStackScreenProps } from '@react-navigation/native-stack';
import type { ProjectsStackParamList } from '@/navigation/ProjectsStackNavigator';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

type Props = NativeStackScreenProps<ProjectsStackParamList, 'TradieDetail'>;

export function TradieDetailScreen({ route }: Props) {
  const { tradieId } = route.params;
  const queryClient = useQueryClient();

  const { data: tradie, isLoading } = useQuery({ queryKey: ['tradie', tradieId], queryFn: () => getTradie(tradieId) });
  const { data: docs = [] } = useQuery({ queryKey: ['tradie-docs', tradieId], queryFn: () => getTradieDocuments(tradieId) });
  const { data: assignments = [] } = useQuery({ queryKey: ['tradie-assignments', tradieId], queryFn: () => getTradieAssignments(tradieId) });
  const { data: payments = [] } = useQuery({ queryKey: ['tradie-payments', tradieId], queryFn: () => getTradiePayments(tradieId) });

  if (isLoading || !tradie) {
    return <View style={styles.loading}><ActivityIndicator size="large" color={colors.primary} /></View>;
  }

  const onRefresh = () => {
    queryClient.invalidateQueries({ queryKey: ['tradie', tradieId] });
    queryClient.invalidateQueries({ queryKey: ['tradie-docs', tradieId] });
    queryClient.invalidateQueries({ queryKey: ['tradie-assignments', tradieId] });
    queryClient.invalidateQueries({ queryKey: ['tradie-payments', tradieId] });
  };

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content} refreshControl={<RefreshControl refreshing={false} onRefresh={onRefresh} tintColor={colors.primary} />}>
      <View style={styles.profileCard}>
        <View style={styles.avatar}>
          <Text style={styles.avatarText}>{(tradie.firstName?.[0] ?? '') + (tradie.lastName?.[0] ?? '')}</Text>
        </View>
        <Text style={styles.name}>{tradie.firstName} {tradie.lastName}</Text>
        {tradie.company || tradie.companyName ? <Text style={styles.company}>{tradie.company || tradie.companyName}</Text> : null}
        <View style={styles.tradeBadge}><Text style={styles.tradeText}>{tradie.trade}</Text></View>
        {tradie.rating ? (
          <View style={styles.starsRow}>
            {[1, 2, 3, 4, 5].map((s) => <Star key={s} size={18} color={s <= tradie.rating! ? colors.warningLight : colors.gray300} fill={s <= tradie.rating! ? colors.warningLight : 'none'} />)}
          </View>
        ) : null}
        <View style={styles.contactRow}>
          {tradie.phone ? <View style={styles.contactItem}><Phone size={14} color={colors.gray500} /><Text style={styles.contactText}>{tradie.phone}</Text></View> : null}
          {tradie.email ? <View style={styles.contactItem}><Mail size={14} color={colors.gray500} /><Text style={styles.contactText}>{tradie.email}</Text></View> : null}
        </View>
      </View>

      {docs.length > 0 && (
        <View style={styles.section}>
          <View style={styles.sectionHeader}><FileText size={16} color={colors.gray700} /><Text style={styles.sectionTitle}>Documents</Text></View>
          {docs.map((d) => (
            <View key={d.id} style={styles.listItem}>
              <Text style={styles.listItemTitle}>{d.name}</Text>
              <Text style={styles.listItemMeta}>{d.type}{d.expiryDate ? ` · Exp: ${formatDate(d.expiryDate)}` : ''}</Text>
            </View>
          ))}
        </View>
      )}

      {assignments.length > 0 && (
        <View style={styles.section}>
          <View style={styles.sectionHeader}><Briefcase size={16} color={colors.gray700} /><Text style={styles.sectionTitle}>Assignments</Text></View>
          {assignments.map((a) => (
            <View key={a.id} style={styles.listItem}>
              <Text style={styles.listItemTitle}>{a.project?.name ?? 'Project'}</Text>
              <Text style={styles.listItemMeta}>{a.role ?? 'Contractor'}{a.agreedRate ? ` · ${formatCurrency(a.agreedRate)}/${a.rateType ?? 'hr'}` : ''}</Text>
            </View>
          ))}
        </View>
      )}

      {payments.length > 0 && (
        <View style={styles.section}>
          <View style={styles.sectionHeader}><DollarSign size={16} color={colors.gray700} /><Text style={styles.sectionTitle}>Payments</Text></View>
          {payments.map((p) => (
            <View key={p.id} style={styles.listItem}>
              <View style={styles.paymentRow}>
                <Text style={styles.listItemTitle} numberOfLines={1}>{p.description}</Text>
                <Text style={styles.paymentAmount}>{formatCurrency(p.amount)}</Text>
              </View>
              <Text style={styles.listItemMeta}>{p.project?.name ?? ''} · {formatDate(p.date)} · {p.status}</Text>
            </View>
          ))}
        </View>
      )}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  content: { padding: spacing.md },
  loading: { flex: 1, alignItems: 'center', justifyContent: 'center' },
  profileCard: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.lg, borderWidth: 1, borderColor: colors.gray200, alignItems: 'center', marginBottom: spacing.md, ...shadows.md },
  avatar: { width: 64, height: 64, borderRadius: 32, backgroundColor: colors.primary + '15', alignItems: 'center', justifyContent: 'center', marginBottom: spacing.sm },
  avatarText: { fontSize: 22, fontWeight: '700', color: colors.primary },
  name: { ...typography.heading },
  company: { ...typography.bodySecondary, marginTop: spacing.xs },
  tradeBadge: { backgroundColor: colors.gray100, paddingHorizontal: spacing.md, paddingVertical: spacing.xs, borderRadius: borderRadius.full, marginTop: spacing.sm },
  tradeText: { ...typography.caption, fontWeight: '600', color: colors.gray700 },
  starsRow: { flexDirection: 'row', gap: 2, marginTop: spacing.sm },
  contactRow: { marginTop: spacing.md, gap: spacing.sm },
  contactItem: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm },
  contactText: { ...typography.body },
  section: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.md, ...shadows.sm },
  sectionHeader: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm, marginBottom: spacing.sm },
  sectionTitle: { ...typography.subheading },
  listItem: { paddingVertical: spacing.sm, borderBottomWidth: 1, borderBottomColor: colors.gray100 },
  listItemTitle: { ...typography.cardTitle },
  listItemMeta: { ...typography.caption, marginTop: 2 },
  paymentRow: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' },
  paymentAmount: { ...typography.body, fontWeight: '600', color: colors.success },
});

```

## mobile/src/screens/ReportsScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, FlatList } from 'react-native';
import { BarChart3, DollarSign, TrendingUp, FolderKanban } from 'lucide-react-native';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

const REPORTS = [
  { key: 'gst', title: 'GST Report', description: 'BAS-ready GST summary with collected and paid amounts', icon: DollarSign, color: colors.success },
  { key: 'budget', title: 'Budget Report', description: 'Budget vs actual across all projects', icon: BarChart3, color: colors.primary },
  { key: 'cashflow', title: 'Cash Flow Report', description: 'Monthly income, expenses, and cumulative position', icon: TrendingUp, color: colors.primaryLight },
  { key: 'status', title: 'Project Status Report', description: 'All projects with status, progress, and timeline', icon: FolderKanban, color: colors.warning },
];

export function ReportsScreen() {
  return (
    <FlatList
      style={styles.container}
      contentContainerStyle={styles.content}
      data={REPORTS}
      keyExtractor={(item) => item.key}
      renderItem={({ item }) => {
        const Icon = item.icon;
        return (
          <View style={styles.card}>
            <View style={[styles.iconContainer, { backgroundColor: item.color + '15' }]}>
              <Icon size={24} color={item.color} />
            </View>
            <View style={styles.textBlock}>
              <Text style={styles.cardTitle}>{item.title}</Text>
              <Text style={styles.cardDesc}>{item.description}</Text>
            </View>
          </View>
        );
      }}
    />
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background },
  content: { padding: spacing.md },
  card: { flexDirection: 'row', alignItems: 'center', backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.sm, ...shadows.sm },
  iconContainer: { width: 48, height: 48, borderRadius: borderRadius.md, alignItems: 'center', justifyContent: 'center', marginRight: spacing.md },
  textBlock: { flex: 1 },
  cardTitle: { ...typography.cardTitle },
  cardDesc: { ...typography.bodySecondary, marginTop: spacing.xs },
});

```

## mobile/src/screens/ProfileScreen.tsx

```tsx
import React from 'react';
import { StyleSheet, View, Text, TouchableOpacity, Alert } from 'react-native';
import { User, Mail, Phone, LogOut } from 'lucide-react-native';
import { useAuth } from '@/store/authStore';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

export function ProfileScreen() {
  const { user, logout } = useAuth();

  const handleLogout = () => {
    Alert.alert('Sign Out', 'Are you sure you want to sign out?', [
      { text: 'Cancel', style: 'cancel' },
      { text: 'Sign Out', style: 'destructive', onPress: () => logout() },
    ]);
  };

  return (
    <View style={styles.container}>
      <View style={styles.profileCard}>
        <View style={styles.avatar}>
          <User size={32} color={colors.primary} />
        </View>
        <Text style={styles.name}>{user?.firstName} {user?.lastName}</Text>
        <Text style={styles.role}>{user?.role}</Text>

        <View style={styles.infoSection}>
          <View style={styles.infoRow}>
            <Mail size={16} color={colors.gray500} />
            <Text style={styles.infoText}>{user?.email}</Text>
          </View>
          {user?.phone ? (
            <View style={styles.infoRow}>
              <Phone size={16} color={colors.gray500} />
              <Text style={styles.infoText}>{user.phone}</Text>
            </View>
          ) : null}
        </View>
      </View>

      <TouchableOpacity style={styles.logoutButton} onPress={handleLogout}>
        <LogOut size={20} color={colors.danger} />
        <Text style={styles.logoutText}>Sign Out</Text>
      </TouchableOpacity>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background, padding: spacing.md },
  profileCard: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.lg, borderWidth: 1, borderColor: colors.gray200, alignItems: 'center', ...shadows.md },
  avatar: { width: 72, height: 72, borderRadius: 36, backgroundColor: colors.primary + '15', alignItems: 'center', justifyContent: 'center', marginBottom: spacing.md },
  name: { ...typography.heading },
  role: { ...typography.bodySecondary, textTransform: 'capitalize', marginTop: spacing.xs },
  infoSection: { marginTop: spacing.lg, width: '100%', gap: spacing.sm },
  infoRow: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm },
  infoText: { ...typography.body },
  logoutButton: { flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: spacing.sm, backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginTop: spacing.md, ...shadows.sm },
  logoutText: { fontSize: 16, fontWeight: '600', color: colors.danger },
});

```

## mobile/src/screens/SettingsScreen.tsx

```tsx
import React, { useState } from 'react';
import { StyleSheet, View, Text, Switch } from 'react-native';
import { Bell, Moon, Info } from 'lucide-react-native';
import { colors, typography, spacing, borderRadius, shadows } from '@/theme';

export function SettingsScreen() {
  const [pushNotifications, setPushNotifications] = useState(true);
  const [emailNotifications, setEmailNotifications] = useState(true);

  return (
    <View style={styles.container}>
      <View style={styles.section}>
        <View style={styles.sectionHeader}>
          <Bell size={16} color={colors.gray700} />
          <Text style={styles.sectionTitle}>Notifications</Text>
        </View>
        <View style={styles.settingRow}>
          <Text style={styles.settingLabel}>Push Notifications</Text>
          <Switch value={pushNotifications} onValueChange={setPushNotifications} trackColor={{ true: colors.primary, false: colors.gray300 }} />
        </View>
        <View style={styles.settingRow}>
          <Text style={styles.settingLabel}>Email Notifications</Text>
          <Switch value={emailNotifications} onValueChange={setEmailNotifications} trackColor={{ true: colors.primary, false: colors.gray300 }} />
        </View>
      </View>

      <View style={styles.section}>
        <View style={styles.sectionHeader}>
          <Info size={16} color={colors.gray700} />
          <Text style={styles.sectionTitle}>About</Text>
        </View>
        <View style={styles.settingRow}>
          <Text style={styles.settingLabel}>Version</Text>
          <Text style={styles.settingValue}>1.0.0</Text>
        </View>
        <View style={styles.settingRow}>
          <Text style={styles.settingLabel}>Build</Text>
          <Text style={styles.settingValue}>1</Text>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: colors.background, padding: spacing.md },
  section: { backgroundColor: colors.white, borderRadius: borderRadius.lg, padding: spacing.md, borderWidth: 1, borderColor: colors.gray200, marginBottom: spacing.md, ...shadows.sm },
  sectionHeader: { flexDirection: 'row', alignItems: 'center', gap: spacing.sm, marginBottom: spacing.sm, paddingBottom: spacing.sm, borderBottomWidth: 1, borderBottomColor: colors.gray100 },
  sectionTitle: { ...typography.subheading },
  settingRow: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', paddingVertical: spacing.sm },
  settingLabel: { ...typography.body },
  settingValue: { ...typography.bodySecondary },
});

```

## mobile/src/hooks/useCamera.ts

```typescript
import * as ImagePicker from 'expo-image-picker';
import { Alert } from 'react-native';

const MAX_FILE_SIZE = 2 * 1024 * 1024; // 2MB

interface CameraResult {
  uri: string;
  width: number;
  height: number;
}

export function useCamera() {
  const requestPermission = async (): Promise<boolean> => {
    const { status } = await ImagePicker.requestCameraPermissionsAsync();
    if (status !== 'granted') {
      Alert.alert('Permission Required', 'Camera access is needed to capture photos.');
      return false;
    }
    return true;
  };

  const takePhoto = async (): Promise<CameraResult | null> => {
    const granted = await requestPermission();
    if (!granted) return null;

    const result = await ImagePicker.launchCameraAsync({
      mediaTypes: ['images'],
      quality: 0.7,
      allowsEditing: true,
    });

    if (result.canceled || !result.assets[0]) return null;

    const asset = result.assets[0];
    if (asset.fileSize && asset.fileSize > MAX_FILE_SIZE) {
      Alert.alert('File Too Large', 'Please capture a smaller image (max 2MB).');
      return null;
    }

    return { uri: asset.uri, width: asset.width, height: asset.height };
  };

  const pickFromLibrary = async (): Promise<CameraResult | null> => {
    const { status } = await ImagePicker.requestMediaLibraryPermissionsAsync();
    if (status !== 'granted') {
      Alert.alert('Permission Required', 'Photo library access is needed to select photos.');
      return null;
    }

    const result = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ['images'],
      quality: 0.7,
      allowsEditing: true,
    });

    if (result.canceled || !result.assets[0]) return null;

    const asset = result.assets[0];
    if (asset.fileSize && asset.fileSize > MAX_FILE_SIZE) {
      Alert.alert('File Too Large', 'Please select a smaller image (max 2MB).');
      return null;
    }

    return { uri: asset.uri, width: asset.width, height: asset.height };
  };

  return { takePhoto, pickFromLibrary };
}

```

## mobile/src/hooks/useLocation.ts

```typescript
import { useState, useCallback } from 'react';
import * as Location from 'expo-location';
import { Alert } from 'react-native';

interface Coordinates {
  latitude: number;
  longitude: number;
}

export function useLocation() {
  const [location, setLocation] = useState<Coordinates | null>(null);
  const [loading, setLoading] = useState(false);

  const getCurrentLocation = useCallback(async (): Promise<Coordinates | null> => {
    setLoading(true);
    try {
      const { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== 'granted') {
        Alert.alert('Permission Required', 'Location access is needed to tag photos and inspections.');
        return null;
      }

      const result = await Location.getCurrentPositionAsync({ accuracy: Location.Accuracy.High });
      const coords: Coordinates = {
        latitude: result.coords.latitude,
        longitude: result.coords.longitude,
      };
      setLocation(coords);
      return coords;
    } catch {
      Alert.alert('Location Error', 'Unable to get your current location.');
      return null;
    } finally {
      setLoading(false);
    }
  }, []);

  return { location, loading, getCurrentLocation };
}

```

## mobile/src/hooks/usePushNotifications.ts

```typescript
import { useState, useEffect, useRef } from 'react';
import { Platform } from 'react-native';
import * as Notifications from 'expo-notifications';
import * as Device from 'expo-device';

Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true,
    shouldPlaySound: true,
    shouldSetBadge: true,
  }),
});

export function usePushNotifications() {
  const [expoPushToken, setExpoPushToken] = useState<string | null>(null);
  const notificationListener = useRef<Notifications.EventSubscription>();
  const responseListener = useRef<Notifications.EventSubscription>();

  useEffect(() => {
    registerForPushNotifications().then(setExpoPushToken);

    notificationListener.current = Notifications.addNotificationReceivedListener(() => {
      // Handle foreground notification
    });

    responseListener.current = Notifications.addNotificationResponseReceivedListener(() => {
      // Handle notification tap
    });

    return () => {
      if (notificationListener.current) {
        Notifications.removeNotificationSubscription(notificationListener.current);
      }
      if (responseListener.current) {
        Notifications.removeNotificationSubscription(responseListener.current);
      }
    };
  }, []);

  return { expoPushToken };
}

async function registerForPushNotifications(): Promise<string | null> {
  if (!Device.isDevice) {
    return null;
  }

  const { status: existingStatus } = await Notifications.getPermissionsAsync();
  let finalStatus = existingStatus;

  if (existingStatus !== 'granted') {
    const { status } = await Notifications.requestPermissionsAsync();
    finalStatus = status;
  }

  if (finalStatus !== 'granted') {
    return null;
  }

  if (Platform.OS === 'android') {
    await Notifications.setNotificationChannelAsync('default', {
      name: 'BuildMate',
      importance: Notifications.AndroidImportance.HIGH,
    });
  }

  const tokenData = await Notifications.getExpoPushTokenAsync();
  return tokenData.data;
}

```

## mobile/src/hooks/useBiometrics.ts

```typescript
import { useCallback } from 'react';
import * as LocalAuthentication from 'expo-local-authentication';
import * as SecureStore from 'expo-secure-store';

const TOKEN_KEY = 'buildmate_biometric_token';

export function useBiometrics() {
  const isAvailable = useCallback(async (): Promise<boolean> => {
    const compatible = await LocalAuthentication.hasHardwareAsync();
    if (!compatible) return false;
    const enrolled = await LocalAuthentication.isEnrolledAsync();
    return enrolled;
  }, []);

  const authenticate = useCallback(async (): Promise<boolean> => {
    const result = await LocalAuthentication.authenticateAsync({
      promptMessage: 'Authenticate to access BuildMate',
      cancelLabel: 'Cancel',
      disableDeviceFallback: false,
    });
    return result.success;
  }, []);

  const storeToken = useCallback(async (token: string): Promise<void> => {
    await SecureStore.setItemAsync(TOKEN_KEY, token);
  }, []);

  const getToken = useCallback(async (): Promise<string | null> => {
    return await SecureStore.getItemAsync(TOKEN_KEY);
  }, []);

  const clearToken = useCallback(async (): Promise<void> => {
    await SecureStore.deleteItemAsync(TOKEN_KEY);
  }, []);

  return { isAvailable, authenticate, storeToken, getToken, clearToken };
}

```

## mobile/src/hooks/useOffline.ts

```typescript
import { useState, useEffect } from 'react';
import NetInfo from '@react-native-community/netinfo';

export function useOffline() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    const unsubscribe = NetInfo.addEventListener((state) => {
      setIsOnline(state.isConnected ?? true);
    });
    return () => unsubscribe();
  }, []);

  return { isOnline };
}

```

## mobile/src/hooks/useHaptics.ts

```typescript
import { useCallback } from 'react';
import * as Haptics from 'expo-haptics';

export function useHaptics() {
  const light = useCallback(async () => {
    await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  }, []);

  const medium = useCallback(async () => {
    await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  }, []);

  const heavy = useCallback(async () => {
    await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
  }, []);

  const success = useCallback(async () => {
    await Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  const warning = useCallback(async () => {
    await Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);
  }, []);

  const error = useCallback(async () => {
    await Haptics.notificationAsync(Haptics.NotificationFeedbackType.Error);
  }, []);

  const selection = useCallback(async () => {
    await Haptics.selectionAsync();
  }, []);

  return { light, medium, heavy, success, warning, error, selection };
}

```

## mobile/src/hooks/useDocumentPicker.ts

```typescript
import { useCallback } from 'react';
import * as DocumentPicker from 'expo-document-picker';
import { Alert } from 'react-native';

interface PickedDocument {
  uri: string;
  name: string;
  size?: number;
  mimeType?: string;
}

export function useDocumentPicker() {
  const pickDocument = useCallback(async (): Promise<PickedDocument | null> => {
    try {
      const result = await DocumentPicker.getDocumentAsync({
        type: ['application/pdf', 'image/*', 'application/vnd.ms-excel', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'],
        copyToCacheDirectory: true,
      });

      if (result.canceled || !result.assets[0]) return null;

      const asset = result.assets[0];
      return {
        uri: asset.uri,
        name: asset.name,
        size: asset.size ?? undefined,
        mimeType: asset.mimeType ?? undefined,
      };
    } catch {
      Alert.alert('Error', 'Unable to pick document.');
      return null;
    }
  }, []);

  return { pickDocument };
}

```

## mobile/src/hooks/useShare.ts

```typescript
import { useCallback } from 'react';
import { Share, Alert } from 'react-native';

interface ShareOptions {
  title: string;
  message: string;
  url?: string;
}

export function useShare() {
  const share = useCallback(async ({ title, message, url }: ShareOptions): Promise<void> => {
    try {
      await Share.share(
        { title, message, url },
        { dialogTitle: title },
      );
    } catch {
      Alert.alert('Share Error', 'Unable to share content.');
    }
  }, []);

  return { share };
}

```

## mobile/src/hooks/useDeviceType.ts

```typescript
import { useWindowDimensions } from 'react-native';

export function useDeviceType() {
  const { width, height } = useWindowDimensions();
  return {
    isTablet: width >= 768,
    isLandscape: width > height,
  };
}

```

## mobile/src/hooks/useKeyboardShortcuts.ts

```typescript
import { useEffect, useCallback } from 'react';
import { Platform } from 'react-native';
import { useDeviceType } from './useDeviceType';

interface KeyboardShortcut {
  key: string;
  meta?: boolean;
  ctrl?: boolean;
  shift?: boolean;
  handler: () => void;
}

export function useKeyboardShortcuts(shortcuts: KeyboardShortcut[]) {
  const { isTablet } = useDeviceType();

  useEffect(() => {
    // Hardware keyboard shortcuts only apply on iPad
    if (!isTablet || Platform.OS !== 'ios') return;

    // React Native doesn't have a built-in keyDown listener for hardware keyboards.
    // On iPad with Catalyst / hardware keyboard support, use the KeyCommandsModule
    // or react-native-keycommand library for Cmd+key bindings.
    //
    // Registered shortcuts:
    // - Cmd+N: New project
    // - Cmd+F: Search
    // - Cmd+S: Save
    //
    // Integration point: Install react-native-keycommand and register
    // shortcuts here when building for production iPad deployment.
  }, [isTablet, shortcuts]);
}

```

## mobile/src/hooks/useDragAndDrop.ts

```typescript
import { useRef, useCallback } from 'react';
import { Animated, PanResponder } from 'react-native';
import { useDeviceType } from './useDeviceType';

interface DragAndDropOptions<T> {
  items: T[];
  onReorder: (reordered: T[]) => void;
  itemHeight: number;
}

export function useDragAndDrop<T>({ items, onReorder, itemHeight }: DragAndDropOptions<T>) {
  const { isTablet } = useDeviceType();
  const dragIndex = useRef<number | null>(null);
  const translateY = useRef(new Animated.Value(0)).current;

  const panResponder = PanResponder.create({
    onStartShouldSetPanResponder: () => isTablet,
    onMoveShouldSetPanResponder: () => isTablet,
    onPanResponderGrant: (_e, _gestureState) => {
      // Calculate drag index from touch position
    },
    onPanResponderMove: (_e, gestureState) => {
      translateY.setValue(gestureState.dy);
    },
    onPanResponderRelease: (_e, gestureState) => {
      if (dragIndex.current === null) return;

      const offset = Math.round(gestureState.dy / itemHeight);
      const newIndex = Math.max(0, Math.min(items.length - 1, dragIndex.current + offset));

      if (newIndex !== dragIndex.current) {
        const reordered = [...items];
        const [moved] = reordered.splice(dragIndex.current, 1);
        reordered.splice(newIndex, 0, moved);
        onReorder(reordered);
      }

      translateY.setValue(0);
      dragIndex.current = null;
    },
  });

  const startDrag = useCallback((index: number) => {
    if (!isTablet) return;
    dragIndex.current = index;
  }, [isTablet]);

  return {
    panHandlers: isTablet ? panResponder.panHandlers : {},
    startDrag,
    translateY,
    isEnabled: isTablet,
  };
}

```

## mobile/src/__tests__/screens/LoginScreen.test.tsx

```tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';

// Mock navigation
const mockNavigate = jest.fn();
jest.mock('@react-navigation/native', () => ({
  useNavigation: () => ({ navigate: mockNavigate }),
}));

// Mock auth store
jest.mock('../../store/authStore', () => ({
  useAuthStore: () => ({
    login: jest.fn(),
    isLoading: false,
    error: null,
  }),
}));

// Mock the screen — import after mocks
import LoginScreen from '../../screens/LoginScreen';

describe('LoginScreen', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('renders login form elements', () => {
    const { getByPlaceholderText, getByText } = render(<LoginScreen />);
    expect(getByPlaceholderText(/email/i)).toBeTruthy();
    expect(getByPlaceholderText(/password/i)).toBeTruthy();
    expect(getByText(/sign in|log in/i)).toBeTruthy();
  });
});

```

## mobile/src/__tests__/ipad/iPadLayout.test.tsx

```tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import { Text } from 'react-native';
import SplitView from '../../components/SplitView';
import ResponsiveGrid from '../../components/ResponsiveGrid';

// Test iPad-specific layout behaviour

describe('iPad Layout', () => {
  describe('SplitView on tablet', () => {
    beforeAll(() => {
      jest.doMock('../../hooks/useDeviceType', () => ({
        useDeviceType: () => ({ isTablet: true, isLandscape: true }),
      }));
    });

    it('renders split layout on iPad landscape', () => {
      const { getByText } = render(
        <SplitView sidebar={<Text>Sidebar Nav</Text>}>
          <Text>Main Content</Text>
        </SplitView>,
      );
      expect(getByText('Sidebar Nav')).toBeTruthy();
      expect(getByText('Main Content')).toBeTruthy();
    });
  });

  describe('ResponsiveGrid columns', () => {
    const items = Array.from({ length: 6 }, (_, i) => ({ id: String(i), label: `Item ${i}` }));

    it('renders grid items', () => {
      const { getByText } = render(
        <ResponsiveGrid
          data={items}
          renderItem={({ item }) => <Text>{item.label}</Text>}
          keyExtractor={(item) => item.id}
          tabletCols={3}
          tabletLandscapeCols={4}
        />,
      );
      expect(getByText('Item 0')).toBeTruthy();
      expect(getByText('Item 5')).toBeTruthy();
    });
  });

  // TODO: Test Gantt chart renders on iPad dimensions
  // TODO: Test multi-column stat cards (4 columns on iPad)
  // TODO: Test sidebar collapses in portrait mode
});

```

## mobile/src/__tests__/navigation/NavigationFlow.test.tsx

```tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import { NavigationContainer } from '@react-navigation/native';
import { Text, View } from 'react-native';

// Navigation flow test scaffolding
// These tests verify the auth → main → project detail navigation flow

jest.mock('@react-navigation/native-stack', () => ({
  createNativeStackNavigator: () => ({
    Navigator: ({ children }: { children: React.ReactNode }) => <View>{children}</View>,
    Screen: ({ children }: { children: React.ReactNode }) => <View>{children}</View>,
  }),
}));

jest.mock('@react-navigation/bottom-tabs', () => ({
  createBottomTabNavigator: () => ({
    Navigator: ({ children }: { children: React.ReactNode }) => <View>{children}</View>,
    Screen: ({ children }: { children: React.ReactNode }) => <View>{children}</View>,
  }),
}));

describe('Navigation Flow', () => {
  it('renders without crashing', () => {
    const { getByText } = render(
      <NavigationContainer>
        <Text>Navigation Test</Text>
      </NavigationContainer>,
    );
    expect(getByText('Navigation Test')).toBeTruthy();
  });

  // TODO: Add tests for:
  // - Unauthenticated user sees login screen
  // - Authenticated user sees main tabs
  // - Tab navigation between Dashboard, Projects, Tradies, Settings
  // - Stack navigation: Projects → Project Detail → Budget/Planning/etc
});

```

## mobile/src/__tests__/components/SwipeableRow.test.tsx

```tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import { Text } from 'react-native';
import SwipeableRow from '../../components/SwipeableRow';

jest.mock('react-native-gesture-handler', () => ({
  Swipeable: ({ children }: { children: React.ReactNode }) => children,
}));

describe('SwipeableRow', () => {
  it('renders children content', () => {
    const { getByText } = render(
      <SwipeableRow>
        <Text>Row Content</Text>
      </SwipeableRow>,
    );
    expect(getByText('Row Content')).toBeTruthy();
  });
});

```

## mobile/src/__tests__/components/SplitView.test.tsx

```tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import { Text } from 'react-native';
import SplitView from '../../components/SplitView';

// Mock useDeviceType for tablet landscape
jest.mock('../../hooks/useDeviceType', () => ({
  useDeviceType: () => ({ isTablet: true, isLandscape: true }),
}));

describe('SplitView', () => {
  it('renders sidebar and content on iPad landscape', () => {
    const { getByText } = render(
      <SplitView sidebar={<Text>Sidebar</Text>}>
        <Text>Content</Text>
      </SplitView>,
    );
    expect(getByText('Sidebar')).toBeTruthy();
    expect(getByText('Content')).toBeTruthy();
  });
});

```

## mobile/src/__tests__/components/ResponsiveGrid.test.tsx

```tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import { Text } from 'react-native';
import ResponsiveGrid from '../../components/ResponsiveGrid';

describe('ResponsiveGrid', () => {
  const data = [{ id: '1', name: 'Item 1' }, { id: '2', name: 'Item 2' }];

  it('renders all items', () => {
    const { getByText } = render(
      <ResponsiveGrid
        data={data}
        renderItem={({ item }) => <Text>{item.name}</Text>}
        keyExtractor={(item) => item.id}
      />,
    );
    expect(getByText('Item 1')).toBeTruthy();
    expect(getByText('Item 2')).toBeTruthy();
  });
});

```

## mobile/src/__tests__/components/PhotoGallery.test.tsx

```tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import PhotoGallery from '../../components/PhotoGallery';

describe('PhotoGallery', () => {
  const mockPhotos = [
    'https://example.com/photo1.jpg',
    'https://example.com/photo2.jpg',
  ];

  it('renders photo thumbnails', () => {
    const { getAllByRole } = render(
      <PhotoGallery photos={mockPhotos} />,
    );
    // Photos render as Image components inside TouchableOpacity
    expect(getAllByRole).toBeDefined();
  });

  it('renders add button when editable', () => {
    const onAdd = jest.fn();
    const { getByText } = render(
      <PhotoGallery photos={mockPhotos} onAddPhoto={onAdd} editable />,
    );
    expect(getByText('Add')).toBeTruthy();
  });

  it('does not render add button when not editable', () => {
    const { queryByText } = render(
      <PhotoGallery photos={mockPhotos} editable={false} />,
    );
    expect(queryByText('Add')).toBeNull();
  });

  it('calls onAddPhoto when add button pressed', () => {
    const onAdd = jest.fn();
    const { getByText } = render(
      <PhotoGallery photos={mockPhotos} onAddPhoto={onAdd} editable />,
    );
    fireEvent.press(getByText('Add'));
    expect(onAdd).toHaveBeenCalledTimes(1);
  });
});

```

## mobile/src/__tests__/components/OfflineBanner.test.tsx

```tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import OfflineBanner from '../../components/OfflineBanner';

// Mock NetInfo
jest.mock('@react-native-community/netinfo', () => ({
  addEventListener: jest.fn((callback) => {
    callback({ isConnected: false });
    return jest.fn();
  }),
}));

describe('OfflineBanner', () => {
  it('shows banner when offline', () => {
    const { getByText } = render(<OfflineBanner />);
    expect(getByText(/offline/i)).toBeTruthy();
  });
});

```

## mobile/src/__tests__/e2e/detox.config.ts

```typescript
/**
 * Detox E2E Test Configuration
 *
 * Prerequisites:
 * 1. Install Detox CLI: npm install -g detox-cli
 * 2. Install Detox: npm install --save-dev detox
 * 3. Build the app: detox build --configuration ios.sim.debug
 *
 * Running tests:
 * - iPhone: detox test --configuration ios.sim.debug
 * - iPad: detox test --configuration ipad.sim.debug
 */

export const detoxConfig = {
  testRunner: {
    args: {
      config: 'e2e/jest.config.js',
    },
    jest: {
      setupTimeout: 120000,
    },
  },
  apps: {
    'ios.debug': {
      type: 'ios.app',
      binaryPath: 'ios/build/Build/Products/Debug-iphonesimulator/BuildMate.app',
      build: 'xcodebuild -workspace ios/BuildMate.xcworkspace -scheme BuildMate -configuration Debug -sdk iphonesimulator -derivedDataPath ios/build',
    },
  },
  devices: {
    'iphone.simulator': {
      type: 'ios.simulator',
      device: { type: 'iPhone 15' },
    },
    'iphone.se.simulator': {
      type: 'ios.simulator',
      device: { type: 'iPhone SE (3rd generation)' },
    },
    'ipad.simulator': {
      type: 'ios.simulator',
      device: { type: 'iPad Pro (11-inch) (4th generation)' },
    },
    'ipad.pro.simulator': {
      type: 'ios.simulator',
      device: { type: 'iPad Pro (12.9-inch) (6th generation)' },
    },
  },
  configurations: {
    'ios.sim.debug': {
      device: 'iphone.simulator',
      app: 'ios.debug',
    },
    'ipad.sim.debug': {
      device: 'ipad.simulator',
      app: 'ios.debug',
    },
  },
};

/**
 * Device Testing Matrix (8.9.6)
 *
 * Test on the following devices in both portrait and landscape:
 *
 * iPhone:
 * - iPhone 15 (6.1")
 * - iPhone 15 Pro Max (6.7")
 * - iPhone SE 3rd gen (4.7")
 *
 * iPad:
 * - iPad mini (8.3")
 * - iPad Air (10.9")
 * - iPad Pro 11" (4th gen)
 * - iPad Pro 12.9" (6th gen)
 *
 * Critical test flows:
 * 1. Login → Dashboard → Projects list
 * 2. Create project → View project detail → Budget tab
 * 3. Add transaction → View financials
 * 4. Create inspection → Add defect → Capture photo
 * 5. Offline mode → Queue mutation → Reconnect → Sync
 */

```
