..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 4: Practical Patterns with Generators
=============================================

You've learned **what** generators are and **how** to create them. Now let's see **where** they shine in real programming!

This lesson explores practical patterns you'll use in actual projects:

- Infinite sequences
- File processing
- Data filtering and transformation
- Pagination and batching
- Custom range alternatives

By the end, generators won't feel like a mysterious exam topic - they'll be a natural tool in your programming toolkit.

----

Pattern 1: Infinite Sequences
------------------------------

Generators can create **infinite sequences** without using infinite memory!

**Example: Infinite Counter**

.. activecode:: pattern_infinite_counter
   :language: python

   def counter(start=0):
       """Infinite counter starting from start"""
       n = start
       while True:
           yield n
           n += 1

   # Use it with any loop that has a break condition
   count = counter(1)
   for i in count:
       print(i, end=" ")
       if i >= 5:
           break

   print("\n\nCount by 10s:")
   count_tens = counter(0)
   for i in count_tens:
       if i > 50:
           break
       if i % 10 == 0:
           print(i, end=" ")

**Why this works:**

- The ``while True`` loop never ends
- But ``yield`` pauses execution after each value
- Memory usage: just one integer variable ``n``
- Compare to creating a list: impossible (infinite memory!)

.. note::

   **Infinite generators are safe** because they only generate values on demand. The ``for`` loop controls when to stop.

**Example: Cycle Through Values**

.. activecode:: pattern_cycle
   :language: python

   def cycle(items):
       """Cycle through items infinitely"""
       while True:
           for item in items:
               yield item

   # Cycle through colors
   colors = cycle(["red", "green", "blue"])
   for i in range(10):
       print(f"Item {i}: {next(colors)}")

.. mchoice:: infinite_safe
   :answer_a: They crash with a memory error
   :answer_b: They only generate values when requested
   :answer_c: Python limits them automatically
   :answer_d: They can't really be infinite
   :correct: b
   :feedback_a: They're memory-efficient because they're lazy!
   :feedback_b: Exactly! They pause at each yield and only continue when next() is called.
   :feedback_c: No automatic limit - you control when to stop!
   :feedback_d: They can genuinely be infinite, but safe because of lazy evaluation!

   Why don't infinite generators cause memory problems?

----

Pattern 2: Processing Large Files
----------------------------------

Generators are perfect for reading large files **one line at a time**!

**The Problem with Lists:**

.. activecode:: pattern_file_problem
   :language: python

   # DON'T do this with huge files!
   # This loads the ENTIRE file into memory

   # Simulating file content
   large_file_content = "\n".join([f"Line {i}" for i in range(1, 101)])

   # Bad approach: read all lines at once
   lines = large_file_content.split('\n')  # All 100 lines in memory!

   for line in lines:
       if "50" in line:
           print(line)
           break

   print(f"\nMemory used: list with {len(lines)} strings")

**The Generator Solution:**

.. activecode:: pattern_file_solution
   :language: python

   def read_lines(text):
       """Generator that yields lines one at a time"""
       for line in text.split('\n'):
           yield line.strip()

   # Simulating file content
   large_file_content = "\n".join([f"Line {i}" for i in range(1, 101)])

   # Good approach: process line by line
   for line in read_lines(large_file_content):
       if "50" in line:
           print(line)
           break  # Stop early - didn't process all 100 lines!

   print("\nMemory used: only 1 line at a time!")

**Real File Example:**

.. code-block:: python

   # In real code with actual files:
   def read_large_file(filename):
       """Generator for reading large files"""
       with open(filename, 'r') as file:
           for line in file:
               yield line.strip()

   # Process a gigabyte file with minimal memory
   for line in read_large_file('huge_log.txt'):
       if 'ERROR' in line:
           print(line)

.. important::

   **File Processing Benefits:**

   - ✅ Constant memory usage (one line at a time)
   - ✅ Can stop early without reading entire file
   - ✅ Fast startup (no waiting to load everything)
   - ✅ Works with files larger than available RAM

----

Pattern 3: Filtering Data
--------------------------

Generators make it easy to filter data without creating intermediate lists!

.. activecode:: pattern_filtering
   :language: python

   def filter_even(numbers):
       """Yield only even numbers"""
       for n in numbers:
           if n % 2 == 0:
               yield n

   def filter_positive(numbers):
       """Yield only positive numbers"""
       for n in numbers:
           if n > 0:
               yield n

   data = range(-10, 11)  # -10 to 10

   # Chain filters together!
   positive_evens = filter_positive(filter_even(data))

   print("Positive even numbers:")
   for num in positive_evens:
       print(num, end=" ")

