..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Lesson 7: Practice and Consolidation
=====================================

This lesson is all about **you** working with recursion. You'll check your vocabulary, solve Parsons puzzles, debug broken code, and write your own recursive functions.

----

Part 1: Vocabulary Check
-------------------------

Let's make sure the core concepts are clear!

.. mchoice:: recursion_vocab_base_case
   :answer_a: The condition that stops recursion
   :answer_b: The part that calls the function again
   :answer_c: The memory structure that tracks function calls
   :answer_d: An error when recursion goes too deep
   :correct: a
   :feedback_a: Correct! The base case provides the stopping condition.
   :feedback_b: That's the recursive case.
   :feedback_c: That's the call stack.
   :feedback_d: That's a stack overflow error.

   What is a **base case**?

.. mchoice:: recursion_vocab_call_stack
   :answer_a: A recursive pattern for tree-like problems
   :answer_b: The memory structure that tracks active function calls
   :answer_c: A function that calls itself
   :answer_d: The combination step in recursion
   :correct: b
   :feedback_a: That's tree recursion, not the call stack.
   :feedback_b: Correct! The call stack holds stack frames for each active function call.
   :feedback_c: That's the definition of a recursive function.
   :feedback_d: That's the combination step.

   What is the **call stack**?

.. mchoice:: recursion_vocab_leap_of_faith
   :answer_a: Assuming your base case is correct
   :answer_b: Trusting that recursion works on simpler inputs without tracing every call
   :answer_c: Hoping your code doesn't crash
   :answer_d: Testing with large inputs first
   :correct: b
   :feedback_a: That's important, but not the "leap of faith."
   :feedback_b: Exactly! You trust that if it works for n-1, it works for n.
   :feedback_c: No! The leap of faith is a logical reasoning technique.
   :feedback_d: The leap of faith is about reasoning, not testing.

   What does the **"leap of faith"** mean in recursion?

.. mchoice:: recursion_vocab_stack_overflow
   :answer_a: When the call stack exceeds its memory limit
   :answer_b: When you forget to write a base case
   :answer_c: When recursion is too slow
   :answer_d: When you use a loop instead of recursion
   :correct: a
   :feedback_a: Correct! Stack overflow happens when too many stack frames accumulate (usually from infinite recursion).
   :feedback_b: That often CAUSES a stack overflow, but stack overflow is the actual error name.
   :feedback_c: Performance and stack overflow are different issues.
   :feedback_d: Loops don't use the call stack like recursion does.

   What is a **stack overflow** error?

----

Part 2: Conceptual Understanding
---------------------------------

Now let's check your understanding of how recursion actually works.

.. mchoice:: recursion_concept_trust
   :answer_a: Trace every single recursive call to understand the code
   :answer_b: Trust that the recursive call works on a simpler input
   :answer_c: Always use loops instead
   :answer_d: Only use recursion for mathematical problems
   :correct: b
   :feedback_a: That's possible but defeats the "leap of faith" — you'd go insane tracing deep recursion!
   :feedback_b: Exactly! The leap of faith is key to thinking recursively.
   :feedback_c: Recursion is a powerful tool for certain problems!
   :feedback_d: Recursion works for many types of problems, not just math.

   When thinking recursively, what should you do?

.. mchoice:: recursion_concept_base_case_purpose
   :answer_a: Make the function run faster
   :answer_b: Prevent infinite recursion by providing a stopping condition
   :answer_c: Make the code easier to read
   :answer_d: Allow the function to call itself
   :correct: b
   :feedback_a: The base case doesn't affect speed — it prevents infinite loops!
   :feedback_b: Correct! Without a base case, recursion never stops.
   :feedback_c: That's a side benefit, but not the primary purpose.
   :feedback_d: The recursive case does that, not the base case.

   What is the **main purpose** of a base case?

.. mchoice:: recursion_concept_stack_frames
   :answer_a: They are created when a function is called and destroyed when it returns
   :answer_b: They store global variables
   :answer_c: They prevent recursion from working
   :answer_d: They are only used in loops
   :correct: a
   :feedback_a: Correct! Each function call creates a stack frame, and it's removed when the function returns.
   :feedback_b: Stack frames store LOCAL variables and return addresses.
   :feedback_c: Stack frames ENABLE recursion!
   :feedback_d: Loops don't use stack frames the way function calls do.

   What happens to stack frames during recursion?

