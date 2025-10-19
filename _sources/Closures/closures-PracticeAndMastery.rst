..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 6: Practice and Mastery
================================

.. index:: closures practice, closures assessment, mastery

Introduction
------------

**Congratulations!** You've completed all instructional sections on closures and advanced functions. Now it's time to **test your mastery** and solidify your understanding.

This comprehensive assessment section includes:

- **Vocabulary Review** — test your understanding of closure terminology
- **Conceptual Understanding** — verify you understand how closures work
- **Parsons Problems** — arrange code blocks to create working closures
- **Coding Challenges** — build closures from scratch (10 challenges)
- **Debugging Challenges** — find and fix broken closures (5 challenges)
- **Self-Assessment Checklist** — comprehensive skills inventory
- **Quick Reference Guide** — syntax and patterns at a glance

Take your time, work through each section, and use this as a learning opportunity. **Good luck!** 🚀

---

Part 1: Vocabulary Review
--------------------------

.. index:: closure vocabulary, closure terminology

Test your understanding of closure terminology:

.. mchoice:: closure_practice_vocab_closure
   :answer_a: A function that is defined inside another function
   :answer_b: A function that captures variables from its enclosing scope
   :answer_c: A function that returns another function
   :answer_d: A function that uses the nonlocal keyword
   :correct: b
   :feedback_a: Being nested is part of it, but not the defining characteristic.
   :feedback_b: Correct! A closure is a function that "closes over" (captures) variables from its enclosing scope.
   :feedback_c: Returning a function is common, but not required for a closure.
   :feedback_d: nonlocal is used for modifying captured variables, but isn't required for a closure to exist.

   What is a **closure**?

.. mchoice:: closure_practice_vocab_free_variable
   :answer_a: A variable that is defined inside a function
   :answer_b: A variable that is captured from an enclosing scope
   :answer_c: A variable that doesn't have a value
   :answer_d: A variable that is global
   :correct: b
   :feedback_a: Variables defined inside the function are local, not free.
   :feedback_b: Correct! Free variables are captured from the enclosing (outer) function's scope.
   :feedback_c: Free variables have values—they're captured from the enclosing scope.
   :feedback_d: Global variables are in the global scope, not the enclosing scope.

   What is a **free variable** in a closure?

.. mchoice:: closure_practice_vocab_nonlocal
   :answer_a: Access variables from the global scope
   :answer_b: Create new variables in the enclosing scope
   :answer_c: Modify variables from the enclosing scope
   :answer_d: Delete variables from the enclosing scope
   :correct: c
   :feedback_a: That's what global does, not nonlocal.
   :feedback_b: nonlocal doesn't create variables, it refers to existing ones.
   :feedback_c: Correct! nonlocal allows you to modify (reassign) variables from the enclosing scope.
   :feedback_d: nonlocal doesn't delete variables.

   What does the ``nonlocal`` keyword do?

.. mchoice:: closure_practice_vocab_decorator
   :answer_a: A function that adds comments to other functions
   :answer_b: A function that takes a function and returns a modified version
   :answer_c: A function that makes other functions look prettier
   :answer_d: A function that can only be used with the @ syntax
   :correct: b
   :feedback_a: Decorators don't add comments—they modify behavior.
   :feedback_b: Correct! A decorator takes a function as input and returns a modified (wrapped) version.
   :feedback_c: While the @ syntax is "decorative," the term refers to wrapping functionality.
   :feedback_d: The @ syntax is convenient, but decorators work without it too.

   What is a **decorator**?

.. mchoice:: closure_practice_vocab_enclosing_scope
   :answer_a: The global scope
   :answer_b: The scope of a function that contains another function
   :answer_c: The local scope of the innermost function
   :answer_d: The scope of built-in functions
   :correct: b
   :feedback_a: Global scope is different from enclosing scope.
   :feedback_b: Correct! The enclosing scope is the scope of the outer function that contains the inner function.
   :feedback_c: That's the local scope, not the enclosing scope.
   :feedback_d: That's the built-in scope in the LEGB rule.

   What is an **enclosing scope**?

.. mchoice:: closure_practice_vocab_legb
   :answer_a: Local, Enclosing, Global, Built-in
   :answer_b: Local, External, Global, Base
   :answer_c: List, Element, Generator, Boolean
   :answer_d: Loop, Enclosing, Global, Boolean
   :correct: a
   :feedback_a: Correct! LEGB is the order Python searches for variables: Local → Enclosing → Global → Built-in.
   :feedback_b: "External" and "Base" are not part of Python's scope resolution.
   :feedback_c: These are data types, not scopes.
   :feedback_d: "Loop" and "Boolean" are not part of the LEGB rule.

   What does **LEGB** stand for in Python's scope resolution?

.. mchoice:: closure_practice_vocab_function_factory
   :answer_a: A function that creates other functions
   :answer_b: A function that creates classes
   :answer_c: A factory design pattern implementation
   :answer_d: A function that imports other modules
   :correct: a
   :feedback_a: Correct! A function factory is a function that creates and returns new functions (often closures).
   :feedback_b: Function factories create functions, not classes.
   :feedback_c: While similar to factory pattern, this term specifically means functions that create functions.
   :feedback_d: Importing modules is unrelated to function factories.

   What is a **function factory**?

.. mchoice:: closure_practice_vocab_introspection
   :answer_a: Looking at a function's source code
   :answer_b: Examining a closure's captured variables at runtime
   :answer_c: Debugging a function with print statements
   :answer_d: Testing a function's performance
   :correct: b
   :feedback_a: Introspection is about runtime examination, not source code.
   :feedback_b: Correct! Closure introspection means examining captured variables using __closure__ and cell_contents.
   :feedback_c: That's debugging, not introspection.
   :feedback_d: That's performance testing, not introspection.

   What is **closure introspection**?

---

Part 2: Conceptual Understanding
---------------------------------

