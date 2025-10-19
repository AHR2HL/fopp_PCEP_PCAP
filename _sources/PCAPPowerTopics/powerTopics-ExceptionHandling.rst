..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 1: Advanced Exception Handling
=======================================

.. index:: exception handling, assert, except-else, exception hierarchy

Introduction
------------

You've learned the basics of exception handling with ``try``/``except``. Now it's time to master **advanced exception techniques** that are crucial for PCAP certification and professional Python development.

Exception handling is more than just catching errors—it's about:

- **Asserting** assumptions about your code
- **Validating** inputs and state
- **Creating** custom exception hierarchies
- **Chaining** exceptions for better debugging
- **Designing** robust error handling strategies

In this section, you'll learn:

- The ``assert`` statement for debugging and validation
- The ``else`` clause in ``try``/``except`` blocks
- Creating custom exception classes and hierarchies
- Exception chaining with ``from``
- The ``finally`` clause for cleanup
- Best practices for exception handling
- Common patterns and anti-patterns

By the end, you'll handle exceptions like a pro!

---

The ``assert`` Statement
------------------------

.. index:: assert statement, assertion, debugging

**``assert``** checks if a condition is true. If false, it raises an ``AssertionError``.

**Syntax:**

.. code-block:: python

   assert condition, optional_message

**Basic Example:**

.. activecode:: exception_assert_basic
   :language: python

   def divide(a, b):
       assert b != 0, "Divisor cannot be zero"
       return a / b

   # Test assertions
   print(f"10 / 2 = {divide(10, 2)}")

   # This will raise AssertionError
   try:
       print(divide(10, 0))
   except AssertionError as e:
       print(f"❌ AssertionError: {e}")

**Output:**

::

   10 / 2 = 5.0
   ❌ AssertionError: Divisor cannot be zero

.. important::
   **When to use assert:**

   ✅ **DO use assert for:**
   - Debugging during development
   - Checking internal invariants
   - Documenting assumptions in code
   - Unit test assertions

   ❌ **DON'T use assert for:**
   - Input validation (can be disabled with -O flag)
   - Production error handling
   - User-facing error messages
   - Critical security checks

---

**Assert vs Exceptions**

.. activecode:: exception_assert_vs_exception
   :language: python

   # ❌ BAD: Using assert for user input validation
   def bad_withdraw(balance, amount):
       assert amount > 0, "Amount must be positive"  # BAD!
       assert amount <= balance, "Insufficient funds"  # BAD!
       return balance - amount

   # ✅ GOOD: Using exceptions for validation
   def good_withdraw(balance, amount):
       if amount <= 0:
           raise ValueError("Amount must be positive")
       if amount > balance:
           raise ValueError("Insufficient funds")
       return balance - amount

   # Test both
   print("Testing good_withdraw:")
   try:
       result = good_withdraw(100, 150)
   except ValueError as e:
       print(f"  ✅ Caught error: {e}")

   print("\nWhy assert is bad for this:")
   print("  • Can be disabled with python -O")
   print("  • Raises AssertionError (wrong exception type)")
   print("  • Meant for debugging, not validation")

**Output:**

::

   Testing good_withdraw:
     ✅ Caught error: Insufficient funds

   Why assert is bad for this:
     • Can be disabled with python -O
     • Raises AssertionError (wrong exception type)
     • Meant for debugging, not validation

---

**Practical Assert Examples**

.. activecode:: exception_assert_practical
   :language: python

   def calculate_average(numbers):
       """Calculate average of a list of numbers."""
       # Assert: Input should be a list
       assert isinstance(numbers, list), "Input must be a list"

       # Assert: List should not be empty
       assert len(numbers) > 0, "Cannot calculate average of empty list"

       # Assert: All elements should be numbers
       assert all(isinstance(n, (int, float)) for n in numbers), \
              "All elements must be numbers"

       return sum(numbers) / len(numbers)

   # ✅ Valid usage
   print(f"Average of [1, 2, 3]: {calculate_average([1, 2, 3])}")

   # ❌ Invalid usages
   test_cases = [
       ("string", "Expected list, got string"),
       ([], "Empty list"),
       ([1, 2, "three"], "Non-numeric element")
   ]

   for test_input, description in test_cases:
       try:
           calculate_average(test_input)
       except AssertionError as e:
           print(f"❌ {description}: {e}")

