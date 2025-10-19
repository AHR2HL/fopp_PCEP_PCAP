..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 1: What Are Generators?
===============================

You're about to learn one of Python's most powerful features for working with data efficiently. By the end of this lesson, you'll understand **what generators are** and **why they matter**.

----

Building on What You Know: List Comprehensions
-----------------------------------------------

You've already learned to create lists with **list comprehensions**:

.. activecode:: gen_intro_list_comp_review
   :language: python

   Remember list comprehensions? They're a concise way to create lists.
   ~~~~
   numbers = [1, 2, 3, 4, 5]
   doubled = [n * 2 for n in numbers]
   print(doubled)  # [2, 4, 6, 8, 10]

   # You can also filter
   evens = [n for n in numbers if n % 2 == 0]
   print(evens)  # [2, 4]

This works beautifully! But there's a hidden cost...

----

The Memory Problem
------------------

Let's see what happens when we work with larger datasets:

.. activecode:: gen_intro_memory_problem
   :language: python

   import sys

   # Create a list of 1 million numbers
   big_list = [n for n in range(1_000_000)]

   # How much memory does it use?
   memory_bytes = sys.getsizeof(big_list)
   memory_mb = memory_bytes / (1024 * 1024)

   print(f"Memory used: {memory_mb:.2f} MB")
   print(f"That's {len(big_list):,} numbers stored in memory!")

**The problem:** List comprehensions create the **entire list in memory** before you can use it.

What if you have:

- A file with 10 million lines?
- Sensor data streaming continuously?
- A calculation that produces billions of values?

**You'd run out of memory!**

.. mchoice:: memory_problem_check
   :answer_a: Create a list with 10 million items
   :answer_b: Store everything in a file first
   :answer_c: Process items one at a time without storing everything
   :answer_d: Buy more RAM
   :correct: c
   :feedback_a: That would use even MORE memory!
   :feedback_b: Files have limits too, and this doesn't solve the memory issue.
   :feedback_c: Exactly! Process each item as you go, then discard it.
   :feedback_d: That's expensive and doesn't scale! Good programmers find efficient solutions.

   What's the best solution for processing huge datasets?

----

The Generator Solution: Lazy Evaluation
----------------------------------------

**Generators** solve this problem with a simple idea: **Don't create all the values at once. Create them one at a time, as needed.**

This is called **lazy evaluation** (or "lazy computation"):

- Values are computed **only when you ask for them**
- Once you use a value, it's discarded (unless you explicitly save it)
- Memory usage stays **constant**, no matter how many values you generate

.. note::

   **Key Concept: Lazy Evaluation**

   **Eager (List):** "Give me ALL the results RIGHT NOW!" (Computes everything immediately)

   **Lazy (Generator):** "Give me the NEXT result when I ask." (Computes one at a time)

----

Metaphor 1: Recipe vs. Ingredients
-----------------------------------

Think of it this way:

**List = Ingredients All Prepared**

- You buy vegetables, chop them all, and store everything in containers
- Takes up lots of fridge space
- But everything is ready to grab when you need it

**Generator = Recipe Instructions**

- The recipe tells you **how** to make each dish
- You prepare each ingredient **as you need it**
- Minimal storage space

.. mchoice:: recipe_metaphor
   :answer_a: A recipe book (instructions to follow)
   :answer_b: A meal prep service (everything pre-made)
   :correct: a
   :feedback_a: Exactly! A generator is like a recipe - it tells you HOW to produce values, not the values themselves.
   :feedback_b: That's more like a list - everything is already prepared and stored!

   Which is more like a generator?

----

Metaphor 2: Streaming vs. Downloading
--------------------------------------

You've probably streamed movies online:

**Downloading = List**

- Download the **entire movie** to your device (might take forever!)
- Takes up **gigabytes** of storage
- Once downloaded, you can watch anytime, skip around, rewatch

**Streaming = Generator**

- Download **just enough** for the next few seconds
- Minimal storage (buffer only)
- Watch as it streams, but can't easily go backward

.. mchoice:: streaming_metaphor
   :answer_a: Streaming - you get data as you need it
   :answer_b: Downloading - you get everything at once
   :correct: a
   :feedback_a: Correct! Generators stream values one at a time, like streaming video.
   :feedback_b: That's how lists work - everything stored at once!

   Which approach is more like a generator?

