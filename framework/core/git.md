# Core: Git & Version Control

This document outlines the rules for version control. A clean, understandable Git history is crucial for project maintainability.

## 1. Commits

### 1.1. Conventional Commits
All commit messages **must** follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) standard. This is essential for automated versioning and changelog generation.

**Format:** `type(scope): subject`
- `type`: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `ci`, `perf`.
- `scope` (optional): The part of the codebase affected (e.g., `api`, `db`, `ui`).
- `subject`: A short, imperative-mood description of the change.

**Examples:**
- Simple commit:
  ```
  fix: correct typo in user validation
  ```
- Commit with scope:
  ```
  feat(api): add endpoint for user profile lookup
  ```
- Commit with multi-line body and breaking change footer:
  ```
  feat(auth): switch to JWT for authentication

  The old session-based authentication is deprecated. This commit introduces
  a new JWT-based flow, including token generation and validation middleware.

  BREAKING CHANGE: The `/auth/session` endpoint is removed.
  Clients must now use the `/auth/token` endpoint and handle JWTs.
  ```

### 1.2. Atomic Commits
Commits must be small, logical, and atomic. One commit should represent one single, complete logical change. Do not mix unrelated changes (e.g., a feature and a refactor) in one commit.

## 2. Branching Strategy

A consistent branching strategy is mandatory. We use a simplified GitFlow model.

- `main`: Production-ready, stable code. Direct commits are forbidden. Merges happen only from `develop`.
- `develop`: The main development branch. All feature branches are created from `develop` and merged back into it.
- `feature/<task-id-or-name>`: For developing new features. Example: `feature/PROJ-123-user-login`.
- `fix/<task-id-or-name>`: For bug fixes. Example: `fix/PROJ-456-password-reset-bug`.
- `chore/<task-name>`: For tasks that don't add features or fix bugs. Example: `chore/update-dependencies`.

**Workflow:**
1. Create a new branch from the latest `develop`.
2. Do your work, making atomic commits.
3. Push your branch to the remote repository.
4. Create a Pull Request (PR) to merge your branch into `develop`.

## 3. Pull Requests (PRs)

All code must enter the `develop` branch through a Pull Request.

### 3.1. PR Requirements
- **Clear Title:** The title should be descriptive and follow the Conventional Commits format (e.g., `feat(api): Add user profile endpoint`).
- **Detailed Description:** Explain *what* the PR does and *why*. If it resolves an issue, link to it. Use a PR template if one is provided.
- **Passing Checks:** All automated checks (CI builds, tests, linting) must pass.
- **Code Review:** At least one other developer must review and approve the PR. You are responsible for addressing all comments before merging.

### 3.2. Merging
- Use "Squash and Merge" to combine all commits from the feature branch into a single, clean commit on the `develop` branch. This keeps the main branch history linear and easy to read.
- The squashed commit message should be a well-formed Conventional Commit message.

## 4. General Rules

- **Pre-commit:** The `pre-commit` framework **must** be used. You cannot bypass it (`--no-verify`). If hooks fail, you must fix the code before committing.
- **.gitignore:** Ensure secrets (`.env`), virtual environments (`.venv/`), IDE files (`.idea/`, `.vscode/`), and system files (`__pycache__`, `.DS_Store`) are in `.gitignore`.
