..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 3: Generator Expressions
================================

In Lesson 2, you learned to create generators using **generator functions** with ``yield``. But there's an even simpler way to create generators for common tasks: **generator expressions**.

By the end of this lesson, you'll understand:

- The syntax of generator expressions
- How they compare to list comprehensions
- When to use expressions vs. functions
- How to chain generators for powerful data pipelines

----

Quick Review: List Comprehensions
----------------------------------

You're already familiar with list comprehensions:

.. activecode:: genexpr_list_comp_review
   :language: python

   # List comprehension - creates a list
   numbers = [1, 2, 3, 4, 5]
   squares = [n ** 2 for n in numbers]

   print(f"Type: {type(squares)}")
   print(f"Values: {squares}")

   # With filtering
   even_squares = [n ** 2 for n in numbers if n % 2 == 0]
   print(f"Even squares: {even_squares}")

**Characteristics:**

- Uses **square brackets** ``[...]``
- Creates a **list** (eager evaluation)
- **All values computed immediately**
- Stored in memory

----

Generator Expressions: The Lazy Alternative
--------------------------------------------

A **generator expression** looks almost identical, but uses **parentheses** instead of brackets:

.. activecode:: genexpr_first_example
   :language: python

   # List comprehension - eager
   list_comp = [n ** 2 for n in range(5)]
   print(f"List: {type(list_comp)} = {list_comp}")

   # Generator expression - lazy
   gen_expr = (n ** 2 for n in range(5))
   print(f"Generator: {type(gen_expr)} = {gen_expr}")

   # Convert to list to see values
   print(f"Values: {list(gen_expr)}")

.. note::

   **Key Difference: ONE CHARACTER!**

   - ``[n ** 2 for n in range(5)]`` → List (eager)
   - ``(n ** 2 for n in range(5))`` → Generator (lazy)

.. important::

   **Generator expressions are NOT functions!**

   They're **expressions** that create generator objects, just like list comprehensions are **expressions** that create lists.

   - Generator **function**: ``def my_gen(): yield x``
   - Generator **expression**: ``(x for x in range(10))``

.. mchoice:: genexpr_syntax_check
   :answer_a: [x * 2 for x in range(10)]
   :answer_b: (x * 2 for x in range(10))
   :answer_c: {x * 2 for x in range(10)}
   :answer_d: def gen(): yield x * 2
   :correct: b
   :feedback_a: Square brackets create a list comprehension (eager).
   :feedback_b: Correct! Parentheses create a generator expression (lazy).
   :feedback_c: Curly braces create a set comprehension (eager, unordered).
   :feedback_d: This is a generator function, not an expression!

   Which syntax creates a generator expression?

----

Side-by-Side Comparison
------------------------

Let's compare list comprehensions and generator expressions directly:

.. activecode:: genexpr_comparison
   :language: python

   import sys

   n = 100_000

   # List comprehension - all values computed now
   print("Creating list comprehension...")
   list_comp = [x ** 2 for x in range(n)]
   print(f"Type: {type(list_comp)}")
   print(f"Memory: {sys.getsizeof(list_comp):,} bytes")
   print(f"First 5: {list_comp[:5]}")
   print()

   # Generator expression - values computed on demand
   print("Creating generator expression...")
   gen_expr = (x ** 2 for x in range(n))
   print(f"Type: {type(gen_expr)}")
   print(f"Memory: {sys.getsizeof(gen_expr):,} bytes")
   print(f"Can't index, but can iterate:")

   # Get first 5 values manually
   for i, value in enumerate(gen_expr):
       if i < 5:
           print(f"  {value}")
       else:
           break

**Results:**

- List: ~800,000 bytes (hundreds of KB)
- Generator: ~200 bytes (constant, regardless of size!)
- Memory savings: **4,000x less memory!**

----

Generator Expressions vs. List Comprehensions
----------------------------------------------

