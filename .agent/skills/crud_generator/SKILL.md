---
name: crud_generator
description: A blueprint for generating new CRUD modules (Backend & Frontend) based on the Laundry Admin pattern.
---

# CRUD Generator Blueprint

Follow this blueprint to create a new management module (e.g., Inventory, Transactions, Customers).

## 1. Backend: Data Model
Create `backend/models/[name].go`.

```go
package models

import (
	"time"
	"gorm.io/gorm"
)

type [ModelName] struct {
	ID        uint           `gorm:"primaryKey" json:"id"`
	// Add fields here
	CreatedAt time.Time      `json:"created_at"`
	UpdatedAt time.Time      `json:"updated_at"`
	DeletedAt gorm.DeletedAt `gorm:"index" json:"deleted_at"`
}
```

## 2. Backend: Handler
Create `backend/handlers/[name]_handler.go`. Implement the following pattern:
- `Get[Models]` (Filtered, Paginated, Unscoped for Archived)
- `Create[Model]`
- `Update[Model]`
- `Archive[Model]` (Soft delete)
- `Restore[Model]` (Set `deleted_at` to `gorm.DeletedAt{}`)
- `Delete[Model]` (Permanent)

## 3. Frontend: Type Definition
Create `frontend/src/types/[name].ts`.

```typescript
export interface [ModelName] {
    id: number;
    // fields
    created_at: string;
    updated_at: string;
    deleted_at: any | null;
}
```

## 4. Frontend: Service Layer
Create `frontend/src/services/[name].service.ts`. Use the Axios `api` instance.

## 5. Frontend: Page Component
Create `frontend/src/pages/admin/[ModelName]Page.tsx`.
- Copy structure from `UserPage.tsx`.
- Include `isArchived` helper.
- use `DataTable` component.
- Implement `stats` cards at the top.

## 6. Registration
1. **Backend**: Add to `database.DB.AutoMigrate` in `main.go`.
2. **Backend**: Register routes in `routes/routes.go`.
3. **Frontend**: Add route to `App.tsx`.
4. **Frontend**: Add to sidebar in `AdminLayout.tsx`.
