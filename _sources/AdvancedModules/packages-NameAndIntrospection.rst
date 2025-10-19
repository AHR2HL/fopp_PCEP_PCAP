..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

The ``__name__`` Variable and Module Introspection
===================================================

Have you ever seen this mysterious code at the bottom of Python files?

.. code-block:: python

   if __name__ == '__main__':
       # code here

What does it mean? Why is it everywhere in professional Python code? In this lesson, you'll discover the purpose of the ``__name__`` variable and learn how to make your modules both **importable** and **executable**.

What Is ``__name__``?
---------------------

Every Python module has a built-in variable called ``__name__``. This variable tells you **how the module is being used**:

- If you **run** the file directly → ``__name__`` equals ``'__main__'``
- If you **import** the file → ``__name__`` equals the module name

Let's see this in action:

.. activecode:: name_variable_demo
   :nocodelens:

   # Every Python file has __name__
   print(f"The value of __name__ is: {__name__}")

   # In this ActiveCode environment, __name__ is '__main__'
   # because this code is being run directly

   if __name__ == '__main__':
       print("✓ This code is being run directly!")
   else:
       print("✓ This code is being imported!")

**Key insight:** The ``__name__`` variable allows your code to behave differently depending on whether it's being run as a script or imported as a module.

The Two Faces of ``__name__``
------------------------------

Let's create a practical example to demonstrate both scenarios.

Scenario 1: Running a File Directly
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Imagine you create a file called ``greetings.py``:

.. code-block:: python

   # File: greetings.py

   def say_hello(name):
       return f"Hello, {name}!"

   def say_goodbye(name):
       return f"Goodbye, {name}!"

   # Check if this file is being run directly
   print(f"__name__ is: {__name__}")

   if __name__ == '__main__':
       print("Running greetings.py directly!")
       print(say_hello("World"))

**When you run:** ``python greetings.py``

**Output:**
   .. code-block:: text

      __name__ is: __main__
      Running greetings.py directly!
      Hello, World!

Scenario 2: Importing the File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now imagine you import ``greetings.py`` from another file:

.. code-block:: python

   # File: main.py

   import greetings

   print(greetings.say_hello("Python"))

**When you run:** ``python main.py``

**Output:**
   .. code-block:: text

      __name__ is: greetings
      Hello, Python!

Notice:
- The ``if __name__ == '__main__':`` block **didn't run** when imported
- ``__name__`` was ``'greetings'`` (the module name), not ``'__main__'``

.. activecode:: name_variable_explanation
   :nocodelens:

   # Simulating the two scenarios:

   def demonstrate_name(scenario):
       """Show how __name__ works in different scenarios."""
       print(f"\n{'='*50}")
       print(f"SCENARIO: {scenario}")
       print('='*50)

       if scenario == "run_directly":
           simulated_name = "__main__"
       else:  # imported
           simulated_name = "greetings"

       print(f"__name__ = '{simulated_name}'")
       print()

       # This always runs
       print("✓ Function definitions loaded")

       # This only runs when __name__ == '__main__'
       if simulated_name == '__main__':
           print("✓ Running test code (because __name__ == '__main__')")
       else:
           print("✗ Skipping test code (because __name__ != '__main__')")

   demonstrate_name("run_directly")
   demonstrate_name("imported")

.. mchoice:: name_variable_understanding_1
   :answer_a: '__main__'
   :answer_b: The filename without .py
   :answer_c: 'module'
   :answer_d: None
   :correct: a
   :feedback_a: Correct! When you run a Python file directly, __name__ is always '__main__'.
   :feedback_b: That's the value when the file is imported, not run directly.
   :feedback_c: __name__ is never 'module'; it's either '__main__' or the module name.
   :feedback_d: __name__ always has a value; it's never None.

   What is the value of ``__name__`` when you run a Python file directly?

The ``if __name__ == '__main__':`` Pattern
------------------------------------------

This pattern is one of the most common idioms in Python. It allows you to write code that only runs when the file is executed directly, not when it's imported.

Why Use This Pattern?
~~~~~~~~~~~~~~~~~~~~~~

**Without the pattern:**

.. code-block:: python

   # calculator.py

   def add(a, b):
       return a + b

   def multiply(a, b):
       return a * b

   # Testing code (BAD - always runs)
   print("Testing calculator...")
   print(f"2 + 3 = {add(2, 3)}")
   print(f"2 × 3 = {multiply(2, 3)}")

**Problem:** When someone imports your module:

.. code-block:: python

   import calculator  # Prints test output! Not desired.

**With the pattern (GOOD):**