+---------------------------+--------------------------------+-----------------------------------+
| Feature                   | List Comprehension ``[...]``   | Generator Expression ``(...)``    |
+===========================+================================+===================================+
| **Syntax**                | Square brackets                | Parentheses                       |
+---------------------------+--------------------------------+-----------------------------------+
| **Returns**               | List                           | Generator object                  |
+---------------------------+--------------------------------+-----------------------------------+
| **Evaluation**            | Eager (all at once)            | Lazy (on demand)                  |
+---------------------------+--------------------------------+-----------------------------------+
| **Memory**                | Stores all items               | Constant (tiny)                   |
+---------------------------+--------------------------------+-----------------------------------+
| **Indexing**              | Yes (``result[0]``)            | No                                |
+---------------------------+--------------------------------+-----------------------------------+
| **Iteration**             | Multiple times                 | Once (then exhausted)             |
+---------------------------+--------------------------------+-----------------------------------+
| **Speed (first item)**    | Slow (computes everything)     | Fast (computes one)               |
+---------------------------+--------------------------------+-----------------------------------+
| **Best for**              | Small datasets, multiple uses  | Large datasets, one-time use      |
+---------------------------+--------------------------------+-----------------------------------+

.. mchoice:: genexpr_vs_listcomp_memory
   :answer_a: List comprehensions always use less memory
   :answer_b: Generator expressions always use less memory
   :answer_c: They use the same amount of memory
   :correct: b
   :feedback_a: Lists store all items in memory - generators don't!
   :feedback_b: Correct! Generators use constant memory regardless of size.
   :feedback_c: Lists grow with data size; generators stay constant.

   Which uses less memory?

----

Filtering with Generator Expressions
-------------------------------------

Just like list comprehensions, generator expressions support filtering:

.. activecode:: genexpr_filtering
   :language: python

   # List comprehension with filter
   even_list = [x for x in range(20) if x % 2 == 0]
   print(f"Even list: {even_list}")

   # Generator expression with filter
   even_gen = (x for x in range(20) if x % 2 == 0)
   print(f"Even generator: {even_gen}")
   print(f"Values: {list(even_gen)}")

   # Complex filtering
   large_squares = (x ** 2 for x in range(100) if x ** 2 > 50)
   print(f"\nSquares > 50:")
   for i, val in enumerate(large_squares):
       if i < 5:
           print(f"  {val}")
       else:
           break

----

Converting Between Lists and Generators
----------------------------------------

You can easily convert between them:

.. activecode:: genexpr_conversion
   :language: python

   # Generator expression
   gen = (x ** 2 for x in range(5))

   # Convert to list (exhausts the generator)
   my_list = list(gen)
   print(f"List: {my_list}")

   # Try to iterate again - it's exhausted!
   print(f"Iterate again: {list(gen)}")  # Empty!

   # Convert list to generator
   new_gen = (x * 2 for x in my_list)
   print(f"New generator: {new_gen}")
   print(f"Values: {list(new_gen)}")

.. warning::

   Calling ``list()`` on a generator **exhausts it** and loads everything into memory. Only do this when necessary!

----

When to Use Generator Expressions vs. Generator Functions
----------------------------------------------------------

**Use generator expressions when:**

- Simple transformation or filtering
- One-liner is sufficient
- Don't need complex logic or multiple yields

**Use generator functions when:**

- Complex logic required
- Need loops, conditionals, or multiple steps
- Want to preserve state across multiple yields
- Code readability matters (multi-line is clearer)

.. activecode:: genexpr_when_to_use
   :language: python

   # Simple case - use expression
   squares_expr = (x ** 2 for x in range(10))

   # Complex case - use function
   def fibonacci(n):
       a, b = 0, 1
       for _ in range(n):
           yield a
           a, b = b, a + b

   print("Squares:", list(squares_expr))
   print("Fibonacci:", list(fibonacci(10)))

.. mchoice:: genexpr_vs_genfunc
   :answer_a: Use a generator expression
   :answer_b: Use a generator function
   :correct: b
   :feedback_a: Generator expressions can't handle complex logic like this!
   :feedback_b: Correct! Complex logic needs a generator function.

   You need to generate prime numbers (requires loops and conditionals). What should you use?

----

Chaining Generators
--------------------

One of the most powerful features of generators is **chaining** - feeding one generator into another:

.. activecode:: genexpr_chaining_basic
   :language: python

   # Chain 1: Generate numbers
   numbers = (x for x in range(10))

   # Chain 2: Square them
   squares = (n ** 2 for n in numbers)

   # Chain 3: Filter evens
   even_squares = (s for s in squares if s % 2 == 0)

   print("Result:", list(even_squares))

**What's happening?**

1. No values are computed yet (all lazy!)
2. When we call ``list()``, it pulls from ``even_squares``
3. ``even_squares`` pulls from ``squares``
4. ``squares`` pulls from ``numbers``
5. Values flow through the pipeline one at a time

