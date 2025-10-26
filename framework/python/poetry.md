# Python: Poetry (Dependency Management)

Effective dependency management is key to a reproducible and stable application. `Poetry` is the mandated tool for this.

## 1. Core Principles

1.  **Always Use Poetry CLI:** All package management **must** be done via the `poetry` CLI. Never edit the `[tool.poetry.dependencies]` section in `pyproject.toml` manually.
2.  **Commit Lock File:** The `poetry.lock` file **must** be committed to version control after any dependency change (`poetry add`, `poetry remove`, `poetry update`). This ensures that every developer and CI/CD environment uses the exact same versions of all packages.
3.  **Use `poetry install`:** To install dependencies from a `poetry.lock` file, always run `poetry install`. This is the standard way to synchronize your environment with the project's dependencies.

## 2. Adding & Managing Dependencies

### 2.1. Dependency Groups
Poetry allows separating dependencies into groups. We use the following standard groups:
- **`main` (default):** For runtime dependencies required for the application to run in production.
  ```bash
  poetry add <package_name>
  ```
- **`dev`:** For development tools like linters, formatters, and test runners. These are not installed in production environments.
  ```bash
  poetry add <package_name> --group dev
  ```
- **Custom Groups (e.g., `docs`):** For optional dependencies, like tools for building documentation.
  ```bash
  poetry add sphinx --group docs
  ```

### 2.2. Versioning Constraints
Correct versioning prevents unexpected breaking changes.
- **Libraries:** For libraries your project produces, use caret constraints (`^`) to allow compatible patch and minor updates. This is the default.
  ```toml
  # Allows versions >= 1.2.3 and < 2.0.0
  requests = "^1.2.3"
  ```
- **Applications:** For final applications, pin the exact versions (`==`) or use tilde constraints (`~`) for more control and stability. To add a package with a specific version:
  ```bash
  # Pin exact version
  poetry add "requests==2.28.1"

  # Allow only patch updates (>=2.28.1, <2.29.0)
  poetry add "requests~=2.28.1"
  ```

## 3. Running Commands & Scripts

- **Execute Commands:** To run a command within the Poetry-managed virtual environment, use `poetry run`.
  ```bash
  poetry run pytest
  poetry run python my_script.py
  ```
- **Activate Shell:** To activate the virtual environment in your current shell, use `poetry shell`.

## 4. Keeping Dependencies Updated

- **Check for Updates:** To see which packages have newer versions available, run:
  ```bash
  poetry show --latest
  ```
- **Update Packages:** To update your packages to the latest versions allowed by `pyproject.toml` and regenerate `poetry.lock`, run:
  ```bash
  poetry update
  ```
  Be cautious with `poetry update` in applications. It's better to update specific packages intentionally:
  ```bash
  poetry update <package_name>
  ```
