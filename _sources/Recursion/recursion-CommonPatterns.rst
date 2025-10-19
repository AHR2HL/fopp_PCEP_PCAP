..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Common Recursive Patterns
==========================

You've learned how recursion works. Now let's see the **patterns** that appear again and again. Once you recognize these patterns, you'll be able to solve problems that seem impossible at first glance!

.. note::

   **Think of these patterns like chess openings**: once you know them, you can play them instinctively. Recursion isn't about memorizing solutions—it's about recognizing which pattern fits the problem.

Pattern 1: Linear Recursion (Process Each Element)
---------------------------------------------------

**The Pattern**: Process one element, then recursively process the rest.

**Template**:

.. code-block:: python

   def process(data):
       if len(data) == 0:           # Base case: nothing left
           return base_value
       else:
           # Do something with data[0]
           # Then process data[1:]
           return combine(data[0], process(data[1:]))

**You've Already Seen This!**

- ``factorial(n)``: process n, then factorial(n-1)
- ``sum_list(lst)``: process first item, then sum_list(rest)
- ``reverse_string(s)``: process last character, then reverse the rest

**New Example**: Count how many times a character appears in a string.

.. activecode:: pattern_count_char
   :caption: Count Character Occurrences

   def count_char(s, target):
       """Count how many times target appears in string s."""
       if len(s) == 0:
           return 0  # Base case: empty string has 0 occurrences
       else:
           # Check if first character matches
           found = 1 if s[0] == target else 0
           # Add to count from the rest of the string
           return found + count_char(s[1:], target)
   
   print(count_char("hello", "l"))  # Should print 2
   print(count_char("mississippi", "s"))  # Should print 4
   print(count_char("python", "z"))  # Should print 0

.. mchoice:: pattern_linear_identify
   :answer_a: It processes every element exactly once
   :answer_b: It makes two recursive calls per function call
   :answer_c: It requires a sorted list
   :answer_d: It only works with numbers
   :correct: a
   :feedback_a: Exactly! Linear recursion walks through the data one element at a time.
   :feedback_b: That's tree recursion (Pattern 4)! Linear recursion makes ONE recursive call per frame.
   :feedback_c: No sorting required! Linear recursion works on any sequence.
   :feedback_d: It works with strings, lists, any sequence—not just numbers!

   What's the key feature of linear recursion?

Pattern 2: Multiple Base Cases
-------------------------------

**The Pattern**: Sometimes you need more than one stopping condition!

**Example**: The Fibonacci sequence (0, 1, 1, 2, 3, 5, 8, 13, ...)

- fib(0) = 0
- fib(1) = 1  
- fib(n) = fib(n-1) + fib(n-2) for n ≥ 2

Notice we need **TWO base cases** (for 0 and 1) before we can compute other values!

.. activecode:: pattern_fibonacci
   :caption: Fibonacci with Multiple Base Cases

   def fibonacci(n):
       """Compute the nth Fibonacci number."""
       if n == 0:
           return 0  # Base case 1
       elif n == 1:
           return 1  # Base case 2
       else:
           # Recursive case: sum of previous two
           return fibonacci(n - 1) + fibonacci(n - 2)
   
   # Print the first 10 Fibonacci numbers
   for i in range(10):
       print(f"fib({i}) = {fibonacci(i)}")