.. index:: closure concepts, closure behavior

Test your understanding of how closures work:

.. mchoice:: closure_practice_concept_memory
   :answer_a: Variables are copied and stored in the closure
   :answer_b: Variables are referenced, creating a live connection
   :answer_c: Variables are deleted from the enclosing scope
   :answer_d: Variables are converted to global variables
   :correct: b
   :feedback_a: Variables aren't copied—closures maintain a reference to the original.
   :feedback_b: Correct! Closures keep a live reference to enclosing variables, so changes are reflected.
   :feedback_c: Enclosing variables continue to exist in their original scope.
   :feedback_d: Captured variables remain in the enclosing scope, not global.

   When a closure captures a variable from the enclosing scope, what happens?

.. mchoice:: closure_practice_concept_modification
   :answer_a: You can modify it directly without any keywords
   :answer_b: You need to use the global keyword
   :answer_c: You need to use the nonlocal keyword
   :answer_d: You cannot modify it—closures are read-only
   :correct: c
   :feedback_a: Direct modification creates a new local variable instead.
   :feedback_b: global is for global scope, not enclosing scope.
   :feedback_c: Correct! nonlocal tells Python you want to modify the variable from the enclosing scope.
   :feedback_d: You can modify with nonlocal—closures aren't read-only.

   If you want to modify a captured variable in a closure, what must you do?

.. mchoice:: closure_practice_concept_loop_capture
   :answer_a: Each function captures its own copy of i
   :answer_b: All functions share a reference to the same i
   :answer_c: The functions can't access i at all
   :answer_d: Python raises an error
   :correct: b
   :feedback_a: That would be ideal, but it's not what happens!
   :feedback_b: Correct! All functions share a reference to i, so they all see the final value after the loop ends.
   :feedback_c: They can access i—that's the problem!
   :feedback_d: No error is raised, but the result is usually unexpected.

   What happens when you create closures in a loop without using default arguments?

.. mchoice:: closure_practice_concept_privacy
   :answer_a: Variables are somewhat private (convention-based with _)
   :answer_b: Variables are completely private and inaccessible from outside
   :answer_c: Variables are public and easily accessible
   :answer_d: Privacy depends on using the private keyword
   :correct: b
   :feedback_a: Classes use underscore convention, but closure variables have true privacy.
   :feedback_b: Correct! Closure variables are truly private—there's no way to access them from outside the closure.
   :feedback_c: Closure variables are completely private, not public.
   :feedback_d: Python doesn't have a private keyword.

   How private are variables captured in a closure?

.. mchoice:: closure_practice_concept_decorator_order
   :answer_a: Top to bottom (farthest from function first)
   :answer_b: Bottom to top (closest to function first)
   :answer_c: Alphabetically by decorator name
   :answer_d: Randomly each time
   :correct: b
   :feedback_a: It's the opposite direction!
   :feedback_b: Correct! Decorators are applied bottom to top—the one closest to the function wraps it first.
   :feedback_c: Order is based on position, not alphabetical.
   :feedback_d: Decorator order is deterministic and consistent.

   When multiple decorators are stacked, in what order are they applied?

.. mchoice:: closure_practice_concept_when_created
   :answer_a: When the outer function is defined
   :answer_b: When the outer function is called
   :answer_c: When the inner function is called
   :answer_d: When the inner function is returned
   :correct: b
   :feedback_a: Defining the outer function doesn't create a closure yet.
   :feedback_b: Correct! The closure is created when the outer function executes and returns the inner function.
   :feedback_c: The closure already exists by the time the inner function is called.
   :feedback_d: Returning the inner function is when the closure is created, but "called" is more accurate.

   When is a closure actually created?

.. mchoice:: closure_practice_concept_performance
   :answer_a: Closures are much slower than classes
   :answer_b: Classes are much slower than closures
   :answer_c: Closures and classes have similar performance
   :answer_d: Closures can't be compared to classes
   :correct: c
   :feedback_a: Closures are actually slightly faster for simple operations.
   :feedback_b: The difference is small, not "much slower."
   :feedback_c: Correct! For simple state, performance is comparable (closures slightly faster, but negligible).
   :feedback_d: They absolutely can be compared!

   How does closure performance compare to class performance?

.. mchoice:: closure_practice_concept_use_case
   :answer_a: Closures and classes are interchangeable
   :answer_b: Always use closures instead of classes
   :answer_c: Use closures for simple state, classes for complex state
   :answer_d: Never use closures in production code
   :correct: c
   :feedback_a: They have different strengths and use cases.
   :feedback_b: Classes are better for complex scenarios with multiple methods.
   :feedback_c: Correct! Closures excel at simple state (1-3 variables), classes are better for complex state and multiple methods.
   :feedback_d: Closures are used extensively in production Python code!

   When should you use closures vs. classes?

---

Part 3: Parsons Problems
-------------------------

.. index:: parsons problems, code arrangement

Arrange the code blocks in the correct order to create working closures:

**Problem 1: Basic Counter**

.. parsonsprob:: closure_practice_parsons_counter
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a counter function that starts at 0 and increments by 1 each time it's called.
   -----
   def make_counter():
   =====
       count = 0
   =====
       count = 0 #distractor
       global count
   =====
       def increment():
   =====
           nonlocal count
   =====
           global count #distractor
   =====
           count += 1
   =====
           count = count + 1 #distractor
   =====
           return count
   =====
       return increment

**Problem 2: Multiplier Factory**

.. parsonsprob:: closure_practice_parsons_multiplier
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a function factory that returns multiplier functions.
   -----
   def make_multiplier(factor):
   =====
   def make_multiplier(): #distractor
   =====
       def multiply(x):
   =====
       def multiply(x, factor): #distractor
   =====
           return x * factor
   =====
           return x * self.factor #distractor
   =====
       return multiply
   =====
       return multiply() #distractor

**Problem 3: Simple Decorator**

