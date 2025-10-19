..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 4: Class Introspection
===============================

.. index:: class introspection, __dict__, __module__, __bases__, __mro__

Introduction
------------

In Section 1, you learned to introspect objects. Now you'll learn to introspect **classes themselves** — examining their attributes, inheritance hierarchy, and internal structure.

Python classes are objects too! And like any object, you can examine them at runtime to discover:

- **What attributes** a class has
- **Where the class** is defined (which module)
- **What classes** it inherits from
- **How inheritance** is resolved
- **The difference** between class and instance attributes

In this section, you'll learn:

- The ``__dict__`` attribute for viewing all attributes
- The ``__module__`` attribute for finding the class's origin
- The ``__bases__`` attribute for direct parent classes
- The ``__mro__`` attribute (Method Resolution Order)
- The ``vars()`` function as an alternative to ``__dict__``
- How to distinguish class attributes from instance attributes
- Practical use cases for class introspection

By the end, you'll be able to peek inside any class and understand its complete structure!

---

The ``__dict__`` Attribute
---------------------------

.. index:: __dict__ attribute, attribute dictionary, namespace

Every object (including classes) has a ``__dict__`` attribute — a dictionary containing all its attributes and their values.

**Basic Example:**

.. activecode:: oop_class_dict_basic
   :language: python

   class Dog:
       species = "Canis familiaris"  # Class attribute

       def __init__(self, name, age):
           self.name = name          # Instance attributes
           self.age = age

       def bark(self):
           return f"{self.name} says Woof!"

   # Create an instance
   buddy = Dog("Buddy", 3)

   # Examine the CLASS __dict__
   print("Class __dict__ (selected keys):")
   for key, value in Dog.__dict__.items():
       if not key.startswith('__'):
           print(f"  {key}: {value}")

   # Examine the INSTANCE __dict__
   print("\nInstance __dict__:")
   print(f"  {buddy.__dict__}")

**Output:**

::

   Class __dict__ (selected keys):
     species: Canis familiaris
     bark: <function Dog.bark at 0x...>

   Instance __dict__:
     {'name': 'Buddy', 'age': 3}

.. important::
   **Key Observations:**

   - **Class ``__dict__``** contains class attributes and methods
   - **Instance ``__dict__``** contains only instance attributes
   - Methods are stored in the class, not each instance
   - This saves memory — one method definition for all instances!

---

**Class vs Instance Attributes**

.. activecode:: oop_class_dict_class_vs_instance
   :language: python

   class Counter:
       count = 0  # Class attribute (shared by all instances)

       def __init__(self, name):
           self.name = name           # Instance attribute (unique to each instance)
           Counter.count += 1

   # Create instances
   c1 = Counter("First")
   c2 = Counter("Second")
   c3 = Counter("Third")

   print("Class attributes (Counter.__dict__):")
   print(f"  count: {Counter.__dict__['count']}")

   print("\nInstance attributes:")
   print(f"  c1.__dict__: {c1.__dict__}")
   print(f"  c2.__dict__: {c2.__dict__}")
   print(f"  c3.__dict__: {c3.__dict__}")

   # Accessing class attribute through instances
   print(f"\nAccessing class attribute through instances:")
   print(f"  c1.count: {c1.count}")
   print(f"  c2.count: {c2.count}")
   print(f"  c3.count: {c3.count}")
   print(f"  All see the same class attribute!")

**Output:**

::

   Class attributes (Counter.__dict__):
     count: 3

   Instance attributes:
     c1.__dict__: {'name': 'First'}
     c2.__dict__: {'name': 'Second'}
     c3.__dict__: {'name': 'Third'}

   Accessing class attribute through instances:
     c1.count: 3
     c2.count: 3
     c3.count: 3
     All see the same class attribute!

---

**Modifying ``__dict__`` Directly**

