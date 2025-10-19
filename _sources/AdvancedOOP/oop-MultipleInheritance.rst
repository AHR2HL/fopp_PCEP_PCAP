..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 5: Multiple Inheritance and the Diamond Problem
========================================================

.. index:: multiple inheritance, diamond problem, cooperative inheritance

Introduction
------------

Most object-oriented languages support **single inheritance** — a class inherits from one parent. Python supports **multiple inheritance** — a class can inherit from multiple parent classes!

This powerful feature enables:

- **Code reuse** from multiple sources
- **Mixins** for adding functionality
- **Flexible class hierarchies**
- **Compositional design patterns**

But it also introduces challenges, most notably the **diamond problem**. Fortunately, Python has elegant solutions!

In this section, you'll learn:

- What multiple inheritance is and how it works
- The famous diamond problem
- How Python's MRO solves the diamond problem
- Using ``super()`` correctly in multiple inheritance
- The mixin pattern for composable functionality
- Best practices and common pitfalls
- When to use multiple inheritance (and when not to)

By the end, you'll understand one of Python's most powerful (and misunderstood) features!

---

What Is Multiple Inheritance?
------------------------------

.. index:: multiple inheritance definition, multiple parents

**Multiple inheritance** means a class can inherit from more than one parent class.

**Syntax:**

.. code-block:: python

   class Child(Parent1, Parent2, Parent3):
       pass

**Basic Example:**

.. activecode:: oop_multi_basic
   :language: python

   class Flyer:
       def fly(self):
           return "Flying through the air!"

   class Swimmer:
       def swim(self):
           return "Swimming through water!"

   class Duck(Flyer, Swimmer):
       """A duck can both fly and swim!"""
       def quack(self):
           return "Quack!"

   # Create a duck
   donald = Duck()

   # Duck has methods from both parents
   print(donald.fly())
   print(donald.swim())
   print(donald.quack())

   # Check the inheritance
   print(f"\nDuck inherits from: {Duck.__bases__}")
   print(f"Duck's MRO: {[c.__name__ for c in Duck.__mro__]}")

**Output:**

::

   Flying through the air!
   Swimming through water!
   Quack!

   Duck inherits from: (<class '__main__.Flyer'>, <class '__main__.Swimmer'>)
   Duck's MRO: ['Duck', 'Flyer', 'Swimmer', 'object']

.. important::
   **Multiple Inheritance Benefits:**

   ✅ Inherit behavior from multiple sources
   ✅ Compose functionality from different classes
   ✅ Model complex real-world relationships
   ✅ Create flexible, reusable mixins

---

The Diamond Problem
--------------------

.. index:: diamond problem, diamond inheritance, ambiguity

The **diamond problem** occurs when a class inherits from two classes that share a common ancestor.

**The Diamond Structure:**

::

       Animal
       /    \
      /      \
   Flyer    Swimmer
      \      /
       \    /
        Duck

**Problem:** If both ``Flyer`` and ``Swimmer`` override a method from ``Animal``, which one should ``Duck`` use?

.. activecode:: oop_multi_diamond_problem
   :language: python

   class Animal:
       def move(self):
           return "Moving somehow"

       def describe(self):
           return "I am an animal"

   class Flyer(Animal):
       def move(self):
           return "Flying"

   class Swimmer(Animal):
       def move(self):
           return "Swimming"

   class Duck(Flyer, Swimmer):
       pass

   # The diamond problem: which move() gets called?
   donald = Duck()
   print(f"donald.move(): {donald.move()}")

   # Python's solution: Use MRO!
   print(f"\nDuck's MRO: {[c.__name__ for c in Duck.__mro__]}")
   print("↑ Python searches left-to-right, depth-first")
   print(f"\nResult: Uses Flyer.move() because Flyer comes first in MRO")

**Output:**

::

   donald.move(): Flying

   Duck's MRO: ['Duck', 'Flyer', 'Swimmer', 'Animal', 'object']
   ↑ Python searches left-to-right, depth-first

   Result: Uses Flyer.move() because Flyer comes first in MRO

