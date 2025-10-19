..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 1: Object Introspection
================================

.. index:: introspection, hasattr, getattr, setattr, delattr, dir

Introduction
------------

You've learned the basics of classes and objects. Now it's time to explore Python's **introspection** capabilities — the ability to examine objects, classes, and their attributes at runtime.

Introspection is a powerful feature that allows your code to:

- **Discover** what attributes an object has
- **Check** if an object has a specific attribute
- **Access** attributes dynamically by name
- **Modify** attributes programmatically
- **Inspect** class structure and relationships

In this section, you'll learn:

- The ``hasattr()`` function for checking attribute existence
- The ``getattr()`` function for dynamic attribute access
- The ``setattr()`` function for dynamic attribute modification
- The ``delattr()`` function for dynamic attribute deletion
- The ``dir()`` function for listing all attributes
- Practical use cases for introspection in real code

By the end, you'll be able to write flexible, dynamic code that adapts to different object types at runtime!

---

What Is Introspection?
-----------------------

.. index:: introspection definition, runtime inspection

**Introspection** is the ability to examine the type, attributes, and methods of an object at runtime.

Think of introspection like looking at an object's "blueprint" while your program is running:

.. activecode:: oop_intro_introspection_demo
   :language: python

   class Dog:
       species = "Canis familiaris"

       def __init__(self, name, age):
           self.name = name
           self.age = age

       def bark(self):
           return f"{self.name} says Woof!"

   # Create an instance
   buddy = Dog("Buddy", 3)

   # Introspection: Ask questions about the object
   print(f"What type is buddy? {type(buddy)}")
   print(f"Does buddy have a 'name' attribute? {hasattr(buddy, 'name')}")
   print(f"What attributes does buddy have? {dir(buddy)[:5]}...")  # First 5
   print(f"What's buddy's name? {getattr(buddy, 'name')}")

**Output:**

::

   What type is buddy? <class '__main__.Dog'>
   Does buddy have a 'name' attribute? True
   What attributes does buddy have? ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__']...
   What's buddy's name? Buddy

**Why is introspection useful?**

- **Dynamic programming** — Work with objects without knowing their exact structure
- **Debugging** — Examine objects to understand their state
- **Frameworks** — Build tools that work with any class (like ORMs, serializers)
- **Plugin systems** — Load and use modules discovered at runtime

---

The ``hasattr()`` Function
---------------------------

.. index:: hasattr function, attribute checking

**``hasattr(object, name)``** checks if an object has an attribute with the given name.

**Syntax:**

.. code-block:: python

   hasattr(object, attribute_name)  # Returns True or False

**Example:**

.. activecode:: oop_intro_hasattr_basic
   :language: python

   class Person:
       def __init__(self, name, age):
           self.name = name
           self.age = age

       def greet(self):
           return f"Hello, I'm {self.name}!"

   alice = Person("Alice", 30)

   # Check for attributes
   print(f"Has 'name'? {hasattr(alice, 'name')}")
   print(f"Has 'age'? {hasattr(alice, 'age')}")
   print(f"Has 'greet'? {hasattr(alice, 'greet')}")
   print(f"Has 'salary'? {hasattr(alice, 'salary')}")

   # Checking methods
   print(f"Has '__init__'? {hasattr(alice, '__init__')}")

**Output:**

::

   Has 'name'? True
   Has 'age'? True
   Has 'greet'? True
   Has 'salary'? False
   Has '__init__'? True

.. note::
   ``hasattr()`` returns ``True`` for:

   - Instance attributes (``self.name``)
   - Class attributes (shared by all instances)
   - Methods (functions defined in the class)
   - Special methods (``__init__``, ``__str__``, etc.)

---

**Practical Use Case: Safe Attribute Access**

.. activecode:: oop_intro_hasattr_safe_access
   :language: python

   class Product:
       def __init__(self, name, price):
           self.name = name
           self.price = price

   class DiscountProduct(Product):
       def __init__(self, name, price, discount):
           super().__init__(name, price)
           self.discount = discount

   # Function that works with both types
   def display_product(product):
       print(f"Product: {product.name}")
       print(f"Price: ${product.price}")

       # Safely check for discount
       if hasattr(product, 'discount'):
           discounted = product.price * (1 - product.discount)
           print(f"Discounted Price: ${discounted:.2f}")
       else:
           print("No discount available")
       print()

   # Test with both types
   regular = Product("Laptop", 1000)
   special = DiscountProduct("Phone", 800, 0.2)

   display_product(regular)
   display_product(special)