.. activecode:: oop_class_dict_modify
   :language: python

   class Person:
       def __init__(self, name):
           self.name = name

   alice = Person("Alice")

   print(f"Initial: {alice.__dict__}")

   # Add attributes directly to __dict__
   alice.__dict__['age'] = 30
   alice.__dict__['city'] = 'NYC'

   print(f"After modification: {alice.__dict__}")

   # Access normally
   print(f"\nAccessing normally:")
   print(f"  alice.name: {alice.name}")
   print(f"  alice.age: {alice.age}")
   print(f"  alice.city: {alice.city}")

   # This is equivalent to:
   bob = Person("Bob")
   bob.age = 25
   bob.city = 'LA'

   print(f"\nBob's __dict__: {bob.__dict__}")

**Output:**

::

   Initial: {'name': 'Alice'}
   After modification: {'name': 'Alice', 'age': 30, 'city': 'NYC'}

   Accessing normally:
     alice.name: Alice
     alice.age: 30
     alice.city: NYC

   Bob's __dict__: {'name': 'Bob', 'age': 25, 'city': 'LA'}

.. note::
   **When to access ``__dict__`` directly:**

   - Debugging and inspection
   - Metaprogramming and frameworks
   - Serialization/deserialization
   - Dynamic attribute manipulation

   **Normal code:** Use ``obj.attr`` syntax instead!

---

The ``vars()`` Function
------------------------

.. index:: vars function, attribute dictionary

**``vars([object])``** is a built-in function that returns ``__dict__``:

.. activecode:: oop_class_vars_function
   :language: python

   class Car:
       wheels = 4

       def __init__(self, brand, model):
           self.brand = brand
           self.model = model

   my_car = Car("Toyota", "Camry")

   # vars() is equivalent to __dict__
   print(f"vars(my_car): {vars(my_car)}")
   print(f"my_car.__dict__: {my_car.__dict__}")
   print(f"Same? {vars(my_car) is my_car.__dict__}")

   # vars() also works with classes
   print(f"\nvars(Car) has 'wheels'? {'wheels' in vars(Car)}")
   print(f"vars(Car) has 'brand'? {'brand' in vars(Car)}")

**Output:**

::

   vars(my_car): {'brand': 'Toyota', 'model': 'Camry'}
   my_car.__dict__: {'brand': 'Toyota', 'model': 'Camry'}
   Same? True

   vars(Car) has 'wheels'? True
   vars(Car) has 'brand'? False

.. important::
   ``vars(obj)`` is just a cleaner way to access ``obj.__dict__``:

   - ✅ More readable
   - ✅ Shorter
   - ✅ Pythonic

   They return the **exact same object**, not a copy!

---

The ``__module__`` Attribute
-----------------------------

.. index:: __module__ attribute, module name, class origin

Every class has a ``__module__`` attribute that tells you which module it's defined in.

.. activecode:: oop_class_module_basic
   :language: python

   class MyClass:
       pass

   # Check where MyClass is defined
   print(f"MyClass.__module__: {MyClass.__module__}")

   # Built-in types
   print(f"\nBuilt-in types:")
   print(f"  str.__module__: {str.__module__}")
   print(f"  list.__module__: {list.__module__}")
   print(f"  dict.__module__: {dict.__module__}")

   # Classes from standard library
   from collections import namedtuple
   from pathlib import Path

   print(f"\nStandard library:")
   print(f"  Path.__module__: {Path.__module__}")

**Output:**

::

   MyClass.__module__: __main__

   Built-in types:
     str.__module__: builtins
     list.__module__: builtins
     dict.__module__: builtins

   Standard library:
     Path.__module__: pathlib

.. note::
   - ``__main__`` means the class is defined in the script you're running
   - ``builtins`` means it's a built-in Python type
   - Other values show the module name (e.g., ``pathlib``, ``collections``)

---

**Practical Use: Dynamic Import**

