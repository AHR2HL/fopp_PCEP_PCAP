..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 2: Generator Functions and ``yield``
============================================

In Lesson 1, you learned **what** generators are and **why** they're useful. Now you'll learn **how to create them** using generator functions.

By the end of this lesson, you'll understand:

- How generator functions work
- The ``yield`` keyword and how it differs from ``return``
- How generators "pause" and "resume" execution
- How to manually step through generators with ``next()``

----

Generator Functions ARE Functions
----------------------------------

Here's the key insight: **Generator functions are regular Python functions with one difference: they use** ``yield`` **instead of** ``return``.

Let's start with a regular function:

.. activecode:: gen_func_regular_function
   :language: python

   # Regular function - returns ONE value, then done
   def count_to_three():
       print("Starting...")
       return 1
       print("After 1")  # This never executes!
       return 2
       return 3

   result = count_to_three()
   print(f"Result: {result}")
   print(f"Type: {type(result)}")

**What happened?**

- The function executed once
- It returned ``1`` and immediately stopped
- Everything after the first ``return`` is unreachable
- We got a single integer value

Now let's make it a **generator function**:

.. activecode:: gen_func_first_generator
   :language: python

   # Generator function - yields MULTIPLE values, pauses between each
   def count_to_three():
       print("Starting...")
       yield 1
       print("After 1")
       yield 2
       print("After 2")
       yield 3
       print("After 3")

   result = count_to_three()
   print(f"Result: {result}")
   print(f"Type: {type(result)}")

   # To get the values, iterate
   print("\nIterating:")
   for num in result:
       print(f"Got: {num}")

**What's different?**

- Calling ``count_to_three()`` **doesn't execute the function body**
- It returns a **generator object** (the recipe, not the values!)
- The function executes **only when you iterate** over the generator
- The function **pauses** at each ``yield`` and **resumes** when you ask for the next value

.. note::

   **Key Concept: Generator Functions**

   - Use ``def`` just like regular functions
   - Use ``yield`` instead of (or in addition to) ``return``
   - Calling the function creates a generator object
   - The function body executes **lazily** as you iterate

.. mchoice:: gen_func_definition_check
   :answer_a: A generator function executes immediately when called
   :answer_b: A generator function returns a generator object when called
   :answer_c: A generator function can only have one yield statement
   :answer_d: Generator functions don't use the def keyword
   :correct: b
   :feedback_a: No! The function body doesn't execute until you iterate over the generator.
   :feedback_b: Exactly! Calling it creates the recipe, not the results.
   :feedback_c: Generator functions can have multiple yield statements!
   :feedback_d: Generator functions DO use def - they're regular functions with yield!

   What happens when you call a generator function?

----

The Magic of ``yield``: Pause and Resume
-----------------------------------------

The ``yield`` keyword does something special: it **pauses** the function and **saves its state**.

Let's see this in action:

.. activecode:: gen_func_pause_resume
   :language: python

   def count_with_message():
       print("Function starts")
       yield 1
       print("Resuming after first yield")
       yield 2
       print("Resuming after second yield")
       yield 3
       print("Function ends")

   print("Creating generator...")
   gen = count_with_message()
   print("Generator created (function hasn't run yet!)\n")

   print("Starting iteration:")
   for num in gen:
       print(f"  -> Received: {num}\n")

**Trace the execution:**

1. ``gen = count_with_message()`` creates the generator object (no print statements yet!)
2. The ``for`` loop asks for the first value:
   - Function starts running
   - Prints "Function starts"
   - Hits ``yield 1``, pauses, returns ``1``
3. Loop body prints "Received: 1"
4. Loop asks for next value:
   - Function **resumes** where it left off
   - Prints "Resuming after first yield"
   - Hits ``yield 2``, pauses, returns ``2``
5. And so on...

.. important::

   **State Preservation**

   When a generator pauses at ``yield``, it remembers:

   - Local variables
   - Where it stopped in the code
   - Loop counters
   - Everything!

----

Manual Stepping with ``next()``
--------------------------------

You can manually step through a generator using the ``next()`` function:

.. activecode:: gen_func_next_basic
   :language: python

   def simple_gen():
       print("First yield")
       yield "A"
       print("Second yield")
       yield "B"
       print("Third yield")
       yield "C"
       print("Done!")

   gen = simple_gen()
   print("Generator created\n")

   print("Calling next() #1:")
   value1 = next(gen)
   print(f"Got: {value1}\n")

   print("Calling next() #2:")
   value2 = next(gen)
   print(f"Got: {value2}\n")

   print("Calling next() #3:")
   value3 = next(gen)
   print(f"Got: {value3}\n")