.. warning::

   **Fibonacci is beautiful but SLOW!** It makes TWO recursive calls per function, creating an explosion of duplicate work. For ``fibonacci(5)``, it recalculates ``fibonacci(3)`` multiple times! 
   
   (Advanced technique: use "memoization" to cache results—you'll learn this in later courses!)

.. mchoice:: pattern_multiple_base_why
   :answer_a: To make the code longer
   :answer_b: Because we need to define the first two values to compute the rest
   :answer_c: To confuse other programmers
   :answer_d: It's optional; one base case is always enough
   :correct: b
   :feedback_a: No! Multiple base cases serve a real purpose.
   :feedback_b: Correct! Fibonacci needs TWO starting values to define the sequence.
   :feedback_c: Nope! It's mathematically necessary, not for confusion.
   :feedback_d: Not true! Some problems genuinely need multiple base cases.

   Why does Fibonacci need two base cases?

Pattern 3: Tree Recursion (Multiple Recursive Calls)
-----------------------------------------------------

**The Pattern**: Make **more than one** recursive call per function.

Think of it like a tree branching out:

.. code-block:: text

                        fibonacci(4)
                       /            \
               fibonacci(3)          fibonacci(2)
               /          \          /          \
       fibonacci(2)   fibonacci(1)  fibonacci(1)  fibonacci(0)
         /      \
    fibonacci(1) fibonacci(0)

Each call branches into TWO more calls (except base cases). This creates **exponential** growth in the number of function calls!

**When to Use Tree Recursion**:

- Problems that naturally split into multiple sub-problems
- Exploring multiple paths or choices
- Tree or graph traversal

.. activecode:: pattern_tree_count_ways
   :caption: Tree Recursion: Count Ways to Climb Stairs

   def count_ways(n):
       """
       Count the number of ways to climb n stairs if you can take
       either 1 step or 2 steps at a time.
       
       Example: For 3 stairs, there are 3 ways:
       - 1 + 1 + 1
       - 1 + 2
       - 2 + 1
       """
       if n == 0:
           return 1  # One way to climb 0 stairs: don't move
       elif n < 0:
           return 0  # Can't climb negative stairs
       else:
           # Either take 1 step, then climb (n-1) stairs
           # OR take 2 steps, then climb (n-2) stairs
           return count_ways(n - 1) + count_ways(n - 2)
   
   for stairs in range(1, 6):
       print(f"{stairs} stairs: {count_ways(stairs)} ways")

Notice how similar this is to Fibonacci? **Pattern recognition in action!**

.. dragndrop:: pattern_tree_characteristics
   :match_1: Linear recursion|||Makes ONE recursive call per function
   :match_2: Tree recursion|||Makes MULTIPLE recursive calls per function
   :match_3: fibonacci(n)|||Example of tree recursion
   :match_4: factorial(n)|||Example of linear recursion

   Match each type of recursion to its characteristic:

Pattern 4: Helper Function with Accumulator
--------------------------------------------

**The Pattern**: Use an extra parameter to build up the result as you go.

Sometimes it's easier to pass along a "work-in-progress" value rather than combining results on the way back up.

**Example**: Reverse a string using a helper function.

.. activecode:: pattern_accumulator
   :caption: Accumulator Pattern

   def helper(s, accumulator):
       if len(s) == 0:
           return accumulator  # Base case: return what we've built
       else:
           # Add first character to accumulator, process rest
           return helper(s[1:], s[0] + accumulator)

   def reverse_string(s):
       """Reverse a string using an accumulator helper."""
       
       return helper(s, "")  # Start with empty accumulator
   
   print(reverse_string("hello"))
   print(reverse_string("recursion"))

**Why use a helper?**

- Keeps the main function's interface clean (user just calls ``reverse_string(s)``)
- Allows you to track "state" (like the accumulator) without exposing it to users

.. mchoice:: pattern_accumulator_purpose
   :answer_a: To make the function slower
   :answer_b: To build up the result as we recurse, rather than combining on the way back
   :answer_c: To add unnecessary complexity
   :answer_d: To avoid using base cases
   :correct: b
   :feedback_a: No! It often makes the logic clearer and more efficient.
   :feedback_b: Exactly! The accumulator collects the result as we go deeper into recursion.
   :feedback_c: It actually simplifies the logic for certain problems!
   :feedback_d: You still need base cases! The accumulator is just a different approach to combining results.

   What's the purpose of an accumulator in recursive functions?

Pattern 5: Backtracking and Exploration (The Mind-Blower! 🤯)
--------------------------------------------------------------

**The Pattern**: Recursively explore multiple possibilities, "marking" what you've visited to avoid infinite loops.

This is how you solve mazes, Sudoku, the N-Queens problem, and... **counting islands**!

**The Problem**: Given a 2D grid where ``1`` represents land and ``0`` represents water, count how many separate islands exist. Land cells are connected horizontally or vertically (not diagonally).

**Example Grid**:

.. code-block:: text

   [
     [1, 1, 0, 0, 0],
     [1, 1, 0, 0, 1],
     [0, 0, 0, 1, 1],
     [0, 0, 0, 0, 0],
     [1, 0, 1, 1, 0]
   ]

**How many islands?** Look closely... there are **5 islands**!

.. raw:: html

   <div style="border: 3px solid #FF5722; padding: 20px; margin: 20px 0; background: linear-gradient(135deg, #FFF3E0 0%, #FFE0B2 100%);">
   <h3 style="margin-top: 0; color: #D84315;">🏝️ The Strategy: Recursive Flood Fill</h3>
   <ol style="font-size: 1.1em; line-height: 1.8;">
       <li><strong>Loop through every cell</strong> in the grid</li>
       <li>When you find a <code>1</code> (land), you've discovered a new island! 🎉</li>
       <li><strong>Use recursion to "sink" the entire island</strong>:
           <ul>
               <li>Mark the current cell as visited (change it to <code>0</code>)</li>
               <li>Recursively explore all 4 neighbors (up, down, left, right)</li>
               <li>Each neighbor does the same—until the entire island is sunk!</li>
           </ul>
       </li>
       <li>Continue searching for more islands</li>
   </ol>
   <p style="font-weight: bold; color: #D84315;">Why recursion? Because islands can be ANY SHAPE! Recursion automatically explores all connected land, no matter how complex.</p>
   </div>

The Code: Count Islands
^^^^^^^^^^^^^^^^^^^^^^^^

.. activecode:: pattern_count_islands
   :caption: Mind-Blowing Recursive Island Counter

   def count_islands(grid):
       """Count the number of islands in a 2D grid."""
       if not grid:
           return 0
       
       rows = len(grid)
       cols = len(grid[0])
       island_count = 0

       def sink_island(r, c):
           """Recursively sink (mark as visited) all connected land."""
           # Base cases: out of bounds or water/already visited
           if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] == 0:
               return

           # Mark this land as visited by sinking it (change to 0)
           grid[r][c] = 0

           # Recursively sink all 4 connected neighbors
           sink_island(r - 1, c)  # Up
           sink_island(r + 1, c)  # Down
           sink_island(r, c - 1)  # Left
           sink_island(r, c + 1)  # Right
       
       # Check every cell in the grid
       for r in range(rows):
           for c in range(cols):
               if grid[r][c] == 1:
                   # Found new island! Sink it completely
                   island_count += 1
                   sink_island(r, c)
       
       return island_count
   
   # Test with the example
   test_grid = [
       [1, 1, 0, 0, 0],
       [1, 1, 0, 0, 1],
       [0, 0, 0, 1, 1],
       [0, 0, 0, 0, 0],
       [1, 0, 1, 1, 0]
   ]
   
   print(f"Number of islands: {count_islands(test_grid)}")
   
   # Try another example
   test_grid_2 = [
       [1, 0, 1],
       [0, 1, 0],
       [1, 0, 1]
   ]
   
   print(f"Number of islands: {count_islands(test_grid_2)}")