**With Generator Expressions:**

.. activecode:: pattern_filtering_expressions
   :language: python

   data = range(-10, 11)

   # Same thing with generator expressions
   evens = (n for n in data if n % 2 == 0)
   positive_evens = (n for n in evens if n > 0)

   print("Positive even numbers:")
   print(list(positive_evens))

.. note::

   **Chaining generators** creates a **pipeline** where data flows through multiple transformations - without creating intermediate lists!

----

Pattern 4: Transforming Data
-----------------------------

Generators can transform data on-the-fly:

.. activecode:: pattern_transforming
   :language: python

   def square(numbers):
       """Yield the square of each number"""
       for n in numbers:
           yield n ** 2

   def add_ten(numbers):
       """Add 10 to each number"""
       for n in numbers:
           yield n + 10

   # Build a pipeline
   data = range(1, 6)
   transformed = add_ten(square(data))

   print("Pipeline: square, then add 10")
   for value in transformed:
       print(value, end=" ")

   print("\n\nVerify manually:")
   print("1² + 10 =", 1**2 + 10)
   print("2² + 10 =", 2**2 + 10)
   print("3² + 10 =", 3**2 + 10)

**Real-World Example: Data Cleaning**

.. activecode:: pattern_data_cleaning
   :language: python

   def clean_data(lines):
       """Clean and normalize text data"""
       for line in lines:
           # Strip whitespace
           cleaned = line.strip()
           # Skip empty lines
           if cleaned:
               # Convert to lowercase
               yield cleaned.lower()

   def filter_keywords(lines, keyword):
       """Keep only lines containing keyword"""
       for line in lines:
           if keyword in line:
               yield line

   # Simulate messy data
   data = [
       "  ERROR: Connection failed  ",
       "",
       "  Info: System started  ",
       "ERROR: Disk full",
       "   ",
       "Warning: High memory usage"
   ]

   # Pipeline: clean, then filter for errors
   pipeline = filter_keywords(clean_data(data), 'error')

   print("Error messages:")
   for msg in pipeline:
       print(f"  - {msg}")

.. mchoice:: pipeline_benefit
   :answer_a: It's faster than using lists
   :answer_b: It uses less memory and can stop early
   :answer_c: It's easier to write
   :answer_d: It automatically parallelizes processing
   :correct: b
   :feedback_a: Speed is similar, but memory is much better!
   :feedback_b: Exactly! No intermediate lists, and you can break early.
   :feedback_c: The syntax is similar either way.
   :feedback_d: Generators don't automatically parallelize.

   What's the main benefit of chaining generators together?

----

Pattern 5: Pagination (Batching)
---------------------------------

Generators can split data into chunks (batches):

.. activecode:: pattern_batching
   :language: python

   def batch(items, size):
       """Yield items in batches of given size"""
       batch_list = []
       for item in items:
           batch_list.append(item)
           if len(batch_list) == size:
               yield batch_list
               batch_list = []

       # Don't forget remaining items!
       if batch_list:
           yield batch_list

   # Process data in groups of 3
   data = range(1, 11)

   for i, group in enumerate(batch(data, 3), 1):
       print(f"Batch {i}: {group}")

**Real-World Use: API Requests**

.. activecode:: pattern_api_batching
   :language: python

   def batch_api_calls(user_ids, batch_size=5):
       """Simulate batching API calls"""
       batch_list = []
       for user_id in user_ids:
           batch_list.append(user_id)
           if len(batch_list) == batch_size:
               # In real code, this would be an API call
               yield f"Fetched users: {batch_list}"
               batch_list = []

       if batch_list:
           yield f"Fetched users: {batch_list}"

   # Process 13 users in batches of 5
   user_ids = range(1001, 1014)

   for result in batch_api_calls(user_ids, batch_size=5):
       print(result)

.. important::

   **Batching is crucial when:**

   - Working with APIs that have rate limits
   - Processing data in chunks (e.g., 100 records at a time)
   - Displaying paginated results
   - Sending bulk emails or messages

----

Pattern 6: Custom Range Alternatives
-------------------------------------

You can create range-like generators with custom behavior:

.. activecode:: pattern_custom_ranges
   :language: python

   def frange(start, stop, step):
       """Like range(), but works with floats"""
       current = start
       while current < stop:
           yield current
           current += step

   print("Float range from 0 to 2 by 0.5:")
   for val in frange(0, 2, 0.5):
       print(val, end=" ")

   print("\n\nFloat range from 1 to 3 by 0.25:")
   for val in frange(1, 3, 0.25):
       print(round(val, 2), end=" ")

