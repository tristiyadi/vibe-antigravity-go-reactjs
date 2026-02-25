---
trigger: always_on
---

# Documentation Rules & Standards

This document defines the requirements for maintaining high-quality documentation across the Laundry project.

## 1. File Documentation
- **Every major directory** must have a `README.md`.
- **Root README** must provide an overview of the entire monorepo and setup instructions.
- **Backend/Frontend READMEs** must contain specific installation, running, and architecture details for their respective stacks.

## 2. Code Documentation
- **Functions**: Use descriptive names. For complex logic, add a comment above the function explaining "What" and "Why", not "How" (the code should show how).
- **Go (Backend)**: Use standard Go comment conventions for exported types and functions.
- **React (Frontend)**: Use PropTypes or TypeScript interfaces to document component props.

## 3. Markdown Standards
- Use **GitHub Flavored Markdown (GFM)**.
- Use **Heading Hierarchy** (H1 for title, H2 for sections, H3 for subsections).
- Include **Code Blocks** with language syntax highlighting.
- Use **Tables** for configuration variables or API endpoint listings.

## 4. Maintenance
- Documentation must be updated whenever a new feature, environment variable, or dependency is added.
- Obsolete documentation should be removed or marked as deprecated.

- Detailed API documentation must be maintained in `docs/API.md`.
- Every API endpoint should be documented with:
  - Method (GET, POST, etc.)
  - Path
  - Required Header (e.g., Auth)
  - Request Body (if any)
  - Success Response
  - Error Responses