.. parsonsprob:: closure_practice_parsons_decorator
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a decorator that prints "Before" and "After" around function calls.
   -----
   def logger(func):
   =====
   @logger #distractor
   def logger(func):
   =====
       def wrapper(*args, **kwargs):
   =====
       def wrapper(func): #distractor
   =====
           print("Before")
   =====
           result = func(*args, **kwargs)
   =====
           result = func() #distractor
   =====
           print("After")
   =====
           return result
   =====
       return wrapper
   =====
       return wrapper() #distractor

**Problem 4: Decorator with Arguments**

.. parsonsprob:: closure_practice_parsons_decorator_args
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a repeat decorator that repeats a function call n times.
   -----
   def repeat(times):
   =====
   def repeat(func): #distractor
   =====
       def decorator(func):
   =====
       def decorator(times): #distractor
   =====
           def wrapper(*args, **kwargs):
   =====
           def wrapper(func): #distractor
   =====
               for _ in range(times):
   =====
               for _ in range(func): #distractor
   =====
                   result = func(*args, **kwargs)
   =====
               return result
   =====
           return wrapper
   =====
       return decorator
   =====
       return decorator() #distractor

**Problem 5: State Management**

.. parsonsprob:: closure_practice_parsons_bank_account
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a simple bank account with deposit functionality.
   -----
   def make_account(initial_balance):
   =====
       balance = initial_balance
   =====
       self.balance = initial_balance #distractor
   =====
       def deposit(amount):
   =====
           nonlocal balance
   =====
           global balance #distractor
   =====
           if amount > 0:
   =====
               balance += amount
   =====
               self.balance += amount #distractor
   =====
           return balance
   =====
       return deposit
   =====
       return deposit() #distractor

---

Part 4: Coding Challenges
--------------------------

.. index:: closure coding challenges

Build these closures from scratch! Each challenge tests different aspects of closure mastery.

**Challenge 1: Temperature Converter Factory**

.. activecode:: closure_practice_code_temp_converter
   :language: python
   :autograde: unittest

   Create a function ``make_converter(from_unit, to_unit)`` that returns a temperature converter function.

   Supported conversions:
   - C to F: (C * 9/5) + 32
   - F to C: (F - 32) * 5/9
   - C to K: C + 273.15
   - K to C: K - 273.15

   Example::

       c_to_f = make_converter('C', 'F')
       print(c_to_f(0))   # 32.0
       print(c_to_f(100)) # 212.0

   ~~~~
   def make_converter(from_unit, to_unit):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_c_to_f(self):
           c_to_f = make_converter('C', 'F')
           self.assertAlmostEqual(c_to_f(0), 32.0, places=1)
           self.assertAlmostEqual(c_to_f(100), 212.0, places=1)
           self.assertAlmostEqual(c_to_f(-40), -40.0, places=1)

       def test_f_to_c(self):
           f_to_c = make_converter('F', 'C')
           self.assertAlmostEqual(f_to_c(32), 0.0, places=1)
           self.assertAlmostEqual(f_to_c(212), 100.0, places=1)

       def test_c_to_k(self):
           c_to_k = make_converter('C', 'K')
           self.assertAlmostEqual(c_to_k(0), 273.15, places=1)
           self.assertAlmostEqual(c_to_k(100), 373.15, places=1)

       def test_k_to_c(self):
           k_to_c = make_converter('K', 'C')
           self.assertAlmostEqual(k_to_c(273.15), 0.0, places=1)
           self.assertAlmostEqual(k_to_c(373.15), 100.0, places=1)

   myTests().main()

.. reveal:: closure_practice_code_temp_converter_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_converter(from_unit, to_unit):
          def convert(temp):
              # Convert to Celsius first (if needed)
              if from_unit == 'F':
                  celsius = (temp - 32) * 5/9
              elif from_unit == 'K':
                  celsius = temp - 273.15
              else:  # Already Celsius
                  celsius = temp

              # Convert from Celsius to target (if needed)
              if to_unit == 'F':
                  return (celsius * 9/5) + 32
              elif to_unit == 'K':
                  return celsius + 273.15
              else:  # Already Celsius
                  return celsius

          return convert

---

**Challenge 2: Running Statistics**

.. activecode:: closure_practice_code_running_stats
   :language: python
   :autograde: unittest

   Create a function ``make_stats()`` that returns a function which calculates running statistics.

   The returned function should:
   - Accept a number and add it to the running total
   - Return a dictionary with: {'count': N, 'sum': S, 'avg': A, 'min': M, 'max': X}

   Example::

       stats = make_stats()
       print(stats(10))  # {'count': 1, 'sum': 10, 'avg': 10.0, 'min': 10, 'max': 10}
       print(stats(20))  # {'count': 2, 'sum': 30, 'avg': 15.0, 'min': 10, 'max': 20}
       print(stats(5))   # {'count': 3, 'sum': 35, 'avg': 11.67, 'min': 5, 'max': 20}

   ~~~~
   def make_stats():
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_single_value(self):
           stats = make_stats()
           result = stats(10)
           self.assertEqual(result['count'], 1)
           self.assertEqual(result['sum'], 10)
           self.assertAlmostEqual(result['avg'], 10.0, places=2)

       def test_multiple_values(self):
           stats = make_stats()
           stats(10)
           stats(20)
           result = stats(30)
           self.assertEqual(result['count'], 3)
           self.assertEqual(result['sum'], 60)
           self.assertAlmostEqual(result['avg'], 20.0, places=2)

       def test_min_max(self):
           stats = make_stats()
           stats(15)
           stats(5)
           result = stats(25)
           self.assertEqual(result['min'], 5)
           self.assertEqual(result['max'], 25)

   myTests().main()

.. reveal:: closure_practice_code_running_stats_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_stats():
          values = []

          def add_value(num):
              nonlocal values
              values.append(num)

              return {
                  'count': len(values),
                  'sum': sum(values),
                  'avg': sum(values) / len(values),
                  'min': min(values),
                  'max': max(values)
              }

          return add_value

