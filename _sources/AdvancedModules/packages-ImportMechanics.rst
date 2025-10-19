..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Import Mechanics and Search Paths
==================================
When you write ``import math`` or ``from mypackage import mymodule``, have you ever wondered **how Python finds** the module? In this lesson, you'll learn exactly how Python's import system works and how to control where Python looks for modules.

How Python Finds Modules
-------------------------

When you execute an import statement, Python follows a specific search process:

.. code-block:: python

   import mymodule

Python searches for ``mymodule`` in this order:

1. **Built-in modules** — Modules compiled into Python itself (like ``sys``, ``math``)
2. **Current directory** — The directory containing the script you're running
3. **PYTHONPATH** — Environment variable with additional directories (if set)
4. **Standard library** — Python's installation directory
5. **Site-packages** — Third-party packages installed via ``pip``

.. note::

   **Search order matters!**

   If you create a file named ``math.py`` in your project directory, it will **shadow** (hide) Python's built-in ``math`` module because Python checks the current directory first!

The ``sys.path`` Variable
--------------------------

What Is ``sys.path``?
~~~~~~~~~~~~~~~~~~~~~

Python stores its module search paths in a list called ``sys.path``. This list contains all the directories Python will search when looking for modules.

.. activecode:: sys_path_basic_demo
   :nocodelens:

   import sys

   # Display Python's search paths
   print("Python searches for modules in these directories:")
   print()

   for i, path in enumerate(sys.path, 1):
       print(f"{i}. {path}")

.. important::

   **What you'll see:**

   - The **first entry** is often an empty string (``''``) or the current directory
   - Standard library paths
   - Site-packages directory (where ``pip`` installs packages)
   - Your specific paths depend on your Python installation and operating system

Understanding ``sys.path`` Entries
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Let's break down what each type of entry means:

.. activecode:: sys_path_explanation_demo
   :nocodelens:

   import sys

   print("Current directory:")
   print("  ", sys.path[0])  # Usually empty string or script directory
   print()

   print("Standard library examples:")
   stdlib_paths = [p for p in sys.path if 'lib' in p.lower() and 'site-packages' not in p]
   for path in stdlib_paths[:2]:  # Show first 2
       print("  ", path)
   print()

   print("Site-packages (third-party modules):")
   site_paths = [p for p in sys.path if 'site-packages' in p]
   for path in site_paths[:2]:  # Show first 2
       print("  ", path)

.. mchoice:: sys_path_understanding_1
   :answer_a: A list of all installed Python packages
   :answer_b: A list of directories Python searches for modules
   :answer_c: A list of imported modules
   :answer_d: The Python installation directory
   :correct: b
   :feedback_a: sys.path contains directories, not package names.
   :feedback_b: Correct! sys.path is a list of directories Python searches when importing modules.
   :feedback_c: sys.path contains search paths, not imported modules themselves.
   :feedback_d: That's just one entry in sys.path, not the entire variable.

   What is ``sys.path``?

Modifying ``sys.path``
-----------------------

You can add directories to ``sys.path`` to tell Python where to find your modules.

Adding a Directory Temporarily
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can modify ``sys.path`` within your script:

.. activecode:: sys_path_modify_demo
   :nocodelens:

   import sys

   # Add a custom directory to the search path
   custom_path = '/home/user/my_modules'
   sys.path.append(custom_path)

   print("Updated sys.path:")
   print(f"Last entry: {sys.path[-1]}")

   # Now Python will search this directory for imports
   # import mymodule  # Would look in custom_path too

.. important::

   **When to modify ``sys.path``:**

   - ✅ For quick scripts and prototypes
   - ✅ When you need a temporary workaround
   - ❌ NOT recommended for production code
   - ❌ Better alternatives: proper package installation or PYTHONPATH

Insert vs. Append
~~~~~~~~~~~~~~~~~

Where you add a path in ``sys.path`` matters:

.. activecode:: sys_path_insert_vs_append
   :nocodelens:

   import sys

   # append() adds to the END (searched last)
   sys.path.append('/path/to/modules')
   print("After append():")
   print(f"Last item: {sys.path[-1]}")
   print()

   # insert() adds to a specific position (often the beginning)
   sys.path.insert(0, '/priority/path')
   print("After insert(0, ...):")
   print(f"First item: {sys.path[0]}")
   print()

   print("Tip: Use insert(0, ...) when you want your path")
   print("     searched BEFORE all others!")

