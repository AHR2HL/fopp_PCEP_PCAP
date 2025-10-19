..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 6: MRO and Advanced Polymorphism
=========================================

.. index:: MRO, polymorphism, duck typing, abstract base classes

Introduction
------------

You've learned about multiple inheritance and the basics of MRO. Now it's time to master the **deep mechanics** of Method Resolution Order and explore **advanced polymorphism** techniques.

Polymorphism ("many forms") is one of the pillars of OOP. In Python, polymorphism is everywhere:

- **Duck typing** — if it walks like a duck...
- **Inheritance polymorphism** — overriding methods
- **Protocol polymorphism** — structural subtyping
- **Generic functions** — working with any type

In this section, you'll learn:

- MRO computation algorithms in depth
- Visualizing and debugging complex MRO chains
- Polymorphism types and when to use each
- Method overriding best practices
- Abstract base classes (ABCs) for interfaces
- Protocols and structural subtyping (Python 3.8+)
- Real-world polymorphic design patterns
- Performance considerations

By the end, you'll be an MRO and polymorphism master!

---

MRO Deep Dive
-------------

.. index:: MRO algorithm, C3 linearization deep dive

Let's explore how Python computes MRO step by step.

**The C3 Linearization Algorithm:**

Given a class C with parents (B₁, B₂, ..., Bₙ):

1. Start with C itself
2. Merge the parent MROs
3. Add parents left-to-right
4. Ensure each class appears only once
5. Maintain monotonicity (ordering consistency)

.. activecode:: oop_mro_computation
   :language: python

   class A:
       pass

   class B:
       pass

   class C:
       pass

   class D(A, B):
       pass

   class E(B, C):
       pass

   class F(D, E):
       pass

   def show_mro_chain(cls):
       """Visualize MRO with arrows."""
       mro_chain = ' → '.join(c.__name__ for c in cls.__mro__)
       return mro_chain

   print("Complex Inheritance Structure:")
   print("     A   B   C")
   print("      \\ / \\ /")
   print("       D   E")
   print("        \\ /")
   print("         F")

   print(f"\nMRO Chains:")
   print(f"  A: {show_mro_chain(A)}")
   print(f"  B: {show_mro_chain(B)}")
   print(f"  C: {show_mro_chain(C)}")
   print(f"  D: {show_mro_chain(D)}")
   print(f"  E: {show_mro_chain(E)}")
   print(f"  F: {show_mro_chain(F)}")

   print(f"\n💡 F's MRO explained:")
   print(f"   1. F (the class itself)")
   print(f"   2. D (first parent of F)")
   print(f"   3. A (parent of D, comes before B)")
   print(f"   4. E (second parent of F)")
   print(f"   5. B (common ancestor, appears once)")
   print(f"   6. C (from E)")
   print(f"   7. object (ultimate base)")

**Output:**

::

   Complex Inheritance Structure:
        A   B   C
         \ / \ /
          D   E
           \ /
            F

   MRO Chains:
     A: A → object
     B: B → object
     C: C → object
     D: D → A → B → object
     E: E → B → C → object
     F: F → D → A → E → B → C → object

   💡 F's MRO explained:
      1. F (the class itself)
      2. D (first parent of F)
      3. A (parent of D, comes before B)
      4. E (second parent of F)
      5. B (common ancestor, appears once)
      6. C (from E)
      7. object (ultimate base)

---

**MRO Properties Demonstrated:**