---

**Challenge 3: Rate Limiter**

.. activecode:: closure_practice_code_rate_limiter
   :language: python
   :autograde: unittest

   Create a ``rate_limit(max_calls, period)`` decorator that limits how often a function can be called.

   If called too frequently, should raise ValueError("Rate limit exceeded").

   Example::

       @rate_limit(max_calls=3, period=1.0)
       def api_call():
           return "Success"

       api_call()  # OK
       api_call()  # OK
       api_call()  # OK
       api_call()  # ValueError: Rate limit exceeded

   Note: For testing, we'll track call times rather than using actual time delays.
   ~~~~
   def rate_limit(max_calls, period):
       # Your code here
       # Hint: Store timestamps of recent calls
       pass

   ====
   from unittest.gui import TestCaseGui
   import time

   class myTests(TestCaseGui):
       def test_within_limit(self):
           @rate_limit(max_calls=3, period=1.0)
           def test_func():
               return "OK"

           # These should all succeed
           self.assertEqual(test_func(), "OK")
           self.assertEqual(test_func(), "OK")
           self.assertEqual(test_func(), "OK")

       def test_exceeds_limit(self):
           @rate_limit(max_calls=2, period=1.0)
           def test_func():
               return "OK"

           test_func()
           test_func()

           # This should fail
           with self.assertRaises(ValueError):
               test_func()

       def test_resets_after_period(self):
           @rate_limit(max_calls=2, period=0.1)
           def test_func():
               return "OK"

           test_func()
           test_func()

           time.sleep(0.2)  # Wait for period to pass

           # Should work again
           self.assertEqual(test_func(), "OK")

   myTests().main()

.. reveal:: closure_practice_code_rate_limiter_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def rate_limit(max_calls, period):
          import time

          def decorator(func):
              call_times = []

              def wrapper(*args, **kwargs):
                  nonlocal call_times
                  current_time = time.time()

                  # Remove calls older than the period
                  call_times = [t for t in call_times if current_time - t < period]

                  # Check if we're at the limit
                  if len(call_times) >= max_calls:
                      raise ValueError("Rate limit exceeded")

                  # Record this call
                  call_times.append(current_time)

                  return func(*args, **kwargs)

              return wrapper
          return decorator

---

**Challenge 4: Undo/Redo System**

.. activecode:: closure_practice_code_undo_redo
   :language: python
   :autograde: unittest

   Create ``make_undoable()`` that returns three functions: ``do(action)``, ``undo()``, and ``redo()``.

   - ``do(action)`` executes the action (a function) and adds it to history
   - ``undo()`` reverses the last action (returns the action that was undone)
   - ``redo()`` re-applies the last undone action

   Example::

       do_action, undo, redo = make_undoable()

       do_action(lambda: print("Action 1"))
       do_action(lambda: print("Action 2"))
       undo()  # Returns Action 2 function
       redo()  # Returns Action 2 function

   ~~~~
   def make_undoable():
       # Your code here
       # Hint: You'll need two stacks (lists): history and redo_stack
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_do_action(self):
           do_action, undo, redo = make_undoable()

           result = []
           do_action(lambda: result.append(1))
           do_action(lambda: result.append(2))

           self.assertEqual(result, [1, 2])

       def test_undo(self):
           do_action, undo, redo = make_undoable()

           result = []
           do_action(lambda: result.append(1))
           do_action(lambda: result.append(2))

           undone = undo()
           self.assertIsNotNone(undone)

       def test_redo(self):
           do_action, undo, redo = make_undoable()

           result = []
           action1 = lambda: result.append(1)
           action2 = lambda: result.append(2)

           do_action(action1)
           do_action(action2)
           undo()

           redone = redo()
           self.assertIsNotNone(redone)

       def test_undo_empty(self):
           do_action, undo, redo = make_undoable()
           self.assertIsNone(undo())

       def test_redo_empty(self):
           do_action, undo, redo = make_undoable()
           self.assertIsNone(redo())

   myTests().main()

.. reveal:: closure_practice_code_undo_redo_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_undoable():
          history = []
          redo_stack = []

          def do_action(action):
              nonlocal history, redo_stack
              action()  # Execute the action
              history.append(action)
              redo_stack.clear()  # Clear redo stack on new action

          def undo():
              nonlocal history, redo_stack
              if not history:
                  return None

              action = history.pop()
              redo_stack.append(action)
              return action

          def redo():
              nonlocal history, redo_stack
              if not redo_stack:
                  return None

              action = redo_stack.pop()
              action()  # Re-execute
              history.append(action)
              return action

          return do_action, undo, redo

---

**Challenge 5: Memoization with TTL**

.. activecode:: closure_practice_code_memoize_ttl
   :language: python
   :autograde: unittest

   Create a ``memoize_ttl(ttl)`` decorator that caches results for a specified time-to-live (in seconds).

   After TTL expires, the cached value should be recomputed.

   Example::

       @memoize_ttl(ttl=1.0)
       def expensive(x):
           return x ** 2

       expensive(5)  # Computed
       expensive(5)  # Cached
       time.sleep(1.1)
       expensive(5)  # Computed again (cache expired)

   ~~~~
   def memoize_ttl(ttl):
       # Your code here
       # Hint: Store {args: (result, timestamp)} in cache
       pass

   ====
   from unittest.gui import TestCaseGui
   import time

   class myTests(TestCaseGui):
       def test_caches_result(self):
           call_count = [0]

           @memoize_ttl(ttl=1.0)
           def func(x):
               call_count[0] += 1
               return x ** 2

           func(5)
           func(5)

           self.assertEqual(call_count[0], 1)  # Only computed once

       def test_different_args(self):
           call_count = [0]

           @memoize_ttl(ttl=1.0)
           def func(x):
               call_count[0] += 1
               return x ** 2

           func(5)
           func(10)

           self.assertEqual(call_count[0], 2)  # Different args

       def test_expiration(self):
           call_count = [0]

           @memoize_ttl(ttl=0.1)
           def func(x):
               call_count[0] += 1
               return x ** 2

           func(5)
           time.sleep(0.2)
           func(5)

           self.assertEqual(call_count[0], 2)  # Recomputed after TTL

   myTests().main()

