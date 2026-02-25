---
description: Test and debug the application using Browser Control to ensure all flows (Landing, Login, Dashboard, Logout) work correctly.
---

# 04 Testing & Debugging with Browser Control

This workflow uses the Antigravity Browser Control to verify the end-to-end functionality of Laundry Web App.

## 1. Landing Page Verification
// turbo
Verify all navigation links and sections on the landing page.
```bash
# Task for Browser Subagent:
# 1. Open http://localhost:5173
# 2. Click each menu in the Navbar (Beranda, Layanan, Harga, Tentang, Kontak)
# 3. Ensure the page scrolls to the correct section for each click.
# 4. Check for any broken images or layout issues.
```

## 2. Admin Login Flow
// turbo
Test the login process with seeded credentials or Demo button.
```bash
# Task for Browser Subagent:
# 1. Navigate to http://localhost:5173/admin/login
# 2. OPTION A: Click "Use Demo Account" button.
# 3. OPTION B: Enter email: admin@laundry.com / password: admin123
# 4. Click login button.
# 5. Verify it redirects to /admin/dashboard.
# NOTE: If login fails, verify backend is running on port 8080.
```

## 3. Dashboard Interactivity
// turbo
Test sidebar, multi-tab system, and navigation.
```bash
# Task for Browser Subagent:
# 1. On Dashboard, click sidebar menus: Users, Transactions, Services.
# 2. Verify new tabs are created correctly in the Tab Bar.
# 3. Click "View Website" in the sidebar and verify it navigates back to the landing page.
# 4. Success-click on the Profile dropdown (User Avatar).
```

## 4. Logout & Security
// turbo
Test logout and protected route access.
```bash
# Task for Browser Subagent:
# 1. Click Profile Dropdown -> Logout (Terminate Session).
# 2. Verify it redirects to /admin/login.
# 3. Try manual navigation to /admin/dashboard.
# 4. Verify it redirects back to /admin/login (Protected Route).
```

## 5. Automated Debugging
If the browser subagent reports an error (red screen, console error, or failed navigation):
1. **Analyze logs**: Check `backend/output.log` and browser console.
2. **Fix Code**: Apply necessary fixes via `replace_file_content`.
3. **Re-test**: Run the workflow step again until successful.

## 6. Production Connectivity Troubleshooting
If the application works in local but fails in production (Railway):
- **CORS Check**: Verify Backend logs for "CORS panic" (wildcard + credentials). Wildcards `*` cannot be used with `AllowCredentials: true`.
- **URL Check**: Check Browser Console Network tab. If request hits `http://localhost:8080`, then `VITE_API_URL` was not embedded correctly during build.
- **Cache Purge**: Use "Clear site data" in browser Application tab to bypass Service Worker cache.
- **Isolation**: Temporarily hardcode the Backend URL in `api.ts` to confirm if the issue is with configuration or networking.