.. note::
   **Python's Solution to the Diamond Problem:**

   - Uses **C3 linearization** algorithm to create MRO
   - Ensures each class appears only once
   - Preserves parent order (left-to-right)
   - Guarantees consistent, predictable behavior

---

Visualizing the Diamond Problem
---------------------------------

.. activecode:: oop_multi_diamond_visual
   :language: python

   class A:
       def method(self):
           return "A.method"

   class B(A):
       def method(self):
           return "B.method"

   class C(A):
       def method(self):
           return "C.method"

   class D(B, C):
       pass  # Doesn't override method

   print("Diamond Structure:")
   print("       A")
   print("      / \\")
   print("     B   C")
   print("      \\ /")
   print("       D")

   print(f"\nMRO (Method Resolution Order):")
   for i, cls in enumerate(D.__mro__):
       has_method = 'method' in cls.__dict__
       marker = " ← method defined here" if has_method else ""
       print(f"  {i}. {cls.__name__}{marker}")

   d = D()
   print(f"\nd.method() returns: {d.method()}")
   print(f"↑ Found in {D.__mro__[1].__name__} (first class with method)")

**Output:**

::

   Diamond Structure:
          A
         / \
        B   C
         \ /
          D

   MRO (Method Resolution Order):
     0. D
     1. B ← method defined here
     2. C ← method defined here
     3. A ← method defined here
     4. object

   d.method() returns: B.method
   ↑ Found in B (first class with method)

---

Understanding C3 Linearization
-------------------------------

.. index:: C3 linearization, MRO algorithm, linearization

Python uses the **C3 linearization** algorithm to compute MRO. The rules are:

1. **Children before parents** — a class comes before its parent(s)
2. **Left-to-right** — parents are searched in declaration order
3. **No duplicates** — each class appears exactly once
4. **Consistent** — monotonic (preserves ordering)

.. activecode:: oop_multi_c3_example
   :language: python

   class A:
       pass

   class B:
       pass

   class C(A, B):
       pass

   class D(B, A):  # Different order!
       pass

   # Compare MROs
   print("C(A, B) MRO:")
   print(f"  {[cls.__name__ for cls in C.__mro__]}")

   print("\nD(B, A) MRO:")
   print(f"  {[cls.__name__ for cls in D.__mro__]}")

   print("\n💡 Notice: Parent order matters!")
   print("   C searches A before B")
   print("   D searches B before A")

**Output:**

::

   C(A, B) MRO:
     ['C', 'A', 'B', 'object']

   D(B, A) MRO:
     ['D', 'B', 'A', 'object']

   💡 Notice: Parent order matters!
      C searches A before B
      D searches B before A

---

**Invalid MRO (Inconsistent Hierarchy):**

.. activecode:: oop_multi_invalid_mro
   :language: python

   class A:
       pass

   class B(A):
       pass

   # This creates an impossible MRO
   try:
       class C(A, B):  # Can't put A before B when B inherits from A!
           pass
   except TypeError as e:
       print(f"❌ MRO Error: {e}")
       print("\nWhy this fails:")
       print("  • C says: A before B (C(A, B))")
       print("  • B says: A after B (B(A))")
       print("  • Contradiction! Can't create consistent MRO")

**Output:**

::

   ❌ MRO Error: Cannot create a consistent method resolution order (MRO) for bases A, B

   Why this fails:
     • C says: A before B (C(A, B))
     • B says: A after B (B(A))
     • Contradiction! Can't create consistent MRO

---

Using super() with Multiple Inheritance
----------------------------------------

.. index:: super() function, cooperative inheritance, super in multiple inheritance

``super()`` in multiple inheritance is **cooperative** — it calls the **next** class in the MRO, not necessarily the parent!

**Wrong Approach: Direct Parent Calls**