**Geometric Sequences:**

.. activecode:: pattern_geometric
   :language: python

   def geometric(start, ratio, count):
       """Generate geometric sequence: start, start*ratio, start*ratio², ..."""
       value = start
       for _ in range(count):
           yield value
           value *= ratio

   # Powers of 2
   print("Powers of 2:")
   for val in geometric(1, 2, 10):
       print(val, end=" ")

   print("\n\nPowers of 3:")
   for val in geometric(1, 3, 7):
       print(val, end=" ")

----

Pattern 7: Fibonacci and Number Sequences
------------------------------------------

Generators are perfect for mathematical sequences:

.. activecode:: pattern_fibonacci
   :language: python

   def fibonacci(n):
       """Generate first n Fibonacci numbers"""
       a, b = 0, 1
       for _ in range(n):
           yield a
           a, b = b, a + b

   print("First 10 Fibonacci numbers:")
   for num in fibonacci(10):
       print(num, end=" ")

   print("\n\nFirst 15 Fibonacci numbers:")
   print(list(fibonacci(15)))

**Infinite Fibonacci:**

.. activecode:: pattern_fibonacci_infinite
   :language: python

   def fibonacci_infinite():
       """Infinite Fibonacci sequence"""
       a, b = 0, 1
       while True:
           yield a
           a, b = b, a + b

   # Get Fibonacci numbers until they exceed 100
   print("Fibonacci numbers under 100:")
   for num in fibonacci_infinite():
       if num > 100:
           break
       print(num, end=" ")

----

Pattern 8: Simulations and Randomness
--------------------------------------

Generators work well for simulations:

.. activecode:: pattern_simulations
   :language: python

   import random

   def dice_rolls(n):
       """Simulate n dice rolls"""
       for _ in range(n):
           yield random.randint(1, 6)

   def coin_flips(n):
       """Simulate n coin flips"""
       for _ in range(n):
           yield random.choice(['Heads', 'Tails'])

   print("10 dice rolls:")
   for roll in dice_rolls(10):
       print(roll, end=" ")

   print("\n\n10 coin flips:")
   for flip in coin_flips(10):
       print(flip, end=" ")

**Simulation with Early Stopping:**

.. activecode:: pattern_simulation_stop
   :language: python

   import random

   def roll_until_six():
       """Roll dice until we get a 6"""
       rolls = 0
       while True:
           rolls += 1
           result = random.randint(1, 6)
           yield rolls, result
           if result == 6:
               break

   print("Rolling until we get a 6...")
   for attempt, result in roll_until_six():
       print(f"Roll {attempt}: {result}")

----

Practice: Build Practical Generators
-------------------------------------

.. activecode:: practice_pattern_1
   :language: python
   :autograde: unittest

   **1.** Create a generator ``countdown(n)`` that counts down from ``n`` to 1.

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
           self.assertIsInstance(countdown(5), types.GeneratorType, "Should return a generator")

   myTests().main()

.. activecode:: practice_pattern_2
   :language: python
   :autograde: unittest

   **2.** Create a generator ``repeat(value, times)`` that yields ``value`` exactly ``times`` times.

   Example: ``list(repeat('hi', 3))`` → ``['hi', 'hi', 'hi']``
   ~~~~
   def repeat(value, times):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_repeat_hi_3(self):
           result = list(repeat('hi', 3))
           self.assertEqual(result, ['hi', 'hi', 'hi'], "Testing repeat('hi', 3)")

       def test_repeat_num_5(self):
           result = list(repeat(7, 5))
           self.assertEqual(result, [7, 7, 7, 7, 7], "Testing repeat(7, 5)")

       def test_is_generator(self):
           self.assertIsInstance(repeat('hi', 3), types.GeneratorType, "Should return a generator")

   myTests().main()

.. activecode:: practice_pattern_3
   :language: python
   :autograde: unittest

   **3.** Create a generator ``first_n_evens(n)`` that yields the first ``n`` even numbers (starting from 0).

   Example: ``list(first_n_evens(5))`` → ``[0, 2, 4, 6, 8]``
   ~~~~
   def first_n_evens(n):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_first_5_evens(self):
           result = list(first_n_evens(5))
           self.assertEqual(result, [0, 2, 4, 6, 8], "Testing first_n_evens(5)")

       def test_first_3_evens(self):
           result = list(first_n_evens(3))
           self.assertEqual(result, [0, 2, 4], "Testing first_n_evens(3)")

       def test_is_generator(self):
           self.assertIsInstance(first_n_evens(5), types.GeneratorType, "Should return a generator")

   myTests().main()

