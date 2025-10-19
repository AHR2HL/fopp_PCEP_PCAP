..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 6: Practice and Mastery
===============================

You've learned everything about generators! Now it's time to **prove your mastery** with comprehensive practice problems.

This lesson includes:

- 📚 **Vocabulary Review** - Key terms you must know
- 🧠 **Conceptual Questions** - Test your understanding
- 🧩 **Parsons Problems** - Arrange code correctly
- 💻 **Coding Challenges** - Build real generators

By the end, you'll be **PCEP-ready** and confident with generators!

----

Part 1: Vocabulary Review
--------------------------

Match each term with its correct definition:

.. mchoice:: vocab_lazy_evaluation
   :answer_a: Computing all values immediately and storing them in memory
   :answer_b: Computing values one at a time, only when requested
   :answer_c: Computing values in reverse order
   :answer_d: Computing values using multiple threads
   :correct: b
   :feedback_a: That's eager evaluation - the opposite of lazy!
   :feedback_b: Correct! Lazy evaluation waits until values are needed.
   :feedback_c: No, lazy evaluation is about timing, not order.
   :feedback_d: No, that's parallel processing, not lazy evaluation.

   **Lazy Evaluation** means:

.. mchoice:: vocab_yield
   :answer_a: A keyword that returns a value and ends the function
   :answer_b: A keyword that pauses the function and produces a value
   :answer_c: A keyword that deletes a value from memory
   :answer_d: A keyword that creates a list
   :correct: b
   :feedback_a: That's return! yield pauses, not ends.
   :feedback_b: Correct! yield pauses and can resume later.
   :feedback_c: No, yield produces values, it doesn't delete them.
   :feedback_d: No, yield creates generators, not lists.

   **yield** is:

.. mchoice:: vocab_generator_object
   :answer_a: A function that contains yield
   :answer_b: An object returned by a generator function that produces values lazily
   :answer_c: A type of list
   :answer_d: A keyword in Python
   :correct: b
   :feedback_a: That's a generator function, not a generator object!
   :feedback_b: Correct! Generator objects are what generator functions return.
   :feedback_c: No, generators and lists are different types.
   :feedback_d: No, generator objects are created by functions, not keywords.

   **Generator Object** is:

.. mchoice:: vocab_next
   :answer_a: A function that creates generators
   :answer_b: A function that retrieves the next value from a generator
   :answer_c: A function that sorts generators
   :answer_d: A function that converts generators to lists
   :correct: b
   :feedback_a: No, def with yield creates generators!
   :feedback_b: Correct! next() gets the next yielded value.
   :feedback_c: No, generators can't be sorted directly.
   :feedback_d: That's the list() function!

   **next()** is:

.. mchoice:: vocab_stopiteration
   :answer_a: A keyword that stops loops
   :answer_b: An exception raised when a generator is exhausted
   :answer_c: A function that ends generators
   :answer_d: A method that pauses generators
   :correct: b
   :feedback_a: No, break stops loops! StopIteration is an exception.
   :feedback_b: Correct! Raised when no more values to yield.
   :feedback_c: No, it's an exception, not a function.
   :feedback_d: No, yield pauses generators, not StopIteration.

   **StopIteration** is:

.. mchoice:: vocab_generator_expression
   :answer_a: (x for x in range(10))
   :answer_b: [x for x in range(10)]
   :answer_c: {x for x in range(10)}
   :answer_d: x for x in range(10)
   :correct: a
   :feedback_a: Correct! Parentheses () create generator expressions.
   :feedback_b: Square brackets create lists, not generators!
   :feedback_c: Curly braces create sets!
   :feedback_d: Missing parentheses - this is a syntax error.

   Which is the correct syntax for a **generator expression**?

----

Part 2: Conceptual Understanding
---------------------------------

Test your deep understanding of generators:

.. mchoice:: concept_memory
   :answer_a: Lists use more memory because they store all items immediately
   :answer_b: Generators use more memory because they track state
   :answer_c: Lists and generators use the same memory
   :answer_d: It depends on the number of iterations
   :correct: a
   :feedback_a: Correct! Lists store everything; generators produce one at a time.
   :feedback_b: No, state tracking uses minimal memory compared to storing all items.
   :feedback_c: No, memory usage is very different!
   :feedback_d: No, memory usage depends on storage, not iterations.

   Why do generators use less memory than lists?

