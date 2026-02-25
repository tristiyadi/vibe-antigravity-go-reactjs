---
name: app_runner
description: A skill to run the backend (Go Fiber) and frontend (React/Vite) applications. It includes pre-run checks to clear the required ports (8080 and 5173) if they are already in use.
---

# App Runner Skill

This skill automates the process of starting the Laundry application.

## Prerequisites
- Go installed (for backend)
- Node.js and npm installed (for frontend)
- PostgreSQL database accessible (as configured in `backend/.env`)

## Usage

### 1. Check and Clear Ports
Before starting the applications, identify and terminate any processes running on ports `8080` (Backend) and `5173` (Frontend).

**Command (macOS/Linux):**
```bash
# Check and kill port 8080 (Backend)
lsof -ti:8080 | xargs kill -9 2>/dev/null || true

# Check and kill port 5173 (Frontend)
lsof -ti:5173 | xargs kill -9 2>/dev/null || true
```

### 2. Start Backend
Navigate to the `backend` directory and run the Go application.

**Commands:**
```bash
cd backend
go run cmd/main.go
```

### 3. Start Frontend
Navigate to the `frontend` directory and run the Vite development server.

**Commands:**
```bash
cd frontend
npm run dev
```

**Note on Ports:**
Vite usually runs on port `5173`. However, if the system or another process is locking that port, it may automatically fall back to `5174`. Check the terminal output to verify the active URL.

## Automation Script
You can use the provided script `.agent/skills/app_runner/scripts/run_app.sh` to perform all steps in one go.

### Running with Slash Command
If this skill is active, you can instruct the agent to "Run the application using the app_runner skill".

The agent should:
1.  Read this `SKILL.md`.
2.  Execute the port cleanup commands.
3.  Start the backend in a background terminal.
4.  Start the frontend in a background terminal.