.. activecode:: oop_mro_properties
   :language: python

   class Base:
       def method(self):
           return "Base"

   class Left(Base):
       def method(self):
           return "Left"

   class Right(Base):
       def method(self):
           return "Right"

   class Child(Left, Right):
       pass

   print("MRO Properties:")
   print("="*50)

   # Property 1: Children before parents
   print("\n1. Children before parents:")
   print(f"   Child comes before Left: {Child.__mro__.index(Child) < Child.__mro__.index(Left)}")
   print(f"   Left comes before Base: {Child.__mro__.index(Left) < Child.__mro__.index(Base)}")

   # Property 2: Left-to-right order
   print("\n2. Left-to-right parent order:")
   print(f"   Left before Right: {Child.__mro__.index(Left) < Child.__mro__.index(Right)}")

   # Property 3: Each class appears once
   print("\n3. Each class appears exactly once:")
   mro_names = [c.__name__ for c in Child.__mro__]
   print(f"   MRO: {mro_names}")
   print(f"   All unique: {len(mro_names) == len(set(mro_names))}")

   # Property 4: Consistent with subclass relationship
   print("\n4. Monotonic (preserves subclass ordering):")
   print(f"   If A before B in one chain, A before B everywhere")

   # Which method gets called?
   child = Child()
   print(f"\n5. Method resolution:")
   print(f"   child.method(): '{child.method()}'")
   print(f"   Resolved to: {Child.__mro__[1].__name__}.method()")

**Output:**

::

   MRO Properties:
   ==================================================

   1. Children before parents:
      Child comes before Left: True
      Left comes before Base: True

   2. Left-to-right parent order:
      Left before Right: True

   3. Each class appears exactly once:
      MRO: ['Child', 'Left', 'Right', 'Base', 'object']
      All unique: True

   4. Monotonic (preserves subclass ordering):
      If A before B in one chain, A before B everywhere

   5. Method resolution:
      child.method(): 'Left'
      Resolved to: Left.method()

---

**MRO Debugging Tools:**

.. activecode:: oop_mro_debug_tools
   :language: python

   def analyze_mro(cls):
       """Comprehensive MRO analysis tool."""
       print(f"{'='*60}")
       print(f"MRO ANALYSIS: {cls.__name__}")
       print(f"{'='*60}")

       # Show MRO chain
       print(f"\n📋 MRO Chain:")
       for i, c in enumerate(cls.__mro__):
           print(f"   {i}. {c.__name__} (from {c.__module__})")

       # Show which methods come from where
       print(f"\n🔧 Method Sources:")
       methods = {}
       for attr in dir(cls):
           if not attr.startswith('_'):
               for c in cls.__mro__:
                   if attr in c.__dict__:
                       methods[attr] = c.__name__
                       break

       for method, source in sorted(methods.items()):
           print(f"   {method}() → {source}")

       # Show direct parents
       print(f"\n👥 Direct Parents:")
       for base in cls.__bases__:
           print(f"   - {base.__name__}")

       print(f"\n{'='*60}\n")

   class Animal:
       def speak(self):
           return "..."

       def eat(self):
           return "eating"

   class Flyer:
       def fly(self):
           return "flying"

   class Swimmer:
       def swim(self):
           return "swimming"

   class Duck(Flyer, Swimmer, Animal):
       def speak(self):
           return "quack"

   analyze_mro(Duck)

**Output:**

::

   ============================================================
   MRO ANALYSIS: Duck
   ============================================================

   📋 MRO Chain:
      0. Duck (from __main__)
      1. Flyer (from __main__)
      2. Swimmer (from __main__)
      3. Animal (from __main__)
      4. object (from builtins)

   🔧 Method Sources:
      eat() → Animal
      fly() → Flyer
      speak() → Duck
      swim() → Swimmer

   👥 Direct Parents:
      - Flyer
      - Swimmer
      - Animal

   ============================================================

---

Understanding Polymorphism
---------------------------

.. index:: polymorphism types, compile-time polymorphism, runtime polymorphism

**Polymorphism** means "many forms" — the ability to treat different types uniformly.

**Types of Polymorphism:**

.. list-table:: Polymorphism Types
   :widths: 30 35 35
   :header-rows: 1

   * - Type
     - Description
     - Python Example
   * - **Ad-hoc**
     - Function overloading (same name, different signatures)
     - ``@singledispatch``
   * - **Parametric**
     - Generic functions/types
     - ``List[T]``, ``Dict[K, V]``
   * - **Subtype**
     - Inheritance-based
     - Method overriding
   * - **Duck Typing**
     - Structural (if it has the method, use it)
     - Python's default

---

**Polymorphism Example: One Interface, Many Implementations**