.. mchoice:: concept_one_time
   :answer_a: Generators are faster
   :answer_b: Generators automatically reset
   :answer_c: Once exhausted, generators don't produce more values
   :answer_d: Generators can only hold one value
   :correct: c
   :feedback_a: Speed isn't why - it's about exhaustion!
   :feedback_b: No, generators don't reset - they stay exhausted.
   :feedback_c: Correct! Once exhausted, you must create a new generator.
   :feedback_d: No, generators can produce many values, just not after exhaustion.

   Why can you only iterate through a generator once?

.. mchoice:: concept_when_create
   :answer_a: When you call the generator function
   :answer_b: When you use next() on the generator
   :answer_c: When you convert it to a list
   :answer_d: When Python starts up
   :correct: a
   :feedback_a: Correct! Calling the function creates the generator object.
   :feedback_b: next() retrieves values, but doesn't create the generator.
   :feedback_c: Conversion happens later - creation is earlier!
   :feedback_d: No, generators are created when functions are called.

   When is a generator object created?

.. mchoice:: concept_when_execute
   :answer_a: When you define the generator function
   :answer_b: When you call the generator function
   :answer_c: When you request values with next() or in a loop
   :answer_d: When Python compiles the code
   :correct: c
   :feedback_a: Defining doesn't execute - it just creates the function.
   :feedback_b: Calling creates the generator object, but doesn't execute the body yet!
   :feedback_c: Correct! Lazy evaluation - code runs only when values are requested.
   :feedback_d: No, compilation and execution are different.

   When does the code inside a generator function actually execute?

.. mchoice:: concept_infinite
   :answer_a: Lists can store infinite values if you have enough memory
   :answer_b: Generators can represent infinite sequences because they're lazy
   :answer_c: Both can represent infinite sequences
   :answer_d: Neither can represent infinite sequences
   :correct: b
   :feedback_a: Infinite memory doesn't exist - lists can't be infinite!
   :feedback_b: Correct! Lazy evaluation allows infinite sequences.
   :feedback_c: No, lists must have finite size.
   :feedback_d: Generators can be infinite!

   Why can generators represent infinite sequences but lists cannot?

.. mchoice:: concept_state
   :answer_a: Global variables
   :answer_b: External files
   :answer_c: Local variables in the generator function
   :answer_d: Environment variables
   :correct: c
   :feedback_a: No, generators preserve their own local state!
   :feedback_b: No, state is in memory, not files.
   :feedback_c: Correct! Each generator preserves its local variables between yields.
   :feedback_d: No, environment variables are system-wide.

   Where does a generator preserve its state between yields?

.. mchoice:: concept_use_case
   :answer_a: Processing a 10 GB log file line by line
   :answer_b: Storing 5 user names to display in a dropdown
   :answer_c: Sorting 100 numbers and displaying them multiple times
   :answer_d: Creating a list of menu items to display
   :correct: a
   :feedback_a: Correct! Large files are perfect for generators.
   :feedback_b: Small data + multiple uses = list is better!
   :feedback_c: Sorting and multiple iterations need a list!
   :feedback_d: Small, static data = list is simpler!

   Which scenario is **best** for using a generator?

.. mchoice:: concept_pipeline
   :answer_a: They make the code run faster
   :answer_b: They use less memory by avoiding intermediate lists
   :answer_c: They automatically parallelize operations
   :answer_d: They sort the data automatically
   :correct: b
   :feedback_a: Speed isn't the main benefit - memory is!
   :feedback_b: Correct! Each stage processes one item at a time without storing intermediate results.
   :feedback_c: No, generators are sequential, not parallel.
   :feedback_d: No, generators don't sort - they just process lazily.

   What's the main advantage of using generators in data pipelines?

.. mchoice:: concept_convert_list
   :answer_a: You need to iterate multiple times
   :answer_b: You need random access (indexing)
   :answer_c: You need to use list methods like sort()
   :answer_d: All of the above
   :correct: d
   :feedback_a: True, but not the only reason!
   :feedback_b: True, but not the only reason!
   :feedback_c: True, but not the only reason!
   :feedback_d: Correct! All these scenarios require converting to a list.

   When should you convert a generator to a list?

----

Part 3: Parsons Problems
-------------------------