.. activecode:: oop_multi_super_wrong
   :language: python

   class A:
       def __init__(self):
           print("A.__init__")

   class B(A):
       def __init__(self):
           print("B.__init__")
           A.__init__(self)  # ❌ Direct call

   class C(A):
       def __init__(self):
           print("C.__init__")
           A.__init__(self)  # ❌ Direct call

   class D(B, C):
       def __init__(self):
           print("D.__init__")
           B.__init__(self)  # ❌ Direct call
           C.__init__(self)  # ❌ Direct call

   print("❌ Wrong approach (direct calls):")
   d = D()
   print("\n⚠️  A.__init__ called TWICE! Not cooperative!")

**Output:**

::

   ❌ Wrong approach (direct calls):
   D.__init__
   B.__init__
   A.__init__
   C.__init__
   A.__init__

   ⚠️  A.__init__ called TWICE! Not cooperative!

---

**Correct Approach: Using super()**

.. activecode:: oop_multi_super_correct
   :language: python

   class A:
       def __init__(self):
           print("A.__init__")
           super().__init__()  # Even A uses super()!

   class B(A):
       def __init__(self):
           print("B.__init__")
           super().__init__()  # Calls next in MRO (C)

   class C(A):
       def __init__(self):
           print("C.__init__")
           super().__init__()  # Calls next in MRO (A)

   class D(B, C):
       def __init__(self):
           print("D.__init__")
           super().__init__()  # Calls next in MRO (B)

   print("✅ Correct approach (super):")
   print(f"MRO: {[c.__name__ for c in D.__mro__]}\n")

   d = D()

   print("\n✅ A.__init__ called ONCE! Cooperative!")

**Output:**

::

   ✅ Correct approach (super):
   MRO: ['D', 'B', 'C', 'A', 'object']

   D.__init__
   B.__init__
   C.__init__
   A.__init__

   ✅ A.__init__ called ONCE! Cooperative!

.. important::
   **Super() in Multiple Inheritance:**

   - ``super()`` doesn't call the "parent" — it calls the **next class in MRO**
   - ALL classes should use ``super()`` (even if they seem to have no parent)
   - This enables **cooperative multiple inheritance**
   - Each ``__init__`` is called exactly once
   - Order follows the MRO

---

**Super() with Arguments**

.. activecode:: oop_multi_super_args
   :language: python

   class Shape:
       def __init__(self, color):
           print(f"Shape.__init__(color={color})")
           self.color = color
           super().__init__()

   class Border:
       def __init__(self, width):
           print(f"Border.__init__(width={width})")
           self.width = width
           super().__init__()

   class Rectangle(Shape, Border):
       def __init__(self, color, width, length, height):
           print(f"Rectangle.__init__")
           self.length = length
           self.height = height
           # Pass appropriate args to each parent
           super().__init__(color=color, width=width)

   print("Creating Rectangle:")
   rect = Rectangle(color="red", width=2, length=10, height=5)

   print(f"\nAttributes:")
   print(f"  Color: {rect.color}")
   print(f"  Width: {rect.width}")
   print(f"  Length: {rect.length}")
   print(f"  Height: {rect.height}")

**Output:**

::

   Creating Rectangle:
   Rectangle.__init__
   Shape.__init__(color=red)
   Border.__init__(width=2)

   Attributes:
     Color: red
     Width: 2
     Length: 10
     Height: 5

.. note::
   **Best Practice for super() with Arguments:**

   - Use ``**kwargs`` for flexible argument passing
   - Each class extracts what it needs
   - Pass remaining kwargs to ``super()``
   - This makes the chain cooperative

---

The Mixin Pattern
------------------

.. index:: mixin pattern, mixin classes, compositional inheritance

A **mixin** is a class designed to add specific functionality to other classes through multiple inheritance.

**Mixin Characteristics:**

- Provides methods but doesn't stand alone
- Not meant to be instantiated directly
- Adds a specific capability (logging, timing, caching, etc.)
- Often has "Mixin" in the name (by convention)

