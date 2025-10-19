..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Your First Recursive Function: Factorial
=========================================

Now that you understand the recursive mindset, let's write our first recursive function in Python! We'll use a classic mathematical function: **factorial**.

What Is Factorial?
-------------------

In mathematics, the **factorial** of a non-negative integer N (written as N!) is the product of all positive integers less than or equal to N.

**Examples**:

- 5! = 5 × 4 × 3 × 2 × 1 = 120
- 4! = 4 × 3 × 2 × 1 = 24
- 3! = 3 × 2 × 1 = 6
- 2! = 2 × 1 = 2
- 1! = 1
- 0! = 1  *(by mathematical definition)*

Factorials appear in many areas: counting permutations, probability calculations, and mathematical formulas.

The Recursive Definition
-------------------------

Here's the beautiful insight: factorial is **naturally recursive**!

Look at the pattern:

- 5! = 5 × 4!
- 4! = 4 × 3!
- 3! = 3 × 2!
- 2! = 2 × 1!
- 1! = 1

Do you see it? Each factorial can be defined in terms of a smaller factorial!

**Mathematical recursive definition**:

.. math::

   n! = \begin{cases}
   1 & \text{if } n = 0 \\
   n \times (n-1)! & \text{if } n > 0
   \end{cases}

This translates directly to the two rules of recursion:

- **Base case**: 0! = 1 (the simplest case we know directly)
- **Recursive case**: n! = n × (n-1)! (break it into small work + smaller problem)

.. note::

   **Why is 0! = 1?** This is a mathematical convention that makes many formulas work correctly. You can also think of it as: "There's exactly one way to arrange zero items—by doing nothing!"

Translating to Python
----------------------

Now let's translate this mathematical definition into Python code:

.. code-block:: python

   def factorial(n):
       if n == 0:                    # Base case
           return 1
       else:                          # Recursive case
           return n * factorial(n - 1)

**That's it!** Five lines of code that can compute factorials of any size (until the number gets too large for Python to handle, but we'll talk about those things later).

Let's break down what's happening:

1. **Function definition**: ``def factorial(n):`` — takes one parameter
2. **Base case check**: ``if n == 0:`` — the simplest case
3. **Base case action**: ``return 1`` — we know 0! = 1
4. **Recursive case**: ``else:`` — all other cases
5. **Recursive call**: ``return n * factorial(n - 1)`` — small work (multiply by n) + smaller problem (factorial of n-1)

Understanding the Execution (The Leap of Faith)
------------------------------------------------

Let's think through ``factorial(4)`` using the leap of faith principle from the last lesson:

**Step 1**: Call ``factorial(4)``
  - Is 4 equal to 0? No
  - So return ``4 * factorial(3)``
  - Now we need ``factorial(3)``... **but we trust it will work!**

**Step 2**: What does ``factorial(3)`` return?
  - Is 3 equal to 0? No
  - So return ``3 * factorial(2)``
  - **Trust the pattern continues...**

**Eventually**: We hit the base case
  - ``factorial(0)`` returns 1
  - This value bubbles back up through all the waiting recursive calls

