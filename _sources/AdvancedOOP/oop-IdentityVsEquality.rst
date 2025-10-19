..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 3: Identity vs Equality
================================

.. index:: identity, equality, is operator, is not operator, id function

Introduction
------------

One of the most important (and confusing!) concepts in Python is the difference between **identity** and **equality**.

Consider these questions:

- Are two variables pointing to the **same object** in memory?
- Are two objects **equal in value** but different objects?
- When should you use ``is`` vs ``==``?

In this section, you'll learn:

- The ``is`` and ``is not`` operators for identity checking
- The ``id()`` function for getting object identity
- The fundamental difference between identity and equality
- When to use ``is`` vs ``==``
- Special cases: ``None``, booleans, small integers
- Object interning and memory optimization
- Common pitfalls and best practices

By the end, you'll never confuse ``is`` and ``==`` again!

---

Identity vs Equality: The Core Concept
---------------------------------------

.. index:: identity definition, equality definition

**Identity** asks: "Are these the **same object** in memory?"

**Equality** asks: "Do these objects have the **same value**?"

Think of it like this:

.. note::
   **Real-World Analogy:**

   - **Identity:** Are you and your twin the same person? (No — same DNA, different people)
   - **Equality:** Do you and your twin have the same DNA? (Yes — equal but not identical)

**Visual Example:**

.. activecode:: oop_identity_intro_visual
   :language: python

   # Create two separate lists with the same content
   list1 = [1, 2, 3]
   list2 = [1, 2, 3]

   # Create a reference to list1
   list3 = list1

   # Equality (==): Compare VALUES
   print("Equality Checks (==):")
   print(f"  list1 == list2? {list1 == list2}")  # Same values
   print(f"  list1 == list3? {list1 == list3}")  # Same values

   # Identity (is): Compare OBJECTS
   print("\nIdentity Checks (is):")
   print(f"  list1 is list2? {list1 is list2}")  # Different objects
   print(f"  list1 is list3? {list1 is list3}")  # Same object!

   # Modify list1
   list1.append(4)

   print("\nAfter modifying list1:")
   print(f"  list1: {list1}")
   print(f"  list2: {list2}")  # Unchanged
   print(f"  list3: {list3}")  # Changed! (same object as list1)

**Output:**

::

   Equality Checks (==):
     list1 == list2? True
     list1 == list3? True

   Identity Checks (is):
     list1 is list2? False
     list1 is list3? True

   After modifying list1:
     list1: [1, 2, 3, 4]
     list2: [1, 2, 3]
     list3: [1, 2, 3, 4]

**Key Insight:** ``list1`` and ``list3`` are the **same object**, so modifying one affects the other!

---

The ``id()`` Function
---------------------

.. index:: id function, object identity, memory address

Every object in Python has a unique **identity** — a number that represents its location in memory.

**``id(object)``** returns the unique identifier for an object.

.. activecode:: oop_identity_id_basic
   :language: python

   # Create objects
   a = [1, 2, 3]
   b = [1, 2, 3]
   c = a

   # Get their IDs (memory addresses)
   print(f"id(a): {id(a)}")
   print(f"id(b): {id(b)}")
   print(f"id(c): {id(c)}")

   # Compare identities
   print(f"\nid(a) == id(b)? {id(a) == id(b)}")  # Different objects
   print(f"id(a) == id(c)? {id(a) == id(c)}")  # Same object

   # The 'is' operator compares IDs
   print(f"\na is b? {a is b}")  # Same as: id(a) == id(b)
   print(f"a is c? {a is c}")  # Same as: id(a) == id(c)

**Output (IDs will vary):**

::

   id(a): 140235678123456
   id(b): 140235678987654
   id(c): 140235678123456

   id(a) == id(b)? False
   id(a) == id(c)? True

   a is b? False
   a is c? True

.. important::
   **The ``is`` operator is equivalent to:**

   .. code-block:: python

      a is b  <==>  id(a) == id(b)

   ``is`` checks if two variables point to the **same object in memory**.

---

The ``is`` and ``is not`` Operators
------------------------------------

.. index:: is operator, is not operator, identity operators

**Syntax:**

.. code-block:: python

   object1 is object2      # True if same object
   object1 is not object2  # True if different objects

**Example with Different Types:**