.. reveal:: closure_practice_code_memoize_ttl_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def memoize_ttl(ttl):
          import time

          def decorator(func):
              cache = {}

              def wrapper(*args):
                  current_time = time.time()

                  if args in cache:
                      result, timestamp = cache[args]
                      if current_time - timestamp < ttl:
                          return result  # Still valid

                  # Compute and cache
                  result = func(*args)
                  cache[args] = (result, current_time)
                  return result

              return wrapper
          return decorator

---

**Challenge 6: Accumulator Factory**

.. activecode:: closure_practice_code_accumulator
   :language: python
   :autograde: unittest

   Create ``make_accumulator(operation, initial)`` that returns an accumulator function.

   The accumulator should:
   - Accept a value and apply the operation (e.g., sum, product, max, min)
   - Return the accumulated result

   Example::

       sum_acc = make_accumulator(lambda a, b: a + b, 0)
       print(sum_acc(5))   # 5
       print(sum_acc(10))  # 15
       print(sum_acc(3))   # 18

   ~~~~
   def make_accumulator(operation, initial):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_sum(self):
           sum_acc = make_accumulator(lambda a, b: a + b, 0)
           self.assertEqual(sum_acc(5), 5)
           self.assertEqual(sum_acc(10), 15)
           self.assertEqual(sum_acc(3), 18)

       def test_product(self):
           prod_acc = make_accumulator(lambda a, b: a * b, 1)
           self.assertEqual(prod_acc(5), 5)
           self.assertEqual(prod_acc(2), 10)
           self.assertEqual(prod_acc(3), 30)

       def test_max(self):
           max_acc = make_accumulator(lambda a, b: max(a, b), float('-inf'))
           self.assertEqual(max_acc(5), 5)
           self.assertEqual(max_acc(10), 10)
           self.assertEqual(max_acc(3), 10)

       def test_independent_accumulators(self):
           acc1 = make_accumulator(lambda a, b: a + b, 0)
           acc2 = make_accumulator(lambda a, b: a + b, 0)

           acc1(5)
           acc2(10)

           self.assertEqual(acc1(3), 8)
           self.assertEqual(acc2(2), 12)

   myTests().main()

.. reveal:: closure_practice_code_accumulator_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_accumulator(operation, initial):
          accumulator = initial

          def accumulate(value):
              nonlocal accumulator
              accumulator = operation(accumulator, value)
              return accumulator

          return accumulate

---

**Challenge 7: Validator Factory**

.. activecode:: closure_practice_code_validator
   :language: python
   :autograde: unittest

   Create ``make_validator(**rules)`` that returns a validation function.

   Rules can include:
   - min_length: Minimum string length
   - max_length: Maximum string length
   - pattern: Regex pattern to match
   - allowed: List of allowed values

   The validator should return (is_valid, error_message).

   Example::

       validate = make_validator(min_length=3, max_length=10)
       print(validate("hi"))      # (False, "Too short")
       print(validate("hello"))   # (True, None)

   ~~~~
   def make_validator(**rules):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_min_length(self):
           validate = make_validator(min_length=5)
           is_valid, msg = validate("hi")
           self.assertFalse(is_valid)

           is_valid, msg = validate("hello")
           self.assertTrue(is_valid)

       def test_max_length(self):
           validate = make_validator(max_length=5)
           is_valid, msg = validate("hello world")
           self.assertFalse(is_valid)

           is_valid, msg = validate("hello")
           self.assertTrue(is_valid)

       def test_allowed(self):
           validate = make_validator(allowed=['red', 'green', 'blue'])
           is_valid, msg = validate("yellow")
           self.assertFalse(is_valid)

           is_valid, msg = validate("red")
           self.assertTrue(is_valid)

       def test_combined_rules(self):
           validate = make_validator(min_length=3, max_length=10, allowed=['apple', 'banana'])

           is_valid, msg = validate("hi")
           self.assertFalse(is_valid)  # Too short

           is_valid, msg = validate("apple")
           self.assertTrue(is_valid)  # Valid

   myTests().main()

.. reveal:: closure_practice_code_validator_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_validator(**rules):
          import re

          def validate(value):
              # Check min_length
              if 'min_length' in rules:
                  if len(value) < rules['min_length']:
                      return (False, f"Too short (min {rules['min_length']})")

              # Check max_length
              if 'max_length' in rules:
                  if len(value) > rules['max_length']:
                      return (False, f"Too long (max {rules['max_length']})")

              # Check pattern
              if 'pattern' in rules:
                  if not re.match(rules['pattern'], value):
                      return (False, "Pattern mismatch")

              # Check allowed values
              if 'allowed' in rules:
                  if value not in rules['allowed']:
                      return (False, "Value not allowed")

              return (True, None)

          return validate

---

**Challenge 8: Event System**