**What Just Happened?!** 🤯

- The ``sink_island`` helper function uses **4 recursive calls** (up, down, left, right)
- Each call checks base cases (boundaries and water)
- The recursion automatically follows ALL connected land cells
- By "sinking" visited cells, we avoid infinite recursion
- The outer loop finds each NEW island

**Try to solve this iteratively** (with loops)—you'll quickly realize recursion makes the "impossible" trivial!

.. note::

   **This pattern is called "Depth-First Search (DFS)"** in computer science. It's used for:
   
   - Maze solving
   - Pathfinding
   - Graph traversal
   - Game AI (exploring moves in chess, etc.)
   - Image processing (flood fill in Paint programs!)

Understanding Backtracking
^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Backtracking** means:

1. **Try a path** (explore in one direction)
2. **Mark where you've been** (change ``1`` to ``0``)
3. **If it's a dead end, go back** (the recursion returns)
4. **Try the next path** (the next recursive call)

The call stack handles the "going back" automatically!

.. mchoice:: pattern_island_base_cases
   :answer_a: Only when r < 0 or r >= rows
   :answer_b: When we find a 1 (land)
   :answer_c: When out of bounds OR when the cell is 0 (water/visited)
   :answer_d: Only when c < 0 or c >= cols
   :correct: c
   :feedback_a: That's only part of the base cases—you also need to check for water!
   :feedback_b: That's actually when we continue recursing! A 1 means we should keep exploring.
   :feedback_c: Correct! We stop recursion when we go out of bounds OR hit water/visited cells.
   :feedback_d: That's only part of the base cases—you also need to check rows and water!

   In the ``sink_island`` function, when do we stop recursing (base cases)?

