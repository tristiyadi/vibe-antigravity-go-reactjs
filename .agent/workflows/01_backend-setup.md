---
description: This workflow initializes a Go backend project using Fiber v2, GORM (PostgreSQL), JWT, and Bcrypt. It sets up the folder structure, authentication, and database models for a Laundry Web App.
---

# 01 Backend Setup (Fiber + GORM + JWT)

This workflow sets up the backend for the Laundry Web App using Go Fiber, GORM, and PostgreSQL. It includes authentication (JWT), password hashing (bcrypt), and a modular project structure.

## 1. Initialize Go Module
// turbo
Initialize the Go module in the `backend` directory.
```bash
cd backend
if [ ! -f go.mod ]; then
    go mod init laundry-backend
fi
```

## 1.1 Setup .gitignore
// turbo
Create a `.gitignore` file to prevent sensitive files like `.env` from being tracked.
```bash
cd backend
if [ ! -f .gitignore ]; then
cat <<EOF > .gitignore
# Binaries
*.exe
*.exe~
*.dll
*.so
*.dylib
bin/
tmp/

# Environment variables
.env
.env.*
!.env.example

# Logs
*.log

# OS files
.DS_Store
EOF
fi
```

## 2. Install Dependencies
// turbo
Install Fiber, GORM, Postgres driver, JWT, Bcrypt, UUID, and Godotenv.
```bash
cd backend
go get -u github.com/gofiber/fiber/v2
go get -u gorm.io/gorm
go get -u gorm.io/driver/postgres
go get -u github.com/joho/godotenv
go get -u github.com/golang-jwt/jwt/v5
go get -u golang.org/x/crypto/bcrypt
go get -u github.com/google/uuid
```

## 3. Create Project Structure
// turbo
Create the modular folder structure.
```bash
cd backend
mkdir -p cmd
mkdir -p config
mkdir -p database
mkdir -p handlers
mkdir -p middleware
mkdir -p models
mkdir -p routes
mkdir -p utils
```

## 4. Configuration & Database
Set up the database connection and configuration.

### 4.1 Config
Create `config/config.go`.
```go
package config

import (
	"os"

	"github.com/joho/godotenv"
)

func LoadConfig() {
	if err := godotenv.Load(); err != nil {
		// handle error or ignore if using system envs
	}
}

func Get(key string) string {
	return os.Getenv(key)
}
```

### 4.2 Database Connection
Create `database/connection.go`.
```go
package database

import (
	"fmt"
	"log"

	"laundry-backend/config"
	"gorm.io/driver/postgres"
	"gorm.io/gorm"
	"gorm.io/gorm/logger"
)

var DB *gorm.DB

func Connect() {
	config.LoadConfig()

	dsn := fmt.Sprintf("host=%s user=%s password=%s dbname=%s port=%s sslmode=%s",
		config.Get("DB_HOST"),
		config.Get("DB_USER"),
		config.Get("DB_PASSWORD"),
		config.Get("DB_NAME"),
		config.Get("DB_PORT"),
		config.Get("DB_SSLMODE"),
	)

	var err error
	DB, err = gorm.Open(postgres.Open(dsn), &gorm.Config{
		Logger: logger.Default.LogMode(logger.Info),
	})

	if err != nil {
		panic("Failed to connect to database: " + err.Error())
	}

	log.Println("Database connection established")
}
```

## 5. Utilities (Password & JWT)

### 5.1 Password Hashing
Create `utils/password.go`.
```go
package utils

import (
	"golang.org/x/crypto/bcrypt"
)

func HashPassword(password string) (string, error) {
	bytes, err := bcrypt.GenerateFromPassword([]byte(password), 14)
	return string(bytes), err
}

func CheckPasswordHash(password, hash string) bool {
	err := bcrypt.CompareHashAndPassword([]byte(hash), []byte(password))
	return err == nil
}
```

### 5.2 JWT Utilities
Create `utils/jwt.go`.
```go
package utils

import (
	"time"
	"laundry-backend/config"
	"github.com/golang-jwt/jwt/v5"
)

func GenerateToken(userID uint, role string) (string, error) {
	claims := jwt.MapClaims{
		"user_id": userID,
		"role":    role,
		"exp":     time.Now().Add(time.Hour * 72).Unix(),
	}

	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	return token.SignedString([]byte(config.Get("JWT_SECRET")))
}
```

## 6. Models (User, Service, Transaction)
Create models with GORM tags.

### 6.1 User Model
Create `models/user.go`.
```go
package models

import (
	"time"
	"gorm.io/gorm"
)

type User struct {
	ID        uint           `gorm:"primaryKey" json:"id"`
	Name      string         `json:"name"`
	Email     string         `gorm:"unique" json:"email"`
	Password  string         `gorm:"column:password_hash" json:"-"`
	Role      string         `json:"role"` // admin, customer
	CreatedAt time.Time      `json:"created_at"`
	UpdatedAt time.Time      `json:"updated_at"`
	DeletedAt gorm.DeletedAt `gorm:"index" json:"-"`
}
```

