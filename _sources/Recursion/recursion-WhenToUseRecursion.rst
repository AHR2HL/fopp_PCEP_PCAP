..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 8: When to Use Recursion
================================

You've learned **how** recursion works. Now let's answer the big question: **when should you use it?**

Recursion is a powerful tool, but like any tool, it's not always the right choice. This lesson will help you decide when recursion shines and when iteration is better.

----

Recursion vs. Iteration: The Pragmatic View
--------------------------------------------

**The truth:** Almost anything you can do with recursion, you can do with a loop. And vice versa.

So why learn recursion at all? Because for certain problems, recursion makes the solution **clearer, shorter, and more natural**.

.. mchoice:: recursion_vs_iteration_truth
   :answer_a: Recursion can solve problems that loops cannot
   :answer_b: Loops can solve problems that recursion cannot
   :answer_c: Recursion and iteration are equally powerful — the choice is about clarity
   :answer_d: Recursion is always better than loops
   :correct: c
   :feedback_a: Not quite! Loops can solve the same problems, but sometimes recursion is clearer.
   :feedback_b: Also not true! Recursion can match anything a loop can do.
   :feedback_c: Exactly! They're theoretically equivalent. The choice is about code clarity and efficiency.
   :feedback_d: No — sometimes loops are clearer and more efficient!

   What's the relationship between recursion and iteration?

----

When Recursion Shines
----------------------

Recursion is the **natural choice** when:

**1. The Problem Has Self-Similar Structure**

If a problem can be broken into **smaller versions of itself**, recursion is ideal.

**Examples:**
- **Tree traversal**: A tree contains smaller trees (its subtrees)
- **Fractals**: Each part looks like the whole
- **Divide-and-conquer algorithms**: Split the problem, solve recursively, combine results

**2. The Problem Involves Backtracking**

When you need to explore multiple paths and "undo" choices, recursion with backtracking is elegant.

**Examples:**
- Finding paths in a maze
- Generating all permutations
- Solving puzzles (Sudoku, N-Queens)
- The "Count Islands" problem from Lesson 6

**3. The Data Structure Is Recursive**

If the data is naturally recursive (trees, nested lists, linked lists), recursion mirrors the structure.

**Examples:**
- Processing a file system (folders contain folders)
- Parsing nested JSON or XML
- Evaluating mathematical expressions with nested parentheses

**4. The Mathematical Definition Is Recursive**

Some problems are **defined recursively** in mathematics.

