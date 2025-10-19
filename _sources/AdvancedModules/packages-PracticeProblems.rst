..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".


Practice and Mastery
====================

Welcome to the final lesson of Chapter 26! This is where everything comes together. You've learned about the ``math`` module, ``platform`` module, package creation, import mechanics, module introspection, and pip. Now it's time to **master** these concepts through practice and exploration.

This lesson covers:

- **Nested packages** and complex package structures
- **Naming conventions** (public vs. private variables)
- **Comprehensive practice problems** across all topics
- **Real-world scenarios** and professional patterns
- **Self-assessment checklist** to verify your mastery

Let's finish strong! 🚀

Nested Packages and Complex Structures
---------------------------------------

What Are Nested Packages?
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Nested packages** are packages inside packages — creating a hierarchy of modules.

**Structure example:**

.. code-block:: text

   myproject/
   ├── __init__.py
   ├── database/
   │   ├── __init__.py
   │   ├── connection.py
   │   └── queries.py
   ├── api/
   │   ├── __init__.py
   │   ├── routes.py
   │   └── handlers.py
   └── utils/
       ├── __init__.py
       ├── validators.py
       └── formatters.py

This creates the following importable structure:

.. code-block:: python

   import myproject
   import myproject.database
   import myproject.database.connection
   import myproject.api.routes
   import myproject.utils.validators

.. note::

   **Each directory needs ``__init__.py``** to be recognized as a package, even nested ones!

Why Use Nested Packages?
~~~~~~~~~~~~~~~~~~~~~~~~~

**Nested packages help organize large projects:**

1. **Logical grouping** — Related modules together
2. **Namespace organization** — Clear hierarchies (e.g., ``myproject.database.queries``)
3. **Code navigation** — Easier to find code
4. **Team collaboration** — Different teams work on different subpackages

**Real-world example: Django framework**

.. code-block:: text

   django/
   ├── contrib/
   │   ├── admin/
   │   ├── auth/
   │   └── sessions/
   ├── db/
   │   ├── models/
   │   └── backends/
   └── core/
       ├── management/
       └── mail/

Creating Nested Packages
~~~~~~~~~~~~~~~~~~~~~~~~~

Let's create a nested package structure:

.. code-block:: text

   mathtools/
   ├── __init__.py
   ├── basic/
   │   ├── __init__.py
   │   └── operations.py
   └── advanced/
       ├── __init__.py
       └── calculus.py

**File: mathtools/__init__.py**

.. code-block:: python

   """
   MathTools: A collection of mathematical utilities.
   """
   __version__ = "1.0.0"

**File: mathtools/basic/__init__.py**

.. code-block:: python

   """
   Basic mathematical operations.
   """
   from .operations import add, multiply

**File: mathtools/basic/operations.py**

.. code-block:: python

   def add(a, b):
       """Add two numbers."""
       return a + b

   def multiply(a, b):
       """Multiply two numbers."""
       return a * b

**File: mathtools/advanced/__init__.py**

.. code-block:: python

   """
   Advanced mathematical operations.
   """
   from .calculus import derivative_approx

**File: mathtools/advanced/calculus.py**

.. code-block:: python

   def derivative_approx(f, x, h=0.0001):
       """
       Approximate derivative of function f at point x.
       """
       return (f(x + h) - f(x)) / h

**Usage:**

.. code-block:: python

   # Import from nested packages
   from mathtools.basic import add, multiply
   from mathtools.advanced import derivative_approx

   print(add(5, 3))        # 8
   print(multiply(4, 7))   # 28

   # Approximate derivative of f(x) = x^2 at x=3
   def f(x):
       return x ** 2

   print(derivative_approx(f, 3))  # ≈ 6.0

Importing from Nested Packages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Different import styles:**

.. code-block:: python

   # 1. Import entire subpackage
   import mathtools.basic
   result = mathtools.basic.operations.add(2, 3)

   # 2. Import specific module
   from mathtools.basic import operations
   result = operations.add(2, 3)

   # 3. Import specific function
   from mathtools.basic.operations import add
   result = add(2, 3)

   # 4. Import from __init__.py
   from mathtools.basic import add  # Works if imported in __init__.py
   result = add(2, 3)

.. important::

   **Best practice for nested packages:**

   - Use ``__init__.py`` to expose commonly used functions
   - Keep deep nesting minimal (2-3 levels max)
   - Document the package structure clearly
   - Use relative imports within the same package