**Output:**

::

   Product: Laptop
   Price: $1000
   No discount available

   Product: Phone
   Price: $800
   Discounted Price: $640.00

This pattern allows one function to work with multiple object types!

---

The ``getattr()`` Function
---------------------------

.. index:: getattr function, dynamic attribute access

**``getattr(object, name[, default])``** gets an attribute value by name.

**Syntax:**

.. code-block:: python

   getattr(object, attribute_name)
   getattr(object, attribute_name, default_value)

**Example:**

.. activecode:: oop_intro_getattr_basic
   :language: python

   class Car:
       def __init__(self, brand, model, year):
           self.brand = brand
           self.model = model
           self.year = year

   my_car = Car("Toyota", "Camry", 2020)

   # Static access (normal way)
   print(f"Brand: {my_car.brand}")

   # Dynamic access (using getattr)
   print(f"Brand: {getattr(my_car, 'brand')}")
   print(f"Model: {getattr(my_car, 'model')}")

   # With default value (attribute doesn't exist)
   print(f"Color: {getattr(my_car, 'color', 'Unknown')}")

   # Without default (raises AttributeError)
   try:
       print(getattr(my_car, 'color'))
   except AttributeError as e:
       print(f"Error: {e}")

**Output:**

::

   Brand: Toyota
   Brand: Toyota
   Model: Camry
   Color: Unknown
   Error: 'Car' object has no attribute 'color'

**When to use ``getattr()``:**

- When the attribute name is stored in a variable
- When you want to provide a default value
- When building generic code that works with different object types

---

**Practical Use Case: Configuration Object**

.. activecode:: oop_intro_getattr_config
   :language: python

   class Config:
       def __init__(self):
           self.debug = True
           self.timeout = 30
           self.max_retries = 3

   config = Config()

   # Get configuration values dynamically
   settings = ['debug', 'timeout', 'max_retries', 'cache_size']

   print("Configuration:")
   for setting in settings:
       value = getattr(config, setting, "Not configured")
       print(f"  {setting}: {value}")

**Output:**

::

   Configuration:
     debug: True
     timeout: 30
     max_retries: 3
     cache_size: Not configured

---

**Practical Use Case: Flexible API Calls**

.. activecode:: oop_intro_getattr_api
   :language: python

   class APIClient:
       def get_users(self):
           return ["Alice", "Bob", "Charlie"]

       def get_posts(self):
           return ["Post 1", "Post 2", "Post 3"]

       def get_comments(self):
           return ["Comment A", "Comment B"]

   client = APIClient()

   # Call methods dynamically based on user input
   resources = ['users', 'posts', 'comments', 'likes']

   for resource in resources:
       method_name = f'get_{resource}'

       if hasattr(client, method_name):
           method = getattr(client, method_name)
           data = method()
           print(f"{resource.title()}: {len(data)} items")
       else:
           print(f"{resource.title()}: Not available")

**Output:**

::

   Users: 3 items
   Posts: 3 items
   Comments: 2 items
   Likes: Not available

---

The ``setattr()`` Function
---------------------------

.. index:: setattr function, dynamic attribute modification

**``setattr(object, name, value)``** sets an attribute value dynamically.

**Syntax:**

.. code-block:: python

   setattr(object, attribute_name, value)

**Example:**

.. activecode:: oop_intro_setattr_basic
   :language: python

   class Employee:
       def __init__(self, name):
           self.name = name

   emp = Employee("Alice")

   # Static assignment (normal way)
   emp.salary = 50000

   # Dynamic assignment (using setattr)
   setattr(emp, 'department', 'Engineering')
   setattr(emp, 'years_experience', 5)

   # Verify
   print(f"Name: {emp.name}")
   print(f"Salary: ${emp.salary}")
   print(f"Department: {emp.department}")
   print(f"Experience: {emp.years_experience} years")

   # Can also modify existing attributes
   setattr(emp, 'salary', 55000)
   print(f"Updated Salary: ${emp.salary}")

