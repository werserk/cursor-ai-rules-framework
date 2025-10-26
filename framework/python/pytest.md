# Python: Pytest (Testing)

Thorough testing is the foundation of reliable software. `pytest` is our standard framework for writing tests, from simple unit tests to complex functional testing.

## 1. Core Principles

1.  **Tests are Mandatory:** Any new feature (`feat:`) or bug fix (`fix:`) **must** be accompanied by corresponding tests. Code without tests is considered broken by design.
2.  **Naming Convention:** Test files must be named `test_*.py` and test functions must be prefixed with `test_*`.
3.  **Test Independence:** Tests must be independent and isolated. The outcome of one test must not affect another. Never rely on tests running in a specific order.

4.  **A Failing Test is a Signal:** When a test fails, it requires careful analysis. The failure indicates one of two things:
    - **A Bug in the Code:** The test has correctly identified a flaw in the application's logic. This is the primary goal of testing.
    - **An Error in the Test:** The test itself is incorrect—it might have a typo, flawed logic, or be based on a misunderstanding of the feature's requirements.

    **Action:** Before changing any application code, first verify that the test is correct. If the test is valid, you have successfully reproduced a bug and can proceed with a fix. A failing test for a new feature is a normal part of the Test-Driven Development (TDD) cycle.

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

## 4. Writing Meaningful Tests (Quality over Quantity)

High test coverage is a good starting point, but it is not the ultimate goal. The goal is **confidence**. A good test suite gives you confidence that your application works correctly. Trivial tests that only increase coverage without verifying meaningful logic are discouraged.

### 4.1. What to Test
Focus your efforts on testing the following:
- **Business Logic:** The core rules and processes of your application.
- **Service Endpoints:** The inputs and outputs of your API or service layers.
- **Complex Conditionals:** Functions with multiple `if/elif/else` branches.
- **Edge Cases & Boundary Conditions:** What happens with empty lists, `None` inputs, zero values, negative numbers, or unusually long strings?
- **Integration Points:** How your code interacts with external systems (databases, APIs), even if the external system is mocked. The test should verify that your code *tries* to interact correctly.

### 4.2. What to Avoid Testing (Trivial Tests)
Avoid writing tests for:
- **Simple Getters/Setters:** Testing a function that just returns a value is often redundant.
- **Third-Party Libraries:** Do not test that `requests.get()` works. Trust that the library is already tested. Instead, test *how your code behaves* when `requests.get()` returns a certain value or raises an exception.
- **Code that Mirrors Implementation:** A test should validate the *outcome*, not the exact sequence of steps. If your test is just a line-by-line copy of your function's implementation, it's a brittle and low-value test.

### Example: Trivial vs. Meaningful Test

Consider this function:
```python
def calculate_discount(price: float, member_level: str) -> float:
    if member_level == "gold":
        return price * 0.8  # 20% discount
    if member_level == "silver":
        return price * 0.9  # 10% discount
    return price
```

**Trivial Test (Low Value) 👎:**
```python
def test_calculate_discount_implementation():
    # This test just mirrors the implementation.
    # It doesn't test any real business scenario.
    assert calculate_discount(100, "gold") == 100 * 0.8
```

**Meaningful Tests (High Value) 👍:**
```python
def test_calculate_discount_for_gold_member():
    """Verify that a gold member gets the correct 20% discount."""
    assert calculate_discount(100.0, "gold") == 80.0

def test_calculate_discount_for_non_member():
    """Verify that a user with no membership level gets no discount."""
    assert calculate_discount(100.0, "bronze") == 100.0
    assert calculate_discount(100.0, "") == 100.0

def test_calculate_discount_with_zero_price():
    """Edge Case: Test with a price of zero."""
    assert calculate_discount(0.0, "gold") == 0.0

def test_calculate_discount_with_case_insensitivity():
    """Boundary Condition: Does it handle different casing?"""
    # Assuming the business rule is that it should be case-insensitive.
    # This test might fail initially and reveal a bug or a missing requirement.
    assert calculate_discount(100.0, "GOLD") == 80.0
```

## 5. Best Practices & Examples

### 5.1. Asserting Expected Failures
To test that a function correctly raises an exception, use `pytest.raises`.

```python
import pytest

def test_division_by_zero():
    with pytest.raises(ZeroDivisionError):
        result = 1 / 0
```

### 5.2. Fixtures for Reusable Setup
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

### 5.3. Parametrization for Multiple Inputs
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

### 5.4. Mocking with `pytest-mock`
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
