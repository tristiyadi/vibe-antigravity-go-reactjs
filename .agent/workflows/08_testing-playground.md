---
description: Test the complete User Management lifecycle (CRUD, Archive, Restore, Export) in the Admin Dashboard using Browser Control.
---

# 08 Testing Playground (User Management)

This workflow provides a comprehensive test suite for the User Management module, including CRUD operations, status management, and table utilities like filtering, column visibility, and exports.

## 1. Environment Setup
// turbo
Ensure both backend and frontend are running. If not, start them in the background.
```bash
# Backend
cd backend && go run cmd/main.go &
# Frontend
cd frontend && npm run dev &
```

## 2. Browser Testing Flow
Use the **Browser Tool** to execute the following steps sequentially.

### 2.1 Authentication & Navigation
1.  **Open Browser**: Navigate to `http://localhost:5173/admin/login`.
2.  **Login**: 
    -   **Option A**: Click **Use Demo Account** to auto-fill.
    -   **Option B**: Enter Email: `admin@laundry.com` & Password: `admin123`.
    -   Click **Login**.
3.  **Navigate**: Click on the **Users** tab or navigate directly to `http://localhost:5173/admin/users`.

### 2.2 User CRUD Operations
4.  **Add User**:
    -   Click **Add User** button.
    -   Fill Name: `Testing User`
    -   Fill Email: `test@playground.com`
    -   Select Role: `customer`
    -   Click **Save Changes**.
5.  **Show User**:
    -   Find "Testing User" in the table.
    -   Click **Actions** (MoreHorizontal icon) -> **Show**.
    -   Verify details in the modal and click **Close**.
6.  **Edit User**:
    -   Click **Actions** -> **Edit**.
    -   Change Name to `Testing User Edited`.
    -   Click **Save Changes**.
7.  **Archive User**:
    -   Click **Actions** -> **Archive**.
    -   Handle the browser confirmation dialog (OK).
    -   Verify the badge changes to **Archived**.
8.  **Restore User**:
    -   Click **Actions** -> **Restore**.
    -   Verify the badge changes back to **Active**.
9.  **Delete User**:
    -   Click **Actions** -> **Delete**.
    -   Handle the browser confirmation dialog (OK).
    -   Verify the user is removed from the table.

### 2.3 Table Utilities
10. **Filter Table**:
    -   Locate the search input (Filter name...).
    -   Type "Admin" to filter only admin accounts.
    -   Clear the input to show all users again.
11. **Hide/Show Columns**:
    -   Click the **Columns** button.
    -   Uncheck **Email** to hide the email column.
    -   Check **Email** to show it again.
12. **Export Selection**:
    -   Click the **Export** button.
    -   Trigger **CSV** export.
    -   Trigger **Excel (.xlsx)** export.
    -   Trigger **PDF** export.

## 3. Verification Checklist
- [ ] User added successfully (Toast/Message shown).
- [ ] Edit reflects immediately in the table.
- [ ] Archive/Restore toggles the user state correctly.
- [ ] Delete permanently removes the record.
- [ ] Export files are triggered (check browser downloads if possible).