.. mchoice:: pattern_island_why_mark
   :answer_a: To make the code run faster
   :answer_b: To prevent infinite recursion by avoiding revisiting the same cell
   :answer_c: To color the grid
   :answer_d: It's not necessary; we could skip this step
   :correct: b
   :feedback_a: While it helps with efficiency, that's not the main reason!
   :feedback_b: Exactly! Without marking visited cells, we'd recurse infinitely between neighbors.
   :feedback_c: We're not visualizing—we're preventing infinite loops!
   :feedback_d: Very wrong! Without marking, the recursion would never stop between two adjacent land cells.

   Why do we change visited land cells (``1``) to water (``0``)?

Practice: Apply the Patterns
-----------------------------

Now that you've seen the patterns, try recognizing and applying them!

.. activecode:: pattern_practice_power
   :caption: Challenge: Compute Power (x^n) Recursively

   Write a recursive function to compute x raised to the power n.
   Think about which pattern fits best!
   
   Hints:
   - Base case: x^0 = 1
   - Recursive case: x^n = x * x^(n-1)
   - This is linear recursion (process one element at a time)
   ~~~~
   def power(x, n):
       # Your code here
       pass
   
   ====
   from unittest.gui import TestCaseGui
   import sys
   
   class myTests(TestCaseGui):
       def testRecursive(self):
           import inspect
           try:
               code = inspect.getsource(power)
           except:
               import __main__
               code = str(__main__.__dict__.get('power', ''))
           
           recursion_count = code.count('power(')
           self.assertGreaterEqual(recursion_count, 2, 
               f"Your solution must use recursion. Found {recursion_count-1} recursive calls, need at least 1.")
           
           self.assertNotIn('**', code, 
               "Don't use the ** operator—compute power recursively!")
           self.assertNotIn('pow(', code.lower(), 
               "Don't use the pow() function—implement recursion yourself!")
       
       def testFunctionality(self):
           self.assertEqual(power(2, 3), 8, "2^3 = 8")
           self.assertEqual(power(5, 0), 1, "5^0 = 1")
           self.assertEqual(power(3, 4), 81, "3^4 = 81")
           self.assertEqual(power(10, 2), 100, "10^2 = 100")
           self.assertEqual(power(2, 10), 1024, "2^10 = 1024")
   
   myTests().main()