Each call to ``next()`` runs the generator until it hits the next ``yield``.

.. mchoice:: next_function_check
   :answer_a: Restarts the generator from the beginning
   :answer_b: Runs the generator until the next yield (or end)
   :answer_c: Returns all remaining values at once
   :answer_d: Closes the generator permanently
   :correct: b
   :feedback_a: No! It resumes from where it paused, not from the beginning.
   :feedback_b: Exactly! It continues from the last yield.
   :feedback_c: No, it returns ONE value at a time.
   :feedback_d: It continues the generator, not closes it.

   What does ``next(gen)`` do?

----

What Happens When a Generator Ends?
------------------------------------

When a generator has no more values to yield, it raises a ``StopIteration`` exception:

.. activecode:: gen_func_stopiteration
   :language: python

   def short_gen():
       yield 1
       yield 2

   gen = short_gen()

   print(next(gen))  # 1
   print(next(gen))  # 2

   try:
       print(next(gen))  # No more values!
   except StopIteration:
       print("Generator is exhausted!")

.. note::

   **The ``for`` loop handles this automatically!**

   When you use a ``for`` loop with a generator, Python catches ``StopIteration`` for you and exits the loop gracefully.

.. activecode:: gen_func_for_handles_stop
   :language: python

   def short_gen():
       yield 1
       yield 2

   # for loop catches StopIteration automatically
   for value in short_gen():
       print(value)

   print("Loop finished cleanly!")

----

``yield`` vs. ``return``: Side-by-Side Comparison
--------------------------------------------------

Let's compare ``yield`` and ``return`` directly:

.. activecode:: gen_func_yield_vs_return
   :language: python

   # Function with RETURN - executes once, returns one value
   def regular_function():
       print("Regular function executing")
       return 42
       print("This never prints")
       return 100  # Never reached

   result = regular_function()
   print(f"Type: {type(result)}")
   print(f"Value: {result}")
   print()

   # Function with YIELD - creates generator, yields multiple values
   def generator_function():
       print("Generator starting")
       yield 10
       print("Between yields")
       yield 20
       print("Generator ending")
       yield 30

   gen = generator_function()
   print(f"Type: {type(gen)}")
   print("Values:")
   for value in gen:
       print(f"  {value}")

**Key Differences:**

+-------------------------+--------------------------------+-----------------------------------+
| Feature                 | ``return``                     | ``yield``                         |
+=========================+================================+===================================+
| **How many values?**    | One                            | Many (one per yield)              |
+-------------------------+--------------------------------+-----------------------------------+
| **What it returns**     | The value itself               | A generator object                |
+-------------------------+--------------------------------+-----------------------------------+
| **Function execution**  | Runs once, then exits          | Pauses at each yield, resumes     |
+-------------------------+--------------------------------+-----------------------------------+
| **State**               | Lost after return              | Preserved between yields          |
+-------------------------+--------------------------------+-----------------------------------+
| **Can you iterate?**    | No                             | Yes                               |
+-------------------------+--------------------------------+-----------------------------------+

.. mchoice:: yield_vs_return_check
   :answer_a: return - it's more efficient
   :answer_b: yield - it allows multiple values and pauses
   :answer_c: return - it's simpler
   :correct: b
   :feedback_a: return can't produce multiple values or pause!
   :feedback_b: Exactly! yield enables the pause/resume behavior.
   :feedback_c: Simplicity isn't the goal - we need lazy evaluation!

   Which keyword allows a function to produce multiple values lazily?

----

State Preservation in Action
-----------------------------

Let's see how generators preserve state between ``yield`` statements:

.. activecode:: gen_func_state_preservation
   :language: python

   def counter(start, end):
       print(f"Starting count from {start} to {end}")
       current = start
       while current <= end:
           print(f"  (Before yield, current = {current})")
           yield current
           current += 1  # This happens AFTER the yield
           print(f"  (After increment, current = {current})")
       print("Counting complete!")

   print("Creating generator:")
   gen = counter(1, 3)
   print("\nManual iteration:")
   print(f"First: {next(gen)}")
   print(f"Second: {next(gen)}")
   print(f"Third: {next(gen)}")
   print("\nTrying one more:")
   try:
       print(next(gen))
   except StopIteration:
       print("No more values!")

**Notice:**

