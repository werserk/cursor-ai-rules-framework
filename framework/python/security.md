# Python: Security Best Practices

Writing secure code is a fundamental responsibility. This document outlines the baseline security practices that **must** be followed in all Python projects.

## 1. Secrets Management

**Rule:** Never, under any circumstances, hardcode secrets in the source code. This includes API keys, database passwords, tokens, private certificates, or any other sensitive credentials.

- **Use Environment Variables:** Secrets must be loaded from environment variables.
- **Use `.env` files for Local Development:** For local development, store environment variables in a `.env` file.
- **`.gitignore` is Mandatory:** The `.env` file **must** be listed in `.gitignore` to prevent it from ever being committed.
- **Tooling:** Use a library like `python-dotenv` to load these variables automatically during development.

**Example:**
```python
# In your code (e.g., settings.py)
import os
from dotenv import load_dotenv

# Load variables from .env file
load_dotenv()

# Access the secret securely
DATABASE_PASSWORD = os.getenv("DATABASE_PASSWORD")

if not DATABASE_PASSWORD:
    raise ValueError("DATABASE_PASSWORD environment variable not set.")

```
**In `.env` file (DO NOT COMMIT):**
```
DATABASE_PASSWORD="your-super-secret-password"
```

## 2. Input Validation

**Rule:** Never trust data coming from external sources. Always validate and sanitize input from users, APIs, and other services.

- **Use a Validation Library:** Employ a robust data validation library like `Pydantic` to define strict schemas for all incoming data. This protects against injection attacks, unexpected data types, and malformed payloads.

**Example with `Pydantic`:**
```python
from pydantic import BaseModel, EmailStr, Field

class UserRegistration(BaseModel):
    email: EmailStr  # Pydantic automatically validates this is a valid email format.
    password: str = Field(min_length=8)
    age: int = Field(gt=0, le=120) # Must be a positive integer <= 120.

def register_user(payload: dict):
    try:
        # Pydantic will raise a `ValidationError` if the payload is invalid.
        user_data = UserRegistration(**payload)

        # ... proceed with validated data ...
        print(f"Registering user with email: {user_data.email}")

    except ValidationError as e:
        # Handle the invalid data error
        logger.error(f"Invalid registration payload: {e}")
```

## 3. Dependency Security

**Rule:** Keep dependencies up-to-date and scan them for known vulnerabilities.

- **Regularly Check for Updates:** Use `poetry show --latest` to check for outdated packages. Plan regular updates.
- **Automated Scanning:** Use automated tools to scan for vulnerabilities in your dependency tree.
  - **GitHub:** Enable Dependabot or Snyk integration.
  - **Local/CI:** Use tools like `safety` (`poetry run safety check`) to scan your installed packages against a vulnerability database.

## 4. Static Application Security Testing (SAST)

**Rule:** Integrate static analysis tools specifically designed to find common security issues in your code.

- **Use `bandit`:** `bandit` is a tool that analyzes Python code to find common security vulnerabilities. It should be part of the development dependencies and run regularly in CI.

**Installation:**
```bash
poetry add bandit --group dev
```

**Usage:**
```bash
poetry run bandit -r .
```
This command will scan your entire project for issues like hardcoded passwords, unsafe deserialization, and other common security risks.