.. activecode:: pattern_practice_flatten
   :caption: Challenge: Flatten a Nested List

   Write a recursive function to flatten a list that may contain nested lists.
   Example: [1, [2, 3], [4, [5, 6]]] → [1, 2, 3, 4, 5, 6]
   
   Hints:
   - Base case: empty list returns empty list
   - If first element is a list, flatten it and combine with flattened rest
   - If first element is NOT a list, add it to flattened rest
   - Use isinstance(item, list) to check if something is a list
   ~~~~
   def flatten(lst):
       # Your code here
       pass
   
   ====
   from unittest.gui import TestCaseGui
   
   class myTests(TestCaseGui):
       def testOne(self):
           self.assertEqual(flatten([1, [2, 3], 4]), [1, 2, 3, 4], "Should flatten one level of nesting")
           self.assertEqual(flatten([1, [2, [3, 4]]]), [1, 2, 3, 4], "Should flatten multiple levels")
           self.assertEqual(flatten([]), [], "Empty list should return empty list")
           self.assertEqual(flatten([1, 2, 3]), [1, 2, 3], "Already flat list should stay flat")
           self.assertEqual(flatten([[[[1]]]]), [1], "Should handle deep nesting")
   
   myTests().main()

Pattern Recognition Quiz
-------------------------

.. mchoice:: pattern_recognize_factorial
   :answer_a: Linear recursion
   :answer_b: Tree recursion
   :answer_c: Backtracking
   :answer_d: Multiple base cases
   :correct: a
   :feedback_a: Correct! Factorial makes ONE recursive call per function—it's linear recursion.
   :feedback_b: No, tree recursion makes multiple recursive calls (like Fibonacci).
   :feedback_c: Backtracking is for exploring multiple paths—factorial follows one path.
   :feedback_d: Factorial has one base case (n == 0). Multiple base cases is a different pattern.

   What pattern does ``factorial(n)`` use?

.. mchoice:: pattern_recognize_fibonacci
   :answer_a: Linear recursion
   :answer_b: Tree recursion with multiple base cases
   :answer_c: Backtracking
   :answer_d: Accumulator pattern
   :correct: b
   :feedback_a: No! Fibonacci makes TWO recursive calls—that's tree recursion.
   :feedback_b: Exactly! It makes two recursive calls AND has two base cases (n=0 and n=1).
   :feedback_c: Backtracking is for exploring and marking paths—Fibonacci just computes values.
   :feedback_d: Fibonacci doesn't use an accumulator—it combines results on the way back up the stack.

   What pattern(s) does ``fibonacci(n)`` use?

.. mchoice:: pattern_recognize_islands
   :answer_a: Linear recursion
   :answer_b: Tree recursion only
   :answer_c: Backtracking with recursive exploration
   :answer_d: Accumulator pattern
   :correct: c
   :feedback_a: No! It explores in 4 directions (up, down, left, right)—not linear.
   :feedback_b: Close! It IS tree recursion (4 calls), but it also uses backtracking (marking visited cells).
   :feedback_c: Perfect! It recursively explores in multiple directions AND marks visited cells to avoid infinite loops.
   :feedback_d: No accumulator here—we're marking cells in place and counting islands at the top level.

   What pattern does the ``count_islands`` problem use?

Key Takeaways: Your Pattern Toolbox
------------------------------------

You now have a **powerful toolbox** of recursive patterns! 🧰

✅ **Pattern 1: Linear Recursion** — Process one element at a time (factorial, sum, count)

✅ **Pattern 2: Multiple Base Cases** — Some problems need more than one stopping point (Fibonacci)

✅ **Pattern 3: Tree Recursion** — Make multiple recursive calls to explore branches (Fibonacci, count ways)

✅ **Pattern 4: Accumulator/Helper** — Build up the result as you go (reverse with helper)

✅ **Pattern 5: Backtracking** — Explore multiple paths, mark visited areas (count islands, maze solving, DFS)

.. important::

   **The Secret to Mastery**: When you see a new problem, ask:
   
   - "Which pattern does this look like?"
   - "Do I process elements one at a time, or explore multiple branches?"
   - "Do I need to mark where I've been?"
   
   Pattern recognition transforms hard problems into familiar templates!

In the next lesson, we'll give you even more practice problems to sharpen your recursive thinking!