---
description: This workflow ensures all documentation is kept up to date after major changes.
---

# Documentation Maintenance Workflow

Follow these steps whenever a new feature or API change is implemented.

## 1. Update API Docs
If a new handler or route was added:
1. Open `docs/API.md`.
2. Add the new endpoint details (Method, Path, Request/Response).

## 2. Update READMEs
If a new core technology or environment variable was added:
1. Update `backend/README.md` or `frontend/README.md`.
2. If it's a project-wide change, update the root `README.md`.

## 3. Verify Code Comments
Run a quick check on new exported functions:
```bash
# Example for backend
grep -r "func [A-Z]" backend/handlers | grep -v "//"
```
Ensure all exported functions have a descriptive comment.

## 4. Final Review
- [ ] Are all paths in `README.md` correct?
- [ ] Do setup instructions still work?
- [ ] Is the tech stack list accurate?