.. activecode:: oop_multi_mixin_basic
   :language: python

   # Mixins provide specific capabilities
   class LoggerMixin:
       """Adds logging capability to any class."""
       def log(self, message):
           class_name = self.__class__.__name__
           print(f"[{class_name}] {message}")

   class TimestampMixin:
       """Adds timestamp tracking."""
       def __init__(self, *args, **kwargs):
           super().__init__(*args, **kwargs)
           from datetime import datetime
           self.created_at = datetime.now()

       def get_age(self):
           from datetime import datetime
           age = datetime.now() - self.created_at
           return f"{age.total_seconds():.2f} seconds"

   # Base class
   class Document:
       def __init__(self, title):
           self.title = title
           super().__init__()

   # Compose functionality with mixins
   class RichDocument(LoggerMixin, TimestampMixin, Document):
       def __init__(self, title):
           super().__init__(title=title)
           self.log(f"Created document: {title}")

   # Use the composed class
   doc = RichDocument("My Report")
   doc.log("Opening document")
   doc.log("Editing document")

   print(f"\nDocument age: {doc.get_age()}")

**Output:**

::

   [RichDocument] Created document: My Report
   [RichDocument] Opening document
   [RichDocument] Editing document

   Document age: 0.00 seconds

---

**Real-World Mixin Example: JSON Serialization**

.. activecode:: oop_multi_mixin_json
   :language: python

   import json

   class JSONMixin:
       """Adds JSON serialization capability."""
       def to_json(self):
           """Convert object to JSON string."""
           data = {}
           for key, value in self.__dict__.items():
               if not key.startswith('_'):
                   data[key] = value
           return json.dumps(data, indent=2)

       @classmethod
       def from_json(cls, json_string):
           """Create object from JSON string."""
           data = json.loads(json_string)
           return cls(**data)

   class User(JSONMixin):
       def __init__(self, username, email, age):
           self.username = username
           self.email = email
           self.age = age

   # Use JSON functionality
   user = User("alice", "alice@example.com", 30)

   # Serialize to JSON
   json_data = user.to_json()
   print("Serialized:")
   print(json_data)

   # Deserialize from JSON
   user2 = User.from_json(json_data)
   print(f"\nDeserialized:")
   print(f"  Username: {user2.username}")
   print(f"  Email: {user2.email}")
   print(f"  Age: {user2.age}")

**Output:**

::

   Serialized:
   {
     "username": "alice",
     "email": "alice@example.com",
     "age": 30
   }

   Deserialized:
     Username: alice
     Email: alice@example.com
     Age: 30

---

**Multiple Mixins Example**

.. activecode:: oop_multi_multiple_mixins
   :language: python

   class ReprMixin:
       """Provides a nice __repr__."""
       def __repr__(self):
           class_name = self.__class__.__name__
           attrs = ', '.join(f"{k}={v!r}" for k, v in self.__dict__.items())
           return f"{class_name}({attrs})"

   class EqualityMixin:
       """Provides equality comparison."""
       def __eq__(self, other):
           if not isinstance(other, self.__class__):
               return False
           return self.__dict__ == other.__dict__

   class HashMixin:
       """Provides hashing capability."""
       def __hash__(self):
           return hash(tuple(sorted(self.__dict__.items())))

   # Combine multiple mixins
   class Point(ReprMixin, EqualityMixin, HashMixin):
       def __init__(self, x, y):
           self.x = x
           self.y = y

   # Test the combined functionality
   p1 = Point(3, 4)
   p2 = Point(3, 4)
   p3 = Point(5, 6)

   print(f"Repr: {repr(p1)}")
   print(f"\nEquality:")
   print(f"  p1 == p2? {p1 == p2}")
   print(f"  p1 == p3? {p1 == p3}")

   print(f"\nHashing (can use in sets):")
   points = {p1, p2, p3}  # p1 and p2 are equal, so only one is kept
   print(f"  Unique points: {len(points)}")
   print(f"  Set: {points}")

**Output:**

::

   Repr: Point(x=3, y=4)

   Equality:
     p1 == p2? True
     p1 == p3? False

   Hashing (can use in sets):
     Unique points: 2
     Set: {Point(x=5, y=6), Point(x=3, y=4)}

---

Best Practices
--------------

.. index:: multiple inheritance best practices, MI guidelines

**✅ DO: Use Mixins for Composable Functionality**

