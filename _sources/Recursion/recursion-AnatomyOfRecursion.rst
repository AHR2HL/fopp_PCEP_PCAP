..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Anatomy of Recursion
=====================

Now that you've seen recursion in action with the factorial function, let's learn to identify the essential parts of **any** recursive function. Understanding this "anatomy" will help you read, write, and debug recursive code.

The Three Essential Parts
--------------------------

Every well-formed recursive function has three components:

**1. Base Case(s)**
   The simplest version(s) of the problem that can be solved directly without recursion. This is where the recursion **stops**.

**2. Recursive Call(s)**
   Where the function calls itself with a **simpler** or **smaller** version of the original problem. This is how we make progress toward the base case.

**3. Combination Step**
   What we do with the result from the recursive call. This is how we build up the final answer.

.. important::

   Think of these parts like a recipe:
   
   - **Base case**: "If you have just one egg, scramble it directly."
   - **Recursive call**: "If you have N eggs, set one aside and ask someone else to handle N-1 eggs."
   - **Combination**: "Add your egg to what they give you back."

Let's see this in action with a new example.

Example: Reversing a String
----------------------------

**Problem**: Write a function that reverses a string.

- ``reverse_string("hello")`` → ``"olleh"``
- ``reverse_string("Python")`` → ``"nohtyP"``

**Thinking recursively**:

- **Base case**: What's the simplest string to reverse? An empty string or a single character—it's already reversed!
- **Breaking it down**: How can we reverse "hello"?
  
  - Take the first character: ``"h"``
  - Reverse the rest: ``reverse_string("ello")`` → ``"olle"``
  - Combine: Put the first character at the **end**: ``"olle" + "h"`` → ``"olleh"``

Here's the code:

.. activecode:: reverse_string_basic
   :caption: Reversing a String Recursively

   def reverse_string(s):
       # BASE CASE: Empty or single character
       if len(s) <= 1:
           return s
       
       # RECURSIVE CASE
       else:
           # Recursive call: reverse everything except first character
           reversed_rest = reverse_string(s[1:])
           
           # Combination: put first character at the end
           return reversed_rest + s[0]
   
   # Test it
   print(reverse_string("hello"))
   print(reverse_string("Python"))
   print(reverse_string("a"))
   print(reverse_string(""))

Let's dissect this function:

.. code-block:: python

   def reverse_string(s):
       if len(s) <= 1:              # ← BASE CASE
           return s                  # ← BASE CASE ACTION
       else:
           reversed_rest = reverse_string(s[1:])  # ← RECURSIVE CALL
           return reversed_rest + s[0]            # ← COMBINATION STEP

**Part 1: Base Case** — ``if len(s) <= 1: return s``
   If the string is empty or has one character, it's already "reversed." Return it as-is.

**Part 2: Recursive Call** — ``reverse_string(s[1:])``
   Reverse the string **without** the first character. This is a simpler problem (shorter string).

**Part 3: Combination** — ``reversed_rest + s[0]``
   Take the reversed rest and add the first character to the **end**.

Tracing the Execution
----------------------

Let's trace ``reverse_string("cat")`` step by step (but remember, you don't need to do this mentally for large inputs!):

.. code-block:: text

   reverse_string("cat")
   → len("cat") = 3, not <= 1, so recurse
   → reverse_string("at") + "c"
   
       reverse_string("at")
       → len("at") = 2, not <= 1, so recurse
       → reverse_string("t") + "a"
       
           reverse_string("t")
           → len("t") = 1, BASE CASE!
           → return "t"
       
       → return "t" + "a" = "ta"
   
   → return "ta" + "c" = "tac"

**Final result**: ``"tac"`` ✓

.. note::

   Notice how the function "dives down" to the base case (single character), then "builds back up" by adding characters to the end.

Check Your Understanding
-------------------------