.. activecode:: oop_identity_is_types
   :language: python

   # Lists (mutable)
   list_a = [1, 2, 3]
   list_b = [1, 2, 3]
   list_c = list_a

   print("Lists:")
   print(f"  list_a is list_b? {list_a is list_b}")  # False
   print(f"  list_a is list_c? {list_a is list_c}")  # True

   # Strings (immutable)
   str_a = "hello"
   str_b = "hello"
   str_c = "hel" + "lo"

   print("\nStrings:")
   print(f"  str_a is str_b? {str_a is str_b}")  # True (interning!)
   print(f"  str_a is str_c? {str_a is str_c}")  # True (interning!)

   # Integers (immutable)
   int_a = 256
   int_b = 256
   int_c = int_a

   print("\nSmall integers:")
   print(f"  int_a is int_b? {int_a is int_b}")  # True (interning!)
   print(f"  int_a is int_c? {int_a is int_c}")  # True

   # Large integers
   big_a = 1000
   big_b = 1000

   print("\nLarge integers:")
   print(f"  big_a is big_b? {big_a is big_b}")  # False (no interning!)

**Output:**

::

   Lists:
     list_a is list_b? False
     list_a is list_c? True

   Strings:
     str_a is str_b? True
     str_a is str_c? True

   Small integers:
     int_a is int_b? True
     int_a is int_c? True

   Large integers:
     big_a is big_b? False

**Wait, what?** Why are some objects with equal values the same object and others different? Keep reading!

---

Python's Object Interning
--------------------------

.. index:: interning, singleton, optimization, cached objects

For **performance optimization**, Python caches (interns) certain immutable objects:

.. list-table:: Python Interning Rules
   :widths: 30 30 40
   :header-rows: 1

   * - Object Type
     - Interned?
     - Details
   * - **None**
     - ✅ Always
     - Only one ``None`` object exists
   * - **True/False**
     - ✅ Always
     - Only one ``True`` and one ``False`` exist
   * - **Small integers**
     - ✅ Yes (-5 to 256)
     - Integers in range [-5, 256] are cached
   * - **Large integers**
     - ❌ Usually not
     - Created fresh each time
   * - **Short strings**
     - ✅ Often
     - Strings with identifier-like characters
   * - **Long strings**
     - ❌ Usually not
     - Created fresh each time
   * - **Lists**
     - ❌ Never
     - Always separate objects
   * - **Dicts**
     - ❌ Never
     - Always separate objects

.. activecode:: oop_identity_interning_demo
   :language: python

   # None (always interned)
   a = None
   b = None
   print(f"None: a is b? {a is b}")

   # Booleans (always interned)
   x = True
   y = True
   print(f"Boolean: x is y? {x is y}")

   # Small integers (interned)
   i1 = 42
   i2 = 42
   print(f"Small int (42): i1 is i2? {i1 is i2}")

   # Large integers (not interned)
   j1 = 999999
   j2 = 999999
   print(f"Large int (999999): j1 is j2? {j1 is j2}")

   # Short strings (usually interned)
   s1 = "hello"
   s2 = "hello"
   print(f"Short string: s1 is s2? {s1 is s2}")

   # Strings with spaces (may not be interned)
   t1 = "hello world"
   t2 = "hello world"
   print(f"String with space: t1 is t2? {t1 is t2}")

   # Lists (never interned)
   l1 = [1, 2, 3]
   l2 = [1, 2, 3]
   print(f"Lists: l1 is l2? {l1 is l2}")

**Output:**

::

   None: a is b? True
   Boolean: x is y? True
   Small int (42): i1 is i2? True
   Large int (999999): j1 is j2? False
   Short string: s1 is s2? True
   String with space: t1 is t2? True
   Lists: l1 is l2? False

.. warning::
   **Don't rely on interning!**

   - Interning is an **implementation detail**
   - Behavior can vary between Python versions
   - Different interpreters (CPython, PyPy) behave differently
   - **Always use** ``==`` for value comparison
   - **Only use** ``is`` for specific cases (see below)

---

When to Use ``is`` vs ``==``
-----------------------------

.. index:: is vs ==, when to use is, when to use ==

