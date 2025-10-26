# Architecture & Design Principles

This document outlines the core architectural and design principles you must adhere to when generating or modifying code. Each principle is illustrated with Python code examples demonstrating "bad" (👎) and "good" (👍) practices.

## 1. SOLID

### S: Single Responsibility Principle (SRP)
*A class or function should be responsible for only one task.*

👎 **Bad:** The class manages both user data and database persistence.
```python
class UserManager:
    def get_user(self, user_id: int):
        # Fetches user from DB
        pass

    def save_user(self, user: dict):
        # Validation logic
        if not user.get("name"):
            raise ValueError("Name is required")
        # Persistence logic
        print("Saving user to the database...")
```

👍 **Good:** Responsibilities are split into separate classes.
```python
class User:
    # Data class or business object
    def __init__(self, name: str):
        self.name = self._validate_name(name)

    def _validate_name(self, name: str) -> str:
        if not name:
            raise ValueError("Name is required")
        return name

class UserRepository:
    # Persistence class
    def save(self, user: User):
        print(f"Saving {user.name} to the database...")
```

### O: Open/Closed Principle (OCP)
*Entities should be open for extension but closed for modification.*

👎 **Bad:** Adding a new report type requires modifying the `generate_report` function.
```python
def generate_report(data: list, report_type: str):
    if report_type == "csv":
        print("Generating CSV report...")
    elif report_type == "json":
        print("Generating JSON report...")
    # New report type? Add another `elif` here.
```

👍 **Good:** Use a strategy pattern to allow adding new types without changing the core logic.
```python
from abc import ABC, abstractmethod

class ReportGenerator(ABC):
    @abstractmethod
    def generate(self, data: list):
        pass

class CsvReportGenerator(ReportGenerator):
    def generate(self, data: list):
        print("Generating CSV report...")

class JsonReportGenerator(ReportGenerator):
    def generate(self, data: list):
        print("Generating JSON report...")

# No modification needed to add a new format (e.g., XML)
def generate_report(data: list, generator: ReportGenerator):
    generator.generate(data)
```

### L: Liskov Substitution Principle (LSP)
*Subtypes must be substitutable for their base types without altering the correctness of the program.*

👎 **Bad:** The subclass `ReadonlyFile` breaks the contract of the parent `File` class by raising an exception on `write`.
```python
class File:
    def read(self): pass
    def write(self, data): pass

class ReadonlyFile(File):
    def read(self):
        print("Reading file...")

    def write(self, data):
        raise NotImplementedError("Cannot write to a readonly file.")
```

👍 **Good:** Create more specific base classes. A client that expects a writable file shouldn't receive a readonly one.
```python
class ReadableFile:
    def read(self): pass

class WritableFile:
    def write(self, data): pass

class ReadWriteFile(ReadableFile, WritableFile):
    def read(self):
        print("Reading file...")
    def write(self, data):
        print("Writing data...")

class ReadonlyFile(ReadableFile):
    def read(self):
        print("Reading file...")
```

### I: Interface Segregation Principle (ISP)
*Prefer small, specific interfaces over large, monolithic ones.*

👎 **Bad:** A monolithic `Worker` interface forces all implementers to have methods they may not need.
```python
class Worker(ABC):
    @abstractmethod
    def work(self): pass

    @abstractmethod
    def eat(self): pass

class Human(Worker):
    def work(self): print("Human working...")
    def eat(self): print("Human eating...")

class Robot(Worker):
    def work(self): print("Robot working...")
    def eat(self):
        # Robots don't eat, but must implement this method.
        pass
```

👍 **Good:** Split into smaller, more focused interfaces.
```python
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class Human(Workable, Eatable):
    def work(self): print("Human working...")
    def eat(self): print("Human eating...")

class Robot(Workable):
    def work(self): print("Robot working...")
```

### D: Dependency Inversion Principle (DIP)
*High-level modules should depend on abstractions, not low-level implementations.*

👎 **Bad:** `ReportGenerator` (high-level) depends directly on `Database` (low-level).
```python
class Database:
    def get_data(self):
        return "Data from database"

class ReportGenerator:
    def generate(self):
        db = Database()
        data = db.get_data()
        print(f"Generating report with: {data}")
```

👍 **Good:** Both modules depend on an abstraction (`DataSource`).
```python
class DataSource(ABC):
    @abstractmethod
    def get_data(self):
        pass

class Database(DataSource):
    def get_data(self):
        return "Data from database"

class ApiSource(DataSource):
    def get_data(self):
        return "Data from API"

class ReportGenerator:
    def __init__(self, source: DataSource):
        self.source = source

    def generate(self):
        data = self.source.get_data()
        print(f"Generating report with: {data}")
```

