..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 5: When to Use Generators
=================================

You've learned what generators are, how to create them, and practical patterns. Now the most important question:

**When should you use a generator instead of a list?**

This lesson gives you a **decision framework** to choose the right tool for the job.

By the end, you'll be able to confidently answer:

- "Should I use a list or a generator here?"
- "What are the trade-offs?"
- "What mistakes should I avoid?"

----

When Generators Shine ✨
------------------------

Use generators when:

**1. Processing Large Datasets**

.. activecode:: when_large_data
   :language: python

   import sys

   # List approach - loads everything into memory
   def all_numbers_list(n):
       return list(range(n))

   # Generator approach - one at a time
   def all_numbers_gen(n):
       for i in range(n):
           yield i

   # Compare memory usage
   big_list = all_numbers_list(100000)
   big_gen = all_numbers_gen(100000)

   print(f"List memory: {sys.getsizeof(big_list):,} bytes")
   print(f"Generator memory: {sys.getsizeof(big_gen):,} bytes")
   print(f"\nMemory savings: {(sys.getsizeof(big_list) / sys.getsizeof(big_gen)):.0f}x smaller!")

.. important::

   **Use generators for large datasets:**

   - Processing log files (millions of lines)
   - Reading database results (thousands of rows)
   - Working with API responses (paginated data)
   - Analyzing large CSV files

**2. You Don't Need All Items at Once**

.. activecode:: when_not_all_items
   :language: python

   def find_first_even(numbers):
       """Find the first even number using a generator"""
       evens = (n for n in numbers if n % 2 == 0)
       return next(evens, None)

   # Process millions of numbers
   huge_range = range(1000000)

   # Generator stops as soon as it finds the first even
   first_even = find_first_even(huge_range)
   print(f"First even number: {first_even}")
   print("Generator stopped early - didn't process all 1,000,000 numbers!")

.. note::

   **Early stopping is a superpower!**

   - Search operations (find first match, stop immediately)
   - Sampling (get first N items, ignore rest)
   - Validation (check condition, exit if false)

**3. Creating Infinite Sequences**

.. activecode:: when_infinite
   :language: python

   def natural_numbers():
       """Infinite sequence of natural numbers"""
       n = 1
       while True:
           yield n
           n += 1

   # Can't create an infinite list, but infinite generator is fine!
   numbers = natural_numbers()

   print("First 10 natural numbers:")
   for i in range(10):
       print(next(numbers), end=" ")

**4. Building Data Pipelines**

.. activecode:: when_pipeline
   :language: python

   def read_data(data):
       """Step 1: Read data"""
       for item in data:
           yield item

   def clean_data(items):
       """Step 2: Clean"""
       for item in items:
           yield item.strip().lower()

   def filter_data(items, keyword):
       """Step 3: Filter"""
       for item in items:
           if keyword in item:
               yield item

   # Build pipeline
   raw = ["  ERROR: Failed  ", "  Info: Started  ", "ERROR: Timeout"]

   pipeline = filter_data(clean_data(read_data(raw)), 'error')

   print("Pipeline results:")
   for result in pipeline:
       print(f"  - {result}")

.. note::

   **Generators are perfect for pipelines:**

   - Each stage processes one item at a time
   - No intermediate lists stored
   - Memory usage stays constant
   - Can chain unlimited transformations

----

When Lists Are Better 📋
-------------------------

Use lists when:

**1. You Need Random Access**

.. activecode:: when_random_access
   :language: python

   # List: access any item instantly
   numbers_list = [1, 2, 3, 4, 5]
   print("List - access middle item:", numbers_list[2])
   print("List - access last item:", numbers_list[-1])
   print("List - access any item:", numbers_list[3])

   print("\n" + "="*40 + "\n")

   # Generator: can only go forward
   numbers_gen = (x for x in range(1, 6))

   # This would cause an error:
   # print(numbers_gen[2])  # TypeError: 'generator' object is not subscriptable

   # Must convert to list first
   gen_as_list = list(numbers_gen)
   print("Generator converted to list:", gen_as_list[2])

**2. You Need to Iterate Multiple Times**

.. activecode:: when_multiple_iterations
   :language: python

   # List: iterate as many times as you want
   numbers_list = [1, 2, 3, 4, 5]

   print("First pass:")
   for n in numbers_list:
       print(n, end=" ")

   print("\n\nSecond pass (same list):")
   for n in numbers_list:
       print(n, end=" ")

   print("\n\n" + "="*40 + "\n")

   # Generator: one-time use only!
   numbers_gen = (x for x in range(1, 6))

   print("First pass:")
   for n in numbers_gen:
       print(n, end=" ")

   print("\n\nSecond pass (generator exhausted!):")
   for n in numbers_gen:
       print(n, end=" ")  # Nothing prints!

   print("[Generator is empty now]")