.. list-table:: ``is`` vs ``==`` Usage Guide
   :widths: 50 50
   :header-rows: 1

   * - Use ``is`` / ``is not``
     - Use ``==`` / ``!=``
   * - Checking against ``None``
     - Comparing values
   * - Checking against ``True`` / ``False`` (rare)
     - Comparing strings
   * - Singleton pattern objects
     - Comparing numbers
   * - Verifying object identity
     - Comparing collections
   * - Performance-critical identity checks
     - Almost all other comparisons

---

**Rule 1: Always Use ``is`` for None**

.. activecode:: oop_identity_none_check
   :language: python

   def process_value(value):
       # ✅ CORRECT: Use 'is' for None
       if value is None:
           return "No value provided"

       # Process the value
       return f"Processing: {value}"

   def process_value_bad(value):
       # ❌ WRONG: Don't use '==' for None
       if value == None:
           return "No value provided"

       return f"Processing: {value}"

   # Test both
   test_values = [None, 0, "", [], False]

   print("Correct (is None):")
   for val in test_values:
       print(f"  {repr(val)}: {process_value(val)}")

**Output:**

::

   Correct (is None):
     None: No value provided
     0: Processing: 0
     '': Processing:
     []: Processing: []
     False: Processing: False

.. important::
   **Why ``is None`` is better than ``== None``:**

   1. ✅ **Faster** — identity check is quicker than equality
   2. ✅ **Safer** — can't be overridden by custom ``__eq__``
   3. ✅ **Clearer** — explicitly checking identity
   4. ✅ **PEP 8** — Python style guide recommends it

---

**Rule 2: Use ``==`` for Value Comparisons**

.. activecode:: oop_identity_value_comparison
   :language: python

   # ✅ CORRECT: Use '==' for value comparisons
   def is_empty_list_correct(obj):
       return obj == []

   # ❌ WRONG: Don't use 'is' for value comparisons
   def is_empty_list_wrong(obj):
       return obj is []

   # Test
   my_list = []

   print(f"Using ==: {is_empty_list_correct(my_list)}")  # True
   print(f"Using is: {is_empty_list_wrong(my_list)}")    # False!

   # Why 'is' fails
   print(f"\nWhy 'is' fails:")
   print(f"  my_list: {id(my_list)}")
   print(f"  []: {id([])}")  # New object every time!
   print(f"  Different objects!")

**Output:**

::

   Using ==: True
   Using is: False

   Why 'is' fails:
     my_list: 140235678123456
     []: 140235678987654
     Different objects!

---

**Rule 3: Be Careful with Booleans**

.. activecode:: oop_identity_boolean_check
   :language: python

   def check_boolean_bad(value):
       # ⚠️ PROBLEMATIC: Using 'is' for boolean
       if value is True:
           return "Explicitly True"
       elif value is False:
           return "Explicitly False"
       else:
           return "Truthy or Falsy"

   def check_boolean_good(value):
       # ✅ BETTER: Use implicit truthiness
       if value:
           return "Truthy"
       else:
           return "Falsy"

   # Test values
   test_values = [True, False, 1, 0, [], [1], None, "hello", ""]

   print("Using 'is' (problematic):")
   for val in test_values:
       print(f"  {repr(val):12} -> {check_boolean_bad(val)}")

   print("\nUsing truthiness (better):")
   for val in test_values:
       print(f"  {repr(val):12} -> {check_boolean_good(val)}")

**Output:**

::

   Using 'is' (problematic):
     True         -> Explicitly True
     False        -> Explicitly False
     1            -> Truthy or Falsy
     0            -> Truthy or Falsy
     []           -> Truthy or Falsy
     [1]          -> Truthy or Falsy
     None         -> Truthy or Falsy
     'hello'      -> Truthy or Falsy
     ''           -> Truthy or Falsy

   Using truthiness (better):
     True         -> Truthy
     False        -> Falsy
     1            -> Truthy
     0            -> Falsy
     []           -> Falsy
     [1]          -> Truthy
     None         -> Falsy
     'hello'      -> Truthy
     ''           -> Falsy

.. note::
   **In Python, use truthiness instead of boolean checks:**

   .. code-block:: python

      # ✅ GOOD: Pythonic
      if my_list:
          process(my_list)

      # ❌ BAD: Unpythonic
      if len(my_list) > 0:
          process(my_list)

      # ❌ VERY BAD: Almost never do this
      if my_list is not []:
          process(my_list)

