---
description: This document outlines the coding standards, architectural patterns, and best practices for the Frontend project using React, Vite, Tailwind CSS, and Shadcn UI.
---

# Frontend Rules & Standards

This document serves as the single source of truth for all frontend development in this repository. Follow these rules to ensure code consistency, maintainability, and performance.

## 1. Project Structure
We follow a modular, scalable React project layout:

```
frontend/
├── public/              # Static assets (favicons, manifest.json)
├── src/
│   ├── assets/          # Images, fonts, SVG
│   ├── components/      # Reusable UI components
│   │   ├── ui/          # Shadcn UI primitives (Button, Card, Input)
│   │   └── shared/      # Shared app components (Navbar, Footer, FloatingWA)
│   ├── hooks/           # Custom React hooks (useAuth, useFetch)
│   ├── layouts/         # Page layouts (MainLayout, AuthLayout)
│   ├── lib/             # Utilities (utils.ts, axios instance, validators)
│   ├── pages/           # Page components (routed views)
│   ├── services/        # API calls (separated from UI logic)
│   ├── store/           # Global Usage State (Context/Zustand)
│   ├── types/           # TypeScript interfaces/types (User, Service, APIResponse)
│   ├── App.tsx          # Main App component & Routing
│   └── main.tsx         # Entry point (Providers)
└── vite.config.ts       # Vite configuration
```

## 2. Coding Standards

### 2.1 General Setup
- **Build Tool**: Vite 6+
- **Language**: TypeScript (`.tsx` for components, `.ts` for logic)
- **Dependency Management**: Use `--legacy-peer-deps` with npm if encountering React 19 peer dependency conflicts (common with libraries like `react-helmet-async`).
- **Formatting**: Use Prettier with standard config.
- **Linting**: ESLint with React Hooks plugin.

### 2.2 Naming Conventions
- **Components**: `PascalCase.tsx` (e.g., `ServiceCard.tsx`, `Hero.tsx`).
- **Directories**: `lowercase` or `kebab-case` (e.g., `components`, `pages`).
- **Hooks**: `camelCase` starting with `use` (e.g., `useAuth.ts`).
- **Functions**: `camelCase` (e.g., `handleSubmit`, `fetchData`).
- **Interfaces/Types**: `PascalCase` (e.g., `User`, `ServiceResponse`).
- **Constants**: `UPPER_CASE` (e.g., `API_BASE_URL`).

### 2.3 Component Structure
Functional components only. Avoid Class components.

```tsx
// Good
export const MyComponent = ({ prop1 }: Props) => {
  return <div>{prop1}</div>;
};
```

## 3. Technology Stack
- **Build Tool**: [Vite](https://vitejs.dev/)
- **UI Framework**: [React](https://react.dev/)
- **Styling**: [Tailwind CSS](https://tailwindcss.com/)
- **Components**: [Shadcn UI](https://ui.shadcn.com/) (Radix UI + Tailwind)
- **Icons**: [Lucide React](https://lucide.dev/)
- **Animations**: [Framer Motion](https://www.framer.com/motion/)
- **Routing**: [React Router DOM](https://reactrouter.com/)
- **HTTP Client**: Axios (with Interceptors)

## 4. Styling & Theming
- **Tailwind First**: Use utility classes for 99% of styling.
- **Global Theme**: Define colors (primary, secondary, accent) in `tailwind.config.js`.
- **CSS Variables**: Use CSS variables for theme colors to support Dark Mode easily.
- **Responsive**: Design Mobile-First. Use `md:`, `lg:` prefixes for larger screens.

## 5. State Management
- **Local State**: `useState`, `useReducer` for component-specific logic.
- **Global State**: React Context or Zustand for app-wide state (Auth, Theme).
- **Server State**: Use `useEffect` + Service layer or TanStack Query (optional but recommended).

## 6. Performance & PWA
- **Cloudimage/WebP**: Optimize all images.
- **Lazy Loading**: Use `React.lazy()` for route-based code splitting.
- **PWA**: Enable `vite-plugin-pwa` for offline caching and installability.
- **Lighthouse**: Target Score > 90 in Performance and Accessibility.

## 7. Security (Frontend)
- **XSS**: Sanitize user input. Avoid `dangerouslySetInnerHTML`.
- **Auth**: Store JWT in `localStorage` (or HttpOnly Cookie if API supports it).
- **Route Guard**: Implement `AuthGuard` wrapper for protected pages.
- **Headers**: Implement `react-helmet-async` for security headers meta tags.

## 8. UX & Navigation Standards
- **Demo Access**: The Admin Login page must include a "Use Demo Account" button for quick access during development/demo.
- **Global Exit**: All administrative views (Login & Dashboard) must provide a visible link to return to the public Landing Page (e.g., "Back to Website" or "View Website").

## 9. Admin Dashboard Standards
- **Data Tables**: Use TanStack Table with the `DataTable` shared component. Must include pagination, sorting, and column visibility.
- **Dropdowns**: Use Shadcn `Select` or `Combobox` for all dropdowns. Searchable selects are preferred for large datasets.
- **Exports**: All data tables must support CSV, XLSX, and PDF exports using `xlsx` and `jspdf`.
- **Row Actions**: Common actions should include View, Edit, Archive (Soft Delete), Restore, and Delete (Permanent).
- **Bulk Actions**: Implement checkboxes for bulk selection and actions.

## 10. API connectivity & Debugging
- **Environment Variables**: Use `import.meta.env.VITE_API_URL`. Ensure it is set in the Deployment Platform (Railway) before building.
- **Hardcoding for Testing**: If encountering persistent connectivity issues in production (e.g., localhost leak), hardcode the `baseURL` in `api.ts` temporarily to isolate the issue from environment variable build-time failures.
- **Redeploy Requirement**: Remind that any change to variables or hardcoded URLs requires a full **Redeploy** to take effect in the browser.
