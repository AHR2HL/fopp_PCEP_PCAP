..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 2: Creating Closures
=============================

.. index:: creating closures, nonlocal keyword, modifying closure variables

Introduction
------------

In Section 1, you learned **what** closures are and saw them in action. But there was a problem with our counter example — it could only **read** variables from the enclosing scope, not **modify** them.

In this section, you'll learn how to create powerful closures that can:

- Modify variables from the enclosing scope
- Capture multiple variables at once
- Maintain state across multiple calls
- Avoid common closure pitfalls

By the end, you'll be able to build practical closures like counters, accumulators, and state managers.

---

The Problem: Read-Only Variables
---------------------------------

.. index:: closure read-only, UnboundLocalError

Let's revisit the counter from Section 1 and try to make it work:

.. activecode:: closure_counter_broken
   :language: python
   :caption: Why This Doesn't Work

   def make_counter():
       count = 0

       def increment():
           count = count + 1  # ❌ This causes an error!
           return count

       return increment

   counter = make_counter()
   try:
       print(counter())
   except UnboundLocalError as e:
       print(f"Error: {e}")

**Output:**

::

   Error: local variable 'count' referenced before assignment

**What went wrong?**

When Python sees ``count = count + 1``, it thinks you're creating a **new local variable** called ``count`` inside ``increment()``. But then it tries to read ``count`` on the right side before it's been assigned, causing the error.

.. important::
   **Key Insight: The Assignment Problem**

   - **Reading** an enclosing variable: ✅ Works automatically
   - **Assigning** to an enclosing variable: ❌ Creates a new local variable instead

   Python assumes any variable you assign to is **local** unless you say otherwise.

---

Solution: The ``nonlocal`` Keyword
-----------------------------------

.. index:: nonlocal keyword, nonlocal declaration

The ``nonlocal`` keyword tells Python: "This variable isn't local — it belongs to the enclosing scope."

Here's the fix:

.. activecode:: closure_counter_working
   :language: python
   :caption: Working Counter with nonlocal

   def make_counter():
       count = 0

       def increment():
           nonlocal count  # ✅ Tell Python to use enclosing 'count'
           count = count + 1
           return count

       return increment

   # Create and test the counter
   counter = make_counter()
   print(counter())  # 1
   print(counter())  # 2
   print(counter())  # 3

   # Create a second independent counter
   counter2 = make_counter()
   print(counter2())  # 1
   print(counter2())  # 2

**It works!** Each closure maintains its own independent ``count`` variable.

---

How ``nonlocal`` Works
----------------------

.. index:: nonlocal scope, local vs nonlocal

Let's visualize the difference between ``local`` and ``nonlocal``:

.. activecode:: closure_nonlocal_comparison
   :language: python
   :caption: Local vs. Nonlocal

   def demo_local():
       x = "OUTER"

       def inner():
           x = "INNER"  # Creates NEW local variable
           print(f"Inside inner: {x}")

       inner()
       print(f"After inner: {x}")  # Outer x unchanged

   def demo_nonlocal():
       x = "OUTER"

       def inner():
           nonlocal x  # Refers to outer x
           x = "MODIFIED"
           print(f"Inside inner: {x}")

       inner()
       print(f"After inner: {x}")  # Outer x changed!

   print("Without nonlocal:")
   demo_local()

   print("\nWith nonlocal:")
   demo_nonlocal()

**Output:**

::

   Without nonlocal:
   Inside inner: INNER
   After inner: OUTER

   With nonlocal:
   Inside inner: MODIFIED
   After inner: MODIFIED

.. list-table:: Local vs. Nonlocal Variables
   :widths: 30 35 35
   :header-rows: 1

   * - Aspect
     - Without ``nonlocal``
     - With ``nonlocal``
   * - **Variable Scope**
     - Creates new local variable
     - References enclosing variable
   * - **Assignment Effect**
     - Only affects inner function
     - Modifies enclosing variable
   * - **Reading Allowed?**
     - ✅ Yes (if not assigned)
     - ✅ Yes
   * - **Writing Allowed?**
     - ✅ Yes (creates new local)
     - ✅ Yes (modifies enclosing)

---

Capturing Multiple Variables
-----------------------------

.. index:: multiple closure variables, multiple nonlocal

Closures can capture and modify multiple variables from the enclosing scope:

.. activecode:: closure_multiple_variables
   :language: python
   :caption: Closure with Multiple Variables

   def make_stats_tracker():
       total = 0
       count = 0

       def add_value(value):
           nonlocal total, count  # Declare multiple nonlocal variables
           total += value
           count += 1
           average = total / count
           return {
               'total': total,
               'count': count,
               'average': average
           }

       return add_value

   # Create a tracker
   tracker = make_stats_tracker()

   print(tracker(10))   # First value
   print(tracker(20))   # Second value
   print(tracker(30))   # Third value

**Output:**

::

   {'total': 10, 'count': 1, 'average': 10.0}
   {'total': 30, 'count': 2, 'average': 15.0}
   {'total': 60, 'count': 3, 'average': 20.0}

**Key Points:**

- You can declare multiple ``nonlocal`` variables: ``nonlocal total, count``
- Each closure maintains its own set of captured variables
- Variables persist across function calls

---

Pattern: Closures with Multiple Functions
------------------------------------------

.. index:: closure multiple functions, getter setter pattern

You can return **multiple** inner functions that all share the same enclosing variables:

.. activecode:: closure_multiple_functions
   :language: python
   :caption: Closure with Multiple Inner Functions

   def make_account(initial_balance):
       balance = initial_balance

       def deposit(amount):
           nonlocal balance
           balance += amount
           return balance

       def withdraw(amount):
           nonlocal balance
           if amount > balance:
               print("Insufficient funds!")
               return balance
           balance -= amount
           return balance

       def get_balance():
           return balance

       # Return all three functions
       return deposit, withdraw, get_balance

   # Create an account
   dep, wd, bal = make_account(100)

   print(f"Initial: ${bal()}")
   print(f"After deposit $50: ${dep(50)}")
   print(f"After withdraw $30: ${wd(30)}")
   print(f"After withdraw $200: ${wd(200)}")
   print(f"Final balance: ${bal()}")

**Output:**

::

   Initial: $100
   After deposit $50: $150
   After withdraw $30: $120
   Insufficient funds!
   After withdraw $200: $120
   Final balance: $120

All three functions (``deposit``, ``withdraw``, ``get_balance``) share the same ``balance`` variable!

---

Common Pitfalls and How to Avoid Them
--------------------------------------

.. index:: closure pitfalls, closure mistakes

**Pitfall #1: Forgetting ``nonlocal``**

.. activecode:: closure_pitfall_1
   :language: python
   :caption: Pitfall - Forgetting nonlocal

   def make_counter():
       count = 0

       def increment():
           # Forgot nonlocal!
           count = count + 1  # UnboundLocalError
           return count

       return increment

   counter = make_counter()
   try:
       counter()
   except UnboundLocalError:
       print("❌ Error: Forgot 'nonlocal count'")

**Fix:** Always use ``nonlocal`` when modifying enclosing variables.

---

**Pitfall #2: Using ``nonlocal`` on Global Variables**

.. activecode:: closure_pitfall_2
   :language: python
   :caption: Pitfall - nonlocal with Globals

   x = 10  # Global variable

   def outer():
       def inner():
           nonlocal x  # ❌ Won't work!
           x = 20
       return inner

   try:
       f = outer()
   except SyntaxError:
       print("❌ Error: nonlocal can't be used with global variables")

   # Correct way:
   def outer_correct():
       def inner():
           global x  # ✅ Use 'global' for module-level variables
           x = 20
       return inner

.. note::
   **Remember:**

   - Use ``nonlocal`` for variables in **enclosing function** scope
   - Use ``global`` for variables in **module** (global) scope
   - You can't use ``nonlocal`` on global variables

---

**Pitfall #3: Loop Variables in Closures**

.. activecode:: closure_pitfall_3
   :language: python
   :caption: Pitfall - Loop Variables

   # ❌ Common mistake
   def make_multipliers_wrong():
       multipliers = []
       for i in range(5):
           def multiply(x):
               return x * i  # i is captured by reference!
           multipliers.append(multiply)
       return multipliers

   funcs = make_multipliers_wrong()
   print([f(2) for f in funcs])  # Expected: [0, 2, 4, 6, 8]
                                  # Actual:   [8, 8, 8, 8, 8]

   print("-" * 40)

   # ✅ Correct approach: Use default argument
   def make_multipliers_correct():
       multipliers = []
       for i in range(5):
           def multiply(x, factor=i):  # Capture i's VALUE
               return x * factor
           multipliers.append(multiply)
       return multipliers

   funcs2 = make_multipliers_correct()
   print([f(2) for f in funcs2])  # [0, 2, 4, 6, 8] ✅