.. note::

   **Memory Efficiency of Chaining**

   Even with 3 generators in a chain, only **one value at a time** flows through the entire pipeline. Total memory: constant!

----

Chaining: A Practical Example
------------------------------

Let's process a large dataset efficiently:

.. activecode:: genexpr_chaining_practical
   :language: python

   # Simulate reading lines from a large file
   def read_lines():
       """Simulate file reading (in reality, this would be a huge file)"""
       for i in range(1000):
           yield f"Line {i}: data value {i * 10}"

   # Pipeline:
   # 1. Read lines (lazy)
   lines = read_lines()

   # 2. Extract numeric values (lazy)
   values = (int(line.split()[-1]) for line in lines)

   # 3. Filter large values (lazy)
   large_values = (v for v in values if v > 5000)

   # 4. Square them (lazy)
   squared = (v ** 2 for v in large_values)

   # Process: only 5 results, but we "processed" 1000 lines!
   print("First 5 large squared values:")
   for i, result in enumerate(squared):
       if i < 5:
           print(f"  {result}")
       else:
           break

   print("\nMemory used: Constant (only 1 value in memory at a time)!")

**Benefits:**

- Processed 1,000 lines
- Only held **1 line in memory at a time**
- Could scale to millions or billions of lines
- Clean, readable code

----

Parentheses Shortcuts
----------------------

When a generator expression is the **only argument** to a function, you can omit the extra parentheses:

.. activecode:: genexpr_parentheses_shortcut
   :language: python

   # Verbose - double parentheses
   total1 = sum((x ** 2 for x in range(10)))
   print(f"Total1: {total1}")

   # Concise - single parentheses (shortcut!)
   total2 = sum(x ** 2 for x in range(10))
   print(f"Total2: {total2}")

   # Works with any function taking an iterable
   maximum = max(x ** 3 for x in range(5))
   print(f"Max cube: {maximum}")

   # But if there are multiple arguments, you need the inner parentheses
   result = sum((x for x in range(10)), 100)  # Start at 100
   print(f"Sum starting at 100: {result}")

.. note::

   **Style tip:** The shortcut ``sum(x for x in range(10))`` is more Pythonic than ``sum((x for x in range(10)))``.

----

Generator Expressions with Multiple Inputs
-------------------------------------------

You can use multiple ``for`` clauses (like nested list comprehensions):

.. activecode:: genexpr_multiple_for
   :language: python

   # Cartesian product
   pairs = ((x, y) for x in range(3) for y in range(3))
   print("Pairs:")
   for pair in pairs:
       print(f"  {pair}")

   # With filtering
   valid_pairs = ((x, y) for x in range(5) for y in range(5) if x < y)
   print("\nPairs where x < y:")
   for pair in valid_pairs:
       print(f"  {pair}")

----

Practice: Generator Expressions
--------------------------------

.. activecode:: genexpr_practice_1
   :language: python
   :autograde: unittest

   **1.** Create a generator expression that yields the cubes of numbers from 1 to 10.

   Store it in a variable called ``cubes``.
   ~~~~
   # Your code here
   cubes = None

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_is_generator(self):
           self.assertIsInstance(cubes, types.GeneratorType, "cubes should be a generator")

       def test_values(self):
           result = list(cubes)
           expected = [1, 8, 27, 64, 125, 216, 343, 512, 729, 1000]
           self.assertEqual(result, expected, "cubes should yield cubes of 1-10")

   myTests().main()

.. activecode:: genexpr_practice_2
   :language: python
   :autograde: unittest

   **2.** Create a generator expression that yields only the **odd squares** from 1 to 100.

   Store it in a variable called ``odd_squares``.
   ~~~~
   # Your code here
   odd_squares = None

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_is_generator(self):
           self.assertIsInstance(odd_squares, types.GeneratorType, "odd_squares should be a generator")

       def test_values(self):
           result = list(odd_squares)
           expected = [x ** 2 for x in range(1, 101) if (x ** 2) % 2 == 1]
           self.assertEqual(result, expected, "Should yield odd squares from 1-100")

       def test_first_few(self):
           # Create a fresh generator
           gen = (x ** 2 for x in range(1, 101) if (x ** 2) % 2 == 1)
           first_five = [next(gen) for _ in range(5)]
           self.assertEqual(first_five, [1, 9, 25, 49, 81], "First 5 values should be 1, 9, 25, 49, 81")

   myTests().main()

.. activecode:: genexpr_practice_3
   :language: python
   :autograde: unittest

   **3.** Use a generator expression with ``sum()`` to calculate the sum of squares from 1 to 50.

   Store the result in a variable called ``total``.
   ~~~~
   # Your code here (use the parentheses shortcut!)
   total = None

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_total(self):
           expected = sum(x ** 2 for x in range(1, 51))
           self.assertEqual(total, expected, f"Sum of squares 1-50 should be {expected}")

       def test_value(self):
           self.assertEqual(total, 42925, "Sum should be 42925")

   myTests().main()

.. activecode:: genexpr_practice_4
   :language: python
   :autograde: unittest

   **4.** Create a chained generator pipeline:

   - Start with numbers 1 to 100
   - Filter only multiples of 3
   - Square each one
   - Convert to list and store in ``result``
   ~~~~
   # Your code here - use multiple generator expressions chained together
   result = None

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_result(self):
           expected = [(x ** 2) for x in range(1, 101) if x % 3 == 0]
           self.assertEqual(result, expected, "Should be squared multiples of 3")

       def test_first_few(self):
           self.assertEqual(result[:5], [9, 36, 81, 144, 225], "First 5 should be 9, 36, 81, 144, 225")

       def test_length(self):
           self.assertEqual(len(result), 33, "Should have 33 values")

   myTests().main()

----

Check Your Understanding
-------------------------

.. mchoice:: genexpr_concept_1
   :answer_a: [x ** 2 for x in range(10)]
   :answer_b: (x ** 2 for x in range(10))
   :answer_c: {x ** 2 for x in range(10)}
   :answer_d: def gen(): yield x ** 2
   :correct: b
   :feedback_a: That's a list comprehension!
   :feedback_b: Correct! Parentheses create generator expressions.
   :feedback_c: That's a set comprehension!
   :feedback_d: That's a generator function!

   Which is a generator expression?

.. mchoice:: genexpr_concept_2
   :answer_a: Generator expressions are faster for small datasets
   :answer_b: Generator expressions use less memory
   :answer_c: Generator expressions can be indexed like lists
   :answer_d: Generator expressions compute all values immediately
   :correct: b
   :feedback_a: For small datasets, lists are often faster!
   :feedback_b: Correct! Constant memory vs. storing everything.
   :feedback_c: No indexing - generators are lazy!
   :feedback_d: That's lists! Generators are lazy.

   What's the main advantage of generator expressions over list comprehensions?

.. mchoice:: genexpr_concept_3
   :answer_a: sum([x ** 2 for x in range(100)])
   :answer_b: sum((x ** 2 for x in range(100)))
   :answer_c: sum(x ** 2 for x in range(100))
   :answer_d: Both b and c
   :correct: d
   :feedback_a: That's a list comprehension - uses extra memory!
   :feedback_b: Valid generator expression syntax
   :feedback_c: Valid shortcut syntax (more Pythonic)
   :feedback_d: Exactly! Both b and c are valid generator expressions.

   Which uses a generator expression to sum squares? (Choose the best answer)

.. mchoice:: genexpr_concept_4
   :answer_a: Processing each file separately
   :answer_b: Loading all files into memory first
   :answer_c: Chaining generators to process files one at a time
   :answer_d: Converting everything to lists first
   :correct: c
   :feedback_a: That would work, but chaining is more elegant!
   :feedback_b: That defeats the purpose - terrible for memory!
   :feedback_c: Perfect! Chain generators for maximum efficiency.
   :feedback_d: Lists would use too much memory!

   You need to process 1,000 large log files. What's the best approach?

----

What's Next?
------------

You've now mastered **generator expressions** - the concise syntax for creating simple generators!

In the next lesson, you'll learn about:

- **The Iterator Protocol** - how generators actually work under the hood
- ``__iter__()`` and ``__next__()`` methods
- How ``for`` loops really work
- Creating your own iterable objects

.. note::

   **✅ Lesson 3 Complete!**

   **You've learned:**

   - Generator expression syntax: ``(x for x in ...)``
   - How they compare to list comprehensions
   - Memory efficiency of generator expressions
   - When to use expressions vs. functions
   - Chaining generators for powerful pipelines
   - The parentheses shortcut in function calls

   **Next:** Learn some practical patterns that generators use!