.. activecode:: nested_package_simulation
   :nocodelens:

   # Simulating a nested package structure

   class Module:
       """Represents a Python module."""
       def __init__(self, name, functions=None):
           self.name = name
           self.functions = functions or {}

       def __repr__(self):
           return f"<module '{self.name}'>"

   class Package:
       """Represents a Python package."""
       def __init__(self, name):
           self.name = name
           self.modules = {}
           self.subpackages = {}

       def add_module(self, name, functions):
           """Add a module to this package."""
           self.modules[name] = Module(f"{self.name}.{name}", functions)

       def add_subpackage(self, name):
           """Add a subpackage to this package."""
           subpkg = Package(f"{self.name}.{name}")
           self.subpackages[name] = subpkg
           return subpkg

       def show_structure(self, indent=0):
           """Display package structure."""
           prefix = "  " * indent
           print(f"{prefix}📦 {self.name}/")

           for mod_name in self.modules:
               print(f"{prefix}  📄 {mod_name}.py")

           for subpkg in self.subpackages.values():
               subpkg.show_structure(indent + 1)

   # Create a nested package structure
   myproject = Package("myproject")

   # Add subpackages
   database = myproject.add_subpackage("database")
   database.add_module("connection", {"connect": "function"})
   database.add_module("queries", {"select": "function", "insert": "function"})

   api = myproject.add_subpackage("api")
   api.add_module("routes", {"get_user": "function"})
   api.add_module("handlers", {"handle_request": "function"})

   utils = myproject.add_subpackage("utils")
   utils.add_module("validators", {"validate_email": "function"})

   # Show structure
   print("🏗️  Package Structure:\n")
   myproject.show_structure()

   print("\n✓ All packages and modules created!")

.. mchoice:: nested_packages_understanding
   :answer_a: Only the top-level package needs __init__.py
   :answer_b: Every directory in the hierarchy needs __init__.py
   :answer_c: Only the deepest directories need __init__.py
   :answer_d: __init__.py is optional for all packages
   :correct: b
   :feedback_a: Each directory that should be a package needs __init__.py.
   :feedback_b: Correct! Every directory that should be recognized as a package needs an __init__.py file.
   :feedback_c: All levels need __init__.py, not just the deepest.
   :feedback_d: While Python 3.3+ has namespace packages, it's best practice to include __init__.py.

   In a nested package structure, which directories need an ``__init__.py`` file?

Public vs. Private Naming Conventions
--------------------------------------

Understanding Naming Conventions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Python uses **naming conventions** to indicate whether variables, functions, or classes are intended for public or private use.

.. list-table:: Naming Convention Guide
   :header-rows: 1
   :widths: 20 30 50

   * - Convention
     - Meaning
     - Example
   * - ``name``
     - **Public** — Intended for external use
     - ``def calculate()``
   * - ``_name``
     - **Internal** — "Private" (by convention)
     - ``def _helper()``
   * - ``__name``
     - **Name mangling** — Strongly private
     - ``def __internal()``
   * - ``__name__``
     - **Magic/Dunder** — Special Python methods
     - ``__init__()``

.. important::

   **Python has no true "private" variables!**

   - Naming conventions are **social contracts**, not enforced by the language
   - A single underscore ``_`` means "treat this as internal"
   - Double underscore ``__`` triggers name mangling for stronger privacy

Single Underscore: ``_name``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Convention:** "This is internal — use at your own risk."

**Example:**

.. code-block:: python

   # mymodule.py

   def public_function():
       """This function is part of the public API."""
       return _internal_helper()

   def _internal_helper():
       """
       This is an internal function.
       Not intended for external use.
       """
       return "Internal result"

**Usage:**

.. code-block:: python

   import mymodule

   # ✓ GOOD: Use public function
   result = mymodule.public_function()

   # ⚠️ ALLOWED BUT DISCOURAGED: Use internal function
   result = mymodule._internal_helper()  # Works, but you shouldn't

**What happens with ``from module import *``?**

.. code-block:: python

   from mymodule import *

   # Only imports public names (not starting with _)
   public_function()   # ✓ Works
   _internal_helper()  # ✗ NameError: not imported

Double Underscore: ``__name`` (Name Mangling)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Convention:** "This is strongly private — Python will mangle the name."

**Name mangling:** Python automatically renames ``__name`` to ``_ClassName__name`` inside classes.

**Example:**

.. code-block:: python

   class BankAccount:
       def __init__(self, balance):
           self.__balance = balance  # Private attribute

       def deposit(self, amount):
           self.__balance += amount

       def get_balance(self):
           return self.__balance

       def __validate(self, amount):
           """Private method."""
           return amount > 0

**Usage:**

.. code-block:: python

   account = BankAccount(1000)

   # ✓ Access through public methods
   account.deposit(500)
   print(account.get_balance())  # 1500

   # ✗ Direct access fails
   print(account.__balance)  # AttributeError

   # ⚠️ But you can still access it (not recommended!)
   print(account._BankAccount__balance)  # 1500 (name mangled)