**Output:**

::

   Average of [1, 2, 3]: 2.0
   ❌ Expected list, got string: Input must be a list
   ❌ Empty list: Cannot calculate average of empty list
   ❌ Non-numeric element: All elements must be numbers

---

The ``else`` Clause in Exception Handling
------------------------------------------

.. index:: except-else, try-except-else, else clause

The ``else`` clause in a ``try``/``except`` block runs **only if no exception was raised**.

**Syntax:**

.. code-block:: python

   try:
       # Code that might raise an exception
       risky_operation()
   except SomeException:
       # Handle the exception
       handle_error()
   else:
       # Runs only if NO exception was raised
       success_operation()

**Basic Example:**

.. activecode:: exception_else_basic
   :language: python

   def divide_with_else(a, b):
       """Divide two numbers with else clause."""
       try:
           result = a / b
       except ZeroDivisionError:
           print("❌ Error: Cannot divide by zero")
           return None
       else:
           print("✅ Division successful")
           return result

   # Test with valid input
   print("Test 1: 10 / 2")
   result1 = divide_with_else(10, 2)
   print(f"Result: {result1}\n")

   # Test with zero division
   print("Test 2: 10 / 0")
   result2 = divide_with_else(10, 0)
   print(f"Result: {result2}")

**Output:**

::

   Test 1: 10 / 2
   ✅ Division successful
   Result: 5.0

   Test 2: 10 / 0
   ❌ Error: Cannot divide by zero
   Result: None

---

**Why Use ``else``?**

.. activecode:: exception_else_why
   :language: python

   # ❌ WITHOUT else (less clear)
   def process_without_else(data):
       error_occurred = False
       try:
           result = int(data)
           # More code here...
       except ValueError:
           print("Invalid data")
           error_occurred = True

       if not error_occurred:
           print(f"Processed: {result}")
           return result

   # ✅ WITH else (clearer)
   def process_with_else(data):
       try:
           result = int(data)
       except ValueError:
           print("Invalid data")
           return None
       else:
           print(f"Processed: {result}")
           return result

   print("Without else:")
   process_without_else("42")

   print("\nWith else (cleaner!):")
   process_with_else("42")

**Output:**

::

   Without else:
   Processed: 42

   With else (cleaner!):
   Processed: 42

.. note::
   **Benefits of ``else`` clause:**

   ✅ **Clarity:** Separates success code from exception handling

   ✅ **Safety:** Code in ``else`` won't accidentally catch exceptions from ``try``

   ✅ **Pythonic:** Explicit is better than implicit

---

**Practical Example: File Processing**

.. activecode:: exception_else_file_example
   :language: python

   def read_config(filename):
       """Read and parse configuration file."""
       try:
           # Simulate file reading
           if filename == "config.txt":
               data = "setting1=value1\nsetting2=value2"
           else:
               raise FileNotFoundError(f"File not found: {filename}")
       except FileNotFoundError as e:
           print(f"❌ Error: {e}")
           return {}
       else:
           # Only runs if file was read successfully
           print(f"✅ Successfully read {filename}")
           config = {}
           for line in data.split('\n'):
               key, value = line.split('=')
               config[key] = value
           return config

   # Test with valid file
   print("Reading valid config:")
   config = read_config("config.txt")
   print(f"Config: {config}\n")

   # Test with missing file
   print("Reading missing config:")
   config = read_config("missing.txt")
   print(f"Config: {config}")

**Output:**

::

   Reading valid config:
   ✅ Successfully read config.txt
   Config: {'setting1': 'value1', 'setting2': 'value2'}

   Reading missing config:
   ❌ Error: File not found: missing.txt
   Config: {}

---

The ``finally`` Clause
-----------------------

.. index:: finally clause, cleanup, resource management