----

Your First Generator: range()
------------------------------

Good news: **You've already used a generator!** (Sort of.)

In Python 3, ``range()`` returns a **range object**, which behaves like a generator:

.. activecode:: gen_intro_range_example
   :language: python

   import sys

   # range() doesn't create a list - it's lazy!
   r = range(1_000_000)

   print(f"Type: {type(r)}")
   print(f"Memory used: {sys.getsizeof(r)} bytes")

   # Compare to a list of the same numbers
   big_list = list(range(1_000_000))
   print(f"\nList memory: {sys.getsizeof(big_list):,} bytes")

   print(f"\nMemory savings: {sys.getsizeof(big_list) / sys.getsizeof(r):.0f}x less memory!")

**Notice:**

- ``range(1_000_000)`` uses only **48 bytes** (no matter how big the range!)
- A list of a million numbers uses **over 8 MB**
- You can still iterate over ``range()`` with a ``for`` loop

.. activecode:: gen_intro_range_iteration
   :language: python

   # Even though range() is lazy, you can still loop over it
   total = 0
   for num in range(1, 11):
       total += num
   print(f"Sum of 1 to 10: {total}")

   # This works with huge ranges!
   count = 0
   for num in range(1_000_000):
       if num % 100_000 == 0:  # Print every 100,000th number
           print(f"Processing: {num:,}")
       count += 1
   print(f"Processed {count:,} numbers without storing them all!")

.. mchoice:: range_memory
   :answer_a: range() stores all million numbers in memory
   :answer_b: range() generates numbers one at a time as the loop needs them
   :answer_c: range() is slower than lists because it has to compute values
   :correct: b
   :feedback_a: No! That's the beauty of range() - it's lazy!
   :feedback_b: Exactly! range() produces each number only when the loop asks for the next one.
   :feedback_c: Actually, range() is often faster because it doesn't allocate memory for a huge list!

   How does ``range(1_000_000)`` work?

----

Lists vs. Generators: Side-by-Side
-----------------------------------

Let's compare lists and generators directly:

.. activecode:: gen_intro_comparison
   :language: python

   import sys

   # LIST: Eager evaluation - compute everything NOW
   print("=== LIST ===")
   my_list = [n * 2 for n in range(10)]
   print(f"Type: {type(my_list)}")
   print(f"Contents: {my_list}")
   print(f"Memory: {sys.getsizeof(my_list)} bytes")
   print(f"Can access multiple times: {my_list[0]}, {my_list[5]}, {my_list[0]}")

   print("\n=== GENERATOR ===")
   my_gen = (n * 2 for n in range(10))  # Note: parentheses, not brackets!
   print(f"Type: {type(my_gen)}")
   print(f"Contents: {my_gen}")  # Can't see values - they haven't been generated yet!
   print(f"Memory: {sys.getsizeof(my_gen)} bytes")

   # To use a generator, iterate over it
   print("Values:")
   for value in my_gen:
       print(value, end=" ")
   print()

   # Try to iterate again - it's exhausted!
   print("\nTrying to iterate again:")
   for value in my_gen:
       print(value, end=" ")
   print("(Nothing! Generator is exhausted.)")

**Key Differences:**

+----------------------+--------------------------------+----------------------------------+
| Feature              | List                           | Generator                        |
+======================+================================+==================================+
| **Memory**           | Stores all items               | Stores only current item         |
+----------------------+--------------------------------+----------------------------------+
| **Evaluation**       | Eager (compute everything now) | Lazy (compute on demand)         |
+----------------------+--------------------------------+----------------------------------+
| **Access**           | Can access any item anytime    | Can iterate only once            |
+----------------------+--------------------------------+----------------------------------+
| **Speed (first use)**| Slower (must create everything)| Faster (just creates recipe)     |
+----------------------+--------------------------------+----------------------------------+
| **Reusability**      | Can iterate many times         | Exhausted after one iteration    |
+----------------------+--------------------------------+----------------------------------+

.. mchoice:: list_vs_gen_when
   :answer_a: When you need to iterate over items multiple times
   :answer_b: When you're processing huge datasets once
   :answer_c: When you need to access items by index (e.g., my_data[5])
   :correct: b
   :feedback_a: Use a list for that! Generators can only iterate once.
   :feedback_b: Perfect use case! Process once, save memory.
   :feedback_c: Generators don't support indexing - use a list!

   When should you use a **generator** instead of a list?