.. mchoice:: sys_path_modification_understanding
   :answer_a: sys.path.add('/new/path')
   :answer_b: sys.path.append('/new/path')
   :answer_c: sys.path.insert('/new/path')
   :answer_d: sys.path['/new/path'] = True
   :correct: b
   :feedback_a: There's no add() method for lists; use append() or insert().
   :feedback_b: Correct! append() adds the path to the end of sys.path.
   :feedback_c: insert() requires two arguments: position and value.
   :feedback_d: This syntax doesn't work; sys.path is a list, not a dictionary.

   Which method correctly adds a directory to the end of ``sys.path``?

The ``dir()`` Function
-----------------------

What Is ``dir()``?
~~~~~~~~~~~~~~~~~~

The ``dir()`` function lets you explore what's available in a module, package, or object. It returns a list of names (attributes, functions, classes) defined in that object.

.. activecode:: dir_basic_demo
   :nocodelens:

   import math

   # See what's available in the math module
   print("Contents of math module:")
   contents = dir(math)

   # Show first 10 items
   print(contents[:10])
   print("...")
   print(f"Total items: {len(contents)}")

Using ``dir()`` to Explore Modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``dir()`` is incredibly useful for discovering what a module offers:

.. activecode:: dir_practical_demo
   :nocodelens:

   import platform

   # Explore the platform module
   contents = dir(platform)

   # Filter out private/magic methods (those starting with _)
   public_items = [item for item in contents if not item.startswith('_')]

   print("Public functions in platform module:")
   for item in sorted(public_items):
       print(f"  - {item}")

.. activecode:: dir_without_arguments
   :nocodelens:

   # dir() without arguments shows names in current scope
   x = 10
   y = 20
   name = "Python"

   print("Variables in current scope:")
   current_scope = [item for item in dir() if not item.startswith('_')]
   print(current_scope)

.. mchoice:: dir_function_understanding
   :answer_a: It shows the directory path of a module
   :answer_b: It lists names defined in a module or object
   :answer_c: It imports all functions from a module
   :answer_d: It deletes unused modules
   :correct: b
   :feedback_a: dir() shows names/attributes, not file system paths.
   :feedback_b: Correct! dir() returns a list of names (attributes, functions, etc.) in a module or object.
   :feedback_c: dir() only lists names; it doesn't import anything.
   :feedback_d: dir() is for exploration, not deletion.

   What does the ``dir()`` function do?

Absolute vs. Relative Imports
------------------------------

When working with packages, you have two ways to import modules: **absolute** and **relative** imports.

Absolute Imports
~~~~~~~~~~~~~~~~

Absolute imports specify the **complete path** from the project root:

.. code-block:: python

   # Absolute imports (recommended)
   from mypackage.subpackage.module import function
   import mypackage.module

**Advantages:**
- Clear and explicit
- Easy to understand where modules come from
- Recommended by PEP 8 (Python style guide)

Relative Imports
~~~~~~~~~~~~~~~~

Relative imports use dots (``.``) to navigate relative to the current module's location:

.. code-block:: python

   # Relative imports (use with caution)
   from . import sibling_module        # Same directory
   from .. import parent_module        # Parent directory
   from ..sibling_package import module  # Parent's sibling

**Dot notation:**
- ``.`` = Current package
- ``..`` = Parent package
- ``...`` = Grandparent package

Example: When to Use Each
~~~~~~~~~~~~~~~~~~~~~~~~~~

Consider this package structure:

.. code-block:: text

   myproject/
   └── mypackage/
       ├── __init__.py
       ├── module_a.py
       └── subpackage/
           ├── __init__.py
           └── module_b.py

**In ``module_b.py``, to import from ``module_a``:**

.. code-block:: python

   # Absolute import (RECOMMENDED)
   from mypackage.module_a import some_function

   # Relative import (alternative)
   from ..module_a import some_function

.. important::

   **Best Practice:**

   - ✅ **Prefer absolute imports** — They're clearer and more maintainable
   - ⚠️ Use relative imports only for complex package internal structures
   - ❌ Relative imports don't work in scripts run directly (only in packages)

