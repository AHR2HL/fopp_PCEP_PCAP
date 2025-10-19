..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".


Section 2: Type Checking
=========================

.. index:: type checking, isinstance, issubclass, duck typing

Introduction
------------

In Section 1, you learned how to inspect objects and their attributes. Now you'll learn how to check **what type** of object you're working with.

Python provides powerful type checking functions that let you:

- **Verify** if an object is an instance of a specific class
- **Check** if a class inherits from another class
- **Write** flexible code that works with multiple types
- **Validate** input before processing
- **Make decisions** based on object types

In this section, you'll learn:

- The ``isinstance()`` function for instance checking
- The ``issubclass()`` function for inheritance checking
- The difference between ``type()`` and ``isinstance()``
- Duck typing vs. explicit type checking
- When to check types and when not to
- Best practices for type validation

By the end, you'll know exactly when and how to check types in your Python code!

---

The ``isinstance()`` Function
------------------------------

.. index:: isinstance function, instance checking, type validation

**``isinstance(object, classinfo)``** checks if an object is an instance of a class (or a tuple of classes).

**Syntax:**

.. code-block:: python

   isinstance(object, Class)           # Single class
   isinstance(object, (Class1, Class2))  # Multiple classes (tuple)

**Basic Example:**

.. activecode:: oop_type_isinstance_basic
   :language: python

   class Dog:
       pass

   class Cat:
       pass

   buddy = Dog()
   whiskers = Cat()

   # Check types
   print(f"Is buddy a Dog? {isinstance(buddy, Dog)}")
   print(f"Is buddy a Cat? {isinstance(buddy, Cat)}")
   print(f"Is whiskers a Cat? {isinstance(whiskers, Cat)}")

   # Check built-in types
   print(f"\nIs 42 an int? {isinstance(42, int)}")
   print(f"Is 'hello' a str? {isinstance('hello', str)}")
   print(f"Is [1,2,3] a list? {isinstance([1, 2, 3], list)}")

   # Check multiple types at once
   print(f"\nIs 42 an int or float? {isinstance(42, (int, float))}")
   print(f"Is 'hello' an int or str? {isinstance('hello', (int, str))}")

**Output:**

::

   Is buddy a Dog? True
   Is buddy a Cat? False
   Is whiskers a Cat? True

   Is 42 an int? True
   Is 'hello' a str? True
   Is [1,2,3] a list? True

   Is 42 an int or float? True
   Is 'hello' an int or str? True

---

**isinstance() with Inheritance**

The power of ``isinstance()`` is that it returns ``True`` for the entire inheritance chain!

.. activecode:: oop_type_isinstance_inheritance
   :language: python

   class Animal:
       def speak(self):
           pass

   class Dog(Animal):
       def speak(self):
           return "Woof!"

   class Labrador(Dog):
       def speak(self):
           return "Woof woof!"

   # Create a Labrador
   buddy = Labrador()

   # Check the inheritance chain
   print(f"Is buddy a Labrador? {isinstance(buddy, Labrador)}")
   print(f"Is buddy a Dog? {isinstance(buddy, Dog)}")
   print(f"Is buddy an Animal? {isinstance(buddy, Animal)}")
   print(f"Is buddy an object? {isinstance(buddy, object)}")  # Everything inherits from object!

   print(f"\nIs buddy a Cat? {isinstance(buddy, Cat) if 'Cat' in dir() else 'Cat not defined'}")

**Output:**

::

   Is buddy a Labrador? True
   Is buddy a Dog? True
   Is buddy an Animal? True
   Is buddy an object? True

   Is buddy a Cat? Cat not defined

.. important::
   **isinstance() checks the entire inheritance hierarchy!**

   If ``Labrador`` inherits from ``Dog``, and ``Dog`` inherits from ``Animal``:
   - A ``Labrador`` instance **is** a ``Labrador``
   - A ``Labrador`` instance **is** a ``Dog``
   - A ``Labrador`` instance **is** an ``Animal``
   - A ``Labrador`` instance **is** an ``object``

---

**Practical Use Case: Input Validation**