.. activecode:: oop_poly_basic
   :language: python

   # Define a common interface through inheritance
   class Shape:
       def area(self):
           raise NotImplementedError("Subclass must implement area()")

       def perimeter(self):
           raise NotImplementedError("Subclass must implement perimeter()")

   class Rectangle(Shape):
       def __init__(self, width, height):
           self.width = width
           self.height = height

       def area(self):
           return self.width * self.height

       def perimeter(self):
           return 2 * (self.width + self.height)

   class Circle(Shape):
       def __init__(self, radius):
           self.radius = radius

       def area(self):
           return 3.14159 * self.radius ** 2

       def perimeter(self):
           return 2 * 3.14159 * self.radius

   class Triangle(Shape):
       def __init__(self, a, b, c):
           self.a = a
           self.b = b
           self.c = c

       def area(self):
           # Heron's formula
           s = (self.a + self.b + self.c) / 2
           return (s * (s - self.a) * (s - self.b) * (s - self.c)) ** 0.5

       def perimeter(self):
           return self.a + self.b + self.c

   # Polymorphic function - works with ANY shape
   def print_shape_info(shape):
       """Works with any Shape subclass!"""
       print(f"{shape.__class__.__name__}:")
       print(f"  Area: {shape.area():.2f}")
       print(f"  Perimeter: {shape.perimeter():.2f}")

   # Create different shapes
   shapes = [
       Rectangle(5, 10),
       Circle(7),
       Triangle(3, 4, 5)
   ]

   # Process them uniformly
   for shape in shapes:
       print_shape_info(shape)
       print()

**Output:**

::

   Rectangle:
     Area: 50.00
     Perimeter: 30.00

   Circle:
     Area: 153.94
     Perimeter: 43.98

   Triangle:
     Area: 6.00
     Perimeter: 12.00

---

Duck Typing vs Inheritance Polymorphism
----------------------------------------

.. index:: duck typing, structural typing, nominal typing

Python supports **two main approaches** to polymorphism:

**1. Duck Typing (Structural)** — "If it walks like a duck..."

.. activecode:: oop_poly_duck_typing
   :language: python

   # No inheritance required!
   class Duck:
       def quack(self):
           return "Quack!"

       def fly(self):
           return "Flying!"

   class Person:
       def quack(self):
           return "I'm imitating a duck!"

       def fly(self):
           return "I'm flapping my arms!"

   class Airplane:
       def fly(self):
           return "Flying at 500 mph!"
       # No quack method!

   # Polymorphic function using duck typing
   def make_it_quack(thing):
       """If it has quack(), call it!"""
       try:
           return thing.quack()
       except AttributeError:
           return f"{thing.__class__.__name__} can't quack"

   def make_it_fly(thing):
       """If it has fly(), call it!"""
       if hasattr(thing, 'fly'):
           return thing.fly()
       return f"{thing.__class__.__name__} can't fly"

   # Test with different objects
   things = [Duck(), Person(), Airplane()]

   print("Duck Typing Demo:")
   for thing in things:
       print(f"\n{thing.__class__.__name__}:")
       print(f"  Quack: {make_it_quack(thing)}")
       print(f"  Fly: {make_it_fly(thing)}")

**Output:**

::

   Duck Typing Demo:

   Duck:
     Quack: Quack!
     Fly: Flying!

   Person:
     Quack: I'm imitating a duck!
     Fly: I'm flapping my arms!

   Airplane:
     Quack: Airplane can't quack
     Fly: Flying at 500 mph!

---

**2. Inheritance Polymorphism (Nominal)** — Explicit type hierarchy

.. activecode:: oop_poly_inheritance
   :language: python

   # Explicit base class
   class Animal:
       def speak(self):
           raise NotImplementedError

   class Dog(Animal):
       def speak(self):
           return "Woof!"

   class Cat(Animal):
       def speak(self):
           return "Meow!"

   class Cow(Animal):
       def speak(self):
           return "Moo!"

   # Polymorphic function with type checking
   def make_animal_speak(animal):
       """Requires an Animal instance."""
       if not isinstance(animal, Animal):
           return f"Error: {type(animal).__name__} is not an Animal"
       return animal.speak()

   # Test
   animals = [Dog(), Cat(), Cow(), "Not an animal"]

   print("Inheritance Polymorphism:")
   for animal in animals:
       result = make_animal_speak(animal)
       print(f"  {result}")