Arrange the code blocks in the correct order to solve each problem.

.. parsonsprob:: parsons_basic_generator
   :numbered: left
   :adaptive:

   Arrange the blocks to create a generator function ``count_up(n)`` that yields numbers from 1 to n.

   Example: ``list(count_up(5))`` → ``[1, 2, 3, 4, 5]``
   -----
   def count_up(n):
   =====
       for i in range(1, n + 1):
   =====
       for i in range(1, n): #paired
   =====
           yield i
   =====
           return i #paired

.. parsonsprob:: parsons_filter_generator
   :numbered: left
   :adaptive:

   Arrange the blocks to create a generator function ``even_squares(n)`` that yields the squares of even numbers from 0 to n.

   Example: ``list(even_squares(10))`` → ``[0, 4, 16, 36, 64, 100]``
   -----
   def even_squares(n):
   =====
       for i in range(n + 1):
   =====
           if i % 2 == 0:
   =====
           if i % 2 == 1: #paired
   =====
               yield i ** 2
   =====
               yield i * 2 #paired

.. parsonsprob:: parsons_pipeline
   :numbered: left
   :adaptive:

   Arrange the blocks to create a pipeline that takes numbers, filters even ones, and doubles them.

   Example: ``list(pipeline([1,2,3,4]))`` → ``[4, 8]``
   -----
   def pipeline(numbers):
   =====
       evens = (n for n in numbers if n % 2 == 0)
   =====
       evens = [n for n in numbers if n % 2 == 0] #paired
   =====
       doubled = (n * 2 for n in evens)
   =====
       return doubled
   =====
       return list(doubled) #paired

.. parsonsprob:: parsons_infinite
   :numbered: left
   :adaptive:

   Arrange the blocks to create an infinite generator ``fibonacci()`` that yields Fibonacci numbers.

   (0, 1, 1, 2, 3, 5, 8, 13, ...)
   -----
   def fibonacci():
   =====
       a, b = 0, 1
   =====
       while True:
   =====
       for i in range(1000000): #paired
   =====
           yield a
   =====
           a, b = b, a + b
   =====
           a = b #paired
   =====
           b = a + b #paired

----

Part 4: Coding Challenges
--------------------------

**Challenge 1: Basic Generator Function**

.. activecode:: challenge_countdown
   :language: python
   :autograde: unittest

   Create a generator function ``countdown(n)`` that yields numbers from n down to 1.

   Example: ``list(countdown(5))`` → ``[5, 4, 3, 2, 1]``
   ~~~~
   def countdown(n):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_countdown_5(self):
           result = list(countdown(5))
           self.assertEqual(result, [5, 4, 3, 2, 1], "Testing countdown(5)")

       def test_countdown_3(self):
           result = list(countdown(3))
           self.assertEqual(result, [3, 2, 1], "Testing countdown(3)")

       def test_is_generator(self):
           gen = countdown(5)
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

**Challenge 2: Filtering with Generators**

.. activecode:: challenge_multiples
   :language: python
   :autograde: unittest

   Create a generator function ``multiples_of(n, limit)`` that yields multiples of n up to limit.

   Example: ``list(multiples_of(3, 15))`` → ``[3, 6, 9, 12, 15]``
   ~~~~
   def multiples_of(n, limit):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_multiples_3(self):
           result = list(multiples_of(3, 15))
           self.assertEqual(result, [3, 6, 9, 12, 15], "Testing multiples of 3 up to 15")

       def test_multiples_5(self):
           result = list(multiples_of(5, 25))
           self.assertEqual(result, [5, 10, 15, 20, 25], "Testing multiples of 5 up to 25")

       def test_is_generator(self):
           gen = multiples_of(2, 10)
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

**Challenge 3: Generator Expression**

.. activecode:: challenge_gen_expression
   :language: python
   :autograde: unittest

   Create a function ``squared_evens(numbers)`` that returns a **generator expression** (not a function!) yielding the squares of even numbers.

   Example: ``list(squared_evens([1,2,3,4,5]))`` → ``[4, 16]``

   Hint: Use (n**2 for n in numbers if ...)
   ~~~~
   def squared_evens(numbers):
       # Return a generator expression here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_squared_evens_1(self):
           result = list(squared_evens([1, 2, 3, 4, 5]))
           self.assertEqual(result, [4, 16], "Testing [1,2,3,4,5]")

       def test_squared_evens_2(self):
           result = list(squared_evens([2, 4, 6]))
           self.assertEqual(result, [4, 16, 36], "Testing [2,4,6]")

       def test_is_generator(self):
           gen = squared_evens([1, 2, 3])
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator expression")

   myTests().main()

