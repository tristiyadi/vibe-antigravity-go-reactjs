---
description: Comprehensive skill for building scalable Go Fiber backend systems, ensuring clean code, standard conventions, and robust error handling.
---

# Go Fiber Backend Development Skill

This skill is designed to assist with developing high-quality, scalable backend applications using Go (Golang), Fiber v2, and GORM. It strictly adheres to the project's coding standards and Clean Architecture principles.

## 1. Core Principles

**Architecture:**
- **Models:** Define data structures (GORM entities) representing database tables.
- **Handlers:** Manage HTTP requests and responses, focusing only on the request/response cycle.
- **Routes:** Map incoming URLs to specific handlers.
- **Services (Optional):** Encapsulate complex business logic, separating it from handlers for better testability.
- **Utils:** House reusable helper functions (e.g., Response formatting, Auth handling).

**Clean Code:**
- **Naming:** strictly use `snake_case` for file/directory names, `PascalCase` for exported types/functions, and `camelCase` for variables.
- **Error Handling:** Explicitly check `if err != nil` after every operation that can fail. Propagate errors meaningfully.
- **Responses:** Always return a consistent JSON format: `{ "status": "...", "message": "...", "data": ... }`.

**Security:**
- Hash passwords using **Bcrypt**.
- Implement **JWT** for authentication, protecting sensitive routes with middleware.
- Configure **CORS** explicitly to allow `Authorization` headers.
- **Critical**: If `AllowCredentials` is `true`, `AllowOrigins` **MUST NOT** use a wildcard (`*`). Use specific domains or an environment variable.
- Validate all user inputs rigorously.

## 2. Implementation Guide

### A. Creating a New Feature (CRUD)
When creating a new feature (e.g., "Manage Orders"):

1.  **Model (`models/order.go`):** Define the struct with GORM tags (`gorm:"primaryKey"`, `json:"id"`).
2.  **Handler (`handlers/order_handler.go`):** Implement handler functions:
    -   `GetOrders`: Retrieve list.
    -   `GetOrder`: Retrieve single item by ID.
    -   `CreateOrder`: Validate input and create new record.
    -   `UpdateOrder`: Update existing record.
    -   `DeleteOrder`: Soft delete or hard delete.
3.  **Route (`routes/setup.go` or `routes/order_routes.go`):** Register routes and group by feature (e.g., `/api/orders`).
4.  **AutoMigrate (`database/connect.go`):** Add the new model to `db.AutoMigrate(&Order{})`.

### B. Standard Response Format
Always use the helper functions in `utils/response.go`:

```go
// Success
return utils.SendSuccessResponse(c, "Data fetched", data)

// Error
return utils.SendErrorResponse(c, fiber.StatusNotFound, "Not found", nil)
```

### C. Error Handling Pattern
Always handle errors gracefully. Avoid panic unless critical during initialization.

```go
// Example in Handler
if err := db.Create(&user).Error; err != nil {
    return utils.JSONResponse(c, fiber.StatusInternalServerError, "Failed to create user", nil)
}
```

## 3. Checklist for Every Modification
- [ ] Conforms to `snake_case` file naming?
- [ ] Exported structs/funcs use `PascalCase`?
- [ ] `go fmt ./...` ran successfully?
- [ ] `.env` is listed in `.gitignore` and not tracked by Git?
- [ ] Errors handled and returned with proper status codes?
- [ ] JSON response standard followed?