**Output:**

::

   Name: Alice
   Salary: $50000
   Department: Engineering
   Experience: 5 years
   Updated Salary: $55000

---

**Practical Use Case: Bulk Attribute Setting**

.. activecode:: oop_intro_setattr_bulk
   :language: python

   class User:
       def __init__(self, username):
           self.username = username

   user = User("john_doe")

   # Set multiple attributes from a dictionary
   user_data = {
       'email': 'john@example.com',
       'age': 28,
       'city': 'New York',
       'verified': True
   }

   for key, value in user_data.items():
       setattr(user, key, value)

   # Verify all attributes were set
   print(f"Username: {user.username}")
   print(f"Email: {user.email}")
   print(f"Age: {user.age}")
   print(f"City: {user.city}")
   print(f"Verified: {user.verified}")

**Output:**

::

   Username: john_doe
   Email: john@example.com
   Age: 28
   City: New York
   Verified: True

This pattern is common in ORMs (Object-Relational Mappers) and data loading!

---

The ``delattr()`` Function
---------------------------

.. index:: delattr function, dynamic attribute deletion

**``delattr(object, name)``** deletes an attribute dynamically.

**Syntax:**

.. code-block:: python

   delattr(object, attribute_name)

**Example:**

.. activecode:: oop_intro_delattr_basic
   :language: python

   class TempObject:
       def __init__(self):
           self.keep_this = "Important"
           self.delete_this = "Temporary"
           self.also_delete = "Also temporary"

   obj = TempObject()

   print("Before deletion:")
   print(f"  keep_this: {obj.keep_this}")
   print(f"  delete_this: {obj.delete_this}")
   print(f"  also_delete: {obj.also_delete}")

   # Delete attributes dynamically
   delattr(obj, 'delete_this')
   delattr(obj, 'also_delete')

   print("\nAfter deletion:")
   print(f"  keep_this: {obj.keep_this}")
   print(f"  delete_this exists? {hasattr(obj, 'delete_this')}")
   print(f"  also_delete exists? {hasattr(obj, 'also_delete')}")

**Output:**

::

   Before deletion:
     keep_this: Important
     delete_this: Temporary
     also_delete: Also temporary

   After deletion:
     keep_this: Important
     delete_this exists? False
     also_delete exists? False

.. warning::
   Deleting attributes that don't exist raises ``AttributeError``:

   .. code-block:: python

      delattr(obj, 'nonexistent')  # AttributeError!

   Use ``hasattr()`` first to check if it exists!

---

The ``dir()`` Function
-----------------------

.. index:: dir function, attribute listing

**``dir([object])``** returns a list of all attributes and methods of an object.

**Syntax:**

.. code-block:: python

   dir()         # Current scope
   dir(object)   # Object's attributes

**Example:**

.. activecode:: oop_intro_dir_basic
   :language: python

   class MyClass:
       class_var = "I'm a class variable"

       def __init__(self, x):
           self.x = x

       def my_method(self):
           pass

   obj = MyClass(10)

   # Get all attributes
   all_attrs = dir(obj)

   # Filter to show different types
   public = [a for a in all_attrs if not a.startswith('_')]
   dunder = [a for a in all_attrs if a.startswith('__')]

   print(f"Total attributes: {len(all_attrs)}")
   print(f"\nPublic attributes: {public}")
   print(f"\nFirst 5 dunder attributes: {dunder[:5]}")

**Output:**

::

   Total attributes: 28

   Public attributes: ['class_var', 'my_method', 'x']

   First 5 dunder attributes: ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__']

---

**Practical Use Case: Object Inspector**