.. activecode:: closure_practice_code_event_system
   :language: python
   :autograde: unittest

   Create ``make_event_system()`` that returns three functions: ``on(event, callback)``, ``off(event, callback)``, and ``emit(event, data)``.

   - ``on(event, callback)`` registers a callback for an event
   - ``off(event, callback)`` unregisters a callback
   - ``emit(event, data)`` calls all callbacks registered for that event

   Example::

       on, off, emit = make_event_system()

       def handler(data):
           print(f"Received: {data}")

       on("message", handler)
       emit("message", "Hello!")  # Calls handler

   ~~~~
   def make_event_system():
       # Your code here
       # Hint: Use a dictionary {event_name: [callback1, callback2, ...]}
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_register_and_emit(self):
           on, off, emit = make_event_system()

           result = []
           on("test", lambda data: result.append(data))
           emit("test", "Hello")

           self.assertEqual(result, ["Hello"])

       def test_multiple_handlers(self):
           on, off, emit = make_event_system()

           result = []
           on("test", lambda data: result.append(data + "1"))
           on("test", lambda data: result.append(data + "2"))
           emit("test", "Hello")

           self.assertEqual(len(result), 2)

       def test_unregister(self):
           on, off, emit = make_event_system()

           result = []
           handler = lambda data: result.append(data)

           on("test", handler)
           off("test", handler)
           emit("test", "Hello")

           self.assertEqual(result, [])

       def test_different_events(self):
           on, off, emit = make_event_system()

           result = []
           on("event1", lambda data: result.append("1:" + data))
           on("event2", lambda data: result.append("2:" + data))

           emit("event1", "A")
           emit("event2", "B")

           self.assertEqual(result, ["1:A", "2:B"])

   myTests().main()

.. reveal:: closure_practice_code_event_system_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_event_system():
          events = {}

          def on(event, callback):
              nonlocal events
              if event not in events:
                  events[event] = []
              events[event].append(callback)

          def off(event, callback):
              nonlocal events
              if event in events:
                  events[event].remove(callback)

          def emit(event, data):
              if event in events:
                  for callback in events[event]:
                      callback(data)

          return on, off, emit

---

**Challenge 9: Retry with Exponential Backoff**

.. activecode:: closure_practice_code_retry_backoff
   :language: python
   :autograde: unittest

   Create ``retry_with_backoff(max_attempts, base_delay)`` decorator that retries with exponentially increasing delays.

   Delays: base_delay, base_delay*2, base_delay*4, etc.

   Example::

       @retry_with_backoff(max_attempts=3, base_delay=0.1)
       def flaky_function():
           # Will retry with delays: 0.1s, 0.2s before giving up
           pass

   ~~~~
   def retry_with_backoff(max_attempts, base_delay):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import time

   class myTests(TestCaseGui):
       def test_succeeds_first_try(self):
           call_count = [0]

           @retry_with_backoff(max_attempts=3, base_delay=0.01)
           def func():
               call_count[0] += 1
               return "Success"

           result = func()
           self.assertEqual(call_count[0], 1)
           self.assertEqual(result, "Success")

       def test_retries_on_failure(self):
           call_count = [0]

           @retry_with_backoff(max_attempts=3, base_delay=0.01)
           def func():
               call_count[0] += 1
               if call_count[0] < 3:
                   raise ValueError("Fail")
               return "Success"

           result = func()
           self.assertEqual(call_count[0], 3)
           self.assertEqual(result, "Success")

       def test_gives_up_after_max_attempts(self):
           @retry_with_backoff(max_attempts=3, base_delay=0.01)
           def func():
               raise ValueError("Always fails")

           with self.assertRaises(ValueError):
               func()

   myTests().main()

.. reveal:: closure_practice_code_retry_backoff_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def retry_with_backoff(max_attempts, base_delay):
          import time

          def decorator(func):
              def wrapper(*args, **kwargs):
                  for attempt in range(1, max_attempts + 1):
                      try:
                          return func(*args, **kwargs)
                      except Exception as e:
                          if attempt == max_attempts:
                              raise

                          delay = base_delay * (2 ** (attempt - 1))
                          time.sleep(delay)

              return wrapper
          return decorator

---

**Challenge 10: Pipeline Builder**

.. activecode:: closure_practice_code_pipeline
   :language: python
   :autograde: unittest

   Create ``make_pipeline()`` that returns a pipeline object with:
   - ``add(func)`` — adds a transformation to the pipeline
   - ``execute(data)`` — runs data through all transformations
   - ``reset()`` — clears the pipeline

   Example::

       pipeline = make_pipeline()
       pipeline.add(lambda x: x * 2)
       pipeline.add(lambda x: x + 10)
       print(pipeline.execute(5))  # (5 * 2) + 10 = 20

   ~~~~
   def make_pipeline():
       # Your code here
       # Return an object (dict) with 'add', 'execute', 'reset' keys
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_single_transform(self):
           pipeline = make_pipeline()
           pipeline['add'](lambda x: x * 2)
           result = pipeline['execute'](5)
           self.assertEqual(result, 10)

       def test_multiple_transforms(self):
           pipeline = make_pipeline()
           pipeline['add'](lambda x: x * 2)
           pipeline['add'](lambda x: x + 10)
           pipeline['add'](lambda x: x ** 2)
           result = pipeline['execute'](5)
           # (5 * 2) = 10, (10 + 10) = 20, (20 ** 2) = 400
           self.assertEqual(result, 400)

       def test_reset(self):
           pipeline = make_pipeline()
           pipeline['add'](lambda x: x * 2)
           pipeline['reset']()
           result = pipeline['execute'](5)
           self.assertEqual(result, 5)  # No transformations

       def test_independent_pipelines(self):
           p1 = make_pipeline()
           p2 = make_pipeline()

           p1['add'](lambda x: x * 2)
           p2['add'](lambda x: x + 10)

           self.assertEqual(p1['execute'](5), 10)
           self.assertEqual(p2['execute'](5), 15)

   myTests().main()

.. reveal:: closure_practice_code_pipeline_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_pipeline():
          transforms = []

          def add(func):
              nonlocal transforms
              transforms.append(func)

          def execute(data):
              result = data
              for transform in transforms:
                  result = transform(result)
              return result

          def reset():
              nonlocal transforms
              transforms = []

          return {
              'add': add,
              'execute': execute,
              'reset': reset
          }

---

Part 5: Debugging Challenges
-----------------------------

.. index:: debugging closures, closure errors

