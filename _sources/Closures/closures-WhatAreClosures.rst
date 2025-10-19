..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 1: What Are Closures?
=============================

Introduction
------------

You've already worked with functions that return values like numbers, strings, or lists. But what if a function could return **another function**? And what if that returned function could "remember" variables from where it was created?

Welcome to **closures** — one of Python's most elegant and powerful features.

In this section, you'll discover:

- What closures are and why they matter
- How nested functions and scope rules create closures
- Real-world analogies to understand closure behavior
- The difference between closures and regular functions

By the end, you'll understand the "magic" behind closures and be ready to create your own.

---

What Is a Closure?
------------------

A **closure** is a function that:

1. Is defined inside another function (nested function)
2. References variables from the outer (enclosing) function's scope
3. Is returned by the outer function
4. "Remembers" the values of those outer variables even after the outer function has finished executing

Here's the simplest possible closure:

.. activecode:: closure_intro_basic
   :language: python
   :caption: A Simple Closure

   def outer():
       message = "Hello from outer!"

       def inner():
           print(message)  # inner accesses outer's variable

       return inner  # Return the function itself, not calling it

   # Create a closure
   my_function = outer()

   # Call the closure
   my_function()  # Prints: Hello from outer!

**What just happened?**

1. ``outer()`` defines a variable ``message``
2. ``outer()`` defines ``inner()`` which uses ``message``
3. ``outer()`` returns ``inner`` (the function object, not ``inner()``)
4. We call ``my_function()``, and it still has access to ``message``

The key insight: **``message`` should be gone** after ``outer()`` finishes, but ``inner()`` "captured" it in a closure.

---

The Scope Review: LEGB Rule
----------------------------

.. index:: LEGB rule, local scope, enclosing scope, global scope, built-in scope

To understand closures, let's quickly review Python's **LEGB** scope resolution rule:

.. list-table:: Python Scope Order (LEGB)
   :widths: 15 25 60
   :header-rows: 1

   * - Letter
     - Scope Type
     - Description
   * - **L**
     - **Local**
     - Variables defined inside the current function
   * - **E**
     - **Enclosing**
     - Variables in the outer (enclosing) function's scope
   * - **G**
     - **Global**
     - Variables at the module level
   * - **B**
     - **Built-in**
     - Python's built-in names (``len``, ``print``, etc.)

Closures rely on the **E** (Enclosing) scope — the inner function "reaches out" to the enclosing function's scope.

.. activecode:: closure_scope_demo
   :language: python
   :caption: LEGB in Action

   x = "GLOBAL"  # Global scope

   def outer():
       x = "ENCLOSING"  # Enclosing scope

       def inner():
           x = "LOCAL"  # Local scope
           print(f"Local x: {x}")

       inner()
       print(f"Enclosing x: {x}")

   outer()
   print(f"Global x: {x}")

**Output:**

::

   Local x: LOCAL
   Enclosing x: ENCLOSING
   Global x: GLOBAL

Each function sees its own ``x`` based on the LEGB rule. Closures use the **E** (Enclosing) scope to remember outer variables.

---

Why "Closure"?
--------------

.. index:: closure etymology, closed over, lexical closure

The term **"closure"** comes from the idea that the inner function **"closes over"** the variables from the enclosing scope. It's like sealing them in a time capsule.

Think of it like this:

.. note::
   **Analogy: The Backpack Metaphor**

   Imagine ``outer()`` is a factory worker who hands you a toolbox (the ``inner`` function). Before giving it to you, the worker puts special tools (the enclosing variables) inside the toolbox. Even after the worker leaves (``outer()`` finishes), you still have those tools in your toolbox. That's a closure!

The inner function carries a "backpack" of variables from its birthplace.

---

Closures vs. Regular Functions
-------------------------------

.. index:: closure vs function, free variables, bound variables

Let's compare a regular function to a closure:

.. activecode:: closure_vs_regular
   :language: python
   :caption: Regular Function vs. Closure

   # Regular function
   def greet_regular(name):
       greeting = "Hello"
       return f"{greeting}, {name}!"

   print(greet_regular("Alice"))
   print(greet_regular("Bob"))

   print("-" * 40)

   # Closure factory
   def make_greeter(greeting):
       def greet(name):
           return f"{greeting}, {name}!"
       return greet

   # Create two different greeters
   hello_greeter = make_greeter("Hello")
   hola_greeter = make_greeter("Hola")

   print(hello_greeter("Alice"))
   print(hola_greeter("Bob"))

**Output:**

::

   Hello, Alice!
   Hello, Bob!
   ----------------------------------------
   Hello, Alice!
   Hola, Bob!

**Key Differences:**