----

Converting Between Lists and Generators
----------------------------------------

You can always convert between them:

.. activecode:: gen_intro_conversion
   :language: python

   # Generator expression
   my_gen = (n ** 2 for n in range(5))

   # Convert to list when you need multiple access
   my_list = list(my_gen)
   print(f"List: {my_list}")
   print(f"First item: {my_list[0]}")
   print(f"All items again: {my_list}")

   # Create a new generator from a list (using a generator expression)
   new_gen = (x * 2 for x in my_list)
   print(f"\nNew generator: {new_gen}")
   print("Iterating:")
   for val in new_gen:
       print(val, end=" ")

.. important::

   Calling ``list()`` on a generator **exhausts it** and stores everything in memory. Do this only when necessary!

----

Real-World Preview: Why This Matters
-------------------------------------

Generators are used everywhere in real Python code:

1. **Reading large files** line-by-line without loading the whole file
2. **Processing streaming data** (sensor readings, network packets, logs)
3. **Working with infinite sequences** (you'll see examples soon!)
4. **Data pipelines** (chaining transformations efficiently)
5. **Web scraping** (processing pages one at a time)

.. mchoice:: real_world_use_case
   :answer_a: Storing all users' profile data in memory at once
   :answer_b: Reading a 50 GB log file line-by-line to find errors
   :answer_c: Creating a small lookup table for frequently accessed data
   :correct: b
   :feedback_a: That could crash your program! Use a database or generator.
   :feedback_b: Perfect! Read one line at a time, check for errors, move on.
   :feedback_c: A small lookup table should be a list or dict for fast access.

   Which problem is BEST solved with a generator?

----

Check Your Understanding
-------------------------

.. mchoice:: gen_concept_check_1
   :answer_a: A list that's stored in a special compressed format
   :answer_b: A recipe for producing values one at a time, as needed
   :answer_c: A faster type of list
   :answer_d: A function that returns multiple values
   :correct: b
   :feedback_a: No! Generators don't store values at all (except the current one).
   :feedback_b: Perfect analogy! It's instructions, not data.
   :feedback_c: Generators aren't lists at all - they're fundamentally different.
   :feedback_d: Close, but not quite. You'll learn the details in the next lesson!

   What is a generator?

.. mchoice:: gen_concept_check_2
   :answer_a: Eager evaluation - compute everything immediately
   :answer_b: Lazy evaluation - compute values only when needed
   :correct: b
   :feedback_a: That's how lists work!
   :feedback_b: Exactly! Lazy evaluation is the key concept.

   Generators use which evaluation strategy?

.. mchoice:: gen_concept_check_3
   :answer_a: [x * 2 for x in range(100)]
   :answer_b: (x * 2 for x in range(100))
   :correct: a
   :feedback_a: Correct! Square brackets create a LIST (eager, stores everything).
   :feedback_b: Parentheses create a GENERATOR (lazy, computes on demand).

   Which syntax creates a **list** (not a generator)?

.. mchoice:: gen_concept_check_4
   :answer_a: Generators use more memory than lists
   :answer_b: Generators compute values only when needed
   :answer_c: Generators can be iterated over multiple times
   :answer_d: Generators are always faster than lists
   :correct: b
   :feedback_a: Opposite! Generators use LESS memory.
   :feedback_b: Exactly! This is lazy evaluation.
   :feedback_c: No - generators are exhausted after one iteration.
   :feedback_d: Not always! For small datasets or multiple iterations, lists can be better.

   Which statement about generators is TRUE?

----

What's Next?
------------

You now understand **what generators are** and **why they're useful**. But how do you **create** them?

In the next lesson, you'll learn about:

- **Generator functions** (using the ``yield`` keyword)
- How generators "pause" and "resume"
- The difference between ``yield`` and ``return``

.. note::

   **✅ Lesson 1 Complete!**

   **You've learned:**

   - The memory problem with lists
   - What lazy evaluation means
   - How generators save memory
   - The difference between ``[...]`` (list) and ``(...)`` (generator)
   - When to use generators vs. lists

   **Next:** Learn to write generator functions with ``yield``!