.. activecode:: absolute_vs_relative_demo
   :nocodelens:

   # Imagine this package structure:
   #
   # calculator/
   # ├── __init__.py
   # ├── basic.py
   # └── advanced.py
   #
   # If advanced.py wants to import from basic.py:

   # ABSOLUTE (Recommended):
   # from calculator.basic import add

   # RELATIVE (Alternative):
   # from .basic import add

   print("Absolute imports are preferred for clarity!")
   print("Example: from calculator.basic import add")
   print()
   print("Relative imports use dots:")
   print("Example: from .basic import add")
   print("         (. means 'current package')")

.. mchoice:: absolute_relative_imports_understanding
   :answer_a: Absolute imports
   :answer_b: Relative imports
   :answer_c: Both are equally recommended
   :answer_d: Neither should be used
   :correct: a
   :feedback_a: Correct! PEP 8 recommends absolute imports for clarity and maintainability.
   :feedback_b: Relative imports work but are less clear; absolute imports are preferred.
   :feedback_c: Absolute imports are explicitly preferred by Python's style guide.
   :feedback_d: Imports are essential; absolute imports are the recommended style.

   Which import style is recommended by PEP 8 (Python's style guide)?

Common Import Errors and Solutions
-----------------------------------

``ModuleNotFoundError``
~~~~~~~~~~~~~~~~~~~~~~~

**Error message:**
   ``ModuleNotFoundError: No module named 'mymodule'``

**Causes and solutions:**

1. **Module doesn't exist** → Check spelling and installation
2. **Wrong directory** → Verify ``sys.path`` includes module location
3. **Missing ``__init__.py``** → Add ``__init__.py`` to package directories

.. activecode:: debugging_imports_demo
   :nocodelens:

   import sys

   # Debugging import issues:

   def debug_import(module_name):
       """Help debug why an import might fail."""
       print(f"Debugging: import {module_name}")
       print("=" * 50)

       print("\nPython will search in these directories:")
       for i, path in enumerate(sys.path[:5], 1):
           print(f"  {i}. {path if path else '(current directory)'}")

       print("\nTroubleshooting checklist:")
       print("  ✓ Is the module name spelled correctly?")
       print("  ✓ Is the module in one of the above directories?")
       print("  ✓ Does the package have __init__.py?")
       print("  ✓ Did you install it? (pip install ...)")

   debug_import('mymodule')

``ImportError`` vs. ``ModuleNotFoundError``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Import Error Types
   :header-rows: 1
   :widths: 30 70

   * - Error Type
     - Meaning
   * - ``ModuleNotFoundError``
     - Python can't find the module at all
   * - ``ImportError``
     - Module found, but something inside it failed to import

.. activecode:: import_error_examples
   :nocodelens:

   # ModuleNotFoundError example:
   # import nonexistent_module  # ← Module doesn't exist

   # ImportError example:
   # from math import fake_function  # ← Module exists, function doesn't

   print("ModuleNotFoundError: Can't find the module")
   print("ImportError: Found module, but import failed")
   print()
   print("Tip: Read error messages carefully—they tell you")
   print("     exactly what went wrong!")

Circular Imports
~~~~~~~~~~~~~~~~

**Problem:** Module A imports Module B, and Module B imports Module A.

**Example:**

.. code-block:: python

   # file_a.py
   from file_b import something  # ← Tries to import from B

   # file_b.py
   from file_a import something  # ← Tries to import from A
   # Result: ImportError!

**Solutions:**
1. Restructure code to eliminate circular dependency
2. Move imports inside functions (not at top of file)
3. Use import statements instead of from-import

.. mchoice:: import_errors_understanding
   :answer_a: The module name is misspelled
   :answer_b: The module is in a directory not in sys.path
   :answer_c: You forgot to install the package
   :answer_d: All of the above
   :correct: d
   :feedback_a: Spelling errors are a common cause, but not the only one.
   :feedback_b: Path issues are common, but not the only cause.
   :feedback_c: Missing installations cause this error, but so do other issues.
   :feedback_d: Correct! ModuleNotFoundError can result from any of these issues.

   Which could cause a ``ModuleNotFoundError``?

Practical Debugging Workflow
-----------------------------

Here's a complete debugging workflow for import issues:

.. activecode:: import_debugging_workflow
   :nocodelens:

   import sys
   import os

   def diagnose_import_problem(module_name):
       """Complete diagnostic for import issues."""
       print(f"🔍 DIAGNOSING: import {module_name}")
       print("=" * 60)

       # Step 1: Check sys.path
       print("\n1️⃣ PYTHON'S SEARCH PATHS (sys.path):")
       for i, path in enumerate(sys.path[:5], 1):
           display_path = path if path else "(current directory)"
           print(f"   {i}. {display_path}")

       # Step 2: Check if it's a built-in module
       print(f"\n2️⃣ IS '{module_name}' A BUILT-IN MODULE?")
       try:
           import sys
           if module_name in sys.builtin_module_names:
               print(f"   ✓ Yes, {module_name} is built-in")
           else:
               print(f"   ✗ No, {module_name} is not built-in")
       except:
           print("   ? Cannot determine")

       # Step 3: Recommendations
       print("\n3️⃣ TROUBLESHOOTING STEPS:")
       print("   a) Check spelling of module name")
       print("   b) Verify module is installed (pip list)")
       print("   c) Check if file exists in sys.path directories")
       print("   d) Ensure package has __init__.py")
       print("   e) Try: pip install <module_name>")

       print("\n" + "=" * 60)

   # Try it out
   diagnose_import_problem('mymodule')

Practice Problems
-----------------

.. activecode:: sys_path_challenge
   :nocodelens:

   Write a function ``module_search_paths()`` that returns a list of all directories in ``sys.path`` that actually exist on the file system. (Hint: Use ``os.path.exists()`` to check if a path exists.)
   ~~~~
   import sys
   import os

   def module_search_paths():
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_module_search_paths(self):
           result = module_search_paths()
           # Should return a list
           self.assertIsInstance(result, list)
           # All items should be strings
           for item in result:
               self.assertIsInstance(item, str)
           # Should contain at least one path
           self.assertGreater(len(result), 0)

   MyTests().main()

.. activecode:: dir_exploration_challenge
   :nocodelens:

   Write a function ``count_public_functions(module)`` that takes a module object and returns the count of public functions (names that don't start with underscore).

   Hint: Use ``dir(module)`` and filter names.
   ~~~~
   import math

   def count_public_functions(module):
       # Your code here
       pass

   # Test with math module
   print(f"Public items in math module: {count_public_functions(math)}")

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_count_public_functions(self):
           import math
           result = count_public_functions(math)
           # Should return an integer
           self.assertIsInstance(result, int)
           # Math module has many public functions (at least 40)
           self.assertGreater(result, 40)

   MyTests().main()

.. mchoice:: practical_import_scenario
   :answer_a: sys.path.append('/my/custom/path')
   :answer_b: import sys; sys.path = '/my/custom/path'
   :answer_c: from sys import path; path.add('/my/custom/path')
   :answer_d: set sys.path '/my/custom/path'
   :correct: a
   :feedback_a: Correct! This appends the custom path to sys.path.
   :feedback_b: This replaces sys.path (a list) with a string, which will break imports.
   :feedback_c: Lists don't have an add() method; use append().
   :feedback_d: This is not valid Python syntax.

   You have custom modules in ``/my/custom/path``. How do you add this directory to Python's module search path?

Key Takeaways
-------------

.. admonition:: Summary

   * **Python's import search order:**

     1. Built-in modules
     2. Current directory
     3. PYTHONPATH
     4. Standard library
     5. Site-packages

   * **``sys.path``** is a list of directories Python searches for modules
   * Modify ``sys.path`` with ``append()`` or ``insert()``
   * **``dir()``** shows available names in a module or object
   * **Absolute imports** (recommended): ``from package.module import func``
   * **Relative imports** (use carefully): ``from .module import func``
   * **Common errors:**

     * ``ModuleNotFoundError`` — Module not found
     * ``ImportError`` — Import failed
     * Circular imports — Two modules importing each other

----

What's Next?
------------

You've mastered Python's import system!

In the next lesson, you'll learn:

- **The ``__name__`` variable** and the ``if __name__ == '__main__':`` pattern
- Module introspection and metadata
- How to make modules runnable as scripts
- Best practices for module design

.. note::

   **✅ Lesson 4 Complete!**

   **You've learned import mechanics:**

   - ✅ How Python searches for modules (search order)
   - ✅ Understanding and modifying ``sys.path``
   - ✅ Using ``dir()`` to explore modules
   - ✅ Absolute vs. relative imports (and when to use each)
   - ✅ Debugging import errors effectively
   - ✅ Best practices for managing module paths

   **Next:** Master the ``__name__`` variable and make your modules executable!