.. activecode:: oop_type_isinstance_validation
   :language: python

   def process_number(value):
       """Process numeric values safely."""
       if not isinstance(value, (int, float)):
           return f"Error: Expected number, got {type(value).__name__}"

       if isinstance(value, int):
           return f"Integer: {value} (square: {value ** 2})"
       else:
           return f"Float: {value:.2f} (rounded: {round(value)})"

   # Test with different types
   test_values = [42, 3.14, "hello", [1, 2, 3], None, True]

   for val in test_values:
       print(process_number(val))

**Output:**

::

   Integer: 42 (square: 1764)
   Float: 3.14 (rounded: 3)
   Error: Expected number, got str
   Error: Expected number, got list
   Error: Expected number, got NoneType
   Integer: True (square: 1)

.. note::
   **Surprise!** ``True`` is considered an integer because ``bool`` inherits from ``int`` in Python!

   .. code-block:: python

      print(isinstance(True, int))   # True
      print(isinstance(False, int))  # True
      print(issubclass(bool, int))   # True

---

**Practical Use Case: Polymorphic Function**

.. activecode:: oop_type_isinstance_polymorphic
   :language: python

   def describe_collection(collection):
       """Describe different collection types."""
       if isinstance(collection, str):
           return f"String with {len(collection)} characters: '{collection}'"
       elif isinstance(collection, list):
           return f"List with {len(collection)} items: {collection}"
       elif isinstance(collection, dict):
           return f"Dictionary with {len(collection)} keys: {list(collection.keys())}"
       elif isinstance(collection, set):
           return f"Set with {len(collection)} unique items"
       elif isinstance(collection, tuple):
           return f"Tuple with {len(collection)} items (immutable)"
       else:
           return f"Unknown collection type: {type(collection).__name__}"

   # Test with various types
   test_data = [
       "Hello",
       [1, 2, 3, 4, 5],
       {'a': 1, 'b': 2, 'c': 3},
       {10, 20, 30},
       (100, 200, 300),
       42
   ]

   for data in test_data:
       print(describe_collection(data))

**Output:**

::

   String with 5 characters: 'Hello'
   List with 5 items: [1, 2, 3, 4, 5]
   Dictionary with 3 keys: ['a', 'b', 'c']
   Set with 3 unique items
   Tuple with 3 items (immutable)
   Unknown collection type: int

---

``isinstance()`` vs ``type()``
-------------------------------

.. index:: isinstance vs type, type comparison

You might be tempted to use ``type()`` for type checking. **Don't!** Here's why:

.. activecode:: oop_type_isinstance_vs_type
   :language: python

   class Animal:
       pass

   class Dog(Animal):
       pass

   buddy = Dog()

   # Using type() - TOO STRICT!
   print("Using type():")
   print(f"  type(buddy) == Dog: {type(buddy) == Dog}")
   print(f"  type(buddy) == Animal: {type(buddy) == Animal}")  # False!

   # Using isinstance() - CORRECT!
   print("\nUsing isinstance():")
   print(f"  isinstance(buddy, Dog): {isinstance(buddy, Dog)}")
   print(f"  isinstance(buddy, Animal): {isinstance(buddy, Animal)}")  # True!

   # The problem with type()
   print("\n❌ Problem: type() doesn't respect inheritance")
   print("✅ Solution: isinstance() respects inheritance")

**Output:**

::

   Using type():
     type(buddy) == Dog: True
     type(buddy) == Animal: False

   Using isinstance():
     isinstance(buddy, Dog): True
     isinstance(buddy, Animal): True

   ❌ Problem: type() doesn't respect inheritance
   ✅ Solution: isinstance() respects inheritance

.. list-table:: ``isinstance()`` vs ``type()``
   :widths: 30 35 35
   :header-rows: 1

   * - Aspect
     - ``isinstance(obj, Class)``
     - ``type(obj) == Class``
   * - **Inheritance**
     - ✅ Respects inheritance
     - ❌ Ignores inheritance
   * - **Subclasses**
     - ✅ Returns True for subclasses
     - ❌ Returns False for subclasses
   * - **Multiple types**
     - ✅ Can check tuple of types
     - ❌ Can't check multiple types
   * - **Recommended?**
     - ✅ Use this!
     - ⚠️ Rarely needed

.. important::
   **Rule of Thumb:**

   - ✅ **Use isinstance()** — almost always the right choice
   - ⚠️ **Use type()** — only when you need the **exact** type (rare!)

---

The ``issubclass()`` Function
------------------------------

.. index:: issubclass function, class hierarchy, inheritance checking