---

Custom Equality with ``__eq__``
--------------------------------

.. index:: __eq__ method, custom equality, equality override

You can define custom equality for your classes by implementing ``__eq__``:

.. activecode:: oop_identity_custom_equality
   :language: python

   class Person:
       def __init__(self, name, age):
           self.name = name
           self.age = age

       def __eq__(self, other):
           """Two people are equal if they have the same name and age."""
           if not isinstance(other, Person):
               return False
           return self.name == other.name and self.age == other.age

       def __repr__(self):
           return f"Person('{self.name}', {self.age})"

   # Create people
   alice1 = Person("Alice", 30)
   alice2 = Person("Alice", 30)
   bob = Person("Bob", 25)

   # Equality (==): Uses __eq__
   print("Equality (==):")
   print(f"  alice1 == alice2? {alice1 == alice2}")  # True (same values)
   print(f"  alice1 == bob? {alice1 == bob}")        # False

   # Identity (is): Checks object identity
   print("\nIdentity (is):")
   print(f"  alice1 is alice2? {alice1 is alice2}")  # False (different objects)
   print(f"  alice1 is alice1? {alice1 is alice1}")  # True (same object)

   # IDs
   print(f"\nObject IDs:")
   print(f"  id(alice1): {id(alice1)}")
   print(f"  id(alice2): {id(alice2)}")
   print(f"  Different objects, but equal values!")

**Output:**

::

   Equality (==):
     alice1 == alice2? True
     alice1 == bob? False

   Identity (is):
     alice1 is alice2? False
     alice1 is alice1? True

   Object IDs:
     id(alice1): 140235678123456
     id(alice2): 140235678987654
     Different objects, but equal values!

.. important::
   **Key Takeaway:**

   - ``==`` can be **customized** with ``__eq__``
   - ``is`` **cannot be overridden** — always checks memory identity
   - This is why ``is None`` is safer than ``== None``

---

Common Pitfalls
---------------

.. index:: identity pitfalls, common mistakes

**Pitfall 1: Using ``is`` for Strings**

.. activecode:: oop_identity_pitfall_strings
   :language: python

   # ❌ DON'T DO THIS
   def check_name_bad(name):
       if name is "Alice":  # WRONG!
           return "Hello Alice!"
       return "Hello stranger!"

   # ✅ DO THIS
   def check_name_good(name):
       if name == "Alice":  # CORRECT!
           return "Hello Alice!"
       return "Hello stranger!"

   # This might work due to interning...
   name1 = "Alice"
   print(f"Direct assignment: {check_name_bad(name1)}")

   # But this will fail!
   name2 = input("Enter name (type 'Alice'): ") if False else "Alice"
   name2 = "Ali" + "ce"  # Simulate non-interned string
   print(f"Constructed string: {check_name_bad(name2)}")
   print(f"Using ==: {check_name_good(name2)}")

**Output:**

::

   Direct assignment: Hello Alice!
   Constructed string: Hello stranger!
   Using ==: Hello Alice!

---

**Pitfall 2: Using ``is`` for Numbers**

.. activecode:: oop_identity_pitfall_numbers
   :language: python

   # ❌ DON'T DO THIS
   def is_answer_bad(number):
       return number is 42  # WRONG!

   # ✅ DO THIS
   def is_answer_good(number):
       return number == 42  # CORRECT!

   # Small numbers are interned, so this might work...
   print(f"Literal 42: {is_answer_bad(42)}")

   # But computed numbers might fail!
   answer = int("42")
   print(f"Parsed '42': {is_answer_bad(answer)}")

   answer = 40 + 2
   print(f"40 + 2: {is_answer_bad(answer)}")

   # Large numbers definitely fail
   big = 1000
   print(f"Literal 1000: {big is 1000}")

   # Always use ==
   print(f"\nUsing ==: {is_answer_good(42)}")
   print(f"Using ==: {is_answer_good(int('42'))}")
   print(f"Using ==: {is_answer_good(40 + 2)}")

**Output:**

::

   Literal 42: True
   Parsed '42': True
   40 + 2: True
   Literal 1000: False

   Using ==: True
   Using ==: True
   Using ==: True

---

**Pitfall 3: Mutable Default Arguments**