.. code-block:: python

   # Good: Mixins add specific capabilities
   class LoggableMixin:
       def log(self, msg): ...

   class CacheableMixin:
       def cache_get(self, key): ...

   class MyClass(LoggableMixin, CacheableMixin, BaseClass):
       pass

**✅ DO: Use super() Consistently**

.. code-block:: python

   # Good: All classes use super()
   class A:
       def __init__(self):
           super().__init__()  # Even base classes!

   class B(A):
       def __init__(self):
           super().__init__()  # Cooperative

**✅ DO: Name Mixins with "Mixin" Suffix**

.. code-block:: python

   # Good: Clear naming convention
   class JSONMixin: ...
   class TimestampMixin: ...
   class ValidationMixin: ...

**✅ DO: Put Mixins Before Base Classes**

.. code-block:: python

   # Good: Mixins first, then base class
   class MyClass(LogMixin, JSONMixin, BaseClass):
       pass

---

**❌ DON'T: Create Deep Diamond Hierarchies**

.. code-block:: python

   # Bad: Complex diamond structure
   class A: pass
   class B(A): pass
   class C(A): pass
   class D(B, C): pass
   class E(D): pass
   class F(D): pass
   class G(E, F): pass  # Too complex!

**❌ DON'T: Use Multiple Inheritance for "Is-A" Relationships**

.. code-block:: python

   # Bad: Dog is-a Animal, is-a Pet (awkward)
   class Dog(Animal, Pet):  # Confusing!
       pass

   # Good: Dog is-a Animal, has-a Owner
   class Dog(Animal):
       def __init__(self, owner):
           self.owner = owner  # Composition

**❌ DON'T: Mix State and Mixins**

.. code-block:: python

   # Bad: Mixin has state
   class BadMixin:
       def __init__(self):
           self.count = 0  # State in mixin!

       def increment(self):
           self.count += 1

   # Good: Mixin is stateless
   class GoodMixin:
       def increment(self):
           self.count = getattr(self, 'count', 0) + 1

---

Common Pitfalls
---------------

.. index:: multiple inheritance pitfalls, MI mistakes

**Pitfall 1: Forgetting super() in Base Class**

.. activecode:: oop_multi_pitfall_super
   :language: python

   class A:
       def __init__(self):
           print("A.__init__")
           # ❌ Forgot super().__init__()

   class B:
       def __init__(self):
           print("B.__init__")
           super().__init__()

   class C(A, B):
       def __init__(self):
           print("C.__init__")
           super().__init__()

   print("❌ A doesn't call super():")
   print(f"MRO: {[cls.__name__ for cls in C.__mro__]}\n")

   c = C()
   print("\n⚠️  B.__init__ was never called! Chain broken!")

**Output:**

::

   ❌ A doesn't call super():
   MRO: ['C', 'A', 'B', 'object']

   C.__init__
   A.__init__

   ⚠️  B.__init__ was never called! Chain broken!

---

**Pitfall 2: Method Name Conflicts**

.. activecode:: oop_multi_pitfall_conflicts
   :language: python

   class Flyer:
       def move(self):
           return "Flying"

   class Swimmer:
       def move(self):
           return "Swimming"

   class Duck(Flyer, Swimmer):
       pass  # Which move() will be used?

   duck = Duck()
   print(f"duck.move(): {duck.move()}")
   print(f"\n⚠️  Only gets Flyer's move()!")
   print(f"   Swimmer's move() is hidden by MRO")
   print(f"   MRO: {[c.__name__ for c in Duck.__mro__]}")

   # Solution: Override and call both explicitly
   class BetterDuck(Flyer, Swimmer):
       def move(self, method="both"):
           if method == "fly":
               return Flyer.move(self)
           elif method == "swim":
               return Swimmer.move(self)
           else:
               return f"{Flyer.move(self)} and {Swimmer.move(self)}"

   print("\n✅ Better solution:")
   better = BetterDuck()
   print(f"  Flying: {better.move('fly')}")
   print(f"  Swimming: {better.move('swim')}")
   print(f"  Both: {better.move('both')}")

