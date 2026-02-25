---
name: Git Manager
description: A comprehensive skill for managing Git repositories, specifically designed to automate the creation of .gitignore files and streamline common git commands.
---

# Git Manager Skill

This skill extends the agent's capabilities to manage Git repositories effectively. It focuses on identifying project types to generate appropriate `.gitignore` files and handling standard git operations.

## 1. Automated .gitignore Creation
The agent should automatically detect the project type and create a `.gitignore` file with standard exclusions.

### Detection Logic
- **Node.js**: Checks for `package.json`, `node_modules`
  - *Excludes*: `node_modules/`, `dist/`, `.env`, `.DS_Store`, `coverage/`, `.npm/`
- **Python**: Checks for `requirements.txt`, `Pipfile`, `pyproject.toml`, `*.py`
  - *Excludes*: `__pycache__/`, `*.pyc`, `.venv/`, `venv/`, `.env`, `.DS_Store`, `.pytest_cache/`
- **Go**: Checks for `go.mod`, `*.go`
  - *Excludes*: `bin/`, `*.exe`, `*.test`, `.env`, `.DS_Store`, `vendor/`
- **Java/Maven/Gradle**: Checks for `pom.xml`, `build.gradle`
  - *Excludes*: `target/`, `build/`, `.gradle/`, `.idea/`, `*.class`, `.env`, `.DS_Store`
- **General Web**: Checks for `index.html`, `css/`
  - *Excludes*: `.DS_Store`, `.env`, `node_modules/` (if using npm)

### Action
1.  **Check existing**: If `.gitignore` existing, append missing critical entries (like `.env`) rather than overwriting, or ask user.
2.  **Create new**: If not exists, create with the appropriate template.

## 2. Git Commands Interface
The agent can execute or suggest the following git commands based on user needs.

### Initialization & Configuration
- `git init`: Initialize a new repository.
- `git config user.name "Your Name"` & `git config user.email "you@example.com"`: Configure identity if missing.

### Staging & Committing
- `git status`: Check the status of files.
- `git add .`: Stage all changes (use with caution, confirm with user if unsure).
- `git commit -m "Your message"`: Commit changes. **Always** use a descriptive message following conventional commit format (e.g., `feat:`, `fix:`, `chore:`).

### Branching & Merging
- `git branch`: List branches.
- `git checkout -b <branch-name>`: Create and switch to a new branch.
- `git merge <branch-name>`: Merge a branch into the current one.

### Remote Interaction
- `git remote add origin <url>`: Add a remote repository.
- `git push -u origin <branch>`: Push changes to remote.
- `git pull`: Pull changes from remote.

## 3. Best Practices
- **Never commit secrets**: Always ensure `.env` and other secret files are in `.gitignore` BEFORE adding files.
- **Atomic Commits**: Encourage small, focused commits rather than one massive commit at the end.
- **Descriptive Messages**: Use clear messages describing *what* and *why* changes were made.

## 4. Handling Exposed Secrets
If a sensitive file (like `.env`) is accidentally committed:
1.  **Untrack immediately**: Run `git rm --cached <file>`.
2.  **Add to `.gitignore`**: Ensure the file pattern is in `.gitignore`.
3.  **Commit the removal**: `git commit -m "chore: remove exposed secrets from tracking"`.
4.  **Rotate Secrets**: Advise the user to immediately change any passwords or keys that were exposed.
5.  **History Erasure (Optional)**: If the repo is public and history must be cleaned, suggest using `git filter-repo` or BFG Repo-Cleaner.

## Example Workflow for "Setup Git"
1.  **Scan Loop**: Check project root for language indicators.
2.  **Generate .gitignore**: Create the file based on findings.
3.  **Init**: Run `git init`.
4.  **Initial Commit**:
    - Run `git add .`
    - Run `git commit -m "chore: initial commit"`
    - notify User.
