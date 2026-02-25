---
description: Deploy the Go Backend and React Frontend to Railway using Docker.
---

# Railway Deployment Workflow

This workflow provides the necessary configuration and steps to deploy the Laundry application to Railway.

## 1. Backend Configuration
Create the following files in the `backend/` directory.

### .dockerignore
```dockerignore
# Environment variables
.env

# Go
*.exe
*.exe~
*.dll
*.so
*.dylib
*.test
*.out
vendor/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Air (hot reload)
tmp/
build-errors.log
```

### Dockerfile
```dockerfile
FROM golang:alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

# Build the application pointing to the correct main file
RUN go build -o main ./cmd/main.go

FROM alpine:latest

RUN apk add --no-cache curl

WORKDIR /app

COPY --from=builder /app/main .

# Expose port 8090
EXPOSE 8090

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s \
    CMD curl -f http://localhost:8090/health || exit 1

CMD ["./main"]
```

## 2. Frontend Configuration
Create the following files in the `frontend/` directory.

### nginx.conf
```nginx
server {
    listen 3001;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|otf)$ {
        expires 1y;
        add_header Cache-Control "public, no-transform";
    }

    location /health {
        access_log off;
        return 200 "OK";
        add_header Content-Type text/plain;
    }


    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}
```

### Dockerfile
```dockerfile
# Build stage
FROM node:20-alpine AS build

# Install pnpm (optional, or use npm)
RUN npm install -g pnpm

WORKDIR /app

# Copy package files
COPY package.json pnpm-lock.yaml* package-lock.json* ./

# Install dependencies
RUN [ -f pnpm-lock.yaml ] && pnpm install --frozen-lockfile || npm install --legacy-peer-deps

# Copy source code
COPY . .

# Build the application
RUN [ -f pnpm-lock.yaml ] && pnpm run build || npm run build

# Production stage
FROM nginx:alpine

# Copy built files from build stage to nginx
COPY --from=build /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 3001

HEALTHCHECK --interval=30s --timeout=3s \
    CMD wget --quiet --tries=1 --spider http://127.0.0.1:3001/health || exit 1

CMD ["nginx", "-g", "daemon off;"]
```

### .dockerignore (Frontend)
```dockerignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

node_modules
dist
dist-ssr
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?
```

## 3. Railway Setup Steps
1. **GitHub Repository**: Ensure your code is pushed to a GitHub repository.
2. **Railway Project**: Create a new project on [Railway](https://railway.app/).
3. **Add Database**: Add a PostgreSQL service to your Railway project.
4. **Deploy Backend**:
    - Connect your GitHub repo.
    - Set the Root Directory to `backend`.
    - Set Environment Variables:
        - `PORT`: `8090`
        - `SERVER_PORT`: `8090`
        - `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME` (copy from Railway Postgres variables)
        - `JWT_SECRET`: (your secure secret)
        - `ALLOWED_ORIGINS`: `https://frontend-laundry-production.up.railway.app, http://localhost:5173`
5. **Deploy Frontend**:
    - Connect your GitHub repo again.
    - Set the Root Directory to `frontend`.
    - Set Environment Variables:
        - `PORT`: `3001`
        - `VITE_API_URL`: (your backend railway URL, e.g., `https://backend-production.up.railway.app`)

### ⚠️ Critical Note on Environment Variables
- **Vite Build Process**: Environment variables prefixed with `VITE_` are embedded into the client-side code **during the build process**.
- **Redeploy Required**: If you change `VITE_API_URL` in the Railway settings, you **MUST** trigger a new deployment (Redeploy) for the changes to take effect in the browser. Changing the variable in the dashboard alone is not enough for the static build.
- **Port Fallback**: Local development may fall back to port `5174` if `5173` is busy. Always check the terminal output for the correct local URL.