.. note::

   **When to use each convention:**

   - **No underscore:** Public API, documented, stable
   - **Single underscore ``_``:** Internal implementation details, may change
   - **Double underscore ``__``:** Prevent accidental name collisions in subclasses

   **Most common:** Single underscore for "internal" variables/functions

Dunder Methods: ``__name__``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Double underscores on both sides** are reserved for Python's special methods.

**Common dunder methods:**

.. code-block:: python

   class MyClass:
       def __init__(self):
           """Constructor."""
           pass

       def __str__(self):
           """String representation."""
           return "MyClass instance"

       def __len__(self):
           """Length (for len() function)."""
           return 42

       def __getitem__(self, key):
           """Indexing (for obj[key])."""
           return key * 2

.. important::

   **Never invent your own dunder methods!**

   - ✗ ``def __my_method():`` — Don't do this
   - ✓ Use existing dunders for special behavior
   - ✓ Use single underscore for internal methods

.. activecode:: naming_conventions_demo
   :nocodelens:

   # Demonstrating naming conventions

   class Calculator:
       """A calculator with public and private methods."""

       def __init__(self):
           self.history = []           # Public
           self._cache = {}            # Internal (single underscore)
           self.__secret_key = "XYZ"   # Private (name mangled)

       def calculate(self, operation, a, b):
           """
           Public method: Perform a calculation.
           """
           result = self._perform_operation(operation, a, b)
           self.history.append(f"{a} {operation} {b} = {result}")
           return result

       def _perform_operation(self, operation, a, b):
           """
           Internal method: Execute the actual operation.
           (Single underscore = internal use)
           """
           if operation == '+':
               return a + b
           elif operation == '*':
               return a * b
           else:
               return None

       def __validate_secret(self):
           """
           Private method: Validate secret key.
           (Double underscore = name mangling)
           """
           return self.__secret_key == "XYZ"

       def show_access_demo(self):
           """Demonstrate which methods are accessible."""
           print("🔍 Access Demonstration:\n")

           # Public method
           print("✓ Public method: calculate('+', 5, 3) =", self.calculate('+', 5, 3))

           # Internal method (works, but discouraged)
           print("⚠️  Internal method: _perform_operation('*', 4, 2) =",
                 self._perform_operation('*', 4, 2))

           # Private method (name mangled)
           print("🔒 Private method: __validate_secret() =", self.__validate_secret())

           print("\n📝 History:", self.history)

   # Create calculator
   calc = Calculator()
   calc.show_access_demo()

   # Try to access private attribute (will work due to name mangling)
   print("\n🔓 Accessing name-mangled attribute:")
   print("calc._Calculator__secret_key =", calc._Calculator__secret_key)
   print("(Not recommended, but possible!)")

.. mchoice:: naming_conventions_understanding_1
   :answer_a: def __helper()
   :answer_b: def _helper()
   :answer_c: def helper()
   :answer_d: def helper_()
   :correct: b
   :feedback_a: Double underscore triggers name mangling, typically used in classes.
   :feedback_b: Correct! Single underscore indicates an internal/private function by convention.
   :feedback_c: No underscore means the function is public.
   :feedback_d: Trailing underscore is used to avoid naming conflicts with keywords.

   Which naming convention indicates a "private" (internal) function in a module?

.. mchoice:: naming_conventions_understanding_2
   :answer_a: Python enforces true privacy for these variables
   :answer_b: Python mangles the name to _ClassName__variable
   :answer_c: The variable cannot be accessed outside the class
   :answer_d: The variable is deleted after use
   :correct: b
   :feedback_a: Python doesn't enforce true privacy; name mangling just makes access harder.
   :feedback_b: Correct! Python renames __variable to _ClassName__variable to avoid accidental collisions.
   :feedback_c: It can still be accessed using the mangled name.
   :feedback_d: Name mangling doesn't delete variables.

   What happens to a variable named ``__variable`` inside a class?

Comprehensive Practice: All Topics
-----------------------------------

Let's practice everything we've learned in this chapter!

Vocabulary Review
~~~~~~~~~~~~~~~~~

