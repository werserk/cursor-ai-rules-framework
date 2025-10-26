# Python: Pytest (Testing)

Thorough testing is the foundation of reliable software. `pytest` is our standard framework for writing tests, from simple unit tests to complex functional testing.

## 1. Core Principles

1.  **Tests are Mandatory:** Any new feature (`feat:`) or bug fix (`fix:`) **must** be accompanied by corresponding tests. Code without tests is considered broken by design.
2.  **Naming Convention:** Test files must be named `test_*.py` and test functions must be prefixed with `test_*`.
3.  **Test Independence:** Tests must be independent and isolated. The outcome of one test must not affect another. Never rely on tests running in a specific order.

## 2. Test Structure

- **Directory Layout:** The `tests/` directory should mirror the structure of your application's source code. For a module `my_app/services/billing.py`, the corresponding test file should be `tests/services/test_billing.py`.
- **AAA Pattern:** Structure your tests using the Arrange-Act-Assert pattern for clarity:
  - **Arrange:** Set up the conditions for the test (create objects, mock dependencies).
  - **Act:** Execute the code being tested.
  - **Assert:** Verify that the outcome is as expected.

```python
def test_user_creation():
    # Arrange
    user_service = UserService()
    user_data = {"name": "Alice"}

    # Act
    new_user = user_service.create(user_data)

    # Assert
    assert new_user.name == "Alice"
    assert new_user.is_active is True
```

## 3. Test Coverage

- **Coverage Measurement:** We use `pytest-cov` to measure test coverage.
- **Minimum Threshold:** Every Pull Request **must** maintain or increase the total test coverage. A minimum of **85% coverage** is required for new code.
- **Running with Coverage:**
  ```bash
  poetry run pytest --cov=my_app --cov-report=term-missing
  ```

## 4. Best Practices & Examples

### 4.1. Asserting Expected Failures
To test that a function correctly raises an exception, use `pytest.raises`.

```python
import pytest

def test_division_by_zero():
    with pytest.raises(ZeroDivisionError):
        result = 1 / 0
```

### 4.2. Fixtures for Reusable Setup
Use `@pytest.fixture` to provide reusable setup logic or data for your tests.

```python
@pytest.fixture
def sample_user() -> Dict[str, any]:
    """Provides a sample user dictionary for tests."""
    return {"id": 1, "name": "Alice", "email": "alice@example.com"}

def test_user_email(sample_user: Dict[str, any]):
    # The fixture `sample_user` is automatically passed as an argument
    assert "@" in sample_user["email"]
```

### 4.3. Parametrization for Multiple Inputs
Use `@pytest.mark.parametrize` to run the same test with different inputs, avoiding code duplication.

```python
@pytest.mark.parametrize("test_input, expected", [
    ("hello", 5),
    ("", 0),
    ("world", 5),
])
def test_string_length(test_input: str, expected: int):
    assert len(test_input) == expected
```

### 4.4. Mocking with `pytest-mock`
Use mocking to isolate the code you are testing from its dependencies (like databases, APIs, or other services). The `mocker` fixture comes from the `pytest-mock` plugin.

```python
# Assume we have a function that sends an email
# from my_app.notifications import send_email

def test_user_registration_sends_email(mocker):
    # Arrange: Mock the `send_email` function
    mock_send_email = mocker.patch("my_app.notifications.send_email")

    # Act: Call the function that should trigger the email
    register_user("test@example.com")

    # Assert: Check that our mock was called exactly once with the correct arguments
    mock_send_email.assert_called_once_with(
        "test@example.com",
        "Welcome!"
    )
```