**Examples:**
- Factorial: ``n! = n × (n-1)!``
- Fibonacci: ``fib(n) = fib(n-1) + fib(n-2)``
- Greatest Common Divisor (Euclid's algorithm)

.. mchoice:: when_recursion_shines
   :answer_a: When you need to process a simple linear list
   :answer_b: When the problem has self-similar structure or involves backtracking
   :answer_c: When you want to show off advanced coding skills
   :answer_d: When loops are too hard to understand
   :correct: b
   :feedback_a: Linear lists are often easier with loops!
   :feedback_b: Exactly! Recursion shines for self-similar problems and backtracking.
   :feedback_c: No! Code clarity and correctness matter, not showing off.
   :feedback_d: If loops are clearer, use loops!

   When is recursion the **best** choice?

----

When Iteration Is Better
-------------------------

Use a **loop** when:

**1. The Problem Is Inherently Iterative**

If the problem naturally involves repeating steps in sequence, a loop is clearer.

**Examples:**
- Summing numbers from 1 to n
- Printing each element in a list
- Reading lines from a file

**2. Performance Matters and the Recursion Is Deep**

Recursion has overhead (stack frames). For problems with thousands of recursive calls, iteration can be faster and avoid stack overflow.

**Examples:**
- Computing the 10,000th Fibonacci number (use iteration or dynamic programming)
- Processing a very long list

**3. The Recursive Solution Is Inefficient Without Optimization**

Some recursive algorithms are **exponentially slow** without memoization or other optimizations.

**Example:**
- Naive recursive Fibonacci: ``fib(n)`` recalculates ``fib(n-2)`` multiple times
- Solution: Use iteration or add memoization

.. activecode:: compare_fib_efficiency
   :language: python

   Let's compare naive recursion vs. iteration for Fibonacci. Try ``fib_recursive(35)`` — it's SLOW!
   ~~~~
   def fib_recursive(n):
       """Naive recursive Fibonacci — exponentially slow!"""
       if n <= 1:
           return n
       return fib_recursive(n - 1) + fib_recursive(n - 2)

   def fib_iterative(n):
       """Iterative Fibonacci — fast and efficient!"""
       if n <= 1:
           return n
       a, b = 0, 1
       for _ in range(n - 1):
           a, b = b, a + b
       return b

   # Try both with n=10 first, then try n=35 (only for iterative!)
   print("Recursive fib(10):", fib_recursive(10))
   print("Iterative fib(10):", fib_iterative(10))
   print("Iterative fib(100):", fib_iterative(100))
   # Uncomment at your own risk — this will take FOREVER:
   # print("Recursive fib(35):", fib_recursive(35))

.. mchoice:: when_iteration_better
   :answer_a: When the problem involves tree structures
   :answer_b: When the problem is inherently sequential or performance matters
   :answer_c: When you want to impress your professor
   :answer_d: When you need to explore multiple paths
   :correct: b
   :feedback_a: Trees often work better with recursion!
   :feedback_b: Correct! Sequential problems and performance-critical code often favor loops.
   :feedback_c: Write clear code, not impressive code!
   :feedback_d: Exploring multiple paths (backtracking) is where recursion shines!

   When should you prefer iteration over recursion?

----

The Decision Framework
----------------------

Use this checklist when deciding between recursion and iteration:

.. note:: **Checklist**
     1. Is the problem self-similar? (Can it be broken into smaller versions of itself?)
     2. Is the data structure recursive? (Trees, nested structures, linked lists?)
     3. Does the problem involve backtracking? (Exploring multiple paths?)
     4. Is the recursive solution significantly clearer?
     5. Is the recursion depth reasonable? (Not thousands of calls?)

    If you answered "yes" to 2+ questions, recursion is likely a good choice!


----

Real-World Applications of Recursion
-------------------------------------

Let's see where recursion is used in the real world:

**1. File Systems**

Your computer's file system is a tree: folders contain files and other folders. Searching or listing all files requires recursion.

.. activecode:: file_system_example
   :language: python

   Here's a simplified model of listing all files in a folder tree:
   ~~~~
   def list_all_files(folder):
       """Recursively list all files in a folder and its subfolders."""
       print(f"Searching in: {folder['name']}")

       # Process files in this folder
       for file in folder.get('files', []):
           print(f"  Found file: {file}")

       # Recursively search subfolders
       for subfolder in folder.get('subfolders', []):
           list_all_files(subfolder)

   # Example folder structure
   root = {
       'name': 'Documents',
       'files': ['resume.pdf', 'cover_letter.docx'],
       'subfolders': [
           {
               'name': 'Photos',
               'files': ['vacation.jpg', 'family.png'],
               'subfolders': []
           },
           {
               'name': 'Projects',
               'files': ['project.py'],
               'subfolders': [
                   {
                       'name': 'Python',
                       'files': ['recursion.py'],
                       'subfolders': []
                   }
               ]
           }
       ]
   }

   list_all_files(root)

**2. Parsing and Compilers**

Programming languages have **nested structures** (expressions inside expressions). Compilers use recursion to parse code.

Example: Evaluating ``(2 + (3 * 4)) - 5`` requires recursively evaluating nested expressions.

**3. Game AI and Pathfinding**

Games use recursion for:
- **Minimax algorithm**: Game tree search (chess, checkers)
- **Pathfinding**: Finding routes in maps
- **Procedural generation**: Creating levels recursively

**4. Web Scraping and Crawling**

Web crawlers follow links recursively: visit a page, find links, visit those pages, repeat.

**5. Sorting Algorithms**

- **Merge Sort**: Divide list in half, recursively sort each half, merge
- **Quick Sort**: Pick a pivot, partition, recursively sort partitions

.. mchoice:: real_world_recursion
   :answer_a: Printing numbers 1 to 100
   :answer_b: Traversing a file system with nested folders
   :answer_c: Calculating the average of a list
   :answer_d: Validating user input
   :correct: b
   :feedback_a: That's a simple loop!
   :feedback_b: Correct! File systems are trees, and recursion is perfect for tree traversal.
   :feedback_c: That's better with a loop.
   :feedback_d: That's typically done with loops and conditionals.

   Which real-world problem is BEST solved with recursion?

----

Practice: Choosing the Right Tool
----------------------------------

For each problem, decide: **Recursion or Iteration?**

.. mchoice:: choose_tool_sum_list
   :answer_a: Recursion — lists are recursive structures
   :answer_b: Iteration — this is a simple sequential task
   :correct: b
   :feedback_a: Lists CAN be processed recursively, but a loop is clearer here.
   :feedback_b: Exactly! A simple for-loop is perfect for this.

   **Problem:** Sum all numbers in a list.

.. mchoice:: choose_tool_tree_depth
   :answer_a: Recursion — trees are recursive structures
   :answer_b: Iteration — loops are always better
   :correct: a
   :feedback_a: Correct! Trees are naturally recursive, and recursion mirrors the structure.
   :feedback_b: Loops would require manual stack management — recursion is clearer!

   **Problem:** Find the maximum depth of a binary tree.

.. mchoice:: choose_tool_maze_solver
   :answer_a: Recursion — backtracking problems fit recursion well
   :answer_b: Iteration — loops are more efficient
   :correct: a
   :feedback_a: Exactly! Maze solving with backtracking is elegant with recursion.
   :feedback_b: Loops with manual backtracking are messy — recursion handles it naturally!

   **Problem:** Find a path through a maze with dead ends.

.. mchoice:: choose_tool_fibonacci_1000
   :answer_a: Recursion — Fibonacci is defined recursively
   :answer_b: Iteration — naive recursion is too slow for large n
   :correct: b
   :feedback_a: The definition is recursive, but naive recursion is exponentially slow!
   :feedback_b: Correct! For fib(1000), use iteration or dynamic programming.

   **Problem:** Compute the 1000th Fibonacci number.

.. mchoice:: choose_tool_json_parser
   :answer_a: Recursion — JSON has nested structures
   :answer_b: Iteration — simpler to implement
   :correct: a
   :feedback_a: Exactly! JSON objects can contain nested objects and arrays — perfect for recursion.
   :feedback_b: Recursion handles nesting naturally; loops require complex logic.

   **Problem:** Parse nested JSON data (objects containing objects and arrays).

----

Final Challenge: Design Your Own
---------------------------------

.. activecode:: final_challenge_directory_sum
   :language: python
   :autograde: unittest

   **Real-world scenario:** You're building a disk space analyzer. Given a folder structure (like the file system example),
   write a recursive function ``total_size(folder)`` that calculates the total size of all files in a folder and its subfolders.

   Each file has a 'size' in MB. Return the total size.
   ~~~~
   def total_size(folder):
       """Recursively calculate total size of all files in folder and subfolders."""
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_total_size(self):
           folder1 = {
               'name': 'Root',
               'files': [{'name': 'a.txt', 'size': 10}, {'name': 'b.txt', 'size': 20}],
               'subfolders': []
           }
           self.assertEqual(total_size(folder1), 30, "Should sum files in root folder")

           folder2 = {
               'name': 'Root',
               'files': [{'name': 'a.txt', 'size': 10}],
               'subfolders': [
                   {
                       'name': 'SubFolder',
                       'files': [{'name': 'b.txt', 'size': 20}],
                       'subfolders': []
                   }
               ]
           }
           self.assertEqual(total_size(folder2), 30, "Should sum files in root and subfolders")

           folder3 = {
               'name': 'Root',
               'files': [],
               'subfolders': [
                   {
                       'name': 'Sub1',
                       'files': [{'name': 'x.txt', 'size': 5}],
                       'subfolders': [
                           {
                               'name': 'Sub2',
                               'files': [{'name': 'y.txt', 'size': 15}],
                               'subfolders': []
                           }
                       ]
                   }
               ]
           }
           self.assertEqual(total_size(folder3), 20, "Should handle nested subfolders")

   MyTests().main()

----

Reflection: Your Recursion Journey
-----------------------------------

.. mchoice:: reflection_confidence
   :answer_a: Very confident — I know when and how to use recursion
   :answer_b: Somewhat confident — I understand the concepts but need more practice
   :answer_c: Still uncertain — I need to review the material
   :correct: a
   :feedback_a: Excellent! You're ready to use recursion in real projects.
   :feedback_b: That's normal! Practice is key. Try implementing some of the real-world examples.
   :feedback_c: Review Lessons 1-6 and try the practice problems again. You'll get there!

   How confident do you feel about using recursion now?

.. mchoice:: reflection_key_insight
   :answer_a: Recursion is always better than loops
   :answer_b: Recursion and iteration are tools — choose based on the problem
   :answer_c: Recursion is too hard and should be avoided
   :answer_d: Recursion is only for math problems
   :correct: b
   :feedback_a: Not true! Sometimes loops are clearer and more efficient.
   :feedback_b: Exactly! The best programmers know when to use each tool.
   :feedback_c: Don't avoid it! Recursion is powerful for the right problems.
   :feedback_d: Recursion is used in file systems, compilers, games, and much more!

   What's the **most important** insight from this chapter?

----

Chapter Summary
---------------

**You've mastered recursion!** Here's what you learned:

1. **What recursion is**: A function calling itself to solve smaller subproblems
2. **How to think recursively**: The "leap of faith" and trusting the recursive call
3. **The anatomy**: Base case(s) and recursive case(s)
4. **The call stack**: How Python tracks function calls
5. **Common patterns**: Linear, tree, and backtracking recursion
6. **When to use it**: Self-similar problems, recursive data structures, backtracking
7. **When NOT to use it**: Simple sequential tasks, deep recursion with performance concerns

**🎉 Congratulations!**
You now have a powerful new tool in your programming toolkit.
Recursion might feel strange at first, but with practice, you'll recognize when it's the perfect solution. Keep practicing, and soon you'll be solving complex problems with elegant recursive code!

----

**Next Steps:**

- Try implementing recursive solutions to coding challenges on platforms like LeetCode or HackerRank
- Look for opportunities to use recursion in your own projects

**Happy coding!** 🚀