**``issubclass(class, classinfo)``** checks if a class is a subclass of another class.

**Syntax:**

.. code-block:: python

   issubclass(SubClass, ParentClass)
   issubclass(Class, (Parent1, Parent2))  # Multiple parents

**Basic Example:**

.. activecode:: oop_type_issubclass_basic
   :language: python

   class Animal:
       pass

   class Mammal(Animal):
       pass

   class Dog(Mammal):
       pass

   class Cat(Mammal):
       pass

   class Bird(Animal):
       pass

   # Check class relationships
   print("Class Hierarchy Checks:")
   print(f"  Dog is subclass of Mammal? {issubclass(Dog, Mammal)}")
   print(f"  Dog is subclass of Animal? {issubclass(Dog, Animal)}")
   print(f"  Dog is subclass of Bird? {issubclass(Dog, Bird)}")

   print(f"\n  Mammal is subclass of Animal? {issubclass(Mammal, Animal)}")
   print(f"  Cat is subclass of Mammal? {issubclass(Cat, Mammal)}")

   print(f"\n  Dog is subclass of object? {issubclass(Dog, object)}")  # Everything!

   # A class is considered a subclass of itself
   print(f"\n  Dog is subclass of Dog? {issubclass(Dog, Dog)}")

**Output:**

::

   Class Hierarchy Checks:
     Dog is subclass of Mammal? True
     Dog is subclass of Animal? True
     Dog is subclass of Bird? False

     Mammal is subclass of Animal? True
     Cat is subclass of Mammal? True

     Dog is subclass of object? True

     Dog is subclass of Dog? True

.. note::
   **Key Difference:**

   - ``isinstance(obj, Class)`` — checks **objects** (instances)
   - ``issubclass(SubClass, Class)`` — checks **classes** (not instances)

---

**Practical Use Case: Plugin Validation**

.. activecode:: oop_type_issubclass_plugin
   :language: python

   class Plugin:
       """Base class for all plugins."""
       def run(self):
           raise NotImplementedError("Plugins must implement run()")

   class DataPlugin(Plugin):
       """Plugin for data processing."""
       def run(self):
           return "Processing data..."

   class NetworkPlugin(Plugin):
       """Plugin for network operations."""
       def run(self):
           return "Connecting to network..."

   class InvalidPlugin:
       """This doesn't inherit from Plugin!"""
       def run(self):
           return "I'm not a real plugin!"

   def register_plugin(plugin_class):
       """Register a plugin class (not an instance)."""
       if not isinstance(plugin_class, type):
           return "❌ Error: Expected a class, not an instance"

       if not issubclass(plugin_class, Plugin):
           return f"❌ Error: {plugin_class.__name__} must inherit from Plugin"

       return f"✅ Registered: {plugin_class.__name__}"

   # Test registration
   print(register_plugin(DataPlugin))
   print(register_plugin(NetworkPlugin))
   print(register_plugin(InvalidPlugin))

   # Common mistake: passing an instance instead of class
   print(register_plugin(DataPlugin()))

**Output:**

::

   ✅ Registered: DataPlugin
   ✅ Registered: NetworkPlugin
   ❌ Error: InvalidPlugin must inherit from Plugin
   ❌ Error: Expected a class, not an instance

---

**Practical Use Case: Framework Base Classes**

.. activecode:: oop_type_issubclass_framework
   :language: python

   class Model:
       """Base class for database models."""
       pass

   class View:
       """Base class for views."""
       pass

   class Controller:
       """Base class for controllers."""
       pass

   # User-defined classes
   class UserModel(Model):
       pass

   class UserView(View):
       pass

   class UserController(Controller):
       pass

   def inspect_framework_classes(classes):
       """Categorize classes by framework type."""
       print("Framework Class Analysis:")
       print("=" * 50)

       for cls in classes:
           categories = []
           if issubclass(cls, Model):
               categories.append("Model")
           if issubclass(cls, View):
               categories.append("View")
           if issubclass(cls, Controller):
               categories.append("Controller")

           if categories:
               print(f"  {cls.__name__}: {', '.join(categories)}")
           else:
               print(f"  {cls.__name__}: Not a framework class")

   # Inspect all classes
   inspect_framework_classes([
       UserModel,
       UserView,
       UserController,
       str,  # Built-in type
       object  # Base of everything
   ])

