---
description: Integrate Frontend and Backend, setup Admin Login, and build a tabbed Dashboard layout with a sidebar.
---

# 03 Admin Dashboard & Backend Integration

This workflow focuses on integrating the React frontend with the Go Fiber backend, implementing the Admin Login, and creating a premium Dashboard with a side-bar and multi-tab system.

## 1. Setup API Client (Axios)
// turbo
Create an Axios instance with base URL and interceptors for JWT.
```bash
# Create lib directory if not exists
mkdir -p frontend/src/lib
```

### 1.1 `frontend/src/lib/api.ts`
```typescript
import axios from 'axios';

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8080/api',
});

api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

## 2. State Management (Zustand)
// turbo
Install Zustand to manage Authentication and Dashboard Tabs.
```bash
cd frontend
npm install zustand
```

### 2.1 `frontend/src/store/useAppStore.ts`
```typescript
import { create } from 'zustand';

interface Tab {
  id: string;
  title: string;
  component: string;
}

interface AppState {
  user: any | null;
  tabs: Tab[];
  activeTabId: string;
  isSidebarOpen: boolean;
  setUser: (user: any) => void;
  setSidebarOpen: (open: boolean) => void;
  addTab: (tab: Tab) => void;
  removeTab: (id: string) => void;
  setActiveTab: (id: string) => void;
  logout: () => void;
}

export const useAppStore = create<AppState>((set) => ({
  user: JSON.parse(localStorage.getItem('user') || 'null'),
  tabs: [{ id: 'dashboard', title: 'Dashboard', component: 'DashboardContent' }],
  activeTabId: 'dashboard',
  isSidebarOpen: true,
  setUser: (user) => {
    localStorage.setItem('user', JSON.stringify(user));
    set({ user });
  },
  setSidebarOpen: (open) => set({ isSidebarOpen: open }),
  addTab: (tab) => set((state) => {
    if (state.tabs.find((t) => t.id === tab.id)) {
      return { activeTabId: tab.id };
    }
    return { tabs: [...state.tabs, tab], activeTabId: tab.id };
  }),
  removeTab: (id) => set((state) => {
    if (id === 'dashboard') return state; // Don't remove dashboard
    const newTabs = state.tabs.filter((t) => t.id !== id);
    const newActiveId = state.activeTabId === id ? newTabs[newTabs.length - 1].id : state.activeTabId;
    return { tabs: newTabs, activeTabId: newActiveId };
  }),
  setActiveTab: (id) => set({ activeTabId: id }),
  logout: () => {
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    set({ user: null, tabs: [{ id: 'dashboard', title: 'Dashboard', component: 'DashboardContent' }], activeTabId: 'dashboard' });
  },
}));
```

## 3. Implement Admin Login Page
Create `frontend/src/pages/AdminLogin.tsx`. 
- **Design**: Clean, professional design with Framer Motion animations.
- **Features**:
    - **Demo Account Button**: One-click fill for `admin@laundry.com` / `admin123`.
    - **Back to Website Button**: Quick navigation back to the Landing Page.

## 4. Admin Layout & Sidebar
Create a layout that includes:
- **Collapsible Sidebar**: 
    - Lists primary menus (Dashboard, Users, Transactions, Services, Settings).
    - **View Website Link**: A direct link to go back to the public landing page.
- **Header**: Contains:
    - Sidebar Toggle Button.
    - **Tab Bar**: Horizontal scrollable tabs with "X" to close.
    - User Profile Dropdown (Logout, Profile settings).
- **Main Content**: Renders the component based on `activeTabId`.

## 5. Implement Tabs Logic
When a sidebar menu is clicked:
1. Call `addTab({ id: 'menu-id', title: 'Menu Name', component: 'ComponentName' })`.
2. The UI switches to that tab.

## 6. Profile & Change Password
- **Profile Modal**: A modal or a dedicated tab to update user details.
- **Change Password**: Form inside the Profile section to hit `/api/auth/change-password` (needs to be implemented in BE).

## 7. Protected Routes (AuthGuard)
Create `frontend/src/components/auth/AuthGuard.tsx`.
```tsx
import { Navigate, Outlet } from 'react-router-dom';
import { useAppStore } from '@/store/useAppStore';

export const AuthGuard = () => {
    const token = localStorage.getItem('token');
    const user = useAppStore(state => state.user);

    if (!token || !user) {
        return <Navigate to="/admin/login" replace />;
    }

    return <Outlet />;
};
```

## 8. Manual Shadcn Components
Due to dependency conflicts, some components are created manually in `frontend/src/components/ui/`:
- `button.tsx`, `input.tsx`, `card.tsx`: Core input and container components.
- `tabs.tsx`: Multi-tab navigation.
- `scroll-area.tsx`, `separator.tsx`: Layout helpers.
- `dropdown-menu.tsx`, `sheet.tsx`: Interactive overlays and sidebar.

## 9. Final assembly (App.tsx)
Configure routes in `App.tsx`.
```tsx
<Routes>
  <Route path="/" element={<LandingPage />} />
  <Route path="/admin/login" element={<AdminLogin />} />
  <Route element={<AuthGuard />}>
    <Route path="/admin" element={<Navigate to="/admin/dashboard" replace />} />
    <Route path="/admin/dashboard" element={<AdminLayout />} />
  </Route>
</Routes>
```