.. mchoice:: recursion_concept_when_to_use
   :answer_a: Always — recursion is always better than loops
   :answer_b: Never — loops are always better
   :answer_c: When a problem can naturally be broken into smaller, similar subproblems
   :answer_d: Only when you can't figure out how to use a loop
   :correct: c
   :feedback_a: Not true! Sometimes loops are clearer and more efficient.
   :feedback_b: Also not true! Recursion shines for tree structures, divide-and-conquer, and backtracking.
   :feedback_c: Exactly! Recursion is ideal for problems with self-similar structure.
   :feedback_d: Recursion is a choice of tool, not a fallback!

   When is recursion a good choice?

----

Part 3: Parsons Problems
-------------------------

Reconstruct these recursive functions by dragging blocks into the correct order.

.. parsonsprob:: parsons_count_down
   :numbered: left
   :adaptive:

   Arrange the blocks to create a recursive ``count_down`` function that prints from n down to 1.
   -----
   def count_down(n):
   =====
       if n <= 0:
   =====
       if n < 0: #distractor
   =====
           return
   =====
       print(n)
   =====
       count_down(n - 1)
   =====
       count_down(n) #distractor

.. parsonsprob:: parsons_sum_to_n
   :numbered: left
   :adaptive:

   Arrange the blocks to create a recursive function that returns the sum of integers from 1 to n.
   -----
   def sum_to_n(n):
   =====
       if n == 0:
   =====
       if n == 1: #distractor
   =====
           return 0
   =====
       return n + sum_to_n(n - 1)
   =====
       return n + sum_to_n(n) #distractor

.. parsonsprob:: parsons_string_length
   :numbered: left
   :adaptive:

   Arrange the blocks to create a recursive function that returns the length of a string.
   -----
   def string_length(s):
   =====
       if s == "":
   =====
       if len(s) == 0: #distractor
   =====
           return 0
   =====
       return 1 + string_length(s[1:])
   =====
       return 1 + string_length(s) #distractor

.. parsonsprob:: parsons_list_max
   :numbered: left
   :adaptive:

   Arrange the blocks to create a recursive function that finds the maximum value in a list.
   -----
   def list_max(lst):
   =====
       if len(lst) == 1:
   =====
       if len(lst) == 0: #distractor
   =====
           return lst[0]
   =====
       rest_max = list_max(lst[1:])
   =====
       rest_max = list_max(lst) #distractor
   =====
       if lst[0] > rest_max:
   =====
           return lst[0]
   =====
       else:
   =====
           return rest_max

----

Part 4: Debug Me!
-----------------

Each of these recursive functions has a bug. Find and fix it!

.. activecode:: debug_factorial
   :language: python
   :autograde: unittest

   This function should compute the factorial of n, but it has a bug. Fix it!
   ~~~~
   def factorial(n):
       if n == 0:
           return 0
       return n * factorial(n - 1)

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_factorial(self):
           self.assertEqual(factorial(0), 1, "factorial(0) should be 1")
           self.assertEqual(factorial(1), 1, "factorial(1) should be 1")
           self.assertEqual(factorial(5), 120, "factorial(5) should be 120")

   MyTests().main()

.. activecode:: debug_sum_list
   :language: python
   :autograde: unittest

   This function should sum all elements in a list, but it has a bug. Fix it!
   ~~~~
   def sum_list(lst):
       if len(lst) == 0:
           return 0
       return lst[0] + sum_list(lst)

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_sum_list(self):
           self.assertEqual(sum_list([]), 0, "sum_list([]) should be 0")
           self.assertEqual(sum_list([5]), 5, "sum_list([5]) should be 5")
           self.assertEqual(sum_list([1, 2, 3, 4]), 10, "sum_list([1,2,3,4]) should be 10")

   MyTests().main()