**Output:**

::

   Inheritance Polymorphism:
     Woof!
     Meow!
     Moo!
     Error: str is not an Animal

---

**Comparison: Duck Typing vs Inheritance**

.. list-table:: Duck Typing vs Inheritance Polymorphism
   :widths: 25 35 40
   :header-rows: 1

   * - Aspect
     - Duck Typing
     - Inheritance Polymorphism
   * - **Type checking**
     - No type checking
     - Explicit ``isinstance()`` checks
   * - **Flexibility**
     - ✅ Very flexible
     - ⚠️ Less flexible
   * - **Coupling**
     - ✅ Loose coupling
     - ⚠️ Tight coupling
   * - **Documentation**
     - ⚠️ Less explicit
     - ✅ Clear type hierarchy
   * - **Safety**
     - ⚠️ Runtime errors
     - ✅ Catches errors earlier
   * - **Pythonic?**
     - ✅ More Pythonic
     - ⚠️ Less Pythonic

---

Method Overriding Best Practices
---------------------------------

.. index:: method overriding, override patterns

**Pattern 1: Complete Override**

.. activecode:: oop_poly_override_complete
   :language: python

   class Animal:
       def move(self):
           return "Moving"

   class Bird(Animal):
       def move(self):
           # Complete replacement - don't call parent
           return "Flying through the air"

   class Fish(Animal):
       def move(self):
           return "Swimming through water"

   bird = Bird()
   fish = Fish()

   print(f"Bird: {bird.move()}")
   print(f"Fish: {fish.move()}")

**Output:**

::

   Bird: Flying through the air
   Fish: Swimming through water

---

**Pattern 2: Extend Parent Behavior**

.. activecode:: oop_poly_override_extend
   :language: python

   class Logger:
       def log(self, message):
           print(f"[LOG] {message}")

   class TimestampLogger(Logger):
       def log(self, message):
           from datetime import datetime
           timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
           # Call parent, then add functionality
           super().log(f"[{timestamp}] {message}")

   class VerboseLogger(TimestampLogger):
       def log(self, message):
           # Call parent, then add more
           super().log(message)
           print(f"[VERBOSE] Message length: {len(message)} characters")

   # Test the chain
   basic = Logger()
   timed = TimestampLogger()
   verbose = VerboseLogger()

   print("Basic Logger:")
   basic.log("Hello")

   print("\nTimestamp Logger:")
   timed.log("Hello")

   print("\nVerbose Logger:")
   verbose.log("Hello")

**Output:**

::

   Basic Logger:
   [LOG] Hello

   Timestamp Logger:
   [LOG] [2025-10-19 14:30:45] Hello

   Verbose Logger:
   [LOG] [2025-10-19 14:30:45] Hello
   [VERBOSE] Message length: 5 characters

---

**Pattern 3: Pre/Post Processing**

.. activecode:: oop_poly_override_prepost
   :language: python

   class FileWriter:
       def write(self, data):
           print(f"Writing: {data}")

   class BufferedWriter(FileWriter):
       def __init__(self):
           self.buffer = []

       def write(self, data):
           # PRE: Buffer the data
           self.buffer.append(data)
           print(f"Buffered: {data}")

           # Call parent if buffer is full
           if len(self.buffer) >= 3:
               print("Buffer full, flushing...")
               for item in self.buffer:
                   super().write(item)
               self.buffer.clear()

   class CompressingWriter(BufferedWriter):
       def write(self, data):
           # PRE: Compress the data
           compressed = data.upper()  # Simulate compression
           print(f"Compressing: {data} → {compressed}")

           # Call parent
           super().write(compressed)

   writer = CompressingWriter()
   writer.write("hello")
   writer.write("world")
   writer.write("test")

**Output:**