.. list-table:: Key Terms
   :header-rows: 1
   :widths: 25 75

   * - Term
     - Definition
   * - **Module**
     - A single Python file containing code
   * - **Package**
     - A directory containing modules and an ``__init__.py`` file
   * - **``__init__.py``**
     - Special file that marks a directory as a package
   * - **``sys.path``**
     - List of directories Python searches for modules
   * - **``dir()``**
     - Function that lists names in a module/object
   * - **``__name__``**
     - Special variable containing the module's name
   * - **``__main__``**
     - Value of ``__name__`` when a script is run directly
   * - **pip**
     - Python's package installer
   * - **PyPI**
     - Python Package Index (package repository)
   * - **Virtual environment**
     - Isolated Python environment for project dependencies
   * - **requirements.txt**
     - File listing project dependencies
   * - **Name mangling**
     - Python's renaming of ``__variable`` to ``_ClassName__variable``
   * - **Nested package**
     - Package inside another package

Math Module Quick Review
~~~~~~~~~~~~~~~~~~~~~~~~~

.. activecode:: math_module_review
   :nocodelens:

   import math

   print("📐 Math Module Quick Review\n")
   print("=" * 50)

   # Rounding functions
   print("Rounding:")
   print(f"  ceil(4.2) = {math.ceil(4.2)}")
   print(f"  floor(4.8) = {math.floor(4.8)}")
   print(f"  trunc(4.8) = {math.trunc(4.8)}")

   # Arithmetic functions
   print("\nArithmetic:")
   print(f"  factorial(5) = {math.factorial(5)}")
   print(f"  sqrt(16) = {math.sqrt(16)}")
   print(f"  pow(2, 3) = {math.pow(2, 3)}")

   # Constants
   print("\nConstants:")
   print(f"  pi = {math.pi:.5f}")
   print(f"  e = {math.e:.5f}")

   # Geometric
   print("\nGeometric:")
   print(f"  hypot(3, 4) = {math.hypot(3, 4)}")

Platform Module Quick Review
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. activecode:: platform_module_review
   :nocodelens:

   import platform

   print("💻 Platform Module Quick Review\n")
   print("=" * 50)

   print("System Information:")
   print(f"  Platform: {platform.platform()}")
   print(f"  System: {platform.system()}")
   print(f"  Machine: {platform.machine()}")

   print("\nPython Information:")
   print(f"  Version: {platform.python_version()}")
   print(f"  Implementation: {platform.python_implementation()}")
   print(f"  Version tuple: {platform.python_version_tuple()}")

Mixed Multiple Choice Questions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. mchoice:: comprehensive_mcq_1
   :answer_a: math.ceil(x)
   :answer_b: math.floor(x)
   :answer_c: math.trunc(x)
   :answer_d: math.round(x)
   :correct: a
   :feedback_a: Correct! ceil() rounds up to the nearest integer.
   :feedback_b: floor() rounds down, not up.
   :feedback_c: trunc() removes the decimal part without rounding.
   :feedback_d: There is no math.round(); use built-in round().

   Which ``math`` function rounds **up** to the nearest integer?

.. mchoice:: comprehensive_mcq_2
   :answer_a: import sys; sys.modules
   :answer_b: import sys; sys.path
   :answer_c: import os; os.path
   :answer_d: import platform; platform.path()
   :correct: b
   :feedback_a: sys.modules shows loaded modules, not search paths.
   :feedback_b: Correct! sys.path is a list of directories Python searches for modules.
   :feedback_c: os.path is for file path operations, not module search paths.
   :feedback_d: There is no platform.path() function.

   Which statement displays the directories Python searches for modules?

.. mchoice:: comprehensive_mcq_3
   :answer_a: if __name__ == "__main__":
   :answer_b: if __name__ == "main":
   :answer_c: if __main__ == True:
   :answer_d: if __file__ == "__main__":
   :correct: a
   :feedback_a: Correct! This checks if the script is run directly (not imported).
   :feedback_b: Missing double underscores; should be "__main__" not "main".
   :feedback_c: __main__ is not a variable; use __name__ == "__main__".
   :feedback_d: __file__ contains the file path, not execution status.

   How do you check if a script is being run directly (not imported)?

.. mchoice:: comprehensive_mcq_4
   :answer_a: python -m pip install requests
   :answer_b: python pip install requests
   :answer_c: python install requests
   :answer_d: pip run install requests
   :correct: a
   :feedback_a: Correct! 'python -m pip' explicitly uses Python's pip module.
   :feedback_b: Missing '-m' flag to run pip as a module.
   :feedback_c: 'install' is not a Python command; use pip.
   :feedback_d: There is no 'pip run' command.

   What is the recommended way to install a package using pip?

.. mchoice:: comprehensive_mcq_5
   :answer_a: They are deleted and cannot be accessed
   :answer_b: They are not imported by 'from module import *'
   :answer_c: They cause a SyntaxError
   :answer_d: They are automatically documented
   :correct: b
   :feedback_a: They're not deleted; they just indicate internal use.
   :feedback_b: Correct! Names starting with '_' are not imported with 'import *'.
   :feedback_c: Single underscore prefix is valid Python syntax.
   :feedback_d: Underscore prefix doesn't affect documentation.

   What happens to names starting with ``_`` in a module?