**Output:**

::

   Framework Class Analysis:
   ==================================================
     UserModel: Model
     UserView: View
     UserController: Controller
     str: Not a framework class
     object: Not a framework class

---

Duck Typing vs Type Checking
-----------------------------

.. index:: duck typing, EAFP, LBYL, type checking philosophy

Python follows the principle of **"duck typing"**: "If it walks like a duck and quacks like a duck, it's a duck."

**Duck Typing Philosophy:**

Instead of checking types, check for **behavior** (methods/attributes).

.. activecode:: oop_type_duck_typing_demo
   :language: python

   # ❌ BAD: Check exact type
   def process_bad(data):
       if type(data) == list:
           return sum(data)
       else:
           return "Not a list!"

   # ⚠️ BETTER: Use isinstance
   def process_better(data):
       if isinstance(data, list):
           return sum(data)
       else:
           return "Not a list!"

   # ✅ BEST: Duck typing - just try it!
   def process_best(data):
       try:
           return sum(data)
       except TypeError:
           return "Can't sum this data"

   # Test with different types
   test_data = [
       [1, 2, 3, 4, 5],        # list
       (10, 20, 30),           # tuple (works with sum!)
       {1, 2, 3},              # set (works with sum!)
       range(1, 6),            # range (works with sum!)
       "12345"                 # string (won't work)
   ]

   print("Bad (type check):")
   for data in test_data:
       print(f"  {process_bad(data)}")

   print("\nBetter (isinstance):")
   for data in test_data:
       print(f"  {process_better(data)}")

   print("\nBest (duck typing):")
   for data in test_data:
       print(f"  {process_best(data)}")

**Output:**

::

   Bad (type check):
     15
     Not a list!
     Not a list!
     Not a list!
     Not a list!

   Better (isinstance):
     15
     Not a list!
     Not a list!
     Not a list!
     Not a list!

   Best (duck typing):
     15
     60
     6
     15
     Can't sum this data

The duck typing version works with **any iterable of numbers**, not just lists!

---

**EAFP vs LBYL**

.. index:: EAFP, LBYL, exception handling

Two philosophies for handling errors:

.. list-table:: Error Handling Philosophies
   :widths: 20 40 40
   :header-rows: 1

   * - Style
     - LBYL (Look Before You Leap)
     - EAFP (Easier to Ask for Forgiveness)
   * - **Meaning**
     - Check conditions first
     - Try it and handle exceptions
   * - **Example**
     - ``if hasattr(obj, 'method'):``
     - ``try: obj.method()``
   * - **Pythonic?**
     - ⚠️ Less Pythonic
     - ✅ More Pythonic
   * - **Performance**
     - Slower (multiple checks)
     - Faster (optimistic path)
   * - **When to use**
     - Expensive operations
     - Most situations

**Example Comparison:**

.. activecode:: oop_type_eafp_vs_lbyl
   :language: python

   class Dog:
       def speak(self):
           return "Woof!"

   class Cat:
       def meow(self):
           return "Meow!"

   # LBYL (Look Before You Leap)
   def make_sound_lbyl(animal):
       if hasattr(animal, 'speak'):
           return animal.speak()
       elif hasattr(animal, 'meow'):
           return animal.meow()
       else:
           return "This animal is silent"

   # EAFP (Easier to Ask for Forgiveness than Permission)
   def make_sound_eafp(animal):
       try:
           return animal.speak()
       except AttributeError:
           try:
               return animal.meow()
           except AttributeError:
               return "This animal is silent"

   # Test both
   dog = Dog()
   cat = Cat()
   rock = object()

   animals = [dog, cat, rock]

   print("LBYL approach:")
   for animal in animals:
       print(f"  {make_sound_lbyl(animal)}")

   print("\nEAFP approach:")
   for animal in animals:
       print(f"  {make_sound_eafp(animal)}")

**Output:**

::

   LBYL approach:
     Woof!
     Meow!
     This animal is silent

   EAFP approach:
     Woof!
     Meow!
     This animal is silent

.. important::
   **Python's Philosophy: EAFP**

   Python prefers EAFP because:

   - ✅ More readable for the "happy path"
   - ✅ Faster when exceptions are rare
   - ✅ Handles race conditions better
   - ✅ More Pythonic style

   Use LBYL only when exceptions are expensive or common.