.. mchoice:: anatomy_base_case_purpose
   :answer_a: To make the recursion faster
   :answer_b: To provide a stopping point where the problem is solved directly
   :answer_c: To handle errors in the input
   :answer_d: To make the code shorter
   :correct: b
   :feedback_a: The base case is about correctness, not speed. It prevents infinite recursion.
   :feedback_b: Exactly! Without a base case, the recursion would never stop.
   :feedback_c: While you might check for errors, the base case's primary job is to stop recursion.
   :feedback_d: Base cases aren't about code length—they're essential for correct recursion.

   What is the purpose of the base case in a recursive function?

.. clickablearea:: anatomy_identify_parts_reverse
   :question: Click on the line that represents the COMBINATION STEP (where we use the recursive result).
   :iscode:
   :feedback: The combination step is where we do something with the result from the recursive call.

   :click-incorrect:def reverse_string(s)::endclick:
       :click-incorrect:if len(s) <= 1::endclick:
           :click-incorrect:return s:endclick:
       :click-incorrect:else::endclick:
           :click-incorrect:reversed_rest = reverse_string(s[1:]):endclick:
           :click-correct:return reversed_rest + s[0]:endclick:

Another Example: Sum of a List
-------------------------------

Let's analyze a different recursive function:

.. activecode:: sum_list_anatomy
   :caption: Summing a List Recursively

   def sum_list(numbers):
       # BASE CASE: Empty list
       if len(numbers) == 0:
           return 0
       
       # RECURSIVE CASE
       else:
           # Recursive call: sum the rest of the list
           rest_sum = sum_list(numbers[1:])
           
           # Combination: add first number to the sum of the rest
           return numbers[0] + rest_sum
   
   # Test it
   print(sum_list([1, 2, 3, 4, 5]))    # Should print 15
   print(sum_list([10]))                # Should print 10
   print(sum_list([]))                  # Should print 0

**Dissecting this function**:

1. **Base case**: Empty list → sum is 0
2. **Recursive call**: ``sum_list(numbers[1:])`` → sum the rest of the list (simpler problem)
3. **Combination**: ``numbers[0] + rest_sum`` → add the first number to the sum of the rest

.. mchoice:: anatomy_identify_simpler
   :answer_a: sum_list(numbers)
   :answer_b: sum_list(numbers[1:])
   :answer_c: sum_list(numbers[0])
   :answer_d: sum_list(0)
   :correct: b
   :feedback_a: That's the same problem, not simpler! This would cause infinite recursion.
   :feedback_b: Correct! We're removing the first element, making the list shorter—simpler problem.
   :feedback_c: numbers[0] is a single number, not a list, so this wouldn't match the function's expected input.
   :feedback_d: 0 is not a list, so this wouldn't work.

   In the sum_list function, which recursive call represents a SIMPLER version of the problem?

Practice: Identify the Parts
-----------------------------

Here's a recursive function that counts how many times a digit appears in a number:

.. code-block:: python

   def count_digit(number, digit):
       # Convert to absolute value to handle negatives
       number = abs(number)
       
       # Base case: single-digit number
       if number < 10:
           return 1 if number == digit else 0
       
       # Recursive case
       else:
           last_digit = number % 10
           rest_of_number = number // 10
           
           if last_digit == digit:
               return 1 + count_digit(rest_of_number, digit)
           else:
               return count_digit(rest_of_number, digit)

.. mchoice:: anatomy_count_digit_base
   :answer_a: if number < 10:
   :answer_b: if last_digit == digit:
   :answer_c: number = abs(number)
   :answer_d: rest_of_number = number // 10
   :correct: a
   :feedback_a: Correct! When we have a single digit, we can directly check if it matches.
   :feedback_b: This is part of the recursive case logic, not the base case.
   :feedback_c: This is preprocessing, not the base case.
   :feedback_d: This is part of breaking down the problem, not the base case.

   Which line represents the BASE CASE condition?