.. mchoice:: comprehensive_mcq_6
   :answer_a: platform.python_version()
   :answer_b: sys.version
   :answer_c: python.version()
   :answer_d: __version__
   :correct: a
   :feedback_a: Correct! platform.python_version() returns the Python version as a string.
   :feedback_b: sys.version includes more info than just version number.
   :feedback_c: There is no 'python' module with a version() function.
   :feedback_d: __version__ is for package versions, not Python itself.

   Which function returns the Python version (e.g., "3.11.4")?

.. mchoice:: comprehensive_mcq_7
   :answer_a: Only the top-level __init__.py is required
   :answer_b: Each directory needs __init__.py to be a package
   :answer_c: __init__.py files are optional in Python 3
   :answer_d: Only directories with modules need __init__.py
   :correct: b
   :feedback_a: Each directory that should be a package needs __init__.py.
   :feedback_b: Correct! Each directory in a package hierarchy needs __init__.py.
   :feedback_c: While namespace packages exist, best practice is to include __init__.py.
   :feedback_d: All package directories need __init__.py, not just those with modules.

   In a nested package structure, which directories need ``__init__.py``?

.. mchoice:: comprehensive_mcq_8
   :answer_a: List all installed packages
   :answer_b: Create a new virtual environment
   :answer_c: Install packages from requirements.txt
   :answer_d: Update pip itself
   :correct: c
   :feedback_a: That's 'pip list'.
   :feedback_b: That's 'python -m venv'.
   :feedback_c: Correct! 'pip install -r requirements.txt' installs all listed packages.
   :feedback_d: That's 'pip install --upgrade pip'.

   What does ``pip install -r requirements.txt`` do?

Parsons Problems
~~~~~~~~~~~~~~~~

.. parsonsprob:: package_structure_parsons
   :numbered: left
   :adaptive:

   Arrange the steps to create and use a new Python package in the correct order.
   -----
   mkdir mypackage
   =====
   cd mypackage
   =====
   touch __init__.py
   =====
   echo "def greet(): return 'Hello'" > module.py
   =====
   cd ..
   =====
   python
   =====
   import mypackage.module
   =====
   mypackage.module.greet()

.. parsonsprob:: virtual_environment_complete_workflow
   :numbered: left
   :adaptive:

   Arrange the complete workflow for setting up a Python project with virtual environment and dependencies.
   -----
   mkdir myproject && cd myproject
   =====
   python -m venv venv
   =====
   source venv/bin/activate
   =====
   pip install requests numpy
   =====
   pip freeze > requirements.txt
   =====
   git init
   =====
   echo "venv/" > .gitignore
   =====
   git add requirements.txt
   =====
   git commit -m "Add dependencies"

Coding Challenges
~~~~~~~~~~~~~~~~~

.. activecode:: math_statistics_challenge
   :nocodelens:

   Write a function ``calculate_statistics(numbers)`` that takes a list of
   numbers and returns a dictionary containing:
   - 'mean': average (use sum/len, not statistics module)
   - 'max': maximum value
   - 'min': minimum value
   - 'range': difference between max and min
   - 'sqrt_of_mean': square root of the mean (use math.sqrt)

   Round all values to 2 decimal places.
   ~~~~
   import math

   def calculate_statistics(numbers):
       # Your code here
       pass

   # Test
   data = [10, 20, 30, 40, 50]
   stats = calculate_statistics(data)
   print(stats)
   # Expected: {'mean': 30.0, 'max': 50, 'min': 10, 'range': 40, 'sqrt_of_mean': 5.48}

   ====
   from unittest.gui import TestCaseGui
   import math

   class MyTests(TestCaseGui):
       def test_calculate_statistics(self):
           result = calculate_statistics([10, 20, 30, 40, 50])
           self.assertAlmostEqual(result['mean'], 30.0, places=1)
           self.assertEqual(result['max'], 50)
           self.assertEqual(result['min'], 10)
           self.assertEqual(result['range'], 40)
           self.assertAlmostEqual(result['sqrt_of_mean'], math.sqrt(30), places=1)

       def test_different_data(self):
           result = calculate_statistics([5, 15, 25])
           self.assertAlmostEqual(result['mean'], 15.0, places=1)
           self.assertEqual(result['range'], 20)

   MyTests().main()