.. important::

   **Generators are one-time use!**

   Once exhausted, you must create a new generator to iterate again.

**3. You Need List-Specific Operations**

.. activecode:: when_list_operations
   :language: python

   numbers = [5, 2, 8, 1, 9]

   # Lists have useful methods
   numbers.sort()
   print("Sorted:", numbers)

   numbers.reverse()
   print("Reversed:", numbers)

   numbers.append(10)
   print("After append:", numbers)

   print("Length:", len(numbers))
   print("Sum:", sum(numbers))
   print("Min:", min(numbers))
   print("Max:", max(numbers))

.. note::

   **List operations generators DON'T support:**

   - ``len()`` - can't know size without consuming
   - Indexing - ``[0]``, ``[-1]``
   - Slicing - ``[1:3]``
   - ``.append()``, ``.sort()``, ``.reverse()``
   - ``in`` operator (consumes generator!)

**4. The Dataset Is Small**

.. activecode:: when_small_data
   :language: python

   # For small data, lists are simpler and just as good

   days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]

   # List is fine here - only 5 items!
   for day in days:
       print(day)

   # Using a generator would be overkill
   # days_gen = (d for d in ["Monday", "Tuesday", ...])

.. tip::

   **If the data fits comfortably in memory, use a list!**

   - Less than 10,000 items? List is fine.
   - Need to process multiple times? List is better.
   - Simple operations? List is clearer.

----

Decision Framework
------------------

Use this checklist to decide:

.. list-table:: **Generator vs. List Decision Guide**
   :header-rows: 1
   :widths: 40 30 30

   * - Question
     - Generator ✅
     - List ✅
   * - Is the dataset very large?
     - YES
     - NO
   * - Do I need to iterate only once?
     - YES
     - NO
   * - Can I stop early?
     - YES
     - Either
   * - Do I need random access (indexing)?
     - NO
     - YES
   * - Do I need to iterate multiple times?
     - NO
     - YES
   * - Do I need list methods (sort, append)?
     - NO
     - YES
   * - Is memory a concern?
     - YES
     - NO
   * - Is this part of a data pipeline?
     - YES
     - Either
   * - Is the sequence infinite?
     - YES
     - Impossible

.. note::

   **Quick Rule of Thumb:**

   - **Generator**: Large, one-pass, memory-efficient
   - **List**: Small, multi-pass, need indexing/methods

----

Common Mistakes to Avoid ⚠️
---------------------------

**Mistake 1: Trying to Access Generator Items by Index**

.. activecode:: mistake_indexing
   :language: python

   gen = (x * 2 for x in range(5))

   # ❌ This will crash!
   try:
       print(gen[0])
   except TypeError as e:
       print(f"Error: {e}")

   # ✅ Convert to list first
   gen = (x * 2 for x in range(5))
   gen_list = list(gen)
   print(f"\nCorrect way: {gen_list[0]}")

**Mistake 2: Iterating Over a Generator Twice**

.. activecode:: mistake_reuse
   :language: python

   gen = (x * 2 for x in range(1, 6))

   print("First iteration:")
   print(list(gen))

   print("\nSecond iteration (empty!):")
   print(list(gen))

   print("\n✅ Solution: Create a list if you need multiple iterations")
   numbers = list(x * 2 for x in range(1, 6))
   print("First:", numbers)
   print("Second:", numbers)

**Mistake 3: Using Generators When You Need the Full Result**

.. activecode:: mistake_full_result
   :language: python

   # ❌ Bad: Generator when you need all values
   def get_scores():
       for score in [85, 92, 78, 90, 88]:
           yield score

   scores_gen = get_scores()

   # These operations consume the generator!
   avg = sum(scores_gen) / 5  # Generator is now empty
   print(f"Average: {avg}")

   # This will fail - generator is exhausted
   # max_score = max(scores_gen)  # Would crash!

   print("\n✅ Solution: Use a list for multiple operations")
   scores_list = [85, 92, 78, 90, 88]
   avg = sum(scores_list) / len(scores_list)
   max_score = max(scores_list)
   min_score = min(scores_list)

   print(f"Average: {avg}")
   print(f"Max: {max_score}")
   print(f"Min: {min_score}")

**Mistake 4: Converting Generators to Lists Unnecessarily**

.. activecode:: mistake_unnecessary_list
   :language: python

   # ❌ Bad: Converting to list when not needed
   def process_data_bad(numbers):
       evens = [n for n in numbers if n % 2 == 0]  # Creates list
       for n in evens:
           print(n, end=" ")

   print("Unnecessary list creation:")
   process_data_bad(range(1, 11))

   print("\n\n✅ Good: Use generator directly")
   def process_data_good(numbers):
       evens = (n for n in numbers if n % 2 == 0)  # Generator
       for n in evens:
           print(n, end=" ")

   process_data_good(range(1, 11))