**Output:**

::

   duck.move(): Flying

   ⚠️  Only gets Flyer's move()!
      Swimmer's move() is hidden by MRO
      MRO: ['Duck', 'Flyer', 'Swimmer', 'object']

   ✅ Better solution:
     Flying: Flying
     Swimming: Swimming
     Both: Flying and Swimming

---

**Pitfall 3: Mixin Order Matters**

.. activecode:: oop_multi_pitfall_order
   :language: python

   class Mixin1:
       def process(self):
           return "Mixin1"

   class Mixin2:
       def process(self):
           return "Mixin2"

   class Base:
       def process(self):
           return "Base"

   # Different orders, different results
   class OrderA(Mixin1, Mixin2, Base):
       pass

   class OrderB(Mixin2, Mixin1, Base):
       pass

   print("Order matters!")
   print(f"OrderA.process(): {OrderA().process()}")
   print(f"  MRO: {[c.__name__ for c in OrderA.__mro__]}")

   print(f"\nOrderB.process(): {OrderB().process()}")
   print(f"  MRO: {[c.__name__ for c in OrderB.__mro__]}")

**Output:**

::

   Order matters!
   OrderA.process(): Mixin1
     MRO: ['OrderA', 'Mixin1', 'Mixin2', 'Base', 'object']

   OrderB.process(): Mixin2
     MRO: ['OrderB', 'Mixin2', 'Mixin1', 'Base', 'object']

---

When to Use Multiple Inheritance
---------------------------------

.. index:: when to use multiple inheritance, alternatives

.. list-table:: Multiple Inheritance Decision Guide
   :widths: 50 50
   :header-rows: 1

   * - **Use Multiple Inheritance For:**
     - **Consider Alternatives For:**
   * - ✅ Mixins (small, focused capabilities)
     - ⚠️ Complex "is-a" relationships
   * - ✅ Horizontal composition
     - ⚠️ Deep hierarchies
   * - ✅ Adding orthogonal features
     - ⚠️ State management
   * - ✅ Framework hooks and plugins
     - ⚠️ Business logic inheritance
   * - ✅ Interface-like protocols
     - ⚠️ When composition would be clearer

**Alternatives to Multiple Inheritance:**

1. **Composition** — "has-a" instead of "is-a"
2. **Delegation** — forward calls to contained objects
3. **Single inheritance** with interfaces/protocols
4. **Decorator pattern** — wrap objects dynamically

---

Check Your Understanding
-------------------------

.. mchoice:: oop_multi_diamond_solution_mcq
   :answer_a: Python raises an error
   :answer_b: Python randomly picks one
   :answer_c: Python uses MRO to determine order
   :answer_d: Both methods are called
   :correct: c
   :feedback_a: No error! Python handles this elegantly.
   :feedback_b: Python has a deterministic solution using MRO.
   :feedback_c: Correct! Python uses Method Resolution Order (MRO) determined by C3 linearization.
   :feedback_d: Only one method is called (the first in MRO).

   How does Python solve the diamond problem?

.. mchoice:: oop_multi_super_behavior_mcq
   :answer_a: Calls the direct parent class
   :answer_b: Calls the next class in the MRO
   :answer_c: Calls all parent classes
   :answer_d: Calls the base class (object)
   :correct: b
   :feedback_a: In multiple inheritance, super() doesn't always call the direct parent!
   :feedback_b: Correct! super() calls the next class in the MRO, enabling cooperative inheritance.
   :feedback_c: super() calls one class at a time; that class's super() calls the next.
   :feedback_d: It calls the next in MRO, not necessarily the ultimate base.

   In multiple inheritance, what does ``super()`` call?

.. mchoice:: oop_multi_mixin_purpose_mcq
   :answer_a: A mixin is any class with multiple parents
   :answer_b: A mixin adds specific functionality without being a standalone class
   :answer_c: A mixin is a class that prevents the diamond problem
   :answer_d: A mixin is Python's term for interfaces
   :correct: b
   :feedback_a: That describes any class with multiple inheritance.
   :feedback_b: Correct! Mixins add specific capabilities through inheritance but aren't meant to be used alone.
   :feedback_c: Mixins don't prevent the diamond problem; they're a design pattern.
   :feedback_d: Python doesn't have formal interfaces; mixins are different.

   What is a mixin class?

