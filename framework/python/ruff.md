# Python: Ruff & Black (Linting, Formatting, and Static Analysis)

`Ruff` is our all-in-one tool for linting, formatting, import sorting, and static analysis. `Black` is the uncompromising code formatter, which is seamlessly integrated into Ruff. Adherence to these rules is mandatory for code consistency and quality.

## 1. Core Mandates

1.  **Ruff is Law:** All Python code **must** pass `ruff check .` without any errors. The specific rules are defined in the project's `pyproject.toml` file and must be followed.
2.  **Black is Formatting:** All Python code **must** be formatted using `ruff format .`. Manual formatting that deviates from Black's style is not permitted.
3.  **Import Sorting:** All imports **must** be automatically sorted by `ruff`. Manually ordering imports is forbidden.

## 2. Type Hints

Comprehensive type hinting is non-negotiable. Code without type hints is considered incomplete.

### 2.1. Requirement
All new functions, methods, variables, and class attributes **must** use Python type hints.

### 2.2. Best Practices & Examples
Use the most specific and descriptive types possible from the `typing` module.

- **For collections**, specify the type of their elements.
  ```python
  # Good 👍
  from typing import List, Dict, Set

  names: List[str] = ["Alice", "Bob"]
  user_ages: Dict[str, int] = {"Alice": 30, "Bob": 25}
  unique_ids: Set[int] = {1, 2, 3}

  # Bad 👎
  names: list = ["Alice", "Bob"]
  user_ages: dict = {"Alice": 30}
  ```

- **For optional values**, use `Optional` or the newer `| None` syntax.
  ```python
  # Good 👍
  from typing import Optional

  def find_user(user_id: int) -> Optional[dict]:
      # ... returns a dict or None

  # Also good (Python 3.10+) 👍
  def find_user(user_id: int) -> dict | None:
      # ...
  ```

- **For complex, reusable type definitions**, use `TypeAlias`.
  ```python
  from typing import List, Dict, TypeAlias

  # Create a custom type alias for clarity
  UserPayload: TypeAlias = Dict[str, str | int]

  def process_user(user: UserPayload) -> None:
      # ...
  ```

## 3. Docstrings

Clear documentation is as important as the code itself. Every non-trivial public function, method, and class must have a docstring.

### 3.1. Style Guide
We adhere to the **Google Python Style Guide** for docstrings.

### 3.2. Example of a Well-Documented Function

```python
from typing import List

def get_active_users(
    user_list: List[Dict[str, any]],
    min_logins: int = 10
) -> List[Dict[str, any]]:
    """Filters a list of users to find active ones.

    This function iterates through a list of user dictionaries and returns
    only those users who have a login count greater than or equal to the
    specified minimum.

    Args:
        user_list: A list of dictionaries, where each dictionary
            represents a user and must contain an 'is_active' (bool)
            and 'logins' (int) key.
        min_logins: The minimum number of logins required for a user
            to be considered active. Defaults to 10.

    Returns:
        A new list of user dictionaries containing only the active users.

    Raises:
        ValueError: If `min_logins` is negative.
    """
    if min_logins < 0:
        raise ValueError("min_logins cannot be negative.")

    active_users = []
    for user in user_list:
        if user.get('is_active') and user.get('logins', 0) >= min_logins:
            active_users.append(user)
    return active_users
```