.. activecode:: module_analyzer_challenge
   :nocodelens:

   Write a function ``analyze_module(module_name)`` that takes a module name
   as a string, imports it, and returns a dictionary with:
   - 'name': module's __name__
   - 'doc': first line of __doc__ (or "No documentation" if None)
   - 'function_count': number of callable functions (use dir() and callable())
   - 'has_main': True if module has a __main__ check pattern

   Hint: You can use __import__(module_name) to import by string.
   ~~~~
   def analyze_module(module_name):
       # Your code here
       pass

   # Test with 'math' module
   info = analyze_module('math')
   print(info)
   # Expected output format:
   # {'name': 'math', 'doc': 'This module provides...', 'function_count': 50+}

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_analyze_math(self):
           result = analyze_module('math')
           self.assertEqual(result['name'], 'math')
           self.assertTrue(result['function_count'] > 30)
           self.assertIsInstance(result['doc'], str)

       def test_analyze_platform(self):
           result = analyze_module('platform')
           self.assertEqual(result['name'], 'platform')
           self.assertIsInstance(result['function_count'], int)

   MyTests().main()

.. activecode:: package_structure_validator
   :nocodelens:

   Write a function ``validate_package_structure(structure)`` that takes
   a dictionary representing a package structure and validates it.

   The structure dict format:
   {
       'name': 'package_name',
       'has_init': True/False,
       'modules': ['module1.py', 'module2.py'],
       'subpackages': [nested structure dicts]
   }

   Return a list of validation errors. If valid, return empty list.

   Rules:
   - Package name must not be empty
   - Must have __init__.py (has_init must be True)
   - All module names must end with .py
   - Recursively validate subpackages
   ~~~~
   def validate_package_structure(structure):
       # Your code here
       pass

   # Test case 1: Valid structure
   valid_pkg = {
       'name': 'mypackage',
       'has_init': True,
       'modules': ['module1.py', 'module2.py'],
       'subpackages': []
   }
   print("Valid package errors:", validate_package_structure(valid_pkg))  # Should be []

   # Test case 2: Invalid structure
   invalid_pkg = {
       'name': '',
       'has_init': False,
       'modules': ['module1.py', 'badmodule'],
       'subpackages': []
   }
   print("Invalid package errors:", validate_package_structure(invalid_pkg))
   # Should list errors: empty name, no __init__.py, bad module name

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_valid_structure(self):
           valid = {
               'name': 'pkg',
               'has_init': True,
               'modules': ['mod.py'],
               'subpackages': []
           }
           result = validate_package_structure(valid)
           self.assertEqual(len(result), 0, "Valid structure should have no errors")

       def test_missing_init(self):
           invalid = {
               'name': 'pkg',
               'has_init': False,
               'modules': [],
               'subpackages': []
           }
           result = validate_package_structure(invalid)
           self.assertTrue(len(result) > 0, "Should report missing __init__.py")

       def test_bad_module_name(self):
           invalid = {
               'name': 'pkg',
               'has_init': True,
               'modules': ['badmodule'],
               'subpackages': []
           }
           result = validate_package_structure(invalid)
           self.assertTrue(len(result) > 0, "Should report bad module name")

   MyTests().main()

.. activecode:: requirements_parser_challenge
   :nocodelens:

   Write a function ``parse_and_categorize_requirements(req_text)`` that parses
   a requirements.txt content and categorizes packages.

   Return a dictionary:
   {
       'pinned': [(package, version), ...],  # Exact version (==)
       'flexible': [(package, constraint), ...],  # Version range (>=, <, etc.)
       'latest': [package, ...],  # No version specified
       'comments': [comment_line, ...]
   }
   ~~~~
   def parse_and_categorize_requirements(req_text):
       # Your code here
       pass

   # Test
   requirements = """
   # Web framework
   flask==2.3.0
   requests>=2.28.0
   numpy
   pandas>=2.0.0,<3.0.0
   # Testing tools
   pytest==7.3.1
   """

   result = parse_and_categorize_requirements(requirements)
   print("Pinned:", result['pinned'])
   print("Flexible:", result['flexible'])
   print("Latest:", result['latest'])
   print("Comments:", result['comments'])

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_categorization(self):
           req = "flask==2.3.0\nrequests>=2.28.0\nnumpy\n# comment"
           result = parse_and_categorize_requirements(req)

           self.assertIn('pinned', result)
           self.assertIn('flexible', result)
           self.assertIn('latest', result)
           self.assertIn('comments', result)

           self.assertEqual(len(result['pinned']), 1)
           self.assertEqual(len(result['flexible']), 1)
           self.assertEqual(len(result['latest']), 1)
           self.assertEqual(len(result['comments']), 1)

   MyTests().main()

Real-World Scenarios
--------------------

Scenario 1: Organizing a Data Science Project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Situation:** You're building a data science project with multiple components.

**Structure:**

