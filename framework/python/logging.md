# Python: Logging

Consistent and structured logging is essential for observability, debugging, and monitoring. These rules provide a standard approach to logging within any Python application.

## 1. Core Principles

1.  **Use the Standard `logging` Module:** Always use Python's built-in `logging` module. Do not use `print()` statements for application events, as they lack context, severity levels, and are hard to control in a production environment.
2.  **Log to `stdout`/`stderr`:** Applications should log to standard output (`stdout`) and standard error (`stderr`). The responsibility of routing, formatting, and storing logs belongs to the execution environment (e.g., Docker, Kubernetes, systemd), not the application itself.
3.  **NEVER Log Sensitive Data:** Under no circumstances should secrets, passwords, API keys, or personally identifiable information (PII) be written to logs.

## 2. Logging Levels

Use the appropriate log level for each message. This allows for filtering logs based on severity in different environments (e.g., INFO in production, DEBUG in development).

- `DEBUG`: Detailed, diagnostic information, typically of interest only when diagnosing problems. *Example: "Sending request to API endpoint X with headers Y."*
- `INFO`: Confirmation that things are working as expected. Used to track the normal flow of the application. *Example: "User alice@example.com logged in successfully."*
- `WARNING`: An indication that something unexpected happened, or a potential problem in the near future (e.g., 'disk space low'). The software is still working as expected. *Example: "API request timed out, retrying..."*
- `ERROR`: Due to a more serious problem, the software has not been able to perform some function. *Example: "Failed to connect to the database after 3 retries."*
- `CRITICAL`: A very serious error, indicating that the program itself may be unable to continue running. *Example: "Application startup failed: configuration file not found."*

## 3. Structured Logging

To make logs machine-readable and easier to query in log management systems (like ELK, Splunk, or Datadog), logs **must** be structured, preferably in JSON format.

**Bad 👎 (Unstructured):**
```
WARNING: User login failed for username: alice. Reason: Invalid password.
```

**Good 👍 (Structured):**
```json
{
    "timestamp": "2023-10-27T10:00:00Z",
    "level": "WARNING",
    "message": "User login failed",
    "logger": "my_app.auth.service",
    "extra": {
        "username": "alice",
        "reason": "invalid_password",
        "client_ip": "192.168.1.100"
    }
}
```

## 4. Configuration and Usage Example

Get a logger instance at the top of each module, named after the module itself. This provides context for where the log message originated.

```python
import logging
import sys
# In a real app, you would use a library like python-json-logger
# or configure a JSONFormatter. This is a simplified example.
import json

# --- Configuration (should be done once at application startup) ---
# In a real application, this would be more robust, likely from a config file.
handler = logging.StreamHandler(sys.stdout)
formatter = logging.Formatter(
    # Simple JSON-like format for demonstration
    '{"timestamp": "%(asctime)s", "level": "%(levelname)s", "logger": "%(name)s", "message": "%(message)s"}'
)
handler.setFormatter(formatter)

# Get the root logger and configure it
root_logger = logging.getLogger()
root_logger.setLevel(logging.INFO) # Set the minimum level to process
root_logger.addHandler(handler)
# --- End of Configuration ---


# --- Usage (in any module, e.g., my_app/services.py) ---
logger = logging.getLogger(__name__) # Creates a logger named 'my_app.services'

class UserService:
    def login(self, username: str, password: str) -> bool:
        logger.info(f"Attempting login for user: {username}")

        # Faking a password check
        if password != "correct-password":
            # Use the `extra` kwarg to add structured context
            logger.warning(
                "User login failed: Invalid password",
                extra={"username": username, "reason": "invalid_password"}
            )
            return False

        logger.info(f"User {username} logged in successfully")
        return True

```
*Note: For a real application, use a library like `structlog` or `python-json-logger` to handle structured logging robustly.*