.. activecode:: oop_identity_pitfall_mutable_default
   :language: python

   # ❌ DANGEROUS: Mutable default argument
   def add_item_bad(item, items=[]):
       items.append(item)
       return items

   # Each call uses the SAME list object!
   list1 = add_item_bad("apple")
   list2 = add_item_bad("banana")
   list3 = add_item_bad("cherry")

   print("Bug: All calls share the same list!")
   print(f"  list1: {list1}")
   print(f"  list2: {list2}")
   print(f"  list3: {list3}")
   print(f"  All identical: {list1 is list2 is list3}")

   # ✅ CORRECT: Use None as default
   def add_item_good(item, items=None):
       if items is None:
           items = []
       items.append(item)
       return items

   list4 = add_item_good("apple")
   list5 = add_item_good("banana")
   list6 = add_item_good("cherry")

   print("\nCorrect: Each call gets a new list!")
   print(f"  list4: {list4}")
   print(f"  list5: {list5}")
   print(f"  list6: {list6}")
   print(f"  All different: {list4 is not list5 and list5 is not list6}")

**Output:**

::

   Bug: All calls share the same list!
     list1: ['apple', 'banana', 'cherry']
     list2: ['apple', 'banana', 'cherry']
     list3: ['apple', 'banana', 'cherry']
     All identical: True

   Correct: Each call gets a new list!
     list4: ['apple']
     list5: ['banana']
     list6: ['cherry']
     All different: True

---

Practical Patterns
------------------

.. index:: identity patterns, singleton pattern

**Pattern 1: Checking for Sentinel Values**

.. activecode:: oop_identity_pattern_sentinel
   :language: python

   # Use a unique object as a sentinel (not None)
   _NOT_PROVIDED = object()

   def fetch_data(key, default=_NOT_PROVIDED):
       """Fetch data with optional default."""
       database = {'name': 'Alice', 'age': 30}

       if key in database:
           return database[key]

       # Check if default was provided
       if default is _NOT_PROVIDED:
           raise KeyError(f"Key '{key}' not found and no default provided")

       return default

   # Test
   print(f"Existing key: {fetch_data('name')}")
   print(f"With default: {fetch_data('email', 'none@example.com')}")
   print(f"With None as default: {fetch_data('phone', None)}")

   try:
       print(fetch_data('missing'))
   except KeyError as e:
       print(f"No default: {e}")

**Output:**

::

   Existing key: Alice
   With default: none@example.com
   With None as default: None
   No default: "Key 'missing' not found and no default provided"

---

**Pattern 2: Singleton Pattern**

.. activecode:: oop_identity_pattern_singleton
   :language: python

   class Database:
       _instance = None

       def __new__(cls):
           """Ensure only one instance exists."""
           if cls._instance is None:
               cls._instance = super().__new__(cls)
               cls._instance.connection = "Connected to DB"
           return cls._instance

   # Create "multiple" instances
   db1 = Database()
   db2 = Database()
   db3 = Database()

   # All are the same object!
   print(f"db1 is db2? {db1 is db2}")
   print(f"db2 is db3? {db2 is db3}")
   print(f"db1 is db3? {db1 is db3}")

   print(f"\nAll share same connection: {db1.connection}")

   # Modifying one affects all (they're the same object)
   db1.connection = "Modified connection"
   print(f"db2 connection: {db2.connection}")
   print(f"db3 connection: {db3.connection}")

**Output:**

::

   db1 is db2? True
   db2 is db3? True
   db1 is db3? True

   All share same connection: Connected to DB
   db2 connection: Modified connection
   db3 connection: Modified connection

---

Check Your Understanding
-------------------------

.. mchoice:: oop_identity_is_vs_eq_mcq
   :answer_a: They are identical
   :answer_b: is checks identity, == checks equality
   :answer_c: is checks equality, == checks identity
   :answer_d: is is faster but they check the same thing
   :correct: b
   :feedback_a: They check different things!
   :feedback_b: Correct! 'is' checks if objects are the same in memory, '==' checks if values are equal.
   :feedback_c: You have it backwards!
   :feedback_d: They check fundamentally different things, not just speed difference.

   What's the difference between ``is`` and ``==``?