## 2. DRY (Don't Repeat Yourself)
*Avoid code duplication. Every piece of logic should have a single, unambiguous representation.*

👎 **Bad:** Validation logic is duplicated.
```python
def process_order(items: list):
    if not items:
        print("Error: No items in order.")
        return
    # process...

def create_invoice(items: list):
    if not items:
        print("Error: No items in invoice.")
        return
    # create...
```

👍 **Good:** Extract the duplicated logic into a reusable function.
```python
def validate_items(items: list):
    if not items:
        raise ValueError("Items list cannot be empty.")

def process_order(items: list):
    validate_items(items)
    # process...

def create_invoice(items: list):
    validate_items(items)
    # create...
```

## 3. KISS (Keep It Simple, Stupid)
*Always prefer the simplest solution that correctly solves the problem. Avoid unnecessary complexity.*

👎 **Bad:** Using complex, hard-to-read comprehensions for simple tasks.
```python
# Get the squares of even numbers from 0 to 10
squares = [x*x for x in range(11) if x % 2 == 0]
```

👍 **Good:** A simple, clear loop is often more readable and maintainable.
```python
# Get the squares of even numbers from 0 to 10
squares = []
for x in range(11):
    if x % 2 == 0:
        squares.append(x*x)
```
*Note: The list comprehension above is not "wrong," but KISS is about prioritizing clarity. If logic becomes nested or complex, a simple loop is better.*

## 4. Loose Coupling, High Cohesion
- **Loose Coupling:** Modules should be as independent as possible.
- **High Cohesion:** The logic within a single module should be closely related and focused.

👎 **Bad:** Low cohesion (class does unrelated things) and tight coupling (depends on a concrete `EmailSender`).
```python
class OrderManager:
    def process_order(self, order):
        # ... processing logic ...
        self.send_confirmation_email(order)
        self.update_inventory(order)

    def send_confirmation_email(self, order):
        # Tightly coupled to a specific implementation
        sender = EmailSender()
        sender.send(order.customer_email, "Order Confirmed")

    def update_inventory(self, order):
        # This seems unrelated to order management itself
        print("Updating inventory...")
```

👍 **Good:** High cohesion (each class has one job) and loose coupling (uses an abstract `Notifier`).
```python
class Notifier(ABC):
    @abstractmethod
    def notify(self, recipient: str, message: str):
        pass

class EmailNotifier(Notifier):
    def notify(self, recipient: str, message: str):
        print(f"Sending email to {recipient}: {message}")

class InventoryService:
    def update_inventory(self, order):
        print("Updating inventory...")

class OrderManager:
    def __init__(self, notifier: Notifier, inventory: InventoryService):
        self.notifier = notifier
        self.inventory = inventory

    def process_order(self, order):
        # ... processing logic ...
        self.notifier.notify(order.customer_email, "Order Confirmed")
        self.inventory.update_inventory(order)
```

## 5. Composition Over Inheritance
*Prefer composition (a class *has* an instance of another) over inheritance (a class *is* a subclass). It is more flexible.*

👎 **Bad:** Using inheritance to add functionality. This leads to rigid hierarchies.
```python
class Report:
    def generate(self):
        return "Base Report"

class ReportWithLogging(Report):
    def generate(self):
        print("LOG: Generating report...")
        result = super().generate()
        print("LOG: Report generated.")
        return result
```

👍 **Good:** Using composition to "inject" functionality. Much more flexible.
```python
class Logger:
    def log(self, message: str):
        print(f"LOG: {message}")

class Report:
    def __init__(self, logger: Logger = None):
        self.logger = logger

    def generate(self):
        if self.logger:
            self.logger.log("Generating report...")

        result = "Base Report"

        if self.logger:
            self.logger.log("Report generated.")

        return result
```

## 6. Pure Functions
*Where possible, prefer pure functions: their output depends *only* on their inputs, and they have *no side effects*.*

👎 **Bad:** The function modifies an external variable (a side effect).
```python
total = 0

def add_to_total(value: int):
    global total
    total += value
    return total

add_to_total(5) # total is now 5
add_to_total(10) # total is now 15
```

👍 **Good:** The function has no side effects. It takes all required inputs and returns a new value, leaving the original unchanged.
```python
def add(a: int, b: int) -> int:
    return a + b

total = 0
total = add(total, 5) # total is now 5
total = add(total, 10) # total is now 15
```