**Why?** All closures share the **same** ``i`` variable, which ends up as ``4`` after the loop. Use default arguments to capture the **value** at each iteration.

---

Practical Pattern: Configuration Closures
------------------------------------------

.. index:: configuration closure, closure factory

Closures are great for creating pre-configured functions:

.. activecode:: closure_config_pattern
   :language: python
   :caption: Configuration Pattern

   def make_formatter(prefix, suffix, uppercase=False):
       """Create a custom string formatter."""
       def format_text(text):
           if uppercase:
               text = text.upper()
           return f"{prefix}{text}{suffix}"
       return format_text

   # Create different formatters
   bold = make_formatter("**", "**")
   italic = make_formatter("*", "*")
   shout = make_formatter("[", "]", uppercase=True)
   quote = make_formatter('"', '"')

   # Use them
   print(bold("important"))
   print(italic("emphasis"))
   print(shout("loud"))
   print(quote("said the teacher"))

**Output:**

::

   **important**
   *emphasis*
   [LOUD]
   "said the teacher"

Each formatter is a closure that "remembers" its configuration!

---

Practice Challenges
--------------------

**Challenge 1: Build a Temperature Tracker**

.. activecode:: closure_practice_temperature
   :language: python
   :caption: Challenge - Temperature Tracker

   def make_temp_tracker():
       """
       Create a closure that:
       - Maintains a list of temperature readings
       - add_temp(temp): adds a temperature
       - get_average(): returns average temperature
       - get_min(): returns minimum temperature
       - get_max(): returns maximum temperature
       """
       # TODO: Your code here
       pass

   # Test your code (uncomment when ready):
   # add, avg, min_t, max_t = make_temp_tracker()
   # add(72)
   # add(68)
   # add(75)
   # add(70)
   # print(f"Average: {avg()}°F")    # Should print: 71.25
   # print(f"Min: {min_t()}°F")       # Should print: 68
   # print(f"Max: {max_t()}°F")       # Should print: 75

.. reveal:: closure_practice_temperature_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_temp_tracker():
          temps = []

          def add_temp(temp):
              nonlocal temps
              temps.append(temp)

          def get_average():
              return sum(temps) / len(temps) if temps else 0

          def get_min():
              return min(temps) if temps else None

          def get_max():
              return max(temps) if temps else None

          return add_temp, get_average, get_min, get_max

---

**Challenge 2: Build a Rate Limiter**

.. activecode:: closure_practice_rate_limiter
   :language: python
   :caption: Challenge - Rate Limiter

   def make_rate_limiter(max_calls):
       """
       Create a closure that limits how many times
       a function can be called.

       Returns a function that:
       - Accepts any function as input
       - Allows it to run only 'max_calls' times
       - After that, prints "Rate limit exceeded!"
       """
       # TODO: Your code here
       pass

   # Test your code (uncomment when ready):
   # limiter = make_rate_limiter(3)
   #
   # def say_hello():
   #     print("Hello!")
   #
   # limited_hello = limiter(say_hello)
   #
   # limited_hello()  # Hello!
   # limited_hello()  # Hello!
   # limited_hello()  # Hello!
   # limited_hello()  # Rate limit exceeded!

.. reveal:: closure_practice_rate_limiter_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_rate_limiter(max_calls):
          call_count = 0

          def limiter(func):
              def wrapper(*args, **kwargs):
                  nonlocal call_count
                  if call_count < max_calls:
                      call_count += 1
                      return func(*args, **kwargs)
                  else:
                      print("Rate limit exceeded!")
              return wrapper

          return limiter

---

**Challenge 3: Build a Toggle**

.. activecode:: closure_practice_toggle
   :language: python
   :caption: Challenge - Toggle Function

   def make_toggle(initial_state=False):
       """
       Create a closure that toggles between True and False.

       Returns a function that:
       - Returns the current state when called
       - Toggles the state for next call
       """
       # TODO: Your code here
       pass

   # Test your code (uncomment when ready):
   # toggle = make_toggle()
   # print(toggle())  # False
   # print(toggle())  # True
   # print(toggle())  # False
   # print(toggle())  # True

.. reveal:: closure_practice_toggle_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_toggle(initial_state=False):
          state = initial_state

          def toggle():
              nonlocal state
              current = state
              state = not state
              return current

          return toggle