::

   Compressing: hello → HELLO
   Buffered: HELLO
   Compressing: world → WORLD
   Buffered: WORLD
   Compressing: test → TEST
   Buffered: TEST
   Buffer full, flushing...
   Writing: HELLO
   Writing: WORLD
   Writing: TEST

---

Abstract Base Classes (ABCs)
-----------------------------

.. index:: abstract base class, ABC, interface pattern

**Abstract Base Classes** define interfaces that subclasses must implement.

.. activecode:: oop_poly_abc_basic
   :language: python

   from abc import ABC, abstractmethod

   class Shape(ABC):
       """Abstract base class for shapes."""

       @abstractmethod
       def area(self):
           """Calculate area - must be implemented by subclasses."""
           pass

       @abstractmethod
       def perimeter(self):
           """Calculate perimeter - must be implemented by subclasses."""
           pass

       def describe(self):
           """Concrete method - inherited by all subclasses."""
           return f"I am a {self.__class__.__name__}"

   # Can't instantiate abstract class
   try:
       shape = Shape()
   except TypeError as e:
       print(f"❌ Can't instantiate ABC: {e}\n")

   # Must implement all abstract methods
   class Rectangle(Shape):
       def __init__(self, width, height):
           self.width = width
           self.height = height

       def area(self):
           return self.width * self.height

       def perimeter(self):
           return 2 * (self.width + self.height)

   # This works!
   rect = Rectangle(5, 10)
   print(f"Rectangle created successfully!")
   print(f"  Area: {rect.area()}")
   print(f"  Perimeter: {rect.perimeter()}")
   print(f"  Description: {rect.describe()}")

**Output:**

::

   ❌ Can't instantiate ABC: Can't instantiate abstract class Shape with abstract methods area, perimeter

   Rectangle created successfully!
     Area: 50
     Perimeter: 30
     Description: I am a Rectangle

---

**ABC with Multiple Abstract Methods**

.. activecode:: oop_poly_abc_multiple
   :language: python

   from abc import ABC, abstractmethod

   class DataSource(ABC):
       """Abstract interface for data sources."""

       @abstractmethod
       def connect(self):
           """Establish connection to data source."""
           pass

       @abstractmethod
       def fetch(self, query):
           """Fetch data using query."""
           pass

       @abstractmethod
       def close(self):
           """Close the connection."""
           pass

   class DatabaseSource(DataSource):
       def connect(self):
           print("Connecting to database...")

       def fetch(self, query):
           print(f"Executing SQL: {query}")
           return [{"id": 1, "name": "Alice"}]

       def close(self):
           print("Closing database connection")

   class APISource(DataSource):
       def connect(self):
           print("Connecting to API...")

       def fetch(self, query):
           print(f"GET request: {query}")
           return {"status": "success", "data": []}

       def close(self):
           print("Closing API connection")

   # Polymorphic function
   def fetch_data(source: DataSource, query: str):
       """Works with ANY DataSource implementation."""
       source.connect()
       data = source.fetch(query)
       source.close()
       return data

   # Test with both implementations
   print("Database Source:")
   db = DatabaseSource()
   fetch_data(db, "SELECT * FROM users")

   print("\nAPI Source:")
   api = APISource()
   fetch_data(api, "/api/users")

**Output:**

::

   Database Source:
   Connecting to database...
   Executing SQL: SELECT * FROM users
   Closing database connection

   API Source:
   Connecting to API...
   GET request: /api/users
   Closing API connection

---

Protocols (Python 3.8+)
------------------------

.. index:: protocols, structural subtyping, typing.Protocol

**Protocols** enable static duck typing — define expected methods without inheritance.