.. code-block:: text

   datascience_project/
   ├── __init__.py
   ├── data/
   │   ├── __init__.py
   │   ├── loaders.py      # Load datasets
   │   └── preprocessors.py  # Clean data
   ├── models/
   │   ├── __init__.py
   │   ├── linear.py       # Linear models
   │   └── neural.py       # Neural networks
   ├── visualization/
   │   ├── __init__.py
   │   └── plots.py        # Create charts
   └── utils/
       ├── __init__.py
       ├── _internal.py    # Private utilities
       └── validators.py   # Public validators

**Questions to consider:**

1. Why use nested packages for this project?
2. What should go in each ``__init__.py``?
3. Why prefix ``_internal.py`` with underscore?
4. How would you import ``linear.py`` from ``plots.py``?

**Answer key:**

1. **Logical organization** — Group related functionality, making code easier to navigate
2. **Common imports** — Import frequently used functions so users can do ``from datascience_project.models import LinearRegression``
3. **Convention** — Signals that ``_internal.py`` contains helper functions not meant for external use
4. **Relative import:** ``from ..models import linear`` or **Absolute:** ``from datascience_project.models import linear``

Scenario 2: Setting Up a Team Project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Situation:** Your team is starting a web application. You need to ensure everyone has the same development environment.

**Steps:**

.. code-block:: bash

   # 1. Project lead creates structure
   mkdir webapp
   cd webapp

   # 2. Set up virtual environment
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate

   # 3. Install dependencies
   pip install flask==2.3.0
   pip install sqlalchemy>=2.0.0
   pip install pytest>=7.3.0

   # 4. Generate requirements
   pip freeze > requirements.txt

   # 5. Set up version control
   git init
   echo "venv/" >> .gitignore
   echo "__pycache__/" >> .gitignore
   git add .
   git commit -m "Initial setup"

   # 6. Team members clone and set up:
   git clone <repository>
   cd webapp
   python -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt

**Why this approach works:**

- ✅ **Isolation** — Virtual environment prevents conflicts
- ✅ **Reproducibility** — ``requirements.txt`` ensures same versions
- ✅ **Clean repository** — ``.gitignore`` prevents committing environment
- ✅ **Easy onboarding** — New members can set up quickly

Scenario 3: Publishing a Reusable Package
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Situation:** You've built a useful library and want to share it on PyPI.

**Package structure:**

.. code-block:: text

   mylib/
   ├── setup.py            # Package metadata
   ├── README.md           # Documentation
   ├── requirements.txt    # Dependencies
   ├── .gitignore
   └── mylib/
       ├── __init__.py     # Export public API
       ├── core.py         # Main functionality
       ├── utils.py        # Public utilities
       └── _internal.py    # Private helpers

**mylib/__init__.py:**

.. code-block:: python

   """
   MyLib: A useful Python library.

   Usage:
       from mylib import main_function
       result = main_function()
   """

   __version__ = "1.0.0"
   __author__ = "Your Name"

   # Export public API
   from .core import main_function, helper_function
   from .utils import validate, format_output

   # Define what 'from mylib import *' imports
   __all__ = [
       'main_function',
       'helper_function',
       'validate',
       'format_output'
   ]

**Key decisions:**

- **Public API** — Only export what users need
- **Version tracking** — ``__version__`` for users to check
- **``__all__``** — Controls ``import *`` behavior
- **Private modules** — ``_internal.py`` for implementation details

Self-Assessment Checklist
--------------------------

Test your mastery of Chapter 26! Check off each skill:

Math Module
~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #2196F3; padding-left: 15px; margin: 20px 0;">

- □ I can import and use ``math`` module functions
- □ I understand the difference between ``ceil()``, ``floor()``, and ``trunc()``
- □ I can use ``math.factorial()`` and ``math.sqrt()``
- □ I know how to use ``math.pi`` and ``math.e``
- □ I can calculate distances with ``math.hypot()``

.. raw:: html

   </div>

Platform Module
~~~~~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #4CAF50; padding-left: 15px; margin: 20px 0;">

- □ I can import and use ``platform`` module functions
- □ I can detect the operating system with ``platform.system()``
- □ I can get Python version information
- □ I understand the difference between ``platform()`` and ``system()``
- □ I can use ``platform`` for system-specific code

.. raw:: html

   </div>

Packages
~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #FF9800; padding-left: 15px; margin: 20px 0;">

- □ I understand the difference between modules and packages
- □ I know what ``__init__.py`` does and when it's required
- □ I can create a package structure from scratch
- □ I understand ``__pycache__`` and ``.pyc`` files
- □ I can use different import styles (absolute, relative, specific)

.. raw:: html

   </div>