---

When to Use Type Checking
--------------------------

.. index:: type checking best practices, when to check types

**Use Type Checking When:**

1. **Input Validation** — Checking user input or API parameters
2. **Different Behavior** — Need to handle types differently
3. **Early Failure** — Want to fail fast with clear errors
4. **Documentation** — Making expectations explicit

**Avoid Type Checking When:**

1. **Duck Typing Suffices** — If it has the method, use it!
2. **Performance Matters** — Type checks add overhead
3. **Limiting Flexibility** — Don't restrict to exact types unnecessarily

---

**Example: When to Check Types**

.. activecode:: oop_type_when_to_check
   :language: python

   # ✅ GOOD: Type checking for different behavior
   def format_value(value):
       """Format values based on type."""
       if isinstance(value, str):
           return f'"{value}"'
       elif isinstance(value, (int, float)):
           return str(value)
       elif isinstance(value, bool):
           return "true" if value else "false"
       elif isinstance(value, (list, tuple)):
           return "[" + ", ".join(format_value(v) for v in value) + "]"
       else:
           return str(value)

   # ❌ BAD: Unnecessary type checking
   def add_bad(a, b):
       if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
           raise TypeError("Arguments must be numbers")
       return a + b  # Python already handles this!

   # ✅ GOOD: Duck typing
   def add_good(a, b):
       return a + b  # Let Python handle invalid types

   # Test format_value
   print("Format values:")
   print(f"  String: {format_value('hello')}")
   print(f"  Number: {format_value(42)}")
   print(f"  Boolean: {format_value(True)}")
   print(f"  List: {format_value([1, 2, 'three'])}")

   # Test add functions
   print("\nAddition:")
   print(f"  Good: {add_good(5, 3)}")
   print(f"  Good with strings: {add_good('Hello', ' World')}")

   try:
       print(f"  Bad: {add_bad(5, '3')}")
   except TypeError as e:
       print(f"  Bad blocks valid use: {e}")

**Output:**

::

   Format values:
     String: "hello"
     Number: 42
     Boolean: true
     List: [1, 2, "three"]

   Addition:
     Good: 8
     Good with strings: Hello World
     Bad blocks valid use: Arguments must be numbers

---

Best Practices Summary
-----------------------

.. index:: type checking best practices

.. code-block:: python

   # ✅ DO: Use isinstance() instead of type()
   if isinstance(obj, MyClass):
       pass

   # ❌ DON'T: Use type() for checking
   if type(obj) == MyClass:
       pass

   # ✅ DO: Check multiple types with tuple
   if isinstance(value, (int, float)):
       pass

   # ❌ DON'T: Multiple type() checks
   if type(value) == int or type(value) == float:
       pass

   # ✅ DO: Use duck typing when possible
   try:
       value.method()
   except AttributeError:
       pass

   # ⚠️ MAYBE: Type check only when necessary
   if isinstance(value, str):
       # Handle strings specially
       pass

   # ✅ DO: Use issubclass() for class relationships
   if issubclass(PluginClass, BasePlugin):
       pass

   # ❌ DON'T: isinstance with classes (use issubclass)
   if isinstance(PluginClass, BasePlugin):  # WRONG!
       pass

---

Check Your Understanding
-------------------------

.. mchoice:: oop_type_isinstance_inheritance_mcq
   :answer_a: True
   :answer_b: False
   :answer_c: AttributeError
   :answer_d: TypeError
   :correct: a
   :feedback_a: Correct! isinstance() returns True for the entire inheritance chain.
   :feedback_b: isinstance() respects inheritance and returns True for parent classes.
   :feedback_c: isinstance() doesn't raise AttributeError.
   :feedback_d: isinstance() doesn't raise TypeError for valid inputs.

   If ``Dog`` inherits from ``Animal``, what does ``isinstance(dog_instance, Animal)`` return?

.. mchoice:: oop_type_isinstance_vs_type_mcq
   :answer_a: isinstance() is faster
   :answer_b: type() is more accurate
   :answer_c: isinstance() respects inheritance
   :answer_d: They are identical
   :correct: c
   :feedback_a: Speed isn't the main difference.
   :feedback_b: type() is actually less flexible because it ignores inheritance.
   :feedback_c: Correct! isinstance() respects inheritance; type() checks exact type only.
   :feedback_d: They behave very differently with inheritance.

   What's the main advantage of ``isinstance()`` over ``type()``?