.. activecode:: debug_reverse_string
   :language: python
   :autograde: unittest

   This function should reverse a string, but it has a bug. Fix it!
   ~~~~
   def reverse_string(s):
       if s == "":
           return ""
       return s[0] + reverse_string(s[1:])

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_reverse_string(self):
           self.assertEqual(reverse_string(""), "", "reverse_string('') should be ''")
           self.assertEqual(reverse_string("a"), "a", "reverse_string('a') should be 'a'")
           self.assertEqual(reverse_string("hello"), "olleh", "reverse_string('hello') should be 'olleh'")

   MyTests().main()

----

Part 5: Coding Challenges
--------------------------

Now write your own recursive functions from scratch!

.. activecode:: challenge_product
   :language: python
   :autograde: unittest

   Write a recursive function ``product(n)`` that returns the product of integers from 1 to n.
   Example: ``product(5)`` should return 120 (1 * 2 * 3 * 4 * 5).
   ~~~~
   def product(n):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_product(self):
           self.assertEqual(product(1), 1, "product(1) should be 1")
           self.assertEqual(product(5), 120, "product(5) should be 120")
           self.assertEqual(product(0), 1, "product(0) should be 1 (by convention)")

           # Enforce recursion
           import inspect
           source = inspect.getsource(product)
           self.assertNotIn('for', source, "Do not use 'for' loops")
           self.assertNotIn('while', source, "Do not use 'while' loops")

   MyTests().main()

.. activecode:: challenge_count_char
   :language: python
   :autograde: unittest

   Write a recursive function ``count_char(s, ch)`` that counts how many times character ``ch`` appears in string ``s``.
   Example: ``count_char("hello", "l")`` should return 2.
   ~~~~
   def count_char(s, ch):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_count_char(self):
           self.assertEqual(count_char("", "a"), 0, "count_char('', 'a') should be 0")
           self.assertEqual(count_char("hello", "l"), 2, "count_char('hello', 'l') should be 2")
           self.assertEqual(count_char("banana", "a"), 3, "count_char('banana', 'a') should be 3")

           # Enforce recursion
           import inspect
           source = inspect.getsource(count_char)
           self.assertNotIn('for', source, "Do not use 'for' loops")
           self.assertNotIn('while', source, "Do not use 'while' loops")

   MyTests().main()

.. activecode:: challenge_gcd
   :language: python
   :autograde: unittest

   Write a recursive function ``gcd(a, b)`` that computes the greatest common divisor using the Euclidean algorithm:
   If b is 0, return a. Otherwise, return gcd(b, a % b).
   Example: ``gcd(48, 18)`` should return 6.
   ~~~~
   def gcd(a, b):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_gcd(self):
           self.assertEqual(gcd(48, 18), 6, "gcd(48, 18) should be 6")
           self.assertEqual(gcd(100, 25), 25, "gcd(100, 25) should be 25")
           self.assertEqual(gcd(17, 5), 1, "gcd(17, 5) should be 1")

           # Enforce recursion
           import inspect
           source = inspect.getsource(gcd)
           self.assertNotIn('for', source, "Do not use 'for' loops")
           self.assertNotIn('while', source, "Do not use 'while' loops")

   MyTests().main()

.. activecode:: challenge_power
   :language: python
   :autograde: unittest

   Write a recursive function ``power(base, exp)`` that computes base raised to the power of exp.
   Assume exp is a non-negative integer.
   Example: ``power(2, 3)`` should return 8.
   ~~~~
   def power(base, exp):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_power(self):
           self.assertEqual(power(2, 0), 1, "power(2, 0) should be 1")
           self.assertEqual(power(2, 3), 8, "power(2, 3) should be 8")
           self.assertEqual(power(5, 2), 25, "power(5, 2) should be 25")

           # Enforce recursion
           import inspect
           source = inspect.getsource(power)
           self.assertNotIn('for', source, "Do not use 'for' loops")
           self.assertNotIn('while', source, "Do not use 'while' loops")

   MyTests().main()