The ``finally`` clause **always executes**, whether an exception occurred or not.

**Syntax:**

.. code-block:: python

   try:
       risky_operation()
   except SomeException:
       handle_error()
   finally:
       cleanup()  # ALWAYS runs

**Basic Example:**

.. activecode:: exception_finally_basic
   :language: python

   def process_data(data):
       """Process data with guaranteed cleanup."""
       print(f"Processing: {data}")

       try:
           if data < 0:
               raise ValueError("Negative value not allowed")
           result = data ** 2
           print(f"Result: {result}")
           return result
       except ValueError as e:
           print(f"Error: {e}")
           return None
       finally:
           print("Cleanup: closing resources")

   # Test with valid data
   print("Test 1: Valid data")
   process_data(5)

   print("\nTest 2: Invalid data")
   process_data(-5)

   print("\n💡 Notice: 'Cleanup' runs in BOTH cases!")

**Output:**

::

   Test 1: Valid data
   Processing: 5
   Result: 25
   Cleanup: closing resources

   Test 2: Invalid data
   Processing: -5
   Error: Negative value not allowed
   Cleanup: closing resources

   💡 Notice: 'Cleanup' runs in BOTH cases!

---

**Complete try/except/else/finally**

.. activecode:: exception_complete_structure
   :language: python

   def complete_example(value):
       """Demonstrates all clauses together."""
       print(f"\n{'='*50}")
       print(f"Processing: {value}")
       print('='*50)

       try:
           print("TRY: Attempting operation...")
           if value == 0:
               raise ZeroDivisionError("Zero not allowed")
           result = 100 / value
           print(f"TRY: Calculation successful: {result}")
       except ZeroDivisionError as e:
           print(f"EXCEPT: Caught error: {e}")
           result = None
       else:
           print(f"ELSE: No exception occurred, result = {result}")
       finally:
           print("FINALLY: Cleanup (always runs)")

       print(f"Returning: {result}")
       return result

   # Test different scenarios
   complete_example(10)   # Normal case
   complete_example(0)    # Exception case

**Output:**

::

   ==================================================
   Processing: 10
   ==================================================
   TRY: Attempting operation...
   TRY: Calculation successful: 10.0
   ELSE: No exception occurred, result = 10.0
   FINALLY: Cleanup (always runs)
   Returning: 10.0

   ==================================================
   Processing: 0
   ==================================================
   TRY: Attempting operation...
   EXCEPT: Caught error: Zero not allowed
   FINALLY: Cleanup (always runs)
   Returning: None

.. important::
   **Execution Order:**

   1. ``try`` block always executes first
   2. If exception: ``except`` block executes
   3. If NO exception: ``else`` block executes
   4. ``finally`` block ALWAYS executes last

   **finally runs even if:**
   - Exception is raised
   - Exception is NOT raised
   - ``return`` is called
   - ``break`` or ``continue`` is used

---

Custom Exception Classes
-------------------------

.. index:: custom exceptions, exception hierarchy, user-defined exceptions

Create custom exceptions by inheriting from ``Exception``.

**Basic Custom Exception:**

.. activecode:: exception_custom_basic
   :language: python

   class NegativeNumberError(Exception):
       """Raised when a negative number is provided."""
       pass

   def calculate_square_root(number):
       """Calculate square root (only for non-negative numbers)."""
       if number < 0:
           raise NegativeNumberError(f"Cannot calculate square root of {number}")
       return number ** 0.5

   # Test with valid input
   print(f"√16 = {calculate_square_root(16)}")

   # Test with invalid input
   try:
       print(f"√-4 = {calculate_square_root(-4)}")
   except NegativeNumberError as e:
       print(f"❌ Error: {e}")

**Output:**

::

   √16 = 4.0
   ❌ Error: Cannot calculate square root of -4

---

**Custom Exception with Attributes**