----

Performance Comparison
----------------------

Let's see the real difference:

.. activecode:: performance_comparison
   :language: python

   import sys
   import time

   # Test with a large dataset
   SIZE = 100000

   # Memory comparison
   print("="*50)
   print("MEMORY COMPARISON")
   print("="*50)

   list_comp = [x * 2 for x in range(SIZE)]
   gen_exp = (x * 2 for x in range(SIZE))

   print(f"List:      {sys.getsizeof(list_comp):>10,} bytes")
   print(f"Generator: {sys.getsizeof(gen_exp):>10,} bytes")
   print(f"Savings:   {sys.getsizeof(list_comp) / sys.getsizeof(gen_exp):>10.0f}x")

   # Time to create
   print("\n" + "="*50)
   print("CREATION TIME")
   print("="*50)

   start = time.time()
   list_comp = [x * 2 for x in range(SIZE)]
   list_time = time.time() - start

   start = time.time()
   gen_exp = (x * 2 for x in range(SIZE))
   gen_time = time.time() - start

   print(f"List:      {list_time:.6f} seconds")
   print(f"Generator: {gen_time:.6f} seconds")
   print(f"Generator is {list_time / gen_time:.0f}x faster to create!")

   # Time to consume (iterate through)
   print("\n" + "="*50)
   print("ITERATION TIME (full consumption)")
   print("="*50)

   start = time.time()
   for _ in list_comp:
       pass
   list_iter_time = time.time() - start

   gen_exp = (x * 2 for x in range(SIZE))
   start = time.time()
   for _ in gen_exp:
       pass
   gen_iter_time = time.time() - start

   print(f"List:      {list_iter_time:.6f} seconds")
   print(f"Generator: {gen_iter_time:.6f} seconds")
   print("\nNote: Full iteration time is similar!")

.. important::

   **Key Performance Insights:**

   - 🚀 **Generators use ~1000x less memory**
   - ⚡ **Generators create instantly** (lazy!)
   - 🔄 **Full iteration speed is similar**
   - ⏹️ **Generators win if you stop early**

----

Real-World Scenarios
--------------------

**Scenario 1: Processing a Log File**

.. activecode:: scenario_logs
   :language: python

   # Simulate a large log file (1000 lines)
   def generate_logs():
       """Simulate reading a large log file"""
       for i in range(1, 1001):
           if i % 100 == 0:
               yield f"ERROR: Connection failed at line {i}"
           else:
               yield f"INFO: Request processed at line {i}"

   # ✅ Generator: memory-efficient, can stop early
   def find_first_error():
       for line in generate_logs():
           if 'ERROR' in line:
               return line

   print("First error found:")
   print(find_first_error())
   print("\nOnly processed until first error - didn't load all 1000 lines!")

**Scenario 2: User Interface Pagination**

.. activecode:: scenario_pagination
   :language: python

   def paginate(items, page_size):
       """Yield pages of items"""
       page = []
       for item in items:
           page.append(item)
           if len(page) == page_size:
               yield page
               page = []
       if page:
           yield page

   # All user data
   users = [f"User{i}" for i in range(1, 26)]

   # Display 5 users per page
   for page_num, page in enumerate(paginate(users, 5), 1):
       print(f"Page {page_num}: {page}")

**Scenario 3: Data Transformation Pipeline**

.. activecode:: scenario_pipeline
   :language: python

   def read_data():
       """Step 1: Simulate reading data"""
       data = [" 100 ", "200", "  300", "400 ", " 500  "]
       for item in data:
           yield item

   def clean(items):
       """Step 2: Clean whitespace"""
       for item in items:
           yield item.strip()

   def convert_to_int(items):
       """Step 3: Convert to integers"""
       for item in items:
           yield int(item)

   def double(items):
       """Step 4: Double values"""
       for item in items:
           yield item * 2

   # Chain the pipeline
   pipeline = double(convert_to_int(clean(read_data())))

   print("Pipeline results:")
   print(list(pipeline))
   print("\nNo intermediate lists created - memory efficient!")

----

Practice: Make the Right Choice
--------------------------------

.. mchoice:: choice_scenario_1
   :answer_a: List
   :answer_b: Generator
   :correct: b
   :feedback_a: This is a large file - generators save memory!
   :feedback_b: Correct! Generators are perfect for processing large files line by line.

   You need to process a 5 GB log file line by line. Which should you use?

