---
description: Manage administrative users with a comprehensive data table featuring pagination, filtering, sorting, and export capabilities.
---

# Admin User Management Workflow

This workflow guides you through implementing and managing the User Management module in the Laundry Admin Dashboard.

## 1. Backend Preparation
Ensure the backend has the necessary endpoints for user CRUD and bulk operations.
- [x] Implement `GET /api/users` with support for:
    - Pagination (`page`, `limit`)
    - Sorting (`sort_by`, `order`)
    - Filtering (`search`, `role`)
    - Status filter (`active`, `archived`, `all`)
- [x] Implement `POST /api/users` for creation.
- [x] Implement `GET /api/users/:id` for details.
- [x] Implement `PUT /api/users/:id` for updates.
- [x] Implement `DELETE /api/users/:id` (Soft delete/archive).
- [x] Implement `POST /api/users/:id/restore` to restore archived users.
- [x] Implement `DELETE /api/users/:id/permanent` for permanent deletion.
- [x] **Technical Note**: Use `gorm.DeletedAt` for soft-delete and `Unscoped()` to query archived items.

## 2. Frontend Infrastructure
- [x] Install required dependencies: `@tanstack/react-table`, `lucide-react`, `xlsx`, `jspdf`, `jspdf-autotable`.
- [x] Setup Shadcn UI components and generic `DataTable` component.

## 3. Dashboard Features
- [x] **Statistics Cards**: Add cards at the top showing key metrics (Total, Active, Archived).
- [x] **Data Table**: TanStack Table with pagination, sorting, and visibility toggle.
- [x] **Export Features**: Support for CSV, XLSX, and PDF (using `autoTable`).
- [x] **User Actions**: Show, Edit, Archive, Restore, and Permanent Delete.

## 4. Implementation Checklist
- [x] Create `UserPage.tsx` and `LayananPage.tsx`.
- [x] Create `user.service.ts` and `service.service.ts`.
- [x] Update `AdminLayout.tsx` for navigation.
- [x] **Fix**: Update `isArchived` helper to handle GORM object structure:
    ```typescript
    const isArchived = (u: any) => {
        if (!u.deleted_at) return false;
        if (typeof u.deleted_at === 'object') return u.deleted_at.Valid;
        return true;
    };
    ```

## 5. Testing & Validation
- [x] Verify Archive/Restore logic with GORM `DeletedAt`.
- [x] Test PDF generation for multi-column tables.
- [x] Check statistics accuracy against table data.
