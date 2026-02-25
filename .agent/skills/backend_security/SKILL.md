---
description: Advanced skill for implementing robust logging and security hardening (anti-hacker) measures in Go Fiber applications.
---

# Backend Security & Logging Skill

This skill provides best practices and implementation guides for securing your Go Fiber backend against common vulnerabilities and ensuring comprehensive observability through structured logging.

## 1. High-Performance Logging (Slog + Fiber Logger)

### A. Application Logic Logging (Use `log/slog`)
For internal application logic (services, database operations), use Go 1.21+ structured logging (`slog`).

**Pattern:** `slog.[Level](msg, "key", value)`
- **Info:** Normal operations (e.g., "User logged in", "Order created").
- **Error:** Failures needing attention (e.g., "Database connection failed").
- **Warn:** Non-critical issues (e.g., "Rate limit hit").
- **Debug:** Detailed dev info.

**Example Implementation:**
```go
import "log/slog"

// In a service or handler
slog.Info("Processing payment", "user_id", userID, "amount", amount)
if err != nil {
    slog.Error("Payment failed", "error", err, "user_id", userID)
}
```

### B. HTTP Request Logging (Fiber Middleware)
Use Fiber's built-in logger for request tracing. Configure it to output JSON for easier parsing in production.

**Setup in `main.go`:**
```go
import "github.com/gofiber/fiber/v2/middleware/logger"

app.Use(logger.New(logger.Config{
    Format:     "[${time}] ${status} - ${method} ${path}\n",
    TimeFormat: "2006-01-02 15:04:05",
    TimeZone:   "Local",
}))
```

---

## 2. Anti-Hacker Security Measures

Implement these essential middlewares to harden your application against common attacks.

### A. Rate Limiting (DDoS Protection)
Prevent abuse by limiting the number of requests a user (IP) can make within a timeframe.

**Implementation (Middleware):**
```go
import "github.com/gofiber/fiber/v2/middleware/limiter"

app.Use(limiter.New(limiter.Config{
    Max:        20,              // 20 requests
    Expiration: 30 * time.Second, // per 30 seconds
    KeyGenerator: func(c *fiber.Ctx) string {
        return c.IP() // Limit by IP
    },
    LimitReached: func(c *fiber.Ctx) error {
        return c.Status(fiber.StatusTooManyRequests).JSON(fiber.Map{
            "status":  "error",
            "message": "Too many requests, please try again later.",
        })
    },
}))
```

### B. Security Headers (Helmet)
Set secure HTTP headers to prevent XSS, clickjacking, and other browser-based attacks.

**Implementation (Middleware):**
```go
import "github.com/gofiber/fiber/v2/middleware/helmet"

app.Use(helmet.New())
```

### C. Panic Recovery (Fail-Safe)
Ensure the server doesn't crash completely if a handler panics.

**Implementation (Middleware):**
```go
import "github.com/gofiber/fiber/v2/middleware/recover"

app.Use(recover.New())
```

### D. CORS (Cross-Origin Resource Sharing)
Restrict API access to trusted domains only.

**Implementation:**
```go
import "github.com/gofiber/fiber/v2/middleware/cors"

app.Use(cors.New(cors.Config{
    AllowOrigins: "http://localhost:3000, https://your-frontend-domain.com",
    AllowHeaders: "Origin, Content-Type, Accept, Authorization",
    AllowMethods: "GET, POST, PUT, DELETE, OPTIONS",
}))
```

---

## 3. Input Validation & Sanitation
Never trust user input. Validate all incoming data.

- **Use Struct Tags:** Use `validate:"required,email,min=..."` tags on your request structs.
- **Sanitize HTML:** If rendering text, strip HTML tags to prevent XSS.

**Example Validator:**
```go
type RegisterInput struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
}
```

---

## checklist for Security Review
- [ ] Rate Limiter enabled?
- [ ] Helmet headers active?
- [ ] CORS restricted to specific domains (not `*` in production)?
- [ ] Panic Recovery middleware in place?
- [ ] All sensitive errors logged internally, generic errors sent to client?