.. activecode:: oop_intro_dir_inspector
   :language: python

   def inspect_object(obj):
       """Display comprehensive information about an object."""
       print(f"{'='*60}")
       print(f"OBJECT INSPECTION: {type(obj).__name__}")
       print(f"{'='*60}")

       attrs = dir(obj)

       # Categorize attributes
       public_attrs = [a for a in attrs if not a.startswith('_')]
       private_attrs = [a for a in attrs if a.startswith('_') and not a.startswith('__')]
       dunder_attrs = [a for a in attrs if a.startswith('__') and a.endswith('__')]

       print(f"\n📊 Attribute Summary:")
       print(f"   Total: {len(attrs)}")
       print(f"   Public: {len(public_attrs)}")
       print(f"   Private: {len(private_attrs)}")
       print(f"   Dunder: {len(dunder_attrs)}")

       print(f"\n🔓 Public Attributes:")
       for attr in public_attrs:
           value = getattr(obj, attr)
           attr_type = "method" if callable(value) else "attribute"
           print(f"   • {attr} ({attr_type})")

       print(f"\n{'='*60}\n")

   # Test with a class
   class Dog:
       species = "Canis familiaris"

       def __init__(self, name, age):
           self.name = name
           self.age = age

       def bark(self):
           return "Woof!"

       def _internal_method(self):
           return "Internal"

   buddy = Dog("Buddy", 3)
   inspect_object(buddy)

**Output:**

::

   ============================================================
   OBJECT INSPECTION: Dog
   ============================================================

   📊 Attribute Summary:
      Total: 29
      Public: 4
      Private: 1
      Dunder: 24

   🔓 Public Attributes:
      • age (attribute)
      • bark (method)
      • name (attribute)
      • species (attribute)

   ============================================================

---

Combining Introspection Functions
----------------------------------

.. index:: introspection patterns, dynamic object handling

The real power comes from combining these functions!

**Pattern: Safe Dynamic Method Calling**

.. activecode:: oop_intro_combined_pattern
   :language: python

   class Calculator:
       def add(self, a, b):
           return a + b

       def subtract(self, a, b):
           return a - b

       def multiply(self, a, b):
           return a * b

       def divide(self, a, b):
           if b == 0:
               return "Error: Division by zero"
           return a / b

   calc = Calculator()

   # User input simulation
   operations = [
       ('add', 10, 5),
       ('multiply', 3, 7),
       ('divide', 20, 4),
       ('modulo', 10, 3),  # This doesn't exist!
   ]

   print("Calculator Results:")
   for operation, num1, num2 in operations:
       # Check if operation exists
       if hasattr(calc, operation):
           # Get the method
           method = getattr(calc, operation)
           # Call it
           result = method(num1, num2)
           print(f"  {num1} {operation} {num2} = {result}")
       else:
           print(f"  {operation}: Operation not supported")

**Output:**

::

   Calculator Results:
     10 add 5 = 15
     3 multiply 7 = 21
     20 divide 4 = 5.0
     modulo: Operation not supported

---

**Pattern: Plugin System**

.. activecode:: oop_intro_plugin_system
   :language: python

   class PluginManager:
       def __init__(self):
           self.plugins = {}

       def register_plugin(self, name, plugin):
           """Register a plugin object."""
           self.plugins[name] = plugin

       def execute_plugin(self, name, *args, **kwargs):
           """Execute a plugin's run method if it exists."""
           if name not in self.plugins:
               return f"Plugin '{name}' not found"

           plugin = self.plugins[name]

           if hasattr(plugin, 'run'):
               method = getattr(plugin, 'run')
               return method(*args, **kwargs)
           else:
               return f"Plugin '{name}' has no 'run' method"

   # Sample plugins
   class GreeterPlugin:
       def run(self, name):
           return f"Hello, {name}!"

   class CalculatorPlugin:
       def run(self, a, b, operation='add'):
           if operation == 'add':
               return a + b
           elif operation == 'multiply':
               return a * b

   class BrokenPlugin:
       pass  # No run method!

   # Use the plugin system
   manager = PluginManager()
   manager.register_plugin('greeter', GreeterPlugin())
   manager.register_plugin('calculator', CalculatorPlugin())
   manager.register_plugin('broken', BrokenPlugin())

   print(manager.execute_plugin('greeter', 'Alice'))
   print(manager.execute_plugin('calculator', 5, 3))
   print(manager.execute_plugin('calculator', 5, 3, operation='multiply'))
   print(manager.execute_plugin('broken'))
   print(manager.execute_plugin('nonexistent'))

**Output:**

::

   Hello, Alice!
   8
   15
   Plugin 'broken' has no 'run' method
   Plugin 'nonexistent' not found

---

Check Your Understanding
-------------------------