.. mchoice:: oop_multi_mro_order_mcq
   :answer_a: Random order
   :answer_b: Right-to-left, parents first
   :answer_c: Left-to-right, depth-first, each class once
   :answer_d: Alphabetical by class name
   :correct: c
   :feedback_a: MRO is deterministic and consistent!
   :feedback_b: Python goes left-to-right, not right-to-left.
   :feedback_c: Correct! C3 linearization ensures left-to-right, depth-first search with no duplicates.
   :feedback_d: Class names don't affect MRO order.

   What order does Python's MRO follow?

.. mchoice:: oop_multi_all_super_mcq
   :answer_a: Only the child class
   :answer_b: Only classes with siblings in the hierarchy
   :answer_c: All classes, including the base class
   :answer_d: Only classes with multiple parents
   :correct: c
   :feedback_a: Even base classes should call super() for cooperative inheritance!
   :feedback_b: All classes should use super(), not just those with siblings.
   :feedback_c: Correct! For cooperative MI, ALL classes should call super(), even the base class.
   :feedback_d: Even single-inheritance classes should use super() for consistency.

   In cooperative multiple inheritance, which classes should call ``super()``?

---

Key Takeaways
-------------

.. important::
   **Section 5 Summary: Multiple Inheritance**

   ✅ **Multiple Inheritance Basics:**
      - Class can inherit from multiple parents
      - Syntax: ``class Child(Parent1, Parent2):``
      - Enables code reuse from multiple sources
      - More flexible than single inheritance

   ✅ **The Diamond Problem:**
      - Occurs when class inherits from two classes with common ancestor
      - Creates ambiguity about which method to use
      - Python solves it with MRO (Method Resolution Order)
      - C3 linearization ensures consistent, predictable behavior

   ✅ **Method Resolution Order (MRO):**
      - Determined by C3 linearization algorithm
      - Left-to-right, depth-first, no duplicates
      - View with ``__mro__`` or ``.mro()``
      - Each class appears exactly once

   ✅ **Super() in Multiple Inheritance:**
      - Calls **next** class in MRO, not necessarily parent
      - Enables **cooperative inheritance**
      - ALL classes should use ``super()`` (even base classes!)
      - Ensures each ``__init__`` called exactly once

   ✅ **The Mixin Pattern:**
      - Small classes that add specific functionality
      - Not meant to be instantiated alone
      - Named with "Mixin" suffix (convention)
      - Placed before base class in inheritance list

   ✅ **Best Practices:**
      - Use mixins for horizontal composition
      - Always use ``super()`` consistently
      - Avoid deep diamond hierarchies
      - Consider composition as alternative
      - Keep mixins stateless when possible

   ✅ **Common Pitfalls:**
      - Forgetting ``super()`` in base class
      - Method name conflicts
      - Mixin order matters (affects MRO)

---

What's Next?
------------

You now understand multiple inheritance, the diamond problem, and how Python elegantly solves it! Next, you'll explore **Method Resolution Order (MRO) and Polymorphism** in even greater depth.

**In Section 6: MRO and Polymorphism**, you'll learn:

- Deep dive into MRO algorithms
- Advanced polymorphism techniques
- Duck typing vs. inheritance polymorphism
- Method overriding best practices
- Abstract base classes and protocols
- Real-world polymorphic designs

Ready to master MRO and polymorphism? Let's go! 🚀

---

.. note::
   **✅ Section 5 Complete!**

   You've learned:
   - [✓] What multiple inheritance is
   - [✓] The diamond problem and its solution
   - [✓] C3 linearization and MRO
   - [✓] Using super() cooperatively
   - [✓] The mixin pattern
   - [✓] Best practices and pitfalls
   - [✓] When to use multiple inheritance

   **Ready for advanced MRO and polymorphism?** → Continue to Section 6!