.. activecode:: challenge_list_sum
   :language: python
   :autograde: unittest

   Write a recursive function ``list_sum(lst)`` that returns the sum of all numbers in a list.
   Example: ``list_sum([1, 2, 3, 4])`` should return 10.
   ~~~~
   def list_sum(lst):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_list_sum(self):
           self.assertEqual(list_sum([]), 0, "list_sum([]) should be 0")
           self.assertEqual(list_sum([5]), 5, "list_sum([5]) should be 5")
           self.assertEqual(list_sum([1, 2, 3, 4]), 10, "list_sum([1,2,3,4]) should be 10")

           # Enforce recursion
           import inspect
           source = inspect.getsource(list_sum)
           self.assertNotIn('for', source, "Do not use 'for' loops")
           self.assertNotIn('while', source, "Do not use 'while' loops")

   MyTests().main()

.. activecode:: challenge_flatten
   :language: python
   :autograde: unittest

   Write a recursive function ``flatten(nested_list)`` that takes a nested list and returns a flat list.
   Example: ``flatten([1, [2, 3], [4, [5, 6]]])`` should return ``[1, 2, 3, 4, 5, 6]``.
   Hint: Use ``isinstance(item, list)`` to check if an item is a list.
   ~~~~
   def flatten(nested_list):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_flatten(self):
           self.assertEqual(flatten([]), [], "flatten([]) should be []")
           self.assertEqual(flatten([1, 2, 3]), [1, 2, 3], "flatten([1,2,3]) should be [1,2,3]")
           self.assertEqual(flatten([1, [2, 3], 4]), [1, 2, 3, 4], "flatten([1,[2,3],4]) should be [1,2,3,4]")
           self.assertEqual(flatten([1, [2, [3, 4]]]), [1, 2, 3, 4], "Deep nesting should work")

   MyTests().main()

.. activecode:: challenge_binary_search
   :language: python
   :autograde: unittest

   Write a recursive function ``binary_search(sorted_list, target, low, high)`` that searches for ``target`` in a sorted list.
   Return the index of target if found, or -1 if not found.
   Example: ``binary_search([1, 3, 5, 7, 9], 5, 0, 4)`` should return 2.
   ~~~~
   def binary_search(sorted_list, target, low, high):
       # YOUR CODE HERE
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_binary_search(self):
           lst = [1, 3, 5, 7, 9, 11, 13]
           self.assertEqual(binary_search(lst, 5, 0, 6), 2, "Should find 5 at index 2")
           self.assertEqual(binary_search(lst, 1, 0, 6), 0, "Should find 1 at index 0")
           self.assertEqual(binary_search(lst, 13, 0, 6), 6, "Should find 13 at index 6")
           self.assertEqual(binary_search(lst, 4, 0, 6), -1, "Should return -1 for missing element")

           # Enforce recursion
           import inspect
           source = inspect.getsource(binary_search)
           self.assertNotIn('for', source, "Do not use 'for' loops")
           self.assertNotIn('while', source, "Do not use 'while' loops")

   MyTests().main()

----

Part 6: Reflection
------------------

.. mchoice:: reflection_difficulty
   :answer_a: Very easy — I got it right away
   :answer_b: Manageable — took some thinking but made sense
   :answer_c: Challenging — I had to work hard to understand
   :answer_d: Very difficult — I'm still confused
   :correct: a
   :feedback_a: Great! You're ready for advanced recursion topics.
   :feedback_b: That's normal! Recursion takes practice.
   :feedback_c: Keep practicing — it will click!
   :feedback_d: Review the earlier lessons and try the problems again. Don't give up!

   How did you find this practice session?

.. mchoice:: reflection_most_helpful
   :answer_a: Parsons problems (dragging blocks)
   :answer_b: Debug challenges
   :answer_c: Writing functions from scratch
   :answer_d: Conceptual MCQs
   :correct: a
   :feedback_a: Parsons problems are great for understanding structure!
   :feedback_b: Debugging helps you see common mistakes.
   :feedback_c: Coding from scratch is where real learning happens!
   :feedback_d: Concepts are the foundation!

   Which type of practice was MOST helpful for you?

----

**Up Next:** In Lesson 8, we'll discuss when to use recursion vs. iteration, and explore real-world applications.