Find and fix the bugs in these broken closures!

**Debug 1: Counter That Doesn't Count**

.. activecode:: closure_practice_debug_counter
   :language: python

   This counter doesn't increment properly. Fix it!
   ~~~~
   def make_counter():
       count = 0

       def increment():
           count = count + 1  # Bug here!
           return count

       return increment

   counter = make_counter()
   print(counter())  # Should print 1
   print(counter())  # Should print 2
   print(counter())  # Should print 3

.. reveal:: closure_practice_debug_counter_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** Missing ``nonlocal`` keyword. Python creates a new local variable instead of modifying the enclosing one.

   **Fix:**

   .. code-block:: python

      def make_counter():
          count = 0

          def increment():
              nonlocal count  # Add this!
              count = count + 1
              return count

          return increment

---

**Debug 2: Loop Variable Capture**

.. activecode:: closure_practice_debug_loop
   :language: python

   These multipliers all return the same value! Fix it!
   ~~~~
   def make_multipliers():
       multipliers = []
       for i in range(1, 4):
           def multiply(x):
               return x * i
           multipliers.append(multiply)
       return multipliers

   m1, m2, m3 = make_multipliers()
   print(m1(10))  # Should print 10 (1 * 10)
   print(m2(10))  # Should print 20 (2 * 10)
   print(m3(10))  # Should print 30 (3 * 10)

.. reveal:: closure_practice_debug_loop_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** All closures share the same ``i`` variable, which has the value 3 after the loop ends.

   **Fix:** Use a default argument to capture the current value:

   .. code-block:: python

      def make_multipliers():
          multipliers = []
          for i in range(1, 4):
              def multiply(x, factor=i):  # Capture current i
                  return x * factor
              multipliers.append(multiply)
          return multipliers

---

**Debug 3: Decorator Missing Functionality**

.. activecode:: closure_practice_debug_decorator
   :language: python

   This decorator loses the function's name and docstring! Fix it!
   ~~~~
   def timer(func):
       def wrapper(*args, **kwargs):
           import time
           start = time.time()
           result = func(*args, **kwargs)
           print(f"Took {time.time() - start:.4f}s")
           return result
       return wrapper

   @timer
   def calculate(n):
       """Calculate sum of squares"""
       return sum(x**2 for x in range(n))

   print(calculate.__name__)  # Should print "calculate"
   print(calculate.__doc__)   # Should print "Calculate sum of squares"

.. reveal:: closure_practice_debug_decorator_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** The wrapper function replaces the original function, losing metadata.

   **Fix:** Use ``functools.wraps``:

   .. code-block:: python

      def timer(func):
          from functools import wraps

          @wraps(func)  # Preserves func's metadata
          def wrapper(*args, **kwargs):
              import time
              start = time.time()
              result = func(*args, **kwargs)
              print(f"Took {time.time() - start:.4f}s")
              return result
          return wrapper

---

**Debug 4: Bank Account with Wrong Scope**

.. activecode:: closure_practice_debug_bank
   :language: python

   This bank account doesn't update the balance! Fix it!
   ~~~~
   balance = 1000  # Global balance

   def make_account():
       def deposit(amount):
           balance = balance + amount  # Bug here!
           return balance

       def get_balance():
           return balance

       return deposit, get_balance

   deposit, get_balance = make_account()
   print(get_balance())  # Should print 1000
   deposit(500)
   print(get_balance())  # Should print 1500

.. reveal:: closure_practice_debug_bank_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** The account should have its own balance, not use the global one. Also missing ``nonlocal``.

   **Fix:**

   .. code-block:: python

      def make_account(initial_balance=1000):
          balance = initial_balance  # Local to make_account

          def deposit(amount):
              nonlocal balance  # Modify enclosing variable
              balance = balance + amount
              return balance

          def get_balance():
              return balance

          return deposit, get_balance

---

**Debug 5: Memoization Doesn't Work**

.. activecode:: closure_practice_debug_memoize
   :language: python

   This memoization decorator doesn't cache anything! Fix it!
   ~~~~
   def memoize(func):
       def wrapper(*args):
           cache = {}  # Bug here!

           if args in cache:
               print("Cache hit!")
               return cache[args]

           print("Computing...")
           result = func(*args)
           cache[args] = result
           return result

       return wrapper

   @memoize
   def fibonacci(n):
       if n <= 1:
           return n
       return fibonacci(n-1) + fibonacci(n-2)

   print(fibonacci(5))
   print(fibonacci(5))  # Should be cached!

.. reveal:: closure_practice_debug_memoize_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** The cache is created inside the wrapper, so it's reset on every call!

   **Fix:** Move the cache outside the wrapper but inside the decorator:

   .. code-block:: python

      def memoize(func):
          cache = {}  # Create once, shared across all calls

          def wrapper(*args):
              if args in cache:
                  print("Cache hit!")
                  return cache[args]

              print("Computing...")
              result = func(*args)
              cache[args] = result
              return result

          return wrapper

---

Part 6: Self-Assessment Checklist
----------------------------------

.. index:: closure skills checklist

Check off the skills you've mastered:

**Fundamentals**

.. code-block:: text

   □ I can define what a closure is
   □ I understand the LEGB scope resolution rule
   □ I can identify free variables in a closure
   □ I understand when closures are created
   □ I know the difference between enclosing and global scope

**State Management**

.. code-block:: text

   □ I understand why modifying captured variables requires nonlocal
   □ I know when to use nonlocal vs. global
   □ I can create closures that maintain mutable state
   □ I understand the "read-only by default" behavior
   □ I can create multiple independent closures with separate state

**Encapsulation**

.. code-block:: text

   □ I understand that closure variables are truly private
   □ I can create "object-like" structures with closures
   □ I know when to use closures vs. classes for encapsulation
   □ I can create closures with multiple "methods"
   □ I understand the privacy advantages of closures

**Decorators**