.. mchoice:: oop_type_issubclass_purpose_mcq
   :answer_a: Check if an object is an instance of a class
   :answer_b: Check if a class inherits from another class
   :answer_c: Check if two objects are the same type
   :answer_d: Check if an attribute exists
   :correct: b
   :feedback_a: That's isinstance(), not issubclass().
   :feedback_b: Correct! issubclass() checks class inheritance relationships.
   :feedback_c: That would require type() or isinstance().
   :feedback_d: That's hasattr(), not issubclass().

   What is ``issubclass()`` used for?

.. mchoice:: oop_type_duck_typing_mcq
   :answer_a: Always check types before using objects
   :answer_b: Try using the object and handle exceptions
   :answer_c: Only work with built-in types
   :answer_d: Avoid using classes altogether
   :correct: b
   :feedback_a: That's LBYL, not duck typing.
   :feedback_b: Correct! Duck typing means "try it and handle exceptions" (EAFP).
   :feedback_c: Duck typing works with any object that has the right behavior.
   :feedback_d: Duck typing is about using objects based on behavior, not avoiding classes.

   What is the "duck typing" philosophy in Python?

.. mchoice:: oop_type_eafp_vs_lbyl_mcq
   :answer_a: EAFP is more Pythonic
   :answer_b: LBYL is more Pythonic
   :answer_c: They are equally Pythonic
   :answer_d: Neither is Pythonic
   :correct: a
   :feedback_a: Correct! EAFP (Easier to Ask Forgiveness than Permission) is the Pythonic way.
   :feedback_b: LBYL (Look Before You Leap) is less Pythonic in Python.
   :feedback_c: Python strongly prefers EAFP.
   :feedback_d: EAFP is definitely Pythonic!

   Which is more Pythonic: EAFP or LBYL?

---

Key Takeaways
-------------

.. important::
   **Section 2 Summary: Type Checking**

   ✅ **isinstance() Function:**
      - ``isinstance(obj, Class)`` — check if object is instance of class
      - ``isinstance(obj, (Class1, Class2))`` — check multiple types
      - Respects inheritance (returns True for parent classes)
      - **Always prefer** ``isinstance()`` over ``type()``

   ✅ **issubclass() Function:**
      - ``issubclass(SubClass, ParentClass)`` — check class relationships
      - Works with classes, not instances
      - Useful for validating class hierarchies
      - A class is considered a subclass of itself

   ✅ **Duck Typing:**
      - "If it walks like a duck and quacks like a duck, it's a duck"
      - Check behavior (methods), not types
      - More flexible and Pythonic
      - Use try/except instead of type checking when possible

   ✅ **EAFP vs LBYL:**
      - **EAFP** (Easier to Ask Forgiveness) — try it, handle exceptions
      - **LBYL** (Look Before You Leap) — check first, then act
      - Python prefers EAFP (more Pythonic)
      - Use LBYL only when exceptions are expensive

   ✅ **When to Use Type Checking:**
      - Input validation
      - Different behavior for different types
      - Documentation/clarity
      - Framework/plugin validation

   ✅ **When to Avoid Type Checking:**
      - Duck typing suffices
      - Performance matters
      - Would limit flexibility unnecessarily

---

What's Next?
------------

You now understand how to check types with ``isinstance()`` and ``issubclass()``, and when to use duck typing instead. Next, you'll learn about **identity vs. equality** — the difference between ``is`` and ``==``!

**In Section 3: Identity vs Equality**, you'll learn:

- The ``is`` and ``is not`` operators
- The ``id()`` function for object identity
- The difference between identity and equality
- When to use ``is`` vs ``==``
- Special cases (``None``, singletons, interning)
- Common pitfalls and best practices

Ready to master object identity? Let's go! 🚀

---

.. note::
   **✅ Section 2 Complete!**

   You've learned:
   - [✓] ``isinstance()`` for instance checking
   - [✓] ``issubclass()`` for inheritance checking
   - [✓] Why isinstance() is better than type()
   - [✓] Duck typing philosophy
   - [✓] EAFP vs LBYL approaches
   - [✓] When to use type checking
   - [✓] Best practices for type validation

   **Ready for identity checking?** → Continue to Section 3!