.. activecode:: exception_custom_attributes
   :language: python

   class InsufficientFundsError(Exception):
       """Raised when withdrawal exceeds balance."""

       def __init__(self, balance, amount):
           self.balance = balance
           self.amount = amount
           self.shortfall = amount - balance
           message = f"Insufficient funds: need ${amount}, have ${balance}"
           super().__init__(message)

   class BankAccount:
       def __init__(self, balance):
           self.balance = balance

       def withdraw(self, amount):
           if amount > self.balance:
               raise InsufficientFundsError(self.balance, amount)
           self.balance -= amount
           return self.balance

   # Test the custom exception
   account = BankAccount(100)

   try:
       account.withdraw(150)
   except InsufficientFundsError as e:
       print(f"❌ {e}")
       print(f"   Balance: ${e.balance}")
       print(f"   Attempted: ${e.amount}")
       print(f"   Shortfall: ${e.shortfall}")

**Output:**

::

   ❌ Insufficient funds: need $150, have $100
      Balance: $100
      Attempted: $150
      Shortfall: $50

---

**Exception Hierarchies**

.. activecode:: exception_hierarchy
   :language: python

   # Base exception for application
   class AppError(Exception):
       """Base exception for all application errors."""
       pass

   # Specific exceptions inherit from base
   class ValidationError(AppError):
       """Input validation failed."""
       pass

   class DatabaseError(AppError):
       """Database operation failed."""
       pass

   class AuthenticationError(AppError):
       """User authentication failed."""
       pass

   def process_user_action(action):
       """Process user action (simulated)."""
       if action == "invalid_input":
           raise ValidationError("Invalid input provided")
       elif action == "db_error":
           raise DatabaseError("Database connection lost")
       elif action == "auth_error":
           raise AuthenticationError("Invalid credentials")
       else:
           return f"Processed: {action}"

   # Catch specific exceptions
   actions = ["valid", "invalid_input", "db_error", "auth_error"]

   for action in actions:
       try:
           result = process_user_action(action)
           print(f"✅ {result}")
       except ValidationError as e:
           print(f"❌ Validation: {e}")
       except DatabaseError as e:
           print(f"❌ Database: {e}")
       except AuthenticationError as e:
           print(f"❌ Auth: {e}")

**Output:**

::

   ✅ Processed: valid
   ❌ Validation: Invalid input provided
   ❌ Database: Database connection lost
   ❌ Auth: Invalid credentials

---

**Catching Base Exception**

.. activecode:: exception_catch_base
   :language: python

   class AppError(Exception):
       """Base exception."""
       pass

   class ValidationError(AppError):
       """Validation error."""
       pass

   class DatabaseError(AppError):
       """Database error."""
       pass

   def risky_operation(error_type):
       if error_type == "validation":
           raise ValidationError("Validation failed")
       elif error_type == "database":
           raise DatabaseError("Database error")

   # Catch all app errors with base class
   errors = ["validation", "database"]

   for error_type in errors:
       try:
           risky_operation(error_type)
       except AppError as e:
           # Catches both ValidationError and DatabaseError!
           print(f"❌ {type(e).__name__}: {e}")

**Output:**

::

   ❌ ValidationError: Validation failed
   ❌ DatabaseError: Database error

---

Exception Chaining
------------------

.. index:: exception chaining, raise from, exception context

**Exception chaining** preserves the original exception when raising a new one.

**Syntax:**

.. code-block:: python

   raise NewException("message") from original_exception

**Without Chaining (Bad):**

.. activecode:: exception_no_chaining
   :language: python

   def process_config(config_string):
       try:
           config = eval(config_string)  # Dangerous but for demo
           return config['setting']
       except (SyntaxError, KeyError) as e:
           # Original exception is lost!
           raise ValueError("Invalid configuration")

   try:
       process_config("not valid python")
   except ValueError as e:
       print(f"Error: {e}")
       print(f"Original cause: Unknown! (lost)")

**Output:**

::

   Error: Invalid configuration
   Original cause: Unknown! (lost)

---

**With Chaining (Good):**

.. activecode:: exception_with_chaining
   :language: python

   def process_config(config_string):
       try:
           config = eval(config_string)
           return config['setting']
       except (SyntaxError, KeyError) as e:
           # Chain the exception!
           raise ValueError("Invalid configuration") from e

   try:
       process_config("not valid python")
   except ValueError as e:
       print(f"Error: {e}")
       print(f"Original cause: {type(e.__cause__).__name__}: {e.__cause__}")