- The variable ``current`` is preserved between yields
- The loop state (which iteration we're on) is preserved
- The function picks up exactly where it left off

----

Can a Generator Function Have Both ``yield`` and ``return``?
-------------------------------------------------------------

Yes! A ``return`` in a generator function signals the end (like ``break``):

.. activecode:: gen_func_yield_and_return
   :language: python

   def gen_with_return():
       yield 1
       yield 2
       return  # Stops the generator here
       yield 3  # Never reached

   for value in gen_with_return():
       print(value)

You can also return a value (though it's rarely used in practice):

.. activecode:: gen_func_return_value
   :language: python

   def gen_with_return_value():
       yield 1
       yield 2
       return "All done!"

   gen = gen_with_return_value()
   print(next(gen))
   print(next(gen))

   try:
       print(next(gen))
   except StopIteration as e:
       print(f"StopIteration raised with value: {e.value}")

.. note::

   **In practice, most generator functions only use** ``yield`` **and let the function naturally end.**

----

Real Example: Generating Fibonacci Numbers
-------------------------------------------

Let's create a practical generator that produces Fibonacci numbers:

.. activecode:: gen_func_fibonacci
   :language: python

   def fibonacci(n):
       """Generate the first n Fibonacci numbers"""
       a, b = 0, 1
       count = 0
       while count < n:
           yield a
           a, b = b, a + b  # Simultaneous assignment
           count += 1

   # Generate first 10 Fibonacci numbers
   print("First 10 Fibonacci numbers:")
   for num in fibonacci(10):
       print(num, end=" ")
   print()

   # Can create multiple independent generators
   gen1 = fibonacci(5)
   gen2 = fibonacci(5)

   print("\nTwo independent generators:")
   print("Gen1:", next(gen1))  # 0
   print("Gen2:", next(gen2))  # 0
   print("Gen1:", next(gen1))  # 1
   print("Gen1:", next(gen1))  # 1
   print("Gen2:", next(gen2))  # 1

**Why is this better than a list?**

- Doesn't store all Fibonacci numbers in memory
- Can generate arbitrarily many numbers (even infinite!)
- Each generator maintains its own state

----

Infinite Generators
-------------------

Generators can produce infinite sequences (try doing that with a list!):

.. activecode:: gen_func_infinite
   :language: python

   def infinite_counter(start=0):
       """Count forever, starting from start"""
       n = start
       while True:  # Infinite loop!
           yield n
           n += 1

   # Use with caution - this is infinite!
   counter = infinite_counter(100)

   # Take just the first 5 values
   for i in range(5):
       print(next(counter))

   # Can resume later - state is preserved
   print("...skipping ahead...")
   print(next(counter))
   print(next(counter))

.. warning::

   **Be careful with infinite generators!**

   Never do ``list(infinite_generator())`` - it will try to create an infinite list and freeze your program!

.. mchoice:: infinite_gen_check
   :answer_a: list(infinite_counter())
   :answer_b: [x for x in infinite_counter()]
   :answer_c: next(infinite_counter())
   :answer_d: for i in range(10): print(next(infinite_counter()))
   :correct: c
   :feedback_a: This would try to create an infinite list - your program would hang!
   :feedback_b: Same problem - tries to create an infinite list comprehension!
   :feedback_c: Correct! Getting one value at a time is safe.
   :feedback_d: Close, but this creates a NEW generator each iteration, so you'd get 0 ten times!

   Which is a safe way to use an infinite generator?

----

Practice: Build Your Own Generators
------------------------------------

.. activecode:: gen_func_practice_countdown
   :language: python
   :autograde: unittest

   **1.** Write a generator function ``countdown(n)`` that yields numbers from ``n`` down to 1, then yields "Blastoff!".

   Example: ``list(countdown(3))`` should produce ``[3, 2, 1, "Blastoff!"]``
   ~~~~
   def countdown(n):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_countdown_3(self):
           result = list(countdown(3))
           self.assertEqual(result, [3, 2, 1, "Blastoff!"], "Testing countdown(3)")

       def test_countdown_5(self):
           result = list(countdown(5))
           self.assertEqual(result, [5, 4, 3, 2, 1, "Blastoff!"], "Testing countdown(5)")

       def test_is_generator(self):
           import types
           self.assertIsInstance(countdown(3), types.GeneratorType, "countdown should return a generator")

       def test_uses_yield(self):
           import inspect
           source = inspect.getsource(countdown)
           self.assertIn('yield', source, "Your function should use yield")

   myTests().main()

.. activecode:: gen_func_practice_evens
   :language: python
   :autograde: unittest

   **2.** Write a generator function ``evens(start, end)`` that yields all even numbers from ``start`` to ``end`` (inclusive).

   Example: ``list(evens(1, 10))`` should produce ``[2, 4, 6, 8, 10]``
   ~~~~
   def evens(start, end):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_evens_1_10(self):
           result = list(evens(1, 10))
           self.assertEqual(result, [2, 4, 6, 8, 10], "Testing evens(1, 10)")

       def test_evens_0_6(self):
           result = list(evens(0, 6))
           self.assertEqual(result, [0, 2, 4, 6], "Testing evens(0, 6)")

       def test_evens_5_5(self):
           result = list(evens(5, 5))
           self.assertEqual(result, [], "Testing evens(5, 5) - no evens in range")

       def test_is_generator(self):
           import types
           self.assertIsInstance(evens(1, 10), types.GeneratorType, "evens should return a generator")

   myTests().main()

.. activecode:: gen_func_practice_squares
   :language: python
   :autograde: unittest

   **3.** Write a generator function ``squares(n)`` that yields the squares of numbers from 1 to ``n``.

   Example: ``list(squares(5))`` should produce ``[1, 4, 9, 16, 25]``
   ~~~~
   def squares(n):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_squares_5(self):
           result = list(squares(5))
           self.assertEqual(result, [1, 4, 9, 16, 25], "Testing squares(5)")

       def test_squares_3(self):
           result = list(squares(3))
           self.assertEqual(result, [1, 4, 9], "Testing squares(3)")

       def test_squares_1(self):
           result = list(squares(1))
           self.assertEqual(result, [1], "Testing squares(1)")

       def test_is_generator(self):
           import types
           self.assertIsInstance(squares(5), types.GeneratorType, "squares should return a generator")

   myTests().main()

----

Check Your Understanding
-------------------------

.. mchoice:: gen_func_concept_1
   :answer_a: They use a special def_generator keyword
   :answer_b: They use yield instead of (or in addition to) return
   :answer_c: They must have "generator" in their name
   :answer_d: They can only have one line of code
   :correct: b
   :feedback_a: No special keyword - just regular def!
   :feedback_b: Exactly! yield is what makes it a generator function.
   :feedback_c: The name doesn't matter - it's the yield keyword that counts.
   :feedback_d: Generator functions can be as long as needed!

   What makes a function a generator function?

.. mchoice:: gen_func_concept_2
   :answer_a: It executes the entire function and returns all values
   :answer_b: It returns a generator object without executing the function body
   :answer_c: It returns None
   :answer_d: It raises an error
   :correct: b
   :feedback_a: No! The function body doesn't execute until you iterate.
   :feedback_b: Correct! You get a generator object (the recipe).
   :feedback_c: You get a generator object, not None.
   :feedback_d: Calling a generator function is perfectly valid!

   What happens when you call a generator function?

.. mchoice:: gen_func_concept_3
   :answer_a: Local variables are reset to their initial values
   :answer_b: Local variables retain their values from the last yield
   :answer_c: Local variables are deleted
   :answer_d: You can't use local variables in generator functions
   :correct: b
   :feedback_a: No! State is preserved between yields.
   :feedback_b: Exactly! This is the magic of generators.
   :feedback_c: Variables are preserved, not deleted.
   :feedback_d: You can definitely use local variables!

   When a generator pauses at ``yield``, what happens to local variables?

.. mchoice:: gen_func_concept_4
   :answer_a: The generator raises StopIteration
   :answer_b: The generator returns None
   :answer_c: The generator restarts from the beginning
   :answer_d: The generator waits forever
   :correct: a
   :feedback_a: Correct! StopIteration signals the generator is done.
   :feedback_b: It raises an exception, not returns a value.
   :feedback_c: Generators don't restart - they're exhausted.
   :feedback_d: It signals completion immediately with StopIteration.

   What happens when you call ``next()`` on an exhausted generator?

----

What's Next?
------------

You now know how to **create generator functions** with ``yield`` and understand how they **pause and resume** execution.

In the next lesson, you'll learn about:

- **Generator expressions** (the compact syntax you saw in Lesson 1)
- How they relate to list comprehensions
- When to use expressions vs. functions

.. note::

   **✅ Lesson 2 Complete!**

   **You've learned:**

   - Generator functions use ``def`` and ``yield``
   - ``yield`` pauses execution and preserves state
   - ``next()`` manually steps through a generator
   - ``StopIteration`` signals when a generator is exhausted
   - Generators can be infinite!
   - How to write your own generator functions

   **Next:** Learn the compact syntax of generator expressions!