# SOLID Principles in Python

The **SOLID principles** are five design principles that help make software more maintainable, scalable, and understandable.  

---

## 1. Single Responsibility Principle (SRP)

**Definition:**  
A class should have **only one responsability** — it should do **only one thing**.

This makes the class easier to understand, maintain, and test.

### ❌ Bad Example

```python
class Report:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    def generate(self):
        return f"{self.title}\n{self.content}"

    def save_to_file(self, filename):
        with open(filename, "w") as f:
            f.write(self.generate())
```

Here, `Report` has **two responsibilities**:
1. Creating a report.
2. Saving it to a file.

### ✅ Good Example

```python
class Report:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    def generate(self):
        return f"{self.title}\n{self.content}"

class ReportSaver:
    @staticmethod
    def save_to_file(report: Report, filename: str):
        with open(filename, "w") as f:
            f.write(report.generate())
```

Now, each class has a **single responsibility**:
- `Report` handles the data.
- `ReportSaver` handles persistence.

---

## 2. Open/Closed Principle (OCP)

**Definition:**  
Software entities should be **open for extension**, but **closed for modification**.

You should be able to **add new functionality** without **changing existing code**.

### ❌ Bad Example

```python
class Discount:
    def __init__(self, customer, price):
        self.customer = customer
        self.price = price

    def give_discount(self):
        if self.customer == "VIP":
            return self.price * 0.8
        elif self.customer == "Regular":
            return self.price * 0.9
```

If you want to add a new discount type, you must **modify** the class.

### ✅ Good Example (Using Inheritance)

```python
class Discount:
    def __init__(self, price):
        self.price = price

    def apply_discount(self):
        return self.price

class VIPDiscount(Discount):
    def apply_discount(self):
        return self.price * 0.8

class RegularDiscount(Discount):
    def apply_discount(self):
        return self.price * 0.9
```

Now we can **extend** by adding new subclasses (e.g., `StudentDiscount`) **without modifying** existing code.

---

## 3. Liskov Substitution Principle (LSP)

**Definition:**  
Objects of a superclass should be **replaceable** with objects of a subclass **without altering the correctness** of the program.

### ❌ Bad Example

```python
class Bird:
    def fly(self):
        print("Flying!")

class Penguin(Bird):
    def fly(self):
        raise Exception("Penguins can't fly!")
```

This breaks the LSP because a `Penguin` **is not truly substitutable** for a `Bird`.

### ✅ Good Example

```python
class Bird:
    def make_sound(self):
        pass

class FlyingBird(Bird):
    def fly(self):
        print("Flying!")

class Penguin(Bird):
    def swim(self):
        print("Swimming!")
```

Now, `Penguin` doesn’t pretend to fly — subclasses maintain valid behavior.

---

## 4. Interface Segregation Principle (ISP)

**Definition:**  
Clients should **not be forced to depend** on methods they do not use.

Instead of one big interface, split it into smaller, **more specific** ones.

### ❌ Bad Example

```python
class WorkerInterface:
    def work(self):
        pass

    def eat(self):
        pass

class Robot(WorkerInterface):
    def work(self):
        print("Robot working")

    def eat(self):
        raise Exception("Robots don't eat!")
```

The `Robot` is forced to implement a method it doesn't need.

### ✅ Good Example

```python
class Workable:
    def work(self):
        pass

class Eatable:
    def eat(self):
        pass

class Human(Workable, Eatable):
    def work(self):
        print("Human working")

    def eat(self):
        print("Human eating")

class Robot(Workable):
    def work(self):
        print("Robot working")
```

Now, classes only implement interfaces that are **relevant** to them.

---

## 5. Dependency Inversion Principle (DIP)

**Definition:**  
- High-level modules should **not depend on low-level modules**.  
- Both should depend on **abstractions**.

This improves flexibility and testability.

### ❌ Bad Example

```python
class MySQLDatabase:
    def connect(self):
        print("Connecting to MySQL...")

class App:
    def __init__(self):
        self.db = MySQLDatabase()

    def start(self):
        self.db.connect()
```

The `App` class is **tightly coupled** to `MySQLDatabase`.

### ✅ Good Example

```python
from abc import ABC, abstractmethod

class Database(ABC):
    @abstractmethod
    def connect(self):
        pass

class MySQLDatabase(Database):
    def connect(self):
        print("Connecting to MySQL...")

class PostgreSQLDatabase(Database):
    def connect(self):
        print("Connecting to PostgreSQL...")

class App:
    def __init__(self, database: Database):
        self.db = database

    def start(self):
        self.db.connect()

# Usage
app = App(MySQLDatabase())
app.start()
```

Now, `App` depends only on the **abstract interface**, not the implementation — making it easy to swap databases or mock them for tests.

---

## ✅ Summary

| Principle | Description | Python Concept |
|------------|--------------|----------------|
| **S** - Single Responsibility | A class should have one job | Separation of concerns |
| **O** - Open/Closed | Open for extension, closed for modification | Inheritance / Polymorphism |
| **L** - Liskov Substitution | Subclasses must be substitutable for base classes | Proper use of inheritance |
| **I** - Interface Segregation | No class should depend on methods it doesn’t use | Multiple smaller interfaces |
| **D** - Dependency Inversion | Depend on abstractions, not concrete implementations | Abstract base classes / Dependency injection |

---

**💡 Tip:**  
Applying SOLID doesn’t mean more code — it means **better structured** and **easier to evolve** code.