.. code-block:: python

   # calculator.py

   def add(a, b):
       return a + b

   def multiply(a, b):
       return a * b

   # Testing code (GOOD - only runs when executed directly)
   if __name__ == '__main__':
       print("Testing calculator...")
       print(f"2 + 3 = {add(2, 3)}")
       print(f"2 × 3 = {multiply(2, 3)}")

**Now:** Importing doesn't run the test code!

.. code-block:: python

   import calculator  # No output - clean import!
   result = calculator.add(10, 5)

Practical Example
~~~~~~~~~~~~~~~~~

Let's create a complete, practical module:

.. activecode:: name_pattern_practical_example
   :nocodelens:

   # Imagine this is utils.py

   def celsius_to_fahrenheit(celsius):
       """Convert Celsius to Fahrenheit."""
       return (celsius * 9/5) + 32

   def fahrenheit_to_celsius(fahrenheit):
       """Convert Fahrenheit to Celsius."""
       return (fahrenheit - 32) * 5/9

   def format_temperature(temp, unit):
       """Format temperature with unit symbol."""
       symbol = '°C' if unit.lower() == 'c' else '°F'
       return f"{temp:.1f}{symbol}"

   # This block only runs when executing utils.py directly
   if __name__ == '__main__':
       print("🌡️  Temperature Converter - Testing Mode")
       print("=" * 45)

       # Test conversions
       temp_c = 0
       temp_f = celsius_to_fahrenheit(temp_c)
       print(f"{temp_c}°C = {format_temperature(temp_f, 'F')}")

       temp_f = 32
       temp_c = fahrenheit_to_celsius(temp_f)
       print(f"{temp_f}°F = {format_temperature(temp_c, 'C')}")

       temp_c = 100
       temp_f = celsius_to_fahrenheit(temp_c)
       print(f"{temp_c}°C = {format_temperature(temp_f, 'F')}")

       print("\n✓ All tests completed!")

.. mchoice:: main_pattern_understanding
   :answer_a: To define the main function
   :answer_b: To run code only when the file is executed directly
   :answer_c: To import modules
   :answer_d: To handle errors
   :correct: b
   :feedback_a: It's not about defining a function; it's about conditional execution.
   :feedback_b: Correct! This pattern separates code that runs on import vs. direct execution.
   :feedback_c: This pattern doesn't import anything; it controls execution.
   :feedback_d: This pattern is not for error handling; it's for execution control.

   What is the purpose of the ``if __name__ == '__main__':`` pattern?

Common Use Cases
----------------

Here are the most common uses for the ``if __name__ == '__main__':`` block:

Use Case 1: Testing Functions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # mymodule.py

   def calculate_average(numbers):
       return sum(numbers) / len(numbers)

   if __name__ == '__main__':
       # Quick tests while developing
       test_data = [10, 20, 30, 40, 50]
       print(f"Average: {calculate_average(test_data)}")

Use Case 2: Command-Line Interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # process_data.py

   def process_file(filename):
       # Process the file
       pass

   if __name__ == '__main__':
       import sys
       if len(sys.argv) > 1:
           filename = sys.argv[1]
           process_file(filename)
       else:
           print("Usage: python process_data.py <filename>")

Use Case 3: Demonstrations
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # visualization.py

   def create_chart(data):
       # Create a chart
       pass

   if __name__ == '__main__':
       # Show example usage
       sample_data = [1, 2, 3, 4, 5]
       create_chart(sample_data)
       print("Example chart created!")

.. activecode:: main_use_cases_demo
   :nocodelens:

   # Complete example showing all three use cases

   def greet(name):
       """Greet a person by name."""
       return f"Hello, {name}!"

   def shout(name):
       """Greet a person LOUDLY."""
       return f"HELLO, {name.upper()}!!!"

   if __name__ == '__main__':
       print("🎭 Greeting Module - Demo Mode")
       print("=" * 40)

       # Use Case 1: Testing
       print("\n1. Testing functions:")
       print(f"   {greet('Alice')}")
       print(f"   {shout('bob')}")

       # Use Case 2: Interactive demo
       print("\n2. Interactive examples:")
       names = ['Charlie', 'Diana', 'Eve']
       for name in names:
           print(f"   {greet(name)}")

       # Use Case 3: Usage instructions
       print("\n3. Usage in other files:")
       print("   from greetings import greet")
       print("   print(greet('Your Name'))")

Module Introspection
--------------------

Python provides several special attributes (called **dunder attributes** because they have **d**ouble **under**scores) that give you information about modules.

Common Module Attributes
~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Special Module Attributes
   :header-rows: 1
   :widths: 20 80

   * - Attribute
     - Description
   * - ``__name__``
     - Module's name (or ``'__main__'`` if run directly)
   * - ``__doc__``
     - Module's documentation string (docstring)
   * - ``__file__``
     - Path to the module's file
   * - ``__package__``
     - Name of the package the module belongs to
   * - ``__dict__``
     - Module's namespace (all defined names)