.. activecode:: oop_poly_protocols
   :language: python

   from typing import Protocol

   class Drawable(Protocol):
       """Protocol: Any class with draw() method."""
       def draw(self) -> str:
           ...

   # No inheritance needed!
   class Circle:
       def draw(self) -> str:
           return "Drawing circle ⭕"

   class Square:
       def draw(self) -> str:
           return "Drawing square ⬛"

   class Text:
       def draw(self) -> str:
           return "Drawing text 📝"

   def render(obj: Drawable) -> None:
       """Accepts anything with draw() method."""
       print(obj.draw())

   # All work without inheriting from Drawable!
   shapes = [Circle(), Square(), Text()]

   print("Protocol Demo (structural typing):")
   for shape in shapes:
       render(shape)

   print("\n💡 None of these classes inherit from Drawable!")
   print(f"   Circle bases: {Circle.__bases__}")
   print(f"   Square bases: {Square.__bases__}")
   print(f"   Text bases: {Text.__bases__}")

**Output:**

::

   Protocol Demo (structural typing):
   Drawing circle ⭕
   Drawing square ⬛
   Drawing text 📝

   💡 None of these classes inherit from Drawable!
      Circle bases: (<class 'object'>,)
      Square bases: (<class 'object'>,)
      Text bases: (<class 'object'>,)

---

**Protocols vs ABCs**

.. list-table:: Protocols vs Abstract Base Classes
   :widths: 30 35 35
   :header-rows: 1

   * - Aspect
     - Protocol
     - ABC
   * - **Inheritance**
     - ❌ Not required
     - ✅ Must inherit
   * - **Checking**
     - Static (type checker)
     - Runtime (isinstance)
   * - **Flexibility**
     - ✅ More flexible
     - ⚠️ Less flexible
   * - **Coupling**
     - ✅ Zero coupling
     - ⚠️ Tight coupling
   * - **Python version**
     - 3.8+
     - Any version
   * - **Use case**
     - Duck typing with type hints
     - Explicit interfaces

---

Real-World Polymorphic Patterns
--------------------------------

.. index:: polymorphic patterns, strategy pattern, factory pattern

**Pattern 1: Strategy Pattern**

.. activecode:: oop_poly_pattern_strategy
   :language: python

   from abc import ABC, abstractmethod

   class PaymentStrategy(ABC):
       """Abstract strategy for payment processing."""
       @abstractmethod
       def pay(self, amount):
           pass

   class CreditCardPayment(PaymentStrategy):
       def __init__(self, card_number):
           self.card_number = card_number

       def pay(self, amount):
           return f"Paid ${amount} with credit card ending in {self.card_number[-4:]}"

   class PayPalPayment(PaymentStrategy):
       def __init__(self, email):
           self.email = email

       def pay(self, amount):
           return f"Paid ${amount} via PayPal ({self.email})"

   class CryptoPayment(PaymentStrategy):
       def __init__(self, wallet):
           self.wallet = wallet

       def pay(self, amount):
           return f"Paid ${amount} with crypto to {self.wallet[:10]}..."

   class ShoppingCart:
       def __init__(self):
           self.items = []
           self.payment_strategy = None

       def add_item(self, item, price):
           self.items.append((item, price))

       def set_payment_strategy(self, strategy: PaymentStrategy):
           self.payment_strategy = strategy

       def checkout(self):
           total = sum(price for _, price in self.items)
           if not self.payment_strategy:
               return "Error: No payment method selected"
           return self.payment_strategy.pay(total)

   # Use different payment strategies
   cart = ShoppingCart()
   cart.add_item("Book", 29.99)
   cart.add_item("Coffee", 4.50)

   # Try different payment methods
   print("Strategy Pattern Demo:")

   cart.set_payment_strategy(CreditCardPayment("1234-5678-9012-3456"))
   print(f"  {cart.checkout()}")

   cart.set_payment_strategy(PayPalPayment("user@example.com"))
   print(f"  {cart.checkout()}")

   cart.set_payment_strategy(CryptoPayment("0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb"))
   print(f"  {cart.checkout()}")

**Output:**

::

   Strategy Pattern Demo:
     Paid $34.49 with credit card ending in 3456
     Paid $34.49 via PayPal (user@example.com)
     Paid $34.49 with crypto to 0x742d35Cc...

---

**Pattern 2: Template Method Pattern**