.. mchoice:: oop_intro_hasattr_understanding
   :answer_a: True
   :answer_b: False
   :answer_c: AttributeError
   :answer_d: None
   :correct: b
   :feedback_a: hasattr returns True only if the attribute exists.
   :feedback_b: Correct! hasattr returns False when an attribute doesn't exist.
   :feedback_c: hasattr doesn't raise errors; it returns True or False.
   :feedback_d: hasattr always returns a boolean, never None.

   What does ``hasattr(obj, 'missing')`` return if ``obj`` doesn't have an attribute called ``missing``?

.. mchoice:: oop_intro_getattr_default
   :answer_a: Raises AttributeError
   :answer_b: Returns None
   :answer_c: Returns the default value
   :answer_d: Returns an empty string
   :correct: c
   :feedback_a: That only happens if you don't provide a default value.
   :feedback_b: None is only returned if you explicitly pass None as the default.
   :feedback_c: Correct! getattr returns the default value when the attribute doesn't exist.
   :feedback_d: Empty string is not automatically returned.

   What does ``getattr(obj, 'missing', 'default')`` return if the attribute doesn't exist?

.. mchoice:: oop_intro_dir_returns
   :answer_a: Only public attributes
   :answer_b: Only methods
   :answer_c: All attributes including dunder methods
   :answer_d: Only instance attributes
   :correct: c
   :feedback_a: dir() returns all attributes, not just public ones.
   :feedback_b: dir() returns both attributes and methods.
   :feedback_c: Correct! dir() returns everything: public, private, and dunder attributes/methods.
   :feedback_d: dir() returns both instance and class attributes.

   What does ``dir(obj)`` return?

.. mchoice:: oop_intro_setattr_effect
   :answer_a: Only creates new attributes
   :answer_b: Only modifies existing attributes
   :answer_c: Can both create new and modify existing attributes
   :answer_d: Only works with class attributes
   :correct: c
   :feedback_a: setattr can also modify existing attributes.
   :feedback_b: setattr can also create new attributes if they don't exist.
   :feedback_c: Correct! setattr can both create new attributes and modify existing ones.
   :feedback_d: setattr works with both instance and class attributes.

   What can ``setattr()`` do?

---

Key Takeaways
-------------

.. important::
   **Section 1 Summary: Object Introspection**

   ✅ **Introspection Functions:**
      - ``hasattr(obj, name)`` — Check if attribute exists
      - ``getattr(obj, name, default)`` — Get attribute value dynamically
      - ``setattr(obj, name, value)`` — Set attribute value dynamically
      - ``delattr(obj, name)`` — Delete attribute dynamically
      - ``dir(obj)`` — List all attributes

   ✅ **Common Patterns:**
      - **Safe access:** Use ``hasattr()`` before accessing
      - **Default values:** Use ``getattr()`` with default
      - **Bulk operations:** Loop through attribute names
      - **Dynamic dispatch:** Call methods by name
      - **Plugin systems:** Load and use discovered functionality

   ✅ **When to Use Introspection:**
      - Working with objects of unknown types
      - Building frameworks and libraries
      - Creating flexible, generic code
      - Debugging and object inspection
      - Dynamic attribute manipulation

   ✅ **Best Practices:**
      - Always check with ``hasattr()`` before using ``getattr()`` without a default
      - Provide defaults with ``getattr()`` when possible
      - Use introspection for flexibility, not obfuscation
      - Document expected attributes in docstrings

---

What's Next?
------------

You now understand how to introspect and manipulate objects dynamically. Next, you'll learn about **type checking** — determining what kind of object you're working with!

**In Section 2: Type Checking**, you'll learn:

- The ``isinstance()`` function for type checking
- The ``issubclass()`` function for class relationships
- When to use type checking vs. duck typing
- Type hints and runtime type validation
- Best practices for type checking

Ready to master type checking? Let's go! 🚀

---

.. note::
   **✅ Section 1 Complete!**

   You've learned:
   - [✓] What introspection is and why it's useful
   - [✓] ``hasattr()`` for checking attribute existence
   - [✓] ``getattr()`` for dynamic attribute access
   - [✓] ``setattr()`` for dynamic attribute modification
   - [✓] ``delattr()`` for dynamic attribute deletion
   - [✓] ``dir()`` for listing all attributes
   - [✓] Practical patterns combining these functions

   **Ready for type checking?** → Continue to Section 2!