The ``__doc__`` Attribute
~~~~~~~~~~~~~~~~~~~~~~~~~

Every module can have a **docstring** at the top of the file. This becomes the ``__doc__`` attribute:

.. activecode:: doc_attribute_demo
   :nocodelens:

   """
   This is a module docstring.

   It describes what this module does and how to use it.
   Docstrings should be placed at the very top of the file.
   """

   def example_function():
       """This is a function docstring."""
       pass

   # Access docstrings
   print("Module docstring:")
   print(__doc__)
   print()
   print("Function docstring:")
   print(example_function.__doc__)

.. important::

   **Best Practice:**

   Always include a docstring at the top of your modules:

   .. code-block:: python

      """
      Module Name: calculator

      This module provides basic arithmetic functions.
      """

The ``__file__`` Attribute
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``__file__`` attribute shows the path to the module's file:

.. activecode:: file_attribute_demo
   :nocodelens:

   import math
   import os

   # Show where the math module is located
   print(f"math module location:")
   print(f"  {math.__file__}")
   print()

   # Get the directory containing the module
   math_dir = os.path.dirname(math.__file__)
   print(f"math module directory:")
   print(f"  {math_dir}")

The ``__dict__`` Attribute
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``__dict__`` attribute is a dictionary containing all names defined in the module:

.. activecode:: dict_attribute_demo
   :nocodelens:

   import math

   # Get all names in the math module
   module_contents = math.__dict__

   # Filter to show only functions (not special attributes)
   functions = [name for name in module_contents.keys()
                if not name.startswith('_')]

   print(f"Public functions in math module ({len(functions)} total):")
   for func in sorted(functions)[:10]:  # Show first 10
       print(f"  - {func}")
   print("  ...")

.. mchoice:: module_attributes_understanding
   :answer_a: __name__
   :answer_b: __doc__
   :answer_c: __file__
   :answer_d: __package__
   :correct: b
   :feedback_a: __name__ contains the module name, not documentation.
   :feedback_b: Correct! __doc__ contains the module's docstring (documentation).
   :feedback_c: __file__ contains the file path, not documentation.
   :feedback_d: __package__ contains the package name, not documentation.

   Which attribute contains a module's documentation string?

Practical Introspection Example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here's a complete function that introspects any module:

.. activecode:: module_introspection_tool
   :nocodelens:

   def inspect_module(module):
       """
       Display detailed information about a module.

       Args:
           module: The module object to inspect
       """
       print(f"{'='*60}")
       print(f"MODULE INSPECTION: {module.__name__}")
       print(f"{'='*60}")

       # Name
       print(f"\n📛 Name: {module.__name__}")

       # Documentation
       if module.__doc__:
           doc_preview = module.__doc__.strip().split('\n')[0]
           print(f"📄 Documentation: {doc_preview}...")
       else:
           print(f"📄 Documentation: None")

       # File location
       if hasattr(module, '__file__') and module.__file__:
           print(f"📁 File: {module.__file__}")

       # Package
       if hasattr(module, '__package__'):
           pkg = module.__package__ or "(not in a package)"
           print(f"📦 Package: {pkg}")

       # Public contents
       public_names = [name for name in dir(module)
                      if not name.startswith('_')]
       print(f"\n🔧 Public items: {len(public_names)}")
       print(f"   First 5: {', '.join(public_names[:5])}")

       print(f"\n{'='*60}\n")

   # Test it with the math module
   import math
   inspect_module(math)

Writing Dual-Purpose Modules
-----------------------------

A **dual-purpose module** can be both imported and run directly. Here's the recommended pattern:

.. activecode:: dual_purpose_module_template
   :nocodelens:

   """
   Temperature Converter Module

   This module provides functions to convert between
   Celsius and Fahrenheit temperature scales.

   Usage as module:
       from temp_converter import celsius_to_fahrenheit
       temp = celsius_to_fahrenheit(100)

   Usage as script:
       python temp_converter.py
   """

   def celsius_to_fahrenheit(celsius):
       """Convert Celsius to Fahrenheit."""
       return (celsius * 9/5) + 32

   def fahrenheit_to_celsius(fahrenheit):
       """Convert Fahrenheit to Celsius."""
       return (fahrenheit - 32) * 5/9

   def main():
       """Main function when run as a script."""
       print("🌡️  Temperature Converter")
       print("=" * 40)

       # Interactive mode
       try:
           temp = float(input("Enter temperature: "))
           unit = input("Unit (C/F): ").strip().upper()

           if unit == 'C':
               result = celsius_to_fahrenheit(temp)
               print(f"{temp}°C = {result:.1f}°F")
           elif unit == 'F':
               result = fahrenheit_to_celsius(temp)
               print(f"{temp}°F = {result:.1f}°C")
           else:
               print("Invalid unit! Use C or F.")
       except ValueError:
           print("Invalid temperature!")

   if __name__ == '__main__':
       main()