.. mchoice:: choice_scenario_2
   :answer_a: List
   :answer_b: Generator
   :correct: a
   :feedback_a: Correct! Small data + multiple iterations = list is better.
   :feedback_b: For small data that needs multiple iterations, lists are simpler.

   You have 10 student names and need to sort them, then print them twice. Which should you use?

.. mchoice:: choice_scenario_3
   :answer_a: List
   :answer_b: Generator
   :correct: b
   :feedback_a: You only need the first match - generators can stop early!
   :feedback_b: Correct! Generators are perfect for "find first" operations.

   You're searching through 1 million records for the first one that matches a condition. Which should you use?

.. mchoice:: choice_scenario_4
   :answer_a: List
   :answer_b: Generator
   :correct: a
   :feedback_a: Correct! Random access requires indexing, which lists provide.
   :feedback_b: Generators don't support indexing - you'd need to convert to a list anyway.

   You need to randomly access items by index (e.g., ``items[5]``). Which should you use?

.. mchoice:: choice_scenario_5
   :answer_a: List
   :answer_b: Generator
   :correct: b
   :feedback_a: Infinite sequences can't be stored in lists!
   :feedback_b: Correct! Only generators can represent infinite sequences.

   You need to generate an infinite sequence of numbers. Which should you use?

.. mchoice:: choice_scenario_6
   :answer_a: List
   :answer_b: Generator
   :correct: b
   :feedback_a: Data pipelines are memory-intensive with lists!
   :feedback_b: Correct! Generators excel at chaining transformations without intermediate storage.

   You're building a data pipeline with 5 transformation steps. Which should you use?

----

Coding Challenge: Apply Your Knowledge
---------------------------------------

.. activecode:: challenge_decision_1
   :language: python
   :autograde: unittest

   **Challenge:** Create a function ``first_divisible(n, divisor)`` that returns the **first** number from 1 to ``n`` that's divisible by ``divisor``. Use a generator for efficiency!

   Example: ``first_divisible(100, 17)`` → ``17``

   Hint: Stop searching as soon as you find the first match!
   ~~~~
   def first_divisible(n, divisor):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_17(self):
           self.assertEqual(first_divisible(100, 17), 17, "Testing divisor 17")

       def test_23(self):
           self.assertEqual(first_divisible(100, 23), 23, "Testing divisor 23")

       def test_7(self):
           self.assertEqual(first_divisible(50, 7), 7, "Testing divisor 7")

   myTests().main()

.. activecode:: challenge_decision_2
   :language: python
   :autograde: unittest

   **Challenge:** Create a generator function ``running_sum(numbers)`` that yields the running sum of numbers.

   Example: ``list(running_sum([1, 2, 3, 4]))`` → ``[1, 3, 6, 10]``

   (1, 1+2=3, 3+3=6, 6+4=10)
   ~~~~
   def running_sum(numbers):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_running_sum_1(self):
           result = list(running_sum([1, 2, 3, 4]))
           self.assertEqual(result, [1, 3, 6, 10], "Testing [1,2,3,4]")

       def test_running_sum_2(self):
           result = list(running_sum([5, 10, 15]))
           self.assertEqual(result, [5, 15, 30], "Testing [5,10,15]")

       def test_is_generator(self):
           self.assertIsInstance(running_sum([1,2]), types.GeneratorType, "Should return a generator")

   myTests().main()

----

Chapter Summary
---------------

You've now mastered when to use generators! Here's what you know:

**When Generators Shine:**

✅ Large datasets
✅ One-time iteration
✅ Early stopping possible
✅ Infinite sequences
✅ Data pipelines
✅ Memory efficiency critical

**When Lists Are Better:**

✅ Small datasets
✅ Multiple iterations needed
✅ Random access required
✅ List methods needed (sort, append, etc.)
✅ Need to know length upfront

**Key Insights:**

- Generators use ~1000x less memory for large data
- Generators create instantly (lazy evaluation)
- Generators are one-time use
- Convert to list only when necessary

.. important::

   **Decision Framework:**

   Ask yourself:

   1. Is the data large? → **Generator**
   2. Do I iterate once? → **Generator**
   3. Do I need indexing? → **List**
   4. Do I iterate multiple times? → **List**
   5. Can I stop early? → **Generator**

----

What's Next?
------------

In the next lesson, you'll:

- Complete comprehensive practice problems
- Test your understanding with challenges
- Review vocabulary and concepts
- Prepare for PCEP exam questions

.. note::

   **✅ Lesson 5 Complete!**

   You now have a complete understanding of:

   - ✅ When generators are the right choice
   - ✅ When lists are better
   - ✅ Decision frameworks and trade-offs
   - ✅ Common mistakes to avoid
   - ✅ Performance considerations
   - ✅ Real-world scenarios

   **Next:** Practice and Mastery - Put it all together!