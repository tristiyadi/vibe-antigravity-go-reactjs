---
description: Skill for building modern, responsive frontend applications using React, Vite, Tailwind CSS, and Shadcn UI with a focus on PWA and performance.
---

# Frontend Development Skill (React + Tailwind + Shadcn)

This skill provides a standardized approach to building frontend interfaces for the Laundry Web App, ensuring consistency in design, performance, and code structure.

## 1. Project Structure & Standards

Follow this directory structure for scalability:

```
frontend/
├── public/              # Static assets (favicons, manifest.json)
├── src/
│   ├── assets/          # Images, fonts, SVG
│   ├── components/      # Reusable UI components
│   │   ├── ui/          # Shadcn UI primitives (Button, Card, Input)
│   │   └── shared/      # Shared app components (Navbar, Footer)
│   ├── hooks/           # Custom React hooks
│   ├── layouts/         # Page layouts (MainLayout, AuthLayout)
│   ├── lib/             # Utilities (utils.ts, axios instance)
│   ├── pages/           # Page components (routed)
│   ├── services/        # API calls (separated from UI logic)
│   ├── store/           # State management (Zustand/Context)
│   ├── types/           # TypeScript interfaces/types
│   ├── App.tsx          # Main App component
│   └── main.tsx         # Entry point
└── vite.config.ts       # Vite configuration
```

**Coding Standards:**
- **Components:** Functional components with TypeScript interfaces. Use `PascalCase`.
- **Styling:** Use Tailwind CSS utility classes. Avoid custom CSS files unless necessary for complex animations.
- **State:** Use local state for UI interactions, and Context/Zustand for global app state.
- **Responsiveness:** Always design Mobile-First using Tailwind breakpoints (`md:`, `lg:`).

## 2. Component Implementation Guide

### A. Creating a New Component
All new UI components should be placed in `src/components`.

**Example (`src/components/ServiceCard.tsx`):**
```tsx
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { formatCurrency } from "@/lib/utils";

interface ServiceCardProps {
  title: string;
  price: number;
  description: string;
}

export const ServiceCard = ({ title, price, description }: ServiceCardProps) => {
  return (
    <Card className="hover:shadow-lg transition-shadow border-blue-100">
      <CardHeader>
        <CardTitle className="text-primary text-xl font-bold">{title}</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-gray-600 mb-4">{description}</p>
        <div className="font-semibold text-lg text-blue-900">
          {formatCurrency(price)}
        </div>
      </CardContent>
    </Card>
  );
};
```

### B. Shadcn UI Usage
Don't reinvent the wheel. Use Shadcn primitives for buttons, inputs, dialogs, etc.

```tsx
import { Button } from "@/components/ui/button";

<Button variant="default" size="lg" className="rounded-full">
  Book Now
</Button>
```

## 3. API Integration Pattern
Keep API logic separate from UI components using a service layer.

**Setup (`src/lib/api.ts`):**
```ts
import axios from 'axios';

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8080/api',
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add Interceptor for JWT
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

**Service (`src/services/auth.service.ts`):**
```ts
import { api } from '@/lib/api';
import { LoginInput, LoginResponse } from '@/types/auth';

export const AuthService = {
  login: async (data: LoginInput) => {
    const response = await api.post<LoginResponse>('/auth/login', data);
    return response.data;
  },
  // ... other auth methods
};
```

## 4. Performance & PWA Best Practices
- **Lazy Loading:** Use `React.lazy()` for route-based code splitting.
- **Image Optimization:** Use WebP format and proper sizing.
- **PWA:** Configure `vite-plugin-pwa` for offline capabilities and installability.

**Lazy Load Example (`src/App.tsx`):**
```tsx
const Dashboard = React.lazy(() => import('./pages/Dashboard'));

<Suspense fallback={<LoadingSpinner />}>
  <Routes>
     <Route path="/dashboard" element={<Dashboard />} />
  </Routes>
</Suspense>
```

## 5. Security & SEO
- **Helmet:** Always include `<Helmet>` tags on every page for setting Titles and Meta Descriptions.
- **XSS Protection:** React handles most XSS, but be careful with `dangerouslySetInnerHTML`.
- **Secure Storage:** Do not store sensitive data in LocalStorage other than the JWT token (and even then, consider HttpOnly cookies if possible).

## Checklist for Review
- [ ] Responsive on mobile (320px+)?
- [ ] No console errors?
- [ ] Accessibility: `aria-label` used on icon-only buttons?
- [ ] Loading states handled for async operations?