.. activecode:: oop_poly_pattern_template
   :language: python

   from abc import ABC, abstractmethod

   class DataProcessor(ABC):
       """Template for data processing pipeline."""

       def process(self, data):
           """Template method - defines the algorithm structure."""
           print(f"Processing with {self.__class__.__name__}:")

           # Step 1: Load
           loaded = self.load(data)
           print(f"  1. Loaded: {loaded}")

           # Step 2: Transform
           transformed = self.transform(loaded)
           print(f"  2. Transformed: {transformed}")

           # Step 3: Validate
           validated = self.validate(transformed)
           print(f"  3. Validated: {validated}")

           # Step 4: Save
           result = self.save(validated)
           print(f"  4. Saved: {result}")

           return result

       @abstractmethod
       def load(self, data):
           """Subclasses implement loading logic."""
           pass

       @abstractmethod
       def transform(self, data):
           """Subclasses implement transformation logic."""
           pass

       def validate(self, data):
           """Default validation (can be overridden)."""
           return data if data else "Invalid"

       @abstractmethod
       def save(self, data):
           """Subclasses implement saving logic."""
           pass

   class CSVProcessor(DataProcessor):
       def load(self, data):
           return f"CSV({data})"

       def transform(self, data):
           return data.upper()

       def save(self, data):
           return f"Saved to file.csv: {data}"

   class JSONProcessor(DataProcessor):
       def load(self, data):
           return f'{{"data": "{data}"}}'

       def transform(self, data):
           return data.replace('"', "'")

       def save(self, data):
           return f"Saved to file.json: {data}"

   # Use template method
   print("CSV Processing:")
   csv_proc = CSVProcessor()
   csv_proc.process("hello world")

   print("\nJSON Processing:")
   json_proc = JSONProcessor()
   json_proc.process("hello world")

**Output:**

::

   CSV Processing:
   Processing with CSVProcessor:
     1. Loaded: CSV(hello world)
     2. Transformed: CSV(HELLO WORLD)
     3. Validated: CSV(HELLO WORLD)
     4. Saved: Saved to file.csv: CSV(HELLO WORLD)

   JSON Processing:
   Processing with JSONProcessor:
     1. Loaded: {"data": "hello world"}
     2. Transformed: {'data': 'hello world'}
     3. Validated: {'data': 'hello world'}
     4. Saved: Saved to file.json: {'data': 'hello world'}

---

Check Your Understanding
-------------------------

.. mchoice:: oop_poly_mro_property_mcq
   :answer_a: Random order
   :answer_b: Alphabetical order
   :answer_c: Children before parents, left-to-right
   :answer_d: Parents before children
   :correct: c
   :feedback_a: MRO is deterministic!
   :feedback_b: Class names don't affect order.
   :feedback_c: Correct! C3 linearization ensures children come before parents, with left-to-right parent ordering.
   :feedback_d: Children always come before their parents in MRO.

   What is the fundamental property of Python's MRO?

.. mchoice:: oop_poly_duck_typing_mcq
   :answer_a: Type checking based on inheritance
   :answer_b: Treating objects based on their behavior, not type
   :answer_c: Only using duck-related classes
   :answer_d: A type checking system for databases
   :correct: b
   :feedback_a: That's nominal typing, not duck typing.
   :feedback_b: Correct! Duck typing means "if it has the right methods, use it" regardless of type.
   :feedback_c: The name is metaphorical ("if it walks like a duck...").
   :feedback_d: Duck typing is unrelated to databases.

   What is duck typing?

.. mchoice:: oop_poly_abc_purpose_mcq
   :answer_a: To provide default method implementations
   :answer_b: To define interfaces that subclasses must implement
   :answer_c: To make classes run faster
   :answer_d: To prevent inheritance
   :correct: b
   :feedback_a: ABCs can have default methods, but their main purpose is defining interfaces.
   :feedback_b: Correct! ABCs define abstract methods that subclasses must implement, creating contracts.
   :feedback_c: ABCs don't affect performance.
   :feedback_d: ABCs encourage inheritance with well-defined interfaces.

   What is the primary purpose of Abstract Base Classes (ABCs)?