.. list-table::
   :widths: 50 50
   :header-rows: 1

   * - Regular Function
     - Closure
   * - All data passed as arguments
     - Some data "baked in" from enclosing scope
   * - No memory of previous context
     - Remembers enclosing variables
   * - Same behavior every time
     - Behavior customized by outer function
   * - Stateless
     - Can maintain state (as we'll see later)

---

A Real-World Example
--------------------

.. index:: closure example, multiplier function

Let's see a practical closure that creates customized multiplier functions:

.. activecode:: closure_multiplier
   :language: python
   :caption: Closure for Custom Multipliers

   def make_multiplier(factor):
       """Returns a function that multiplies by factor."""
       def multiply(number):
           return number * factor
       return multiply

   # Create specialized multipliers
   times_2 = make_multiplier(2)
   times_5 = make_multiplier(5)
   times_10 = make_multiplier(10)

   # Use them
   print(f"7 × 2 = {times_2(7)}")
   print(f"7 × 5 = {times_5(7)}")
   print(f"7 × 10 = {times_10(7)}")

   # Each closure remembers its own 'factor'
   print(f"\n3 × 2 = {times_2(3)}")
   print(f"3 × 5 = {times_5(3)}")

**Why This Works:**

1. ``make_multiplier(2)`` creates ``multiply`` with ``factor = 2`` captured
2. ``make_multiplier(5)`` creates a **different** ``multiply`` with ``factor = 5``
3. Each returned function "remembers" its own ``factor`` value
4. The closures are independent — changing one doesn't affect the other

---

Check Your Understanding
-------------------------

.. mchoice:: closure_definition_mcq
   :answer_a: A function that closes files
   :answer_b: A function that remembers variables from its enclosing scope
   :answer_c: A function that loops until a condition is met
   :answer_d: A function with no return statement
   :correct: b
   :feedback_a: That's just good file handling practice! Closures are about scope, not files.
   :feedback_b: Correct! A closure "closes over" variables from the outer function's scope.
   :feedback_c: That's a loop, not a closure.
   :feedback_d: Functions can have any return behavior and still be closures.

   What is a closure in Python?

.. mchoice:: closure_scope_mcq
   :answer_a: Local scope
   :answer_b: Enclosing scope
   :answer_c: Global scope
   :answer_d: Built-in scope
   :correct: b
   :feedback_a: Local scope is the inner function's own scope.
   :feedback_b: Correct! Closures access the Enclosing scope (the "E" in LEGB).
   :feedback_c: Global scope is available to all functions, not specific to closures.
   :feedback_d: Built-in scope contains Python's built-in names.

   Which scope in the LEGB rule do closures primarily use?

.. mchoice:: closure_lifetime_mcq
   :answer_a: Only while the outer function is running
   :answer_b: Until the program ends
   :answer_c: As long as the closure function exists
   :answer_d: Variables are immediately garbage collected
   :correct: c
   :feedback_a: The magic of closures is that variables persist AFTER the outer function finishes!
   :feedback_b: Variables can be garbage collected if the closure is deleted.
   :feedback_c: Correct! Closure variables live as long as the closure function exists.
   :feedback_d: Python keeps closure variables alive as long as they're needed.

   How long do variables captured by a closure persist?

---

Practice: Build Your First Closure
-----------------------------------

.. activecode:: closure_practice_counter
   :language: python
   :caption: Practice - Build a Closure

   # TODO: Complete this closure that creates a counter
   def make_counter():
       count = 0

       def increment():
           # This won't work yet - we'll fix it in the next section!
           # For now, just return the count
           return count

       return increment

   # Test your code
   counter = make_counter()
   print(counter())  # Should print: 0
   print(counter())  # Should print: 0 (can't increment yet - we'll learn how!)

.. note::
   **Note:** You might notice this counter can't actually increment yet! That's because we need to learn about the ``nonlocal`` keyword. We'll cover that in the next section when we learn to **create** closures properly.

---

Key Takeaways
-------------

.. important::
   **Section 1 Summary: What Are Closures?**

   ✅ **Closure Definition:**
      - A function defined inside another function
      - References variables from the enclosing scope
      - Returned by the outer function
      - "Remembers" enclosing variables after outer function finishes

   ✅ **Scope Foundation:**
      - LEGB rule: Local → Enclosing → Global → Built-in
      - Closures rely on the **Enclosing** scope
      - Inner functions "close over" outer variables

   ✅ **Key Characteristics:**
      - Closures carry a "backpack" of variables
      - Each closure is independent (has its own captured variables)
      - Closures enable function factories and customization
      - Variables persist as long as the closure exists

   ✅ **Real-World Use:**
      - Create specialized versions of functions (e.g., ``times_2``, ``times_5``)
      - Encapsulate behavior with specific context
      - Alternative to classes for simple state management

---

What's Next?
------------

You now understand **what** closures are and **why** they work. But how do you **create** powerful closures that can modify their captured variables?

**In Section 2: Creating Closures**, you'll learn:

- How to properly capture variables in closures
- The ``nonlocal`` keyword for modifying enclosing variables
- Multiple variables in a single closure
- Common pitfalls and how to avoid them
- Building practical closure patterns

Let's move from understanding to creation! 🚀

---

.. note::
   **✅ Section 1 Complete!**

   You've learned:
   - [✓] What closures are and why they matter
   - [✓] How the LEGB scope rule enables closures
   - [✓] The difference between closures and regular functions
   - [✓] Your first closure examples

   **Ready to build closures?** → Continue to Section 2!