.. activecode:: oop_class_module_dynamic
   :language: python

   def get_class_info(cls):
       """Get comprehensive info about a class."""
       return {
           'name': cls.__name__,
           'module': cls.__module__,
           'qualname': cls.__qualname__,
           'full_name': f"{cls.__module__}.{cls.__name__}"
       }

   class Animal:
       pass

   class Dog(Animal):
       pass

   # Inspect classes
   print("Animal info:")
   for key, value in get_class_info(Animal).items():
       print(f"  {key}: {value}")

   print("\nDog info:")
   for key, value in get_class_info(Dog).items():
       print(f"  {key}: {value}")

   # Built-in type
   print("\nlist info:")
   for key, value in get_class_info(list).items():
       print(f"  {key}: {value}")

**Output:**

::

   Animal info:
     name: Animal
     module: __main__
     qualname: Animal
     full_name: __main__.Animal

   Dog info:
     name: Dog
     module: __main__
     qualname: Dog
     full_name: __main__.Dog

   list info:
     name: list
     module: builtins
     qualname: list
     full_name: builtins.list

---

The ``__bases__`` Attribute
----------------------------

.. index:: __bases__ attribute, parent classes, direct inheritance

**``__bases__``** is a tuple containing the **direct parent classes** of a class.

.. activecode:: oop_class_bases_basic
   :language: python

   class Animal:
       pass

   class Mammal(Animal):
       pass

   class Bird(Animal):
       pass

   class Dog(Mammal):
       pass

   class Bat(Mammal, Bird):  # Multiple inheritance
       pass

   # Examine inheritance
   print(f"Animal.__bases__: {Animal.__bases__}")
   print(f"Mammal.__bases__: {Mammal.__bases__}")
   print(f"Dog.__bases__: {Dog.__bases__}")
   print(f"Bat.__bases__: {Bat.__bases__}")  # Multiple parents!

   # Access parent class attributes
   print(f"\nParent class names:")
   for base in Bat.__bases__:
       print(f"  {base.__name__}")

**Output:**

::

   Animal.__bases__: (<class 'object'>,)
   Mammal.__bases__: (<class '__main__.Animal'>,)
   Dog.__bases__: (<class '__main__.Mammal'>,)
   Bat.__bases__: (<class '__main__.Mammal'>, <class '__main__.Bird'>)

   Parent class names:
     Mammal
     Bird

.. important::
   **Key Points:**

   - ``__bases__`` is a **tuple** (immutable)
   - Contains **direct** parents only (not grandparents)
   - Every class ultimately inherits from ``object``
   - Multiple inheritance shows all parent classes

---

**Traversing the Inheritance Tree**

.. activecode:: oop_class_bases_traverse
   :language: python

   def show_inheritance_tree(cls, indent=0):
       """Recursively display inheritance tree."""
       print("  " * indent + f"└─ {cls.__name__}")
       for base in cls.__bases__:
           if base is not object:  # Stop at object
               show_inheritance_tree(base, indent + 1)

   class Animal:
       pass

   class Mammal(Animal):
       pass

   class Reptile(Animal):
       pass

   class Dog(Mammal):
       pass

   class Cat(Mammal):
       pass

   class Snake(Reptile):
       pass

   # Show trees
   print("Dog inheritance tree:")
   show_inheritance_tree(Dog)

   print("\nCat inheritance tree:")
   show_inheritance_tree(Cat)

   print("\nSnake inheritance tree:")
   show_inheritance_tree(Snake)

**Output:**

::

   Dog inheritance tree:
   └─ Dog
     └─ Mammal
       └─ Animal

   Cat inheritance tree:
   └─ Cat
     └─ Mammal
       └─ Animal

   Snake inheritance tree:
   └─ Snake
     └─ Reptile
       └─ Animal

---

The ``__mro__`` Attribute
--------------------------

.. index:: __mro__ attribute, method resolution order, MRO, C3 linearization