**Output:**

::

   Error: Invalid configuration
   Original cause: SyntaxError: invalid syntax (<string>, line 1)

---

**Practical Chaining Example:**

.. activecode:: exception_chaining_practical
   :language: python

   class DataProcessingError(Exception):
       """Error processing data."""
       pass

   def parse_number(text):
       """Parse a number from text."""
       try:
           return int(text)
       except ValueError as e:
           raise DataProcessingError(f"Cannot parse '{text}' as number") from e

   def calculate_total(numbers_text):
       """Calculate total from list of number strings."""
       try:
           numbers = [parse_number(n.strip()) for n in numbers_text.split(',')]
           return sum(numbers)
       except DataProcessingError as e:
           print(f"❌ Processing Error: {e}")
           print(f"   Root cause: {e.__cause__}")
           return None

   # Test with valid data
   print("Valid data:")
   result = calculate_total("10, 20, 30")
   print(f"Total: {result}\n")

   # Test with invalid data
   print("Invalid data:")
   result = calculate_total("10, abc, 30")
   print(f"Total: {result}")

**Output:**

::

   Valid data:
   Total: 60

   Invalid data:
   ❌ Processing Error: Cannot parse 'abc' as number
      Root cause: invalid literal for int() with base 10: 'abc'
   Total: None

---

Best Practices
--------------

.. index:: exception handling best practices

**✅ DO: Be Specific with Exceptions**

.. code-block:: python

   # ✅ GOOD: Catch specific exceptions
   try:
       data = int(input())
   except ValueError:
       print("Invalid number")

   # ❌ BAD: Catch all exceptions
   try:
       data = int(input())
   except:
       print("Something went wrong")

**✅ DO: Use else for Success Code**

.. code-block:: python

   # ✅ GOOD: Separate success code
   try:
       file = open("data.txt")
   except FileNotFoundError:
       print("File not found")
   else:
       process(file)
       file.close()

**✅ DO: Use finally for Cleanup**

.. code-block:: python

   # ✅ GOOD: Guaranteed cleanup
   resource = acquire_resource()
   try:
       use_resource(resource)
   finally:
       release_resource(resource)

**✅ DO: Create Custom Exception Hierarchies**

.. code-block:: python

   # ✅ GOOD: Organized hierarchy
   class AppError(Exception):
       pass

   class ValidationError(AppError):
       pass

   class DatabaseError(AppError):
       pass

---

**❌ DON'T: Catch and Ignore**

.. code-block:: python

   # ❌ BAD: Silently ignoring errors
   try:
       risky_operation()
   except Exception:
       pass  # What went wrong?

**❌ DON'T: Use Bare except**

.. code-block:: python

   # ❌ BAD: Catches EVERYTHING (even KeyboardInterrupt!)
   try:
       operation()
   except:
       handle_error()

**❌ DON'T: Use assert for Validation**

.. code-block:: python

   # ❌ BAD: Can be disabled
   def withdraw(amount):
       assert amount > 0

   # ✅ GOOD: Proper validation
   def withdraw(amount):
       if amount <= 0:
           raise ValueError("Amount must be positive")

---

Check Your Understanding
-------------------------

.. mchoice:: exception_assert_purpose
   :answer_a: Input validation for users
   :answer_b: Debugging and checking internal assumptions
   :answer_c: Production error handling
   :answer_d: Catching all possible errors
   :correct: b
   :feedback_a: Don't use assert for user input—it can be disabled!
   :feedback_b: Correct! assert is for debugging and documenting internal assumptions during development.
   :feedback_c: assert can be disabled, so don't use it in production.
   :feedback_d: assert is for specific assumptions, not catching errors.

   What is the primary purpose of the ``assert`` statement?