.. mchoice:: oop_identity_none_check_mcq
   :answer_a: if x == None:
   :answer_b: if x is None:
   :answer_c: if not x:
   :answer_d: if x is not True:
   :correct: b
   :feedback_a: While this works, 'is None' is the Pythonic way.
   :feedback_b: Correct! Always use 'is None' for checking None.
   :feedback_c: This checks truthiness, not specifically None.
   :feedback_d: This is wrong and checks if x is literally not the True object.

   What's the correct way to check if a variable is None?

.. mchoice:: oop_identity_interning_mcq
   :answer_a: All integers are interned
   :answer_b: Integers from -5 to 256 are typically interned
   :answer_c: No integers are interned
   :answer_d: Only 0 and 1 are interned
   :correct: b
   :feedback_a: Only small integers in a specific range are interned.
   :feedback_b: Correct! CPython interns integers from -5 to 256 for optimization.
   :feedback_c: Small integers are interned for performance.
   :feedback_d: Many more integers are interned, not just 0 and 1.

   Which integers are typically interned in Python?

.. mchoice:: oop_identity_id_function_mcq
   :answer_a: Returns the object's value
   :answer_b: Returns the object's type
   :answer_c: Returns the object's unique identity (memory address)
   :answer_d: Returns the object's string representation
   :correct: c
   :feedback_a: That would be the object itself or its __str__.
   :feedback_b: That's what type() does.
   :feedback_c: Correct! id() returns a unique identifier (typically memory address).
   :feedback_d: That's what str() or repr() does.

   What does the ``id()`` function return?

.. mchoice:: oop_identity_custom_eq_mcq
   :answer_a: Both can be customized
   :answer_b: == can be customized, is cannot
   :answer_c: is can be customized, == cannot
   :answer_d: Neither can be customized
   :correct: b
   :feedback_a: 'is' cannot be customized—it always checks memory identity.
   :feedback_b: Correct! You can define __eq__ to customize ==, but 'is' always checks identity.
   :feedback_c: 'is' cannot be overridden.
   :feedback_d: '==' can be customized with __eq__.

   Which operator can be customized by implementing ``__eq__``?

---

Key Takeaways
-------------

.. important::
   **Section 3 Summary: Identity vs Equality**

   ✅ **Core Concepts:**
      - **Identity (is):** Same object in memory
      - **Equality (==):** Same value
      - ``a is b`` ⟺ ``id(a) == id(b)``

   ✅ **When to Use:**
      - **Use is:** For ``None``, singletons, sentinel values
      - **Use ==:** For all value comparisons
      - **Never use is:** For strings, numbers, collections

   ✅ **Object Interning:**
      - ``None``, ``True``, ``False`` — always interned
      - Small integers (-5 to 256) — typically interned
      - Short strings — often interned
      - Lists, dicts — never interned
      - **Don't rely on interning behavior!**

   ✅ **The ``id()`` Function:**
      - Returns unique object identifier
      - Typically the memory address
      - Two objects with same id are the same object

   ✅ **Custom Equality:**
      - Implement ``__eq__`` to customize ``==``
      - ``is`` cannot be customized
      - This is why ``is None`` is safer than ``== None``

   ✅ **Common Pitfalls:**
      - ❌ Using ``is`` for value comparisons
      - ❌ Using ``==`` for ``None`` checks
      - ❌ Relying on string/number interning
      - ❌ Mutable default arguments

---

What's Next?
------------

You now understand the crucial difference between identity and equality! Next, you'll dive deeper into **class introspection** — examining class attributes, methods, and the class hierarchy.

**In Section 4: Class Introspection**, you'll learn:

- The ``__dict__`` attribute for viewing all attributes
- The ``__module__`` attribute
- The ``__bases__`` attribute for parent classes
- The ``__mro__`` attribute for method resolution order
- Introspecting class and instance attributes
- Practical use cases for class introspection

Ready to peek inside classes? Let's go! 🚀

---

.. note::
   **✅ Section 3 Complete!**

   You've learned:
   - [✓] The difference between identity and equality
   - [✓] The ``is`` and ``is not`` operators
   - [✓] The ``id()`` function
   - [✓] When to use ``is`` vs ``==``
   - [✓] Python's object interning behavior
   - [✓] Custom equality with ``__eq__``
   - [✓] Common pitfalls and how to avoid them

   **Ready to introspect classes?** → Continue to Section 4!