.. note::

   **Best Practice Pattern:**

   1. Write a ``main()`` function with your script logic
   2. Call ``main()`` inside the ``if __name__ == '__main__':`` block
   3. Keep the actual logic inside ``main()``, not directly in the ``if`` block

   This makes your code cleaner and easier to test!

Practice Problems
-----------------

.. mchoice:: main_pattern_identification
   :answer_a: if __name__ == 'main':
   :answer_b: if __name__ == '__main__':
   :answer_c: if __name__ is '__main__':
   :answer_d: if name == '__main__':
   :correct: b
   :feedback_a: Missing the double underscores around 'main'.
   :feedback_b: Correct! This is the proper syntax with double underscores.
   :feedback_c: Use == for comparison, not 'is' (though 'is' might work, == is standard).
   :feedback_d: It's __name__ (with underscores), not name.

   Which is the correct syntax for the main guard pattern?

.. activecode:: main_pattern_challenge
   :nocodelens:

   Create a module with two functions: ``square(n)`` and ``cube(n)``.
   Add a main guard that tests both functions with the values 1-5.
   ~~~~
   def square(n):
       # Your code here
       pass

   def cube(n):
       # Your code here
       pass

   # Add your main guard and testing code here

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_square(self):
           self.assertEqual(square(2), 4)
           self.assertEqual(square(5), 25)

       def test_cube(self):
           self.assertEqual(cube(2), 8)
           self.assertEqual(cube(3), 27)

   MyTests().main()

.. activecode:: module_inspector_challenge
   :nocodelens:

   Write a function ``list_public_functions(module)`` that returns a list
   of all public function names in a module (names that don't start with _
   and are callable).

   Hint: Use ``dir(module)`` and ``callable()``
   ~~~~
   import math

   def list_public_functions(module):
       # Your code here
       pass

   # Test it
   functions = list_public_functions(math)
   print(f"Found {len(functions)} public functions")
   print(f"First 5: {functions[:5]}")

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_list_public_functions(self):
           import math
           result = list_public_functions(math)
           # Should return a list
           self.assertIsInstance(result, list)
           # Should contain common math functions
           self.assertIn('sqrt', result)
           self.assertIn('sin', result)
           # Should not contain private names
           self.assertNotIn('__name__', result)

   MyTests().main()

.. parsonsprob:: main_pattern_parsons
   :numbered: left
   :adaptive:

   Arrange the code to create a proper dual-purpose module with a main guard.
   -----
   """Module for calculating areas."""
   =====
   def area_rectangle(width, height):
       return width * height
   =====
   def area_circle(radius):
       return 3.14159 * radius ** 2
   =====
   def main():
   =====
       print("Testing area functions:")
   =====
       print(f"Rectangle: {area_rectangle(5, 3)}")
   =====
       print(f"Circle: {area_circle(10)}")
   =====
   if __name__ == '__main__':
   =====
       main()

Key Takeaways
-------------

.. admonition:: Summary

   * **``__name__``** is a special variable that equals:

     * ``'__main__'`` when file is run directly
     * Module name when file is imported

   * **Main guard pattern:**

     .. code-block:: python

        if __name__ == '__main__':
            # code only runs when executed directly

   * **Common use cases:**

     * Testing functions during development
     * Creating command-line interfaces
     * Demonstrating module usage

   * **Module introspection attributes:**

     * ``__name__`` — Module name
     * ``__doc__`` — Documentation string
     * ``__file__`` — File path
     * ``__package__`` — Package name
     * ``__dict__`` — Module namespace

   * **Best practice:** Use a ``main()`` function inside the main guard

----

What's Next?
------------

You've mastered the ``__name__`` variable and module introspection!

In the next lesson, you'll learn:

- **Using ``pip`` to install packages** from PyPI
- Exploring the Python Package Index (PyPI)
- Creating requirements files for projects
- Virtual environments basics

.. note::

   **✅ Lesson 5 Complete!**

   **You've learned about ``__name__`` and introspection:**

   - ✅ Understanding the ``__name__`` variable
   - ✅ The ``if __name__ == '__main__':`` pattern
   - ✅ Writing dual-purpose modules (importable + executable)
   - ✅ Module introspection attributes (``__doc__``, ``__file__``, etc.)
   - ✅ Best practices for module design
   - ✅ Creating clean, professional Python modules

   **Next:** Learn to use ``pip`` and access thousands of third-party packages!