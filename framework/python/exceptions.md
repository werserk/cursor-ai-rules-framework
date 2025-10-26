# Python: Exception Handling

Robust error handling is critical for building reliable and maintainable applications. This guide outlines the best practices for working with exceptions in Python.

## 1. Core Principles

1.  **Never Suppress Exceptions Silently:** Avoid empty `except` blocks or catching an exception only to log it without re-raising or handling it properly. A silent failure is one of the hardest problems to debug.
    ```python
    # Bad 👎
    try:
        process_payment()
    except Exception:
        pass # The application continues as if nothing happened!
    ```

2.  **Be Specific in What You Catch:** Catching broad exceptions like `Exception` or `BaseException` can hide bugs and prevent proper error handling. Always catch the most specific exceptions you anticipate.
    ```python
    # Bad 👎
    try:
        user = api.get_user(user_id)
    except Exception as e:
        # What was the error? A network timeout? 404 Not Found?
        # We don't know, so we can't handle it properly.
        logger.error(f"An unknown error occurred: {e}")

    # Good 👍
    try:
        user = api.get_user(user_id)
    except ApiTimeoutError:
        logger.warning("API timed out, will retry later.")
        # Handle timeout...
    except UserNotFoundError:
        logger.info(f"User {user_id} not found.")
        # Handle not found case...
    ```

3.  **Use `finally` for Cleanup:** For cleanup actions that must run regardless of whether an exception occurred (e.g., closing a file or a database connection), use a `finally` block. Context managers (`with` statement) are often a more Pythonic way to achieve this.

## 2. Custom Exceptions

Create a hierarchy of custom exceptions specific to your application's domain. This makes error handling logic much cleaner and more expressive.

1.  **Create a Base Exception:** Define a single base exception for your application. All other custom exceptions should inherit from this.
2.  **Create Specific Exceptions:** For different error scenarios, create specific exceptions that inherit from your base exception.

### Example Hierarchy
```python
# in my_app/exceptions.py

class MyAppError(Exception):
    """Base exception for all errors in this application."""
    pass

class DatabaseError(MyAppError):
    """Raised for database-related errors."""
    pass

class ApiError(MyAppError):
    """Raised for external API-related errors."""
    pass

class AuthenticationError(MyAppError):
    """Raised for authentication failures."""
    pass
```

## 3. Raising and Chaining Exceptions

1.  **Provide Clear Error Messages:** When raising an exception, provide a message that is clear and helpful for debugging.
2.  **Use `raise from` to Preserve Context:** When you catch an exception and raise a new, more specific one, use `raise ... from ...`. This chains the exceptions, preserving the original traceback, which is invaluable for debugging.

### Example of Chaining
```python
# in my_app/services.py
from my_app.exceptions import DatabaseError
import third_party_db_library

def get_user_by_id(user_id: int) -> dict:
    """
    Retrieves a user, wrapping low-level DB errors in our custom exception.
    """
    try:
        # Imagine this function can raise a cryptic `ConnectionFailure`
        user = third_party_db_library.fetch_user(user_id)
        return user
    except third_party_db_library.ConnectionFailure as e:
        # Bad 👎: The original error context is lost.
        # raise DatabaseError("Failed to fetch user.")

        # Good 👍: The original traceback is preserved.
        raise DatabaseError(f"Failed to fetch user {user_id}") from e
```
This will result in a much more informative error message in the logs:
```
third_party_db_library.ConnectionFailure: Timed out while connecting to host

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  ...
my_app.exceptions.DatabaseError: Failed to fetch user 123
```