.. activecode:: practice_pattern_4
   :language: python
   :autograde: unittest

   **4.** Create a generator ``powers_of_two(n)`` that yields the first ``n`` powers of 2 (starting from 2⁰ = 1).

   Example: ``list(powers_of_two(5))`` → ``[1, 2, 4, 8, 16]``
   ~~~~
   def powers_of_two(n):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_powers_5(self):
           result = list(powers_of_two(5))
           self.assertEqual(result, [1, 2, 4, 8, 16], "Testing powers_of_two(5)")

       def test_powers_7(self):
           result = list(powers_of_two(7))
           self.assertEqual(result, [1, 2, 4, 8, 16, 32, 64], "Testing powers_of_two(7)")

       def test_is_generator(self):
           self.assertIsInstance(powers_of_two(5), types.GeneratorType, "Should return a generator")

   myTests().main()

.. activecode:: practice_pattern_5
   :language: python
   :autograde: unittest

   **5.** Create a generator ``filter_long_words(words, min_length)`` that yields only words with length ≥ ``min_length``.

   Example: ``list(filter_long_words(['hi', 'hello', 'yo', 'python'], 4))`` → ``['hello', 'python']``
   ~~~~
   def filter_long_words(words, min_length):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import types

   class myTests(TestCaseGui):
       def test_filter_4(self):
           result = list(filter_long_words(['hi', 'hello', 'yo', 'python'], 4))
           self.assertEqual(result, ['hello', 'python'], "Testing min_length=4")

       def test_filter_3(self):
           result = list(filter_long_words(['a', 'cat', 'dog', 'bird'], 3))
           self.assertEqual(result, ['cat', 'dog', 'bird'], "Testing min_length=3")

       def test_is_generator(self):
           gen = filter_long_words(['hi', 'hello'], 4)
           self.assertIsInstance(gen, types.GeneratorType, "Should return a generator")

   myTests().main()

----

Check Your Understanding
-------------------------

.. mchoice:: practical_pattern_1
   :answer_a: They crash when they reach infinite size
   :answer_b: They only generate values when requested (lazy evaluation)
   :answer_c: Python automatically limits them
   :answer_d: They're not really infinite
   :correct: b
   :feedback_a: They never crash because they don't store all values!
   :feedback_b: Exactly! Each value is generated on demand.
   :feedback_c: No automatic limit - you control when to stop.
   :feedback_d: They can genuinely be infinite!

   How do infinite generators avoid memory problems?

.. mchoice:: practical_pattern_2
   :answer_a: It loads the entire file into memory
   :answer_b: It processes one line at a time
   :answer_c: It only works with small files
   :answer_d: It's slower than reading all lines at once
   :correct: b
   :feedback_a: That's the problem generators solve!
   :feedback_b: Correct! Constant memory usage, regardless of file size.
   :feedback_c: Generators work great with huge files!
   :feedback_d: Speed is similar, but memory usage is much better!

   What's the advantage of using a generator to process a large file?

.. mchoice:: practical_pattern_3
   :answer_a: To make code run faster
   :answer_b: To avoid creating intermediate lists
   :answer_c: To parallelize processing
   :answer_d: To make code shorter
   :correct: b
   :feedback_a: Speed is similar, but memory is better!
   :feedback_b: Exactly! Data flows through without storing intermediate results.
   :feedback_c: Generators don't automatically parallelize.
   :feedback_d: Code length is similar either way.

   Why chain generators together in a pipeline?

.. mchoice:: practical_pattern_4
   :answer_a: Sequences, file processing, and data filtering
   :answer_b: Only mathematical sequences
   :answer_c: Only file reading
   :answer_d: Only random number generation
   :correct: a
   :feedback_a: Correct! Generators have many practical applications.
   :feedback_b: They're useful for much more than math!
   :feedback_c: File processing is just one use case.
   :feedback_d: Random numbers are one example, but there are many more!

   Which are common practical uses for generators?

----

What's Next?
------------

You've now seen generators in action with real-world patterns!

In the next lesson, you'll learn:

- **When to use generators** vs. lists
- Decision frameworks
- Performance considerations
- Best practices

.. note::

   **✅ Lesson 4 Complete!**

   **You've learned practical patterns:**

   - ✅ Infinite sequences (counters, cycles)
   - ✅ File processing (memory-efficient reading)
   - ✅ Data filtering and transformation
   - ✅ Batching and pagination
   - ✅ Custom ranges and sequences
   - ✅ Fibonacci and number sequences
   - ✅ Simulations and randomness

   **Next:** Learn when to choose generators over lists, and master best practices!