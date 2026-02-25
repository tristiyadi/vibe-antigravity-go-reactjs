# Laundry Web App

A modern, full-stack laundry management system built with Go (Fiber) and React (Vite).

## clone Backend Project
```bash
git submodule add https://github.com/tristiyadi/goleng-fiber-starter.git backend
git submodule add https://github.com/tristiyadi/react-laundry.git frontend
git submodule update --remote --merge # update all submodules
git submodule update --remote --merge frontend # update frontend submodule only
git submodule update --remote --merge backend # update backend submodule only

```

## Project Structure

```text
.
├── backend/          # Go Fiber Backend
├── frontend/         # React + Vite + Tailwind CSS Frontend
└── docs/             # Documentation and API references
```

## Features

- **Premium Landing Page**: Responsive design with Framer Motion animations.
- **Admin Dashboard**: Comprehensive management system with multi-tab layout.
- **User Management**: CRUD operations, soft-delete (archive), and data export (Excel/PDF).
- **Security**: JWT Authentication, CORS protection, and Role-based access control.

## Tech Stack

### Backend

- **Go 1.21+**
- **Fiber v2** (Web Framework)
- **GORM** (ORM)
- **PostgreSQL** (Database)
- **JWT** (Authentication)

### Frontend

- **React 18**
- **Vite** (Build Tool)
- **Tailwind CSS** (Styling)
- **Zustand** (State Management)
- **TanStack Table** (Data Tables)
- **Playwright** (End-to-End Testing)

## Getting Started

### Prerequisites

- Go installed
- Node.js & npm installed
- PostgreSQL database

### Installation

1. **Clone the repository**

   ```bash
   git clone <repo-url>
   cd vibe-coding-go-react-laundry
   ```

2. **Backend Setup**

   ```bash
   cd backend
   cp .env.example .env
   # Update .env with your database credentials
   go mod tidy
   go run cmd/main.go
   ```

3. **Frontend Setup**

   ```bash
   cd frontend
   cp .env.example .env
   npm install
   npm run dev
   ```

### Running Tests

1. **End-to-End (E2E) Tests**

   Playwright is used for E2E testing. Ensure the backend is running before testing authentication flows.

   ```bash
   cd frontend
   npx playwright install # Only needed once
   npm run test:e2e
   ```

   To run tests in UI mode:

   ```bash
   npx playwright test --ui
   ```

## Documentation

- [API Documentation](./docs/API.md)