**``__mro__``** (Method Resolution Order) is a tuple showing the **complete inheritance chain** in the order Python searches for methods.

.. activecode:: oop_class_mro_basic
   :language: python

   class A:
       pass

   class B(A):
       pass

   class C(A):
       pass

   class D(B, C):
       pass

   # Examine MRO
   print("Method Resolution Order:")
   print(f"\nA.__mro__:")
   for i, cls in enumerate(A.__mro__):
       print(f"  {i}. {cls.__name__}")

   print(f"\nD.__mro__:")
   for i, cls in enumerate(D.__mro__):
       print(f"  {i}. {cls.__name__}")

   # Alternative: use .mro() method
   print(f"\nD.mro() (same as __mro__):")
   for i, cls in enumerate(D.mro()):
       print(f"  {i}. {cls.__name__}")

**Output:**

::

   Method Resolution Order:

   A.__mro__:
     0. A
     1. object

   D.__mro__:
     0. D
     1. B
     2. C
     3. A
     4. object

   D.mro() (same as __mro__):
     0. D
     1. B
     2. C
     3. A
     4. object

.. note::
   **Understanding MRO:**

   When you call ``d.method()``, Python searches for ``method`` in this order:

   1. ``D`` (the instance's class)
   2. ``B`` (first parent)
   3. ``C`` (second parent)
   4. ``A`` (common ancestor)
   5. ``object`` (ultimate base class)

   It stops at the **first** class that has the method.

---

**MRO with Method Overriding**

.. activecode:: oop_class_mro_override
   :language: python

   class Animal:
       def speak(self):
           return "Some sound"

   class Mammal(Animal):
       def speak(self):
           return "Mammal sound"

   class Dog(Mammal):
       def speak(self):
           return "Woof!"

   class Puppy(Dog):
       pass  # Doesn't override speak

   # Create instance
   puppy = Puppy()

   # Show MRO
   print("Puppy MRO:")
   for cls in Puppy.__mro__:
       print(f"  - {cls.__name__}", end="")
       if hasattr(cls, 'speak') and 'speak' in cls.__dict__:
           print(f" (has speak method)")
       else:
           print()

   # Call speak - which one gets called?
   print(f"\npuppy.speak(): {puppy.speak()}")
   print(f"↑ This is from Dog class (first in MRO with speak method)")

**Output:**

::

   Puppy MRO:
     - Puppy
     - Dog (has speak method)
     - Mammal (has speak method)
     - Animal (has speak method)
     - object

   puppy.speak(): Woof!
   ↑ This is from Dog class (first in MRO with speak method)

---

**The Diamond Problem and MRO**

.. activecode:: oop_class_mro_diamond
   :language: python

   class A:
       def method(self):
           return "A"

   class B(A):
       def method(self):
           return "B"

   class C(A):
       def method(self):
           return "C"

   class D(B, C):
       pass

   # The diamond problem:
   #       A
   #      / \
   #     B   C
   #      \ /
   #       D

   print("Diamond inheritance structure:")
   print("       A")
   print("      / \\")
   print("     B   C")
   print("      \\ /")
   print("       D")

   print(f"\nD's MRO:")
   for cls in D.__mro__:
       print(f"  → {cls.__name__}")

   d = D()
   print(f"\nd.method(): {d.method()}")
   print(f"Resolved to: {type(d).method}")

   print("\n💡 Python's C3 linearization ensures:")
   print("   - Each class appears only once")
   print("   - Parent order is preserved")
   print("   - Consistent and predictable")

**Output:**

::

   Diamond inheritance structure:
          A
         / \
        B   C
         \ /
          D

   D's MRO:
     → D
     → B
     → C
     → A
     → object

   d.method(): B
   Resolved to: <function B.method at 0x...>

   💡 Python's C3 linearization ensures:
      - Each class appears only once
      - Parent order is preserved
      - Consistent and predictable

---

Combining Introspection Tools
------------------------------

.. index:: class inspector, comprehensive introspection

**Building a Complete Class Inspector:**

.. activecode:: oop_class_inspector_complete
   :language: python

   def inspect_class(cls):
       """Comprehensive class inspection tool."""
       print(f"{'='*60}")
       print(f"CLASS INSPECTION: {cls.__name__}")
       print(f"{'='*60}")

       # Basic info
       print(f"\n📋 Basic Info:")
       print(f"   Module: {cls.__module__}")
       print(f"   Full name: {cls.__module__}.{cls.__name__}")

       # Inheritance
       print(f"\n🏗️  Inheritance:")
       print(f"   Direct parents: {', '.join(c.__name__ for c in cls.__bases__)}")
       print(f"   MRO: {' → '.join(c.__name__ for c in cls.__mro__)}")

       # Attributes
       class_attrs = {k: v for k, v in cls.__dict__.items()
                      if not k.startswith('_')}

       print(f"\n📦 Class Attributes:")
       if class_attrs:
           for name, value in class_attrs.items():
               attr_type = "method" if callable(value) else "attribute"
               print(f"   • {name}: {attr_type}")
       else:
           print(f"   (none)")

       # Methods (including inherited)
       print(f"\n🔧 All Methods (including inherited):")
       methods = [m for m in dir(cls) if callable(getattr(cls, m))
                  and not m.startswith('_')]
       for method in methods[:5]:  # Show first 5
           print(f"   • {method}()")
       if len(methods) > 5:
           print(f"   ... and {len(methods) - 5} more")

       print(f"\n{'='*60}\n")

   # Test with a class
   class Animal:
       species = "Unknown"

       def __init__(self, name):
           self.name = name

       def speak(self):
           return "Some sound"

   class Dog(Animal):
       species = "Canis familiaris"

       def speak(self):
           return "Woof!"

       def fetch(self):
           return "Fetching!"

   inspect_class(Dog)

**Output:**

::

   ============================================================
   CLASS INSPECTION: Dog
   ============================================================

   📋 Basic Info:
      Module: __main__
      Full name: __main__.Dog

   🏗️  Inheritance:
      Direct parents: Animal
      MRO: Dog → Animal → object

   📦 Class Attributes:
      • species: attribute
      • speak: method
      • fetch: method

   🔧 All Methods (including inherited):
      • fetch()
      • speak()

   ============================================================

---

Practical Use Cases
-------------------

.. index:: introspection use cases, serialization, debugging

**Use Case 1: Object Serialization**

.. activecode:: oop_class_use_serialization
   :language: python

   def serialize_object(obj):
       """Convert object to dictionary (simple serialization)."""
       result = {
           '_class': type(obj).__name__,
           '_module': type(obj).__module__,
           'attributes': {}
       }

       # Copy all instance attributes
       for key, value in vars(obj).items():
           # Only serialize simple types
           if isinstance(value, (int, float, str, bool, type(None))):
               result['attributes'][key] = value

       return result

   class Person:
       def __init__(self, name, age, city):
           self.name = name
           self.age = age
           self.city = city

   alice = Person("Alice", 30, "NYC")

   # Serialize
   serialized = serialize_object(alice)

   print("Serialized object:")
   import json
   print(json.dumps(serialized, indent=2))

**Output:**

::

   Serialized object:
   {
     "_class": "Person",
     "_module": "__main__",
     "attributes": {
       "name": "Alice",
       "age": 30,
       "city": "NYC"
     }
   }

---

**Use Case 2: Debugging Helper**

.. activecode:: oop_class_use_debugging
   :language: python

   def debug_object(obj, show_methods=False):
       """Print detailed object information for debugging."""
       print(f"🐛 DEBUG: {type(obj).__name__} instance")
       print(f"{'─'*50}")

       # Instance attributes
       print("Instance attributes:")
       for key, value in vars(obj).items():
           print(f"  {key} = {repr(value)}")

       # Class attributes
       print("\nClass attributes:")
       for key, value in type(obj).__dict__.items():
           if not key.startswith('_') and not callable(value):
               print(f"  {key} = {repr(value)}")

       # Methods
       if show_methods:
           print("\nMethods:")
           for key in dir(obj):
               if not key.startswith('_') and callable(getattr(obj, key)):
                   print(f"  {key}()")

       print(f"{'─'*50}\n")

   class Car:
       wheels = 4

       def __init__(self, brand, year):
           self.brand = brand
           self.year = year

       def drive(self):
           return f"Driving {self.brand}"

   my_car = Car("Toyota", 2020)
   debug_object(my_car, show_methods=True)

**Output:**

::

   🐛 DEBUG: Car instance
   ──────────────────────────────────────────────────
   Instance attributes:
     brand = 'Toyota'
     year = 2020

   Class attributes:
     wheels = 4

   Methods:
     drive()
   ──────────────────────────────────────────────────

---

**Use Case 3: Plugin Validator**

.. activecode:: oop_class_use_plugin_validator
   :language: python

   def validate_plugin(plugin_class):
       """Validate that a class meets plugin requirements."""
       errors = []

       # Check if it's a class
       if not isinstance(plugin_class, type):
           return ["Must be a class, not an instance"]

       # Required methods
       required_methods = ['initialize', 'execute', 'cleanup']

       for method in required_methods:
           if method not in plugin_class.__dict__:
               errors.append(f"Missing required method: {method}()")

       # Required attributes
       required_attrs = ['name', 'version']

       for attr in required_attrs:
           if attr not in plugin_class.__dict__:
               errors.append(f"Missing required attribute: {attr}")

       return errors if errors else ["✅ Valid plugin!"]

   # Valid plugin
   class GoodPlugin:
       name = "MyPlugin"
       version = "1.0"

       def initialize(self):
           pass

       def execute(self):
           pass

       def cleanup(self):
           pass

   # Invalid plugin
   class BadPlugin:
       name = "BadPlugin"

       def execute(self):
           pass

   print("GoodPlugin validation:")
   for msg in validate_plugin(GoodPlugin):
       print(f"  {msg}")

   print("\nBadPlugin validation:")
   for msg in validate_plugin(BadPlugin):
       print(f"  {msg}")

**Output:**

::

   GoodPlugin validation:
     ✅ Valid plugin!

   BadPlugin validation:
     Missing required attribute: version
     Missing required method: initialize()
     Missing required method: cleanup()

---

Check Your Understanding
-------------------------

.. mchoice:: oop_class_dict_contains_mcq
   :answer_a: Only instance attributes
   :answer_b: Only class attributes and methods
   :answer_c: Both instance and class attributes
   :answer_d: Only dunder methods
   :correct: b
   :feedback_a: Instance attributes are in the instance's __dict__.
   :feedback_b: Correct! The class __dict__ contains class attributes and methods, not instance attributes.
   :feedback_c: Instance attributes are stored in each instance's __dict__, not the class's.
   :feedback_d: Dunder methods are there, but regular attributes and methods are too.

   What does a class's ``__dict__`` contain?

.. mchoice:: oop_class_module_builtin_mcq
   :answer_a: __main__
   :answer_b: builtins
   :answer_c: system
   :answer_d: None
   :correct: b
   :feedback_a: __main__ is for classes in your script, not built-in types.
   :feedback_b: Correct! Built-in types like str, list, dict have __module__ = 'builtins'.
   :feedback_c: There's no 'system' module.
   :feedback_d: __module__ always has a value, never None.

   What is the ``__module__`` attribute for built-in types like ``str`` or ``list``?

.. mchoice:: oop_class_bases_contains_mcq
   :answer_a: All ancestor classes
   :answer_b: Only direct parent classes
   :answer_c: Only the object class
   :answer_d: All methods from parent classes
   :correct: b
   :feedback_a: That's __mro__, not __bases__.
   :feedback_b: Correct! __bases__ contains only the direct parent classes, not grandparents.
   :feedback_c: object might be there, but other direct parents are too.
   :feedback_d: __bases__ contains classes, not methods.

   What does ``__bases__`` contain?

.. mchoice:: oop_class_mro_purpose_mcq
   :answer_a: List of all methods in a class
   :answer_b: Order in which Python searches for attributes/methods
   :answer_c: List of base classes
   :answer_d: List of all attributes
   :correct: b
   :feedback_a: That would be dir(), not __mro__.
   :feedback_b: Correct! __mro__ shows the Method Resolution Order—how Python searches the inheritance chain.
   :feedback_c: That's __bases__, not __mro__.
   :feedback_d: That's __dict__ or dir(), not __mro__.

   What is the purpose of ``__mro__``?

.. mchoice:: oop_class_vars_vs_dict_mcq
   :answer_a: vars() is faster
   :answer_b: They return different objects
   :answer_c: vars() is just a cleaner way to access __dict__
   :answer_d: vars() only works with classes
   :correct: c
   :feedback_a: They return the exact same object, so speed is identical.
   :feedback_b: They return the exact same object!
   :feedback_c: Correct! vars(obj) and obj.__dict__ return the same object; vars() is just more readable.
   :feedback_d: vars() works with any object that has __dict__.

   What's the relationship between ``vars(obj)`` and ``obj.__dict__``?

---

Key Takeaways
-------------

.. important::
   **Section 4 Summary: Class Introspection**

   ✅ **The __dict__ Attribute:**
      - Dictionary of all attributes and their values
      - Class ``__dict__``: class attributes and methods
      - Instance ``__dict__``: instance attributes only
      - Can be modified directly (use carefully!)

   ✅ **The vars() Function:**
      - ``vars(obj)`` returns ``obj.__dict__``
      - More readable and Pythonic
      - Returns the same object, not a copy

   ✅ **The __module__ Attribute:**
      - Shows which module a class is defined in
      - ``__main__`` for script-defined classes
      - ``builtins`` for built-in types
      - Useful for dynamic imports and debugging

   ✅ **The __bases__ Attribute:**
      - Tuple of **direct** parent classes
      - Does not include grandparents
      - Empty tuple only for ``object``
      - Multiple inheritance shows all parents

   ✅ **The __mro__ Attribute:**
      - Method Resolution Order (complete inheritance chain)
      - Tuple showing search order for attributes/methods
      - Determined by C3 linearization algorithm
      - Use ``.mro()`` method as alternative

   ✅ **Practical Applications:**
      - Serialization and deserialization
      - Debugging and inspection tools
      - Plugin validation
      - Framework development
      - Dynamic code generation

---

What's Next?
------------

You now know how to introspect classes and understand their structure! Next, you'll explore **multiple inheritance** and the famous **diamond problem** in depth.

**In Section 5: Multiple Inheritance and the Diamond Problem**, you'll learn:

- How multiple inheritance works in Python
- The diamond problem explained
- How Python's MRO solves the problem
- ``super()`` in multiple inheritance
- Mixins and their proper use
- Best practices for multiple inheritance

Ready to master multiple inheritance? Let's go! 🚀

---

.. note::
   **✅ Section 4 Complete!**

   You've learned:
   - [✓] The ``__dict__`` attribute for viewing attributes
   - [✓] The ``vars()`` function as a cleaner alternative
   - [✓] The ``__module__`` attribute for class origin
   - [✓] The ``__bases__`` attribute for direct parents
   - [✓] The ``__mro__`` attribute for method resolution
   - [✓] Class vs instance attribute differences
   - [✓] Practical introspection tools and patterns

   **Ready for multiple inheritance?** → Continue to Section 5!