**The full expansion** (but remember, you don't need to trace this deeply in your head):

.. code-block:: text

   factorial(4)
   = 4 * factorial(3)
   = 4 * (3 * factorial(2))
   = 4 * (3 * (2 * factorial(1)))
   = 4 * (3 * (2 * (1 * factorial(0))))
   = 4 * (3 * (2 * (1 * 1)))          ← Base case reached!
   = 4 * (3 * (2 * 1))
   = 4 * (3 * 2)
   = 4 * 6
   = 24

.. important::

   **Professional programmers don't trace recursion this deeply!** Instead, they verify:
   
   1. ✓ Base case correct? (0! = 1) ✓
   2. ✓ Recursive case gets simpler? (n → n-1, eventually reaching 0) ✓
   3. ✓ Combination correct? (n × result) ✓
   
   If all three check out, the function MUST work correctly!

Try It Yourself
----------------

Run this code and watch what happens:

.. activecode:: factorial_basic
   :caption: Basic Factorial Function

   def factorial(n):
       if n == 0:
           return 1
       else:
           return n * factorial(n - 1)
   
   # Test it
   print("5! =", factorial(5))
   print("3! =", factorial(3))
   print("0! =", factorial(0))
   print("7! =", factorial(7))

Now let's add some print statements so you can **see** the recursion in action:

.. activecode:: factorial_traced
   :caption: Factorial with Tracing

   def factorial(n):
       print(f"→ Computing factorial({n})")
       
       if n == 0:
           print(f"← Base case! factorial(0) = 1")
           return 1
       else:
           result = n * factorial(n - 1)
           print(f"← factorial({n}) = {result}")
           return result
   
   # Test it
   print("\n=== Computing 4! ===")
   answer = factorial(4)
   print(f"\nFinal answer: {answer}")

**Run the code above** and watch how:

1. The function "dives down" (→) until it hits the base case
2. Then it "climbs back up" (←) calculating results as it goes

This is the essence of recursion!

Check Your Understanding
-------------------------

.. mchoice:: factorial_base_case
   :answer_a: if n == 1: return 1
   :answer_b: if n == 0: return 1
   :answer_c: if n == 0: return 0
   :answer_d: if n < 0: return 1
   :correct: b
   :feedback_a: This would work for positive integers, but what about factorial(0)? Mathematically, 0! = 1.
   :feedback_b: Correct! This matches the mathematical definition where 0! = 1.
   :feedback_c: Not quite—0! = 1 by mathematical definition, not 0.
   :feedback_d: This doesn't handle the actual base case. Factorials aren't defined for negative numbers!

   What is the correct base case for the factorial function?

.. clickablearea:: factorial_identify_base
   :question: Click on the line(s) that represent the BASE CASE of the factorial function.
   :iscode:
   :feedback: The base case is where recursion stops—the simplest problem we can solve directly.

   :click-correct:def factorial(n)::endclick:
       :click-correct:if n == 0::endclick:
           :click-correct:return 1:endclick:
       :click-incorrect:else::endclick:
           :click-incorrect:return n * factorial(n - 1):endclick:

.. clickablearea:: factorial_identify_recursive
   :question: Click on the line that makes the RECURSIVE CALL (where the function calls itself).
   :iscode:
   :feedback: Look for where the function calls itself with a simpler problem.

   :click-incorrect:def factorial(n)::endclick:
       :click-incorrect:if n == 0::endclick:
           :click-incorrect:return 1:endclick:
       :click-incorrect:else::endclick:
           :click-correct:return n * factorial(n - 1):endclick:

.. mchoice:: factorial_modification
   :answer_a: It would work exactly the same
   :answer_b: It would cause infinite recursion for factorial(0)
   :answer_c: It would return incorrect results but not crash
   :answer_d: It would only work for even numbers
   :correct: b
   :feedback_a: Try tracing factorial(0) with this change. What happens?
   :feedback_b: Exactly! factorial(0) would call factorial(-1), then factorial(-2), etc., never reaching 1. Eventually the program would crash.
   :feedback_c: It would actually crash due to infinite recursion before returning any result.
   :feedback_d: The problem isn't about even/odd—it's about the base case not being reached.

   What would happen if we changed the base case from ``if n == 0:`` to ``if n == 1:`` and called ``factorial(0)``?

Practice: Build It Yourself
----------------------------

Now try reconstructing the factorial function from scrambled pieces:

.. parsonsprob:: factorial_reconstruct
   :numbered: left
   :adaptive:

   Arrange the code blocks to create a working factorial function. Watch out for distractors!
   -----
   def factorial(n):
   =====
       if n == 0:
   =====
       if n == 1: #distractor
   =====
           return 1
   =====
           return 0 #distractor
   =====
       else:
   =====
           return n * factorial(n - 1)
   =====
           return n * factorial(n) #distractor
   =====
           return factorial(n - 1) #distractor

Challenge: Can You Modify It?
------------------------------

.. activecode:: factorial_challenge
   :caption: Challenge: Factorial with Error Checking

   Modify the factorial function to handle negative numbers by returning an error message instead of computing an impossible value.
   
   Hint: Add another condition before the base case to check if n is negative.
   ~~~~
   def factorial(n):
       # Add your error checking here
       
       if n == 0:
           return 1
       else:
           return n * factorial(n - 1)
   
   # Test cases
   print(factorial(5))      # Should print 120
   print(factorial(0))      # Should print 1
   print(factorial(-3))     # Should print an error message or return None
   ====
   from unittest.gui import TestCaseGui
   
   class myTests(TestCaseGui):
       def testOne(self):
           self.assertEqual(factorial(5), 120, "factorial(5)")
           self.assertEqual(factorial(0), 1, "factorial(0)")
           self.assertIsNone(factorial(-3), "factorial should return None for negative numbers")
   
   myTests().main()

Key Takeaways
-------------

You've just written your first recursive function! Here's what you've learned:

✅ **Factorial is naturally recursive**: n! = n × (n-1)!

✅ **Mathematical definitions translate to code**: Base case + recursive case = working function

✅ **Trust the recursion**: You don't need to trace every step in your head

✅ **Recursion "dives down" to the base case, then "climbs back up"** with results

.. note::

   In the next lesson, we'll dissect the anatomy of recursive functions to identify these patterns in any recursive solution!