.. mchoice:: exception_else_when
   :answer_a: Always runs after try block
   :answer_b: Runs only if an exception was raised
   :answer_c: Runs only if NO exception was raised
   :answer_d: Runs before the try block
   :correct: c
   :feedback_a: That's finally, not else.
   :feedback_b: That's the except block.
   :feedback_c: Correct! The else clause runs only if no exception occurred in the try block.
   :feedback_d: else comes after try/except, not before.

   When does the ``else`` clause execute in a try/except block?

.. mchoice:: exception_finally_when
   :answer_a: Only if an exception occurs
   :answer_b: Only if no exception occurs
   :answer_c: Always executes
   :answer_d: Never executes
   :correct: c
   :feedback_a: finally runs whether exception occurs or not.
   :feedback_b: finally runs whether exception occurs or not.
   :feedback_c: Correct! finally ALWAYS executes, regardless of whether an exception occurred.
   :feedback_d: finally always runs!

   When does the ``finally`` clause execute?

.. mchoice:: exception_custom_inherit
   :answer_a: BaseException
   :answer_b: Error
   :answer_c: Exception
   :answer_d: StandardError
   :correct: c
   :feedback_a: You can, but Exception is better for user-defined exceptions.
   :feedback_b: There's no Error base class in Python.
   :feedback_c: Correct! Custom exceptions should inherit from Exception.
   :feedback_d: StandardError doesn't exist in Python 3.

   What should custom exception classes inherit from?

.. mchoice:: exception_chaining
   :answer_a: raise NewError() from original
   :answer_b: raise NewError() with original
   :answer_c: raise NewError() and original
   :answer_d: raise NewError(original)
   :correct: a
   :feedback_a: Correct! Use 'from' to chain exceptions and preserve the original cause.
   :feedback_b: That's not valid Python syntax.
   :feedback_c: That's not valid Python syntax.
   :feedback_d: That would pass original as an argument, not chain it.

   How do you chain exceptions in Python?

---

Key Takeaways
-------------

.. important::
   **Section 1 Summary: Advanced Exception Handling**

   ✅ **assert Statement:**
      - Use for debugging and internal checks
      - Can be disabled with ``python -O``
      - Don't use for validation or production code
      - Raises ``AssertionError`` if condition is false

   ✅ **else Clause:**
      - Runs only if NO exception was raised in try
      - Separates success code from error handling
      - More Pythonic than using flags

   ✅ **finally Clause:**
      - ALWAYS executes, exception or not
      - Perfect for cleanup (files, connections, locks)
      - Runs even if return, break, or continue is used

   ✅ **Execution Order:**
      ``try`` → (``except`` OR ``else``) → ``finally``

   ✅ **Custom Exceptions:**
      - Inherit from ``Exception``
      - Create hierarchies for organization
      - Add custom attributes for context
      - Use descriptive names ending in "Error"

   ✅ **Exception Chaining:**
      - ``raise NewError() from original``
      - Preserves original exception as ``__cause__``
      - Better debugging and error tracing

   ✅ **Best Practices:**
      - Be specific with exception types
      - Don't catch and ignore errors
      - Avoid bare ``except:``
      - Use ``else`` and ``finally`` appropriately
      - Create custom exception hierarchies

---

What's Next?
------------

You now understand advanced exception handling! Next, you'll master **Advanced File I/O** — working with binary files, `bytearray`, `errno`, and standard streams.

**In Section 2: Advanced File I/O**, you'll learn:

- Binary vs text file modes
- The ``bytearray`` type for binary data
- Error codes with ``errno`` module
- Standard streams: ``stdin``, ``stdout``, ``stderr``
- ``readlines()`` vs ``readline()`` vs ``read()``
- Context managers and file handling
- Best practices for file operations

Ready to master file I/O? Let's go! 🚀

---

.. note::
   **✅ Section 1 Complete!**

   You've learned:
   - [✓] The ``assert`` statement
   - [✓] The ``else`` clause in exception handling
   - [✓] The ``finally`` clause for cleanup
   - [✓] Custom exception classes
   - [✓] Exception hierarchies
   - [✓] Exception chaining with ``from``
   - [✓] Best practices and anti-patterns

   **Ready for file I/O?** → Continue to Section 2!