.. code-block:: text

   □ I can write simple decorators (no arguments)
   □ I can write decorators with arguments (3-layer pattern)
   □ I understand decorator application order when stacking
   □ I can use functools.wraps to preserve metadata
   □ I can create decorators for timing, logging, caching, validation

**Practical Patterns**

.. code-block:: text

   □ I can create function factories
   □ I can implement callbacks with closures
   □ I can build memoization/caching with closures
   □ I can create rate limiters with closures
   □ I can implement retry logic with closures
   □ I recognize closure patterns in real frameworks

**Advanced Techniques**

.. code-block:: text

   □ I can use __closure__ and cell_contents for introspection
   □ I understand closure memory overhead
   □ I know when closures are faster/slower than classes
   □ I can use closures in functional programming (map/filter/reduce)
   □ I can compose functions with closures
   □ I understand class decorators (@property, @staticmethod, @classmethod)

**Debugging & Best Practices**

.. code-block:: text

   □ I avoid the loop variable capture pitfall
   □ I remember to use nonlocal when needed
   □ I write clear, well-documented closures
   □ I can debug closure issues with introspection
   □ I make informed decisions about closures vs. classes
   □ I understand when closures add unnecessary complexity

---

Part 7: Quick Reference Guide
------------------------------

.. index:: closure reference, closure cheat sheet

**Basic Closure Pattern**

.. code-block:: python

   def outer(x):
       def inner(y):
           return x + y  # x is captured (free variable)
       return inner

   add_five = outer(5)
   print(add_five(3))  # 8

---

**Modifying Captured Variables**

.. code-block:: python

   def make_counter():
       count = 0

       def increment():
           nonlocal count  # Required to modify!
           count += 1
           return count

       return increment

---

**Simple Decorator**

.. code-block:: python

   def decorator(func):
       def wrapper(*args, **kwargs):
           # Before
           result = func(*args, **kwargs)
           # After
           return result
       return wrapper

   @decorator
   def my_function():
       pass

---

**Decorator with Arguments**

.. code-block:: python

   def decorator_with_args(arg1, arg2):
       def decorator(func):
           def wrapper(*args, **kwargs):
               # Use arg1, arg2, and call func
               return func(*args, **kwargs)
           return wrapper
       return decorator

   @decorator_with_args("a", "b")
   def my_function():
       pass

---

**Function Factory**

.. code-block:: python

   def make_multiplier(factor):
       def multiply(x):
           return x * factor
       return multiply

   double = make_multiplier(2)
   triple = make_multiplier(3)

---

**Closure Introspection**

.. code-block:: python

   def outer(x):
       def inner():
           return x
       return inner

   func = outer(10)

   # Check if it's a closure
   print(func.__closure__)  # Tuple of cells

   # Get captured values
   for cell in func.__closure__:
       print(cell.cell_contents)  # 10

   # Get variable names
   print(func.__code__.co_freevars)  # ('x',)

---

**Common Pitfalls**

.. code-block:: python

   # PITFALL 1: Loop variable capture
   # ❌ WRONG
   funcs = [lambda x: x * i for i in range(3)]
   # All see final i!

   # ✅ RIGHT
   funcs = [lambda x, i=i: x * i for i in range(3)]
   # Captures current i

   # PITFALL 2: Missing nonlocal
   # ❌ WRONG
   def make_counter():
       count = 0
       def inc():
           count += 1  # Error!
       return inc

   # ✅ RIGHT
   def make_counter():
       count = 0
       def inc():
           nonlocal count  # Fixed!
           count += 1
       return inc

   # PITFALL 3: Cache inside wrapper
   # ❌ WRONG
   def memoize(func):
       def wrapper(*args):
           cache = {}  # Recreated every call!
           # ...
       return wrapper

   # ✅ RIGHT
   def memoize(func):
       cache = {}  # Created once!
       def wrapper(*args):
           # ...
       return wrapper

---

**Decision Guide: Closures vs. Classes**

.. code-block:: text

   Use Closures When:
   ✓ Simple state (1-3 variables)
   ✓ Single primary operation
   ✓ Need true privacy
   ✓ Creating decorators
   ✓ Building function factories
   ✓ Functional programming patterns

   Use Classes When:
   ✓ Complex state (many attributes)
   ✓ Multiple related methods
   ✓ Need inheritance
   ✓ Need clear OOP structure
   ✓ Team unfamiliar with closures
   ✓ Need introspection/debugging tools

---

Lesson Complete!
----------------

.. important::
   **🎉 Congratulations! You've completed Chapter 27: Closures and Advanced Functions!**

   You've mastered:
   - ✅ Closure fundamentals (definition, scope, free variables)
   - ✅ State management with ``nonlocal``
   - ✅ Encapsulation and data privacy
   - ✅ Decorators (simple and with arguments)
   - ✅ Practical patterns (factories, callbacks, memoization)
   - ✅ Advanced techniques (introspection, performance, functional programming)
   - ✅ 10 coding challenges
   - ✅ 5 debugging challenges
   - ✅ Comprehensive self-assessment

   **You're now a closure expert!** 🚀

---

What's Next?
------------

With Chapter 27 complete, you're ready for:

**Chapter 28: Advanced OOP Mastery**

**Continue your PCAP journey!** The advanced OOP chapter will complete your understanding of Python's object-oriented features. 💪

---

.. note::
   **✅ Chapter 27: Closures and Advanced Functions - COMPLETE!**

   You've completed all 6 sections:
   1. What Are Closures?
   2. Creating Closures (nonlocal)
   3. State and Encapsulation
   4. Practical Patterns
   5. Advanced Techniques
   6. Practice and Mastery

   **Total Content:**
   - 40+ interactive examples
   - 5 Parsons problems
   - 10 coding challenges
   - 5 debugging challenges
   - 35+ MCQs
   - Comprehensive reference guide

   **Ready for Chapter 28!** 🎯