---

Check Your Understanding
-------------------------

.. mchoice:: closure_nonlocal_purpose
   :answer_a: To create global variables
   :answer_b: To modify variables in the enclosing function scope
   :answer_c: To import modules
   :answer_d: To delete local variables
   :correct: b
   :feedback_a: Use 'global' for that, not 'nonlocal'.
   :feedback_b: Correct! nonlocal allows you to modify variables from the enclosing scope.
   :feedback_c: That's what 'import' is for.
   :feedback_d: nonlocal is for modifying variables, not deleting them.

   What is the purpose of the ``nonlocal`` keyword?

.. mchoice:: closure_multiple_nonlocal
   :answer_a: nonlocal x; nonlocal y
   :answer_b: nonlocal x, y
   :answer_c: nonlocal(x, y)
   :answer_d: Both a and b are correct
   :correct: d
   :feedback_a: This works! You can declare multiple nonlocal statements.
   :feedback_b: This also works! You can list multiple variables with commas.
   :feedback_c: nonlocal is a statement, not a function call.
   :feedback_d: Correct! Both syntaxes are valid for declaring multiple nonlocal variables.

   How do you declare multiple nonlocal variables?

.. mchoice:: closure_reading_vs_writing
   :answer_a: You always need nonlocal to read enclosing variables
   :answer_b: You only need nonlocal when modifying enclosing variables
   :answer_c: You never need nonlocal for closures
   :answer_d: nonlocal is only for global variables
   :correct: b
   :feedback_a: Reading works automatically! You only need nonlocal for modification.
   :feedback_b: Correct! Python lets you read enclosing variables without nonlocal, but requires it for modification.
   :feedback_c: You need nonlocal when you want to modify enclosing variables.
   :feedback_d: nonlocal is for enclosing scope, not global scope (use 'global' for that).

   When do you need to use ``nonlocal`` in a closure?

.. mchoice:: closure_independence
   :answer_a: All closures created by the same outer function share variables
   :answer_b: Each closure has its own independent copy of enclosing variables
   :answer_c: Closures can't have their own state
   :answer_d: Only the first closure works; others fail
   :correct: b
   :feedback_a: Each time you call the outer function, new variables are created for the closure.
   :feedback_b: Correct! Each closure is independent and maintains its own state.
   :feedback_c: Closures are great for maintaining state!
   :feedback_d: You can create unlimited independent closures.

   If you call ``make_counter()`` twice to create two closures, what happens?

---

Key Takeaways
-------------

.. important::
   **Section 2 Summary: Creating Closures**

   ✅ **The ``nonlocal`` Keyword:**
      - Required when **modifying** enclosing variables
      - Not needed for **reading** enclosing variables
      - Syntax: ``nonlocal variable_name``
      - For multiple variables: ``nonlocal x, y, z``

   ✅ **Multiple Variables:**
      - Closures can capture many enclosing variables
      - Each closure maintains independent copies
      - All inner functions share the same enclosing variables

   ✅ **Common Patterns:**
      - **Counters:** Track state across calls
      - **Accumulators:** Build up values over time
      - **Configurations:** Pre-configure function behavior
      - **Multiple functions:** Return several functions sharing state

   ✅ **Pitfalls to Avoid:**
      - ❌ Forgetting ``nonlocal`` when modifying variables
      - ❌ Using ``nonlocal`` on global variables (use ``global`` instead)
      - ❌ Loop variable capture (use default arguments)

   ✅ **Key Principle:**
      - Closures = Functions + State
      - Alternative to classes for simple state management

---

What's Next?
------------

You now know how to create closures that can read **and** modify enclosing variables. But closures become truly powerful when you use them for **encapsulation** — hiding implementation details and protecting data.

**In Section 3: State and Encapsulation**, you'll learn:

- How closures provide data privacy and encapsulation
- Creating "private" variables that can't be accessed directly
- Building lightweight objects using closures
- Closures vs. classes for state management
- When encapsulation matters in real-world code

Ready to see how closures can replace classes? Let's go! 🚀

---

.. note::
   **✅ Section 2 Complete!**

   You've learned:
   - [✓] How to modify enclosing variables with ``nonlocal``
   - [✓] Capturing multiple variables in a single closure
   - [✓] Returning multiple functions that share state
   - [✓] Common pitfalls and how to avoid them
   - [✓] Practical closure patterns

   **Ready to master encapsulation?** → Continue to Section 3!