.. mchoice:: oop_poly_protocol_vs_abc_mcq
   :answer_a: Protocols require inheritance, ABCs don't
   :answer_b: Protocols are checked at runtime, ABCs at compile time
   :answer_c: Protocols use structural typing, ABCs use nominal typing
   :answer_d: Protocols are faster than ABCs
   :correct: c
   :feedback_a: It's the opposite! ABCs require inheritance, Protocols don't.
   :feedback_b: It's the opposite! Protocols are checked statically, ABCs at runtime.
   :feedback_c: Correct! Protocols check structure (has the methods?), ABCs check inheritance.
   :feedback_d: Performance isn't the key difference.

   What's the main difference between Protocols and ABCs?

.. mchoice:: oop_poly_override_super_mcq
   :answer_a: Always call super() in overridden methods
   :answer_b: Never call super() in overridden methods
   :answer_c: Call super() when you want to extend parent behavior
   :answer_d: super() is only for __init__
   :correct: c
   :feedback_a: Not always—only when you want to keep parent behavior.
   :feedback_b: Sometimes you do want to call super() to extend behavior.
   :feedback_c: Correct! Call super() when you want to extend (not replace) parent behavior.
   :feedback_d: super() can be used in any method, not just __init__.

   When should you call ``super()`` in an overridden method?

---

Key Takeaways
-------------

.. important::
   **Section 6 Summary: MRO and Polymorphism**

   ✅ **MRO Deep Dive:**
      - C3 linearization algorithm
      - Properties: children before parents, left-to-right, no duplicates
      - Each class appears exactly once
      - Monotonic (preserves ordering consistency)
      - View with ``__mro__`` or ``.mro()``

   ✅ **Polymorphism Types:**
      - **Subtype:** Inheritance-based (method overriding)
      - **Duck typing:** Structural (if it has the method, use it)
      - **Protocols:** Static duck typing with type hints
      - **Generic:** Parametric polymorphism (``List[T]``)

   ✅ **Duck Typing vs Inheritance:**
      - **Duck typing:** Flexible, loose coupling, Pythonic
      - **Inheritance:** Explicit, type checking, clear hierarchy
      - Use duck typing by default, inheritance when needed

   ✅ **Method Overriding Patterns:**
      - **Complete override:** Replace parent behavior entirely
      - **Extend:** Call ``super()`` then add functionality
      - **Pre/post processing:** Add logic before/after parent

   ✅ **Abstract Base Classes:**
      - Define interfaces with ``@abstractmethod``
      - Can't instantiate ABC directly
      - Subclasses must implement all abstract methods
      - Use for explicit contracts

   ✅ **Protocols (Python 3.8+):**
      - Structural subtyping (no inheritance needed)
      - Static type checking
      - Zero coupling between protocol and implementers
      - More flexible than ABCs

   ✅ **Design Patterns:**
      - **Strategy:** Interchangeable algorithms
      - **Template Method:** Define algorithm structure
      - Both leverage polymorphism for flexibility

---

What's Next?
------------

Congratulations! You've completed all the instructional content for Advanced OOP Mastery! You now understand:

- ✅ Object and class introspection
- ✅ Type checking with isinstance and issubclass
- ✅ Identity vs equality (is vs ==)
- ✅ Class structure inspection
- ✅ Multiple inheritance and the diamond problem
- ✅ MRO mechanics and polymorphism

**In Section 7: Practice and Mastery**, you'll:

- Test your knowledge with comprehensive MCQs
- Solve Parsons problems on OOP concepts
- Build complete OOP systems from scratch
- Debug complex inheritance hierarchies
- Apply everything you've learned

Ready to prove your mastery? Let's finish strong! 🚀

---

.. note::
   **✅ Section 6 Complete!**

   You've learned:
   - [✓] MRO algorithm deep dive
   - [✓] C3 linearization properties
   - [✓] Polymorphism types and approaches
   - [✓] Duck typing vs inheritance polymorphism
   - [✓] Method overriding best practices
   - [✓] Abstract base classes (ABCs)
   - [✓] Protocols for structural typing
   - [✓] Real-world polymorphic patterns

   **Ready for practice and mastery?** → Continue to Section 7!