.. mchoice:: anatomy_count_digit_simpler
   :answer_a: The number gets larger with each recursive call
   :answer_b: We remove the last digit (number // 10), making it smaller
   :answer_c: We change the digit we're looking for
   :answer_d: We convert to absolute value
   :correct: b
   :feedback_a: No, we need to make the problem simpler, not larger!
   :feedback_b: Exactly! Integer division by 10 removes the last digit, eventually reaching a single digit.
   :feedback_c: The digit parameter stays the same—we're always looking for the same digit.
   :feedback_d: That's just handling negative numbers, not making the problem simpler.

   How does count_digit make the problem SIMPLER with each recursive call?

Build It Yourself: Parsons Problem
-----------------------------------

.. parsonsprob:: anatomy_power_function
   :numbered: left
   :adaptive:

   Arrange the code blocks to create a recursive function that calculates base^exponent (e.g., power(2, 3) = 8).
   
   Think about the three parts:
   - Base case: What's the simplest exponent? (Hint: any number to the power of 0 is 1)
   - Recursive call: How can you express base^n in terms of base^(n-1)?
   - Combination: What do you do with the recursive result?
   -----
   def power(base, exponent):
   =====
       if exponent == 0:
   =====
       if exponent == 1: #distractor
   =====
           return 1
   =====
           return base #distractor
   =====
       else:
   =====
           return base * power(base, exponent - 1)
   =====
           return base * power(base, exponent) #distractor
   =====
           return power(base, exponent - 1) #distractor

Challenge: Write Your Own
--------------------------

.. activecode:: anatomy_challenge_is_palindrome
   :caption: Challenge: Check if a String is a Palindrome

   A palindrome is a word that reads the same forwards and backwards (e.g., "racecar", "level", "a").

   Write a recursive function to check if a string is a palindrome.

   Hints:
   - Base case: What's the simplest palindrome? (empty string or single character)
   - Recursive idea: Check if first and last characters match, then check the middle part
   - Use string slicing: s[0] (first char), s[-1] (last char), s[1:-1] (middle part)
   ~~~~
   def is_palindrome(s):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui
   import sys

   class myTests(TestCaseGui):
       def testRecursive(self):
           # Get the source code
           import inspect
           try:
               code = inspect.getsource(is_palindrome)
           except:
               # Fallback: try to get from __main__
               import __main__
               code = str(__main__.__dict__.get('is_palindrome', ''))

           # Check for recursion (function should call itself)
           recursion_count = code.count('is_palindrome(')
           self.assertGreaterEqual(recursion_count, 2,
               f"Your solution must use recursion (call is_palindrome from within itself). Found {recursion_count-1} recursive calls, need at least 1.")

           # Check for no loops
           self.assertNotIn('for', code.lower(),
               "Your solution should not use 'for' loops - use recursion instead!")
           self.assertNotIn('while', code.lower(),
               "Your solution should not use 'while' loops - use recursion instead!")

       def testFunctionality(self):
           self.assertTrue(is_palindrome("racecar"), "racecar is a palindrome")
           self.assertFalse(is_palindrome("hello"), "hello is not a palindrome")
           self.assertTrue(is_palindrome("a"), "single character is a palindrome")
           self.assertTrue(is_palindrome(""), "empty string is a palindrome")
           self.assertTrue(is_palindrome("noon"), "noon is a palindrome")
           self.assertTrue(is_palindrome("A"), "single character (uppercase) is a palindrome")
   
   myTests().main()

The Anatomy Checklist
---------------------

When you encounter or write a recursive function, ask yourself:

.. note:: **recursion_anatomy_checklist**

   - ☐ **Base case**: What's the simplest problem I can solve directly?
   - ☐ **Does it stop?** Will every input eventually reach the base case?
   - ☐ **Recursive call**: Am I calling the function with a **simpler** version of the problem?
   - ☐ **Combination**: What do I do with the result from the recursive call?

If all four check out, your recursion should work correctly!

Key Takeaways
-------------

You now know how to dissect any recursive function:

✅ **Base case(s)** — Where recursion stops (simplest problem)

✅ **Recursive call(s)** — Calling the function with a simpler problem

✅ **Combination step** — Using the recursive result to build the answer

✅ **Making progress** — Each recursive call MUST be simpler, moving toward the base case

.. note::

   In the next lesson, we'll visualize HOW Python executes these recursive calls using the call stack—you'll see exactly what's happening "under the hood"!