**Challenge 4: State Preservation**

.. activecode:: challenge_running_average
   :language: python
   :autograde: unittest

   Create a generator function ``running_average(numbers)`` that yields the running average after each number.

   Example: ``list(running_average([10, 20, 30]))`` → ``[10.0, 15.0, 20.0]``

   (10/1=10.0, (10+20)/2=15.0, (10+20+30)/3=20.0)
   ~~~~
   def running_average(numbers):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_running_avg_1(self):
           result = list(running_average([10, 20, 30]))
           self.assertEqual(result, [10.0, 15.0, 20.0], "Testing [10,20,30]")

       def test_running_avg_2(self):
           result = list(running_average([5, 10, 15, 20]))
           self.assertEqual(result, [5.0, 7.5, 10.0, 12.5], "Testing [5,10,15,20]")

       def test_is_generator(self):
           gen = running_average([1, 2])
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

**Challenge 5: Practical Pattern - Batching**

.. activecode:: challenge_batch
   :language: python
   :autograde: unittest

   Create a generator function ``batch(items, batch_size)`` that yields lists of items in batches.

   Example: ``list(batch([1,2,3,4,5], 2))`` → ``[[1,2], [3,4], [5]]``
   ~~~~
   def batch(items, batch_size):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_batch_2(self):
           result = list(batch([1, 2, 3, 4, 5], 2))
           self.assertEqual(result, [[1, 2], [3, 4], [5]], "Testing batch size 2")

       def test_batch_3(self):
           result = list(batch([1, 2, 3, 4, 5, 6, 7], 3))
           self.assertEqual(result, [[1, 2, 3], [4, 5, 6], [7]], "Testing batch size 3")

       def test_is_generator(self):
           gen = batch([1, 2, 3], 2)
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

**Challenge 6: Pipeline Pattern**

.. activecode:: challenge_pipeline_full
   :language: python
   :autograde: unittest

   Create a generator function ``clean_and_filter(words, min_length)`` that:
   1. Strips whitespace from each word
   2. Converts to lowercase
   3. Filters out words shorter than min_length
   4. Yields the cleaned words

   Example: ``list(clean_and_filter([" HELLO ", "  hi  ", "WORLD"], 3))`` → ``["hello", "world"]``
   ~~~~
   def clean_and_filter(words, min_length):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_pipeline_1(self):
           result = list(clean_and_filter([" HELLO ", "  hi  ", "WORLD"], 3))
           self.assertEqual(result, ["hello", "world"], "Testing with min_length 3")

       def test_pipeline_2(self):
           result = list(clean_and_filter([" Apple ", " a ", " Banana "], 4))
           self.assertEqual(result, ["apple", "banana"], "Testing with min_length 4")

       def test_is_generator(self):
           gen = clean_and_filter(["test"], 1)
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

**Challenge 7: Advanced - Infinite Generator**

.. activecode:: challenge_alternating
   :language: python
   :autograde: unittest

   Create a generator function ``alternating()`` that yields alternating 1 and -1 forever.

   (1, -1, 1, -1, 1, -1, ...)

   Hint: Use while True and track state!
   ~~~~
   def alternating():
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_alternating_pattern(self):
           gen = alternating()
           result = [next(gen) for _ in range(6)]
           self.assertEqual(result, [1, -1, 1, -1, 1, -1], "Testing first 6 values")

       def test_is_generator(self):
           gen = alternating()
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

**Challenge 8: Master Challenge - Custom Range**

.. activecode:: challenge_custom_range
   :language: python
   :autograde: unittest

   Create a generator function ``custom_range(start, end, step)`` that mimics Python's range() but works with floats!

   Example: ``list(custom_range(0, 2.5, 0.5))`` → ``[0, 0.5, 1.0, 1.5, 2.0]``

   Hint: Use a while loop and track the current value!
   ~~~~
   def custom_range(start, end, step):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_float_range_1(self):
           result = list(custom_range(0, 2.5, 0.5))
           self.assertEqual(result, [0, 0.5, 1.0, 1.5, 2.0], "Testing 0 to 2.5 by 0.5")

       def test_float_range_2(self):
           result = list(custom_range(1, 3, 0.5))
           self.assertEqual(result, [1, 1.5, 2.0, 2.5], "Testing 1 to 3 by 0.5")

       def test_is_generator(self):
           gen = custom_range(0, 1, 0.1)
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