### 6.2 Service Model
Create `models/service.go`.
```go
package models

import (
	"time"
	"gorm.io/gorm"
)

type Service struct {
	ID          uint           `gorm:"primaryKey" json:"id"`
	Name        string         `json:"name"`
	Description string         `json:"description"`
	Unit        string         `json:"unit"` // kg, pcs
	Price       float64        `json:"price"`
	CreatedAt   time.Time      `json:"created_at"`
	UpdatedAt   time.Time      `json:"updated_at"`
	DeletedAt   gorm.DeletedAt `gorm:"index" json:"-"`
}
```

### 6.3 Transaction Model
Create `models/transaction.go`.
```go
package models

import (
	"time"
	"gorm.io/gorm"
)

type Transaction struct {
	ID        uint           `gorm:"primaryKey" json:"id"`
	UserID    uint           `json:"user_id"`
	User      User           `gorm:"foreignKey:UserID" json:"user"`
	ServiceID uint           `json:"service_id"`
	Service   Service        `gorm:"foreignKey:ServiceID" json:"service"`
	Quantity  float64        `json:"quantity"`
	Total     float64        `json:"total"`
	Status    string         `json:"status"` // pending, process, done
	CreatedAt time.Time      `json:"created_at"`
	UpdatedAt time.Time      `json:"updated_at"`
	DeletedAt gorm.DeletedAt `gorm:"index" json:"-"`
}
```

## 7. Middleware
Create `middleware/auth.go`.
```go
package middleware

import (
	"laundry-backend/config"
	"github.com/gofiber/fiber/v2"
	jwtware "github.com/gofiber/contrib/jwt"
)

func Protected() fiber.Handler {
	return jwtware.New(jwtware.Config{
		SigningKey:   jwtware.SigningKey{Key: []byte(config.Get("JWT_SECRET"))},
		ErrorHandler: jwtError,
		SuccessHandler: func(c *fiber.Ctx) error {
			user := c.Locals("user").(*jwt.Token)
			claims := user.Claims.(jwt.MapClaims)
			c.Locals("user_id", uint(claims["user_id"].(float64)))
			c.Locals("role", claims["role"].(string))
			return c.Next()
		},
	})
}

func jwtError(c *fiber.Ctx, err error) error {
	if err.Error() == "Missing or malformed JWT" {
		return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"status": "error", "message": "Missing or malformed JWT", "data": nil})
	}
	return c.Status(fiber.StatusUnauthorized).JSON(fiber.Map{"status": "error", "message": "Invalid or expired JWT", "data": nil})
}
```

## 8. Handlers (Basic Setup)
Create `handlers/auth.go` just as an example.
```go
package handlers

import (
	"laundry-backend/database"
	"laundry-backend/models"
	"laundry-backend/utils"
	"github.com/gofiber/fiber/v2"
)

func Login(c *fiber.Ctx) error {
	type LoginInput struct {
		Email    string `json:"email"`
		Password string `json:"password"`
	}
	var input LoginInput
	if err := c.BodyParser(&input); err != nil {
		return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"status": "error", "message": "Error on login request", "data": err})
	}

	var user models.User
	if err := database.DB.Where("email = ?", input.Email).First(&user).Error; err != nil {
		return c.Status(fiber.StatusUnauthorized).JSON(fiber.Map{"status": "error", "message": "User not found", "data": nil})
	}

	if !utils.CheckPasswordHash(input.Password, user.Password) {
		return c.Status(fiber.StatusUnauthorized).JSON(fiber.Map{"status": "error", "message": "Invalid password", "data": nil})
	}

	token, err := utils.GenerateToken(user.ID, user.Role)
	if err != nil {
		return c.Status(fiber.StatusInternalServerError).JSON(fiber.Map{"status": "error", "message": "Could not login", "data": nil})
	}

	return c.JSON(fiber.Map{"status": "success", "message": "Success login", "data": token})
}

func ChangePassword(c *fiber.Ctx) error {
	type ChangePasswordInput struct {
		OldPassword string `json:"old_password"`
		NewPassword string `json:"new_password"`
	}
	var input ChangePasswordInput
	if err := c.BodyParser(&input); err != nil {
		return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"status": "error", "message": "Invalid input", "data": nil})
	}

	userID := c.Locals("user_id").(uint)

	var user models.User
	if err := database.DB.First(&user, userID).Error; err != nil {
		return c.Status(fiber.StatusNotFound).JSON(fiber.Map{"status": "error", "message": "User not found", "data": nil})
	}

	if !utils.CheckPasswordHash(input.OldPassword, user.Password) {
		return c.Status(fiber.StatusUnauthorized).JSON(fiber.Map{"status": "error", "message": "Old password incorrect", "data": nil})
	}

	hashedPassword, _ := utils.HashPassword(input.NewPassword)
	user.Password = hashedPassword
	database.DB.Save(&user)

	return c.JSON(fiber.Map{"status": "success", "message": "Password updated successfully", "data": nil})
}
```

