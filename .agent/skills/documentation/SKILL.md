---
name: Documentation Wizard
description: Skill for generating and maintaining high-quality READMEs, API docs, and architecture overviews.
---

# Documentation Skill

This skill allows the agent to create and maintain comprehensive documentation that follows the project's standards.

## 1. README Generation
When creating a `README.md`, follow this template:
- **Title**: Project Names.
- **Description**: Short, clear explanation of purpose.
- **Tech Stack**: List of main technologies.
- **Prerequisites**: What needs to be installed.
- **Setup & Installation**: Step-by-step guide.
- **Project Structure**: High-level overview of directories.
- **Key Features**: List of what the app does.

## 2. API Documentation
Use a standardized table format for API endpoints:

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/auth/login` | User login | No |

## 3. Architecture Overviews
Describe the flow of data:
1.  **Request**: User interacts with Frontend.
2.  **Logic**: Frontend calls Backend API.
3.  **Validation**: Backend validates JWT and body.
4.  **Database**: GORM interacts with PostgreSQL.
5.  **Response**: Consistent JSON returned.

## 4. Maintenance Logic
- Whenever completing a `feat:`, check if a new endpoint was added.
- If yes, update the relevant `README.md` or `docs/API.md`.
- Ensure all screenshots or diagrams are up to date (if applicable).