Import Mechanics
~~~~~~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #9C27B0; padding-left: 15px; margin: 20px 0;">

- □ I understand how ``sys.path`` works
- □ I can use ``dir()`` to explore modules
- □ I know the difference between absolute and relative imports
- □ I can debug ``ImportError`` and ``ModuleNotFoundError``
- □ I understand import search order

.. raw:: html

   </div>

Module Introspection
~~~~~~~~~~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #E91E63; padding-left: 15px; margin: 20px 0;">

- □ I understand ``__name__`` and ``__main__``
- □ I can write ``if __name__ == '__main__':`` blocks
- □ I know the purpose of ``__doc__``, ``__file__``, and ``__dict__``
- □ I can use module attributes for debugging
- □ I understand when and why to use ``__main__`` checks

.. raw:: html

   </div>

Package Management
~~~~~~~~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #00BCD4; padding-left: 15px; margin: 20px 0;">

- □ I can install packages with ``pip install``
- □ I can create and use ``requirements.txt`` files
- □ I can create and activate virtual environments
- □ I understand version specifiers (``==``, ``>=``, ``<``)
- □ I can set up a complete project environment

.. raw:: html

   </div>

Naming Conventions
~~~~~~~~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #795548; padding-left: 15px; margin: 20px 0;">

- □ I understand public vs. internal naming (``_`` prefix)
- □ I know how name mangling works (``__`` prefix)
- □ I can use dunder methods appropriately (``__init__``, etc.)
- □ I understand ``__all__`` for controlling imports
- □ I follow Python naming conventions in my code

.. raw:: html

   </div>

Advanced Concepts
~~~~~~~~~~~~~~~~~

.. raw:: html

   <div style="border-left: 4px solid #607D8B; padding-left: 15px; margin: 20px 0;">

- □ I can create nested package structures
- □ I understand when to use nested vs. flat packages
- □ I can design a clean package API
- □ I can organize large projects with multiple subpackages
- □ I follow best practices for package design

.. raw:: html

   </div>

Quick Reference Guide
---------------------

Essential Commands
~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # Importing
   import module
   from module import function
   from package.subpackage import module

   # Introspection
   dir(module)              # List names
   help(module)             # Show documentation
   print(module.__name__)   # Module name
   print(module.__file__)   # Module file path
   print(module.__doc__)    # Module docstring

   # System paths
   import sys
   print(sys.path)          # Module search paths
   sys.path.append('/path') # Add search path

Essential pip Commands
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # Installation
   pip install package
   pip install package==1.0.0
   pip install package>=1.0.0
   pip install -r requirements.txt

   # Management
   pip list                 # List installed packages
   pip show package         # Package details
   pip uninstall package    # Remove package
   pip freeze > requirements.txt  # Save dependencies

   # Virtual environments
   python -m venv venv      # Create environment
   source venv/bin/activate # Activate (macOS/Linux)
   venv\Scripts\activate    # Activate (Windows)
   deactivate               # Deactivate

Key Concepts
~~~~~~~~~~~~

.. code-block:: python

   # Module vs. Package
   # Module: Single .py file
   # Package: Directory with __init__.py

   # __name__ check
   if __name__ == '__main__':
       # Code runs only when script executed directly
       main()

   # Naming conventions
   public_var = 1          # Public
   _internal_var = 2       # Internal (convention)
   __private_var = 3       # Name mangled (in classes)

   # __all__ definition
   __all__ = ['public_func', 'PublicClass']

Congratulations! 🎉
-------------------

.. admonition:: Chapter 26 Complete!

   **You've mastered Advanced Modules and Packages!**

   You can now:

   ✅ Use the ``math`` and ``platform`` modules effectively
   ✅ Create and organize packages with proper structure
   ✅ Understand Python's import mechanics and ``sys.path``
   ✅ Use module introspection (``__name__``, ``__doc__``, etc.)
   ✅ Manage dependencies with pip and virtual environments
   ✅ Follow naming conventions for public/private code
   ✅ Design nested package structures
   ✅ Set up professional Python projects

   **These skills are essential for:**

   - 📚 PCAP certification exam
   - 💼 Professional Python development
   - 🏗️ Building large-scale applications
   - 🤝 Collaborating on team projects
   - 📦 Publishing your own packages

----

What's Next?
------------

You've completed **Chapter 26: Advanced Modules and Packages** — congrats! 🎉

**Next Up:**

- **Chapter 27: Advanced OOP Mastery**
  - OOP introspection (hasattr, isinstance, issubclass)
  - Identity operators (is/is not)
  - The diamond problem and multiple inheritance
  - Method Resolution Order (MRO)
  - Polymorphism in depth

🎯 **You're on track for PCAP certification!**