## 9. Routes
Create `routes/routes.go`.
```go
package routes

import (
	"laundry-backend/handlers"
	"laundry-backend/middleware"
	"github.com/gofiber/fiber/v2"
)

func SetupRoutes(app *fiber.App) {
	api := app.Group("/api")
	
	// Auth
	auth := api.Group("/auth")
	auth.Post("/login", handlers.Login)
	auth.Post("/change-password", middleware.Protected(), handlers.ChangePassword)

	// Services (Protected)
	// service := api.Group("/services", middleware.Protected())
	// service.Get("/", handlers.GetAllServices)
}
```

## 10. Database Seeder
Create `database/seeder.go` to handle initial data population.
```go
package database

import (
	"laundry-backend/models"
	"laundry-backend/utils"
	"log"
)

func Seed() {
	// Seed Admin
	var userCount int64
	DB.Model(&models.User{}).Count(&userCount)
	if userCount == 0 {
		hash, _ := utils.HashPassword("admin123")
		DB.Create(&models.User{
			Name:     "Admin",
			Email:    "admin@laundry.com",
			Password: hash,
			Role:     "admin",
		})
		log.Println("Seeded Admin User")
	}

	// Seed Services
	var serviceCount int64
	DB.Model(&models.Service{}).Count(&serviceCount)
	if serviceCount == 0 {
		services := []models.Service{
			{Name: "Cuci Komplit", Description: "Cuci setrika rapi", Unit: "kg", Price: 7000},
			{Name: "Cuci Kering", Description: "Hanya cuci dan kering", Unit: "kg", Price: 5000},
			{Name: "Setrika Saja", Description: "Hanya jasa setrika", Unit: "kg", Price: 4000},
			{Name: "Cuci Bedcover", Description: "Cuci bedcover ukuran besar", Unit: "pcs", Price: 25000},
		}
		DB.Create(&services)
		log.Println("Seeded Default Services")
	}
}
```

## 11. Main Entry & Migration
Create `cmd/main.go`.
```go
package main

import (
	"laundry-backend/config"
	"laundry-backend/database"
	"laundry-backend/models"
	"laundry-backend/routes"

	"github.com/gofiber/fiber/v2"
	"github.com/gofiber/fiber/v2/middleware/cors"
	"github.com/gofiber/fiber/v2/middleware/helmet"
	"github.com/gofiber/fiber/v2/middleware/limiter"
	"github.com/gofiber/fiber/v2/middleware/logger"
	"github.com/gofiber/fiber/v2/middleware/recover"
	"log"
	"time"
)

func main() {
	// 1. Connect DB
	database.Connect()

	// 2. Auto Migrate
	log.Println("Running Migrations...")
	database.DB.AutoMigrate(&models.User{}, &models.Service{}, &models.Transaction{})
	
	// 3. Seed Data
	database.Seed()

	// 4. Init Fiber
	app := fiber.New()
	
	// 5. Middleware
	// Logger
	app.Use(logger.New(logger.Config{
		Format:     "[${time}] ${status} - ${method} ${path}\n",
		TimeFormat: "2006-01-02 15:04:05",
		TimeZone:   "Local",
	}))

	// Security Headers
	app.Use(helmet.New())

	// Panic Recovery
	app.Use(recover.New())

	// CORS configuration
	allowedOrigins := config.Get("ALLOWED_ORIGINS")
	if allowedOrigins == "" {
		allowedOrigins = "http://localhost:5173" // Default for development
	}

	app.Use(cors.New(cors.Config{
		AllowOrigins:     allowedOrigins,
		AllowHeaders:     "Origin, Content-Type, Accept, Authorization",
		AllowMethods:     "GET, POST, PUT, DELETE, OPTIONS",
		AllowCredentials: true,
	}))

	// Rate Limiting
	app.Use(limiter.New(limiter.Config{
		Max:        100,
		Expiration: 60 * time.Second,
		KeyGenerator: func(c *fiber.Ctx) string {
			return c.IP()
		},
		LimitReached: func(c *fiber.Ctx) error {
			return c.Status(fiber.StatusTooManyRequests).JSON(fiber.Map{
				"status":  "error",
				"message": "Too many requests, please try again later.",
			})
		},
	}))

	// Health Check Endpoint
	app.Get("/health", func(c *fiber.Ctx) error {
		return c.JSON(fiber.Map{
			"status":  "success",
			"message": "Server is up and running",
			"timestamp": time.Now(),
		})
	})

	// 6. Routes
	routes.SetupRoutes(app)

	// 7. Listen
	port := config.Get("SERVER_PORT")
	if port == "" {
		port = "8080"
	}
	log.Fatal(app.Listen(":" + port))
}
```

## 12. Run Application
// turbo
Run the application (which runs migrations on start).
```bash
cd backend
go run cmd/main.go
```