----

Part 5: Reflection and Self-Assessment
---------------------------------------

Before moving on, test your mastery:

.. checklist:: generators_mastery_checklist
   :label: Generators Mastery Checklist

   Core Concepts
   - I understand lazy vs. eager evaluation
   - I can explain how yield works
   - I know what generator objects are
   - I understand next() and StopIteration
   - I can explain state preservation in generators

   Generator Functions
   - I can write basic generator functions with yield
   - I can create generators with multiple yield statements
   - I understand when the function body executes
   - I can create infinite generators

   Generator Expressions
   - I can write generator expressions using (...)
   - I know the difference between (...) and [...]
   - I can chain generator expressions

   Practical Patterns
   - I can use generators for file processing
   - I can build data pipelines with generators
   - I can create infinite sequences
   - I can implement filtering and mapping patterns

   Decision-Making
   - I know when to use generators vs. lists
   - I understand memory trade-offs
   - I can identify appropriate use cases
   - I know common mistakes to avoid

   PCEP Readiness
   - I can define and invoke generator functions
   - I understand return vs. yield
   - I can convert generator objects to lists
   - I understand lazy evaluation concepts

.. note::

   **✅ If you checked 75% or more**: You're ready for the next chapter!

   **⚠️ If you checked 50-75%**: Review the lessons where you're unsure.

   **❌ If you checked less than 50%**: Go back through the chapter - you'll get it!

----

Quick Reference Guide
---------------------

Keep this reference handy:

**Generator Function Syntax:**

.. code-block:: python

   def my_generator():
       yield value  # Pauses and produces value

**Generator Expression Syntax:**

.. code-block:: python

   gen = (expression for item in iterable if condition)

**Using Generators:**

.. code-block:: python

   # Get next value
   value = next(gen)

   # Loop through all values
   for value in gen:
       print(value)

   # Convert to list
   all_values = list(gen)

**Key Properties:**

- ✅ Memory-efficient (lazy evaluation)
- ✅ Preserve state between yields
- ❌ One-time use (can't reset)
- ❌ No indexing (not subscriptable)
- ❌ No len() (unknown size)

**Decision Framework:**

.. list-table::
   :header-rows: 1
   :widths: 50 25 25

   * - Scenario
     - Generator
     - List
   * - Large dataset
     - ✅
     - ❌
   * - One-time iteration
     - ✅
     - Either
   * - Multiple iterations
     - ❌
     - ✅
   * - Random access needed
     - ❌
     - ✅
   * - Memory-critical
     - ✅
     - ❌
   * - Infinite sequence
     - ✅
     - Impossible

----

Chapter Summary
---------------

🎉 **Congratulations!** You've completed the Generators chapter!

You've mastered:

✅ **Lazy vs. eager evaluation** - When and why to compute on-demand
✅ **Generator functions** - Using yield to create memory-efficient sequences
✅ **Generator expressions** - Compact syntax for simple generators
✅ **Practical patterns** - Pipelines, infinite sequences, file processing
✅ **Decision-making** - When to use generators vs. lists
✅ **PCEP skills** - Everything you need for the certification exam

.. important::

   **Key Takeaways:**

   - Generators use **~1000x less memory** for large datasets
   - `yield` **pauses and resumes** - preserving state
   - Generators are **one-time use** - can't reset
   - Use generators for **large data, pipelines, infinite sequences**
   - Use lists for **small data, multiple iterations, indexing**

----

What's Next?
------------

You're now ready to:

- ✅ Apply generators to real-world problems
- ✅ Build memory-efficient data processing pipelines
- ✅ Tackle PCEP exam questions with confidence
- ✅ Continue to the next chapter in your Python journey!

.. note::

   **✨ Chapter 25 Complete! ✨**

   You've added a powerful tool to your Python toolkit. Generators will make your code more efficient and elegant!

   **Keep practicing and keep coding!** 🚀