..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Thinking Recursively
=====================

Now that you understand recursion as a concept, let's learn how to **think recursively** when solving problems. This is a different mindset from the step-by-step thinking you've used with loops.

The Leap of Faith
-----------------

The key to understanding recursion is what computer scientists call the **"leap of faith"** or the **"recursive leap"**:

    *When solving a problem recursively, you must trust that your function will correctly solve smaller versions of the problem, even before you've fully written the function.*

This sounds circular—and it is! A bit. But it works because of a principle similar to mathematical induction:

**Mathematical Induction** (if you've seen it in math class):
1. Prove something works for the simplest case (base case)
2. Prove that if it works for case N-1, then it works for case N
3. Therefore, it works for all cases

**Recursive Thinking**:
1. Solve the simplest version directly (base case)
2. For larger versions, break the problem into: *"a small piece of work + a smaller version of the same problem"*
3. Trust that your function handles the smaller version correctly

.. note::

   **Expert Tip**: Professional programmers don't try to mentally trace recursion through many levels. Instead, they verify two things: (1) the base case works, and (2) the recursive case correctly breaks the problem down. If both are right, the recursion *must* work!

The Two Rules of Recursion
---------------------------

Every recursive solution must have these two components:

**Rule 1: The Base Case**
  The simplest version of the problem that you can solve directly, without recursion. This is where recursion STOPS.

**Rule 2: The Recursive Case**
  For all other cases, break the problem into:
  
  - A small piece you can handle now
  - A smaller version of the original problem (solved recursively)

.. important::

   **What happens if you forget the base case?**
   
   Without a base case, your function calls itself forever—like standing between two mirrors where the reflections never end. Eventually, your computer runs out of memory and the program crashes with an error. This is called **infinite recursion**, and it's one of the most common mistakes when learning recursion.
   
   Always ask: *"What's the simplest case I can solve without recursion?"*

Example: Countdown (in Pseudocode)
-----------------------------------

Let's think through a simple problem: **counting down from a number to zero**.

**The problem**: Given a number N, print N, N-1, N-2, ..., 1, 0, then print "Blastoff!"

**Thinking recursively**:

- **Base case**: If N is 0, just print "Blastoff!" and stop
- **Recursive case**: If N is greater than 0, print N, then count down from N-1

Here's the pseudocode:

.. code-block:: text

   Function countdown(N):
       If N equals 0:                    ← BASE CASE
           Print "Blastoff!"
           Stop
       Otherwise:                         ← RECURSIVE CASE
           Print N
           countdown(N - 1)               ← RECURSIVE CALL (trust it works!)

**Let's trace countdown(3) using the leap of faith**:

1. ``countdown(3)`` → N is 3 (not 0), so print 3, then call ``countdown(2)``
2. *Trust that* ``countdown(2)`` *will correctly print 2, 1, 0, Blastoff!*
3. Done!

We don't need to manually trace through ``countdown(2)``, ``countdown(1)``, etc. We trust that if our logic is correct, it will work.

.. mchoice:: recursion_leap_of_faith
   :answer_a: Trace every single recursive call in your head to make sure it works
   :answer_b: Trust that if the base case and recursive case are correct, the recursion will work
   :answer_c: Always use a loop instead, because recursion is too confusing
   :answer_d: Hope for the best and run the code without checking anything
   :correct: b
   :feedback_a: This becomes impossible with large inputs! Instead, verify your logic is correct.
   :feedback_b: Exactly! This is the "leap of faith"—trust the logic, not mental tracing.
   :feedback_c: Recursion is a powerful tool for certain problems. Learning to think recursively expands your problem-solving skills!
   :feedback_d: Not quite—you should verify the base case and recursive case are correct, then trust the logic.

   What does the "leap of faith" in recursive thinking mean?

Another Example: Sum of First N Numbers
----------------------------------------

**Problem**: Calculate 1 + 2 + 3 + ... + N

**Thinking recursively**:

- What's the simplest case? If N is 1, the sum is just 1
- How can we break down a larger case? 
  
  The sum of first N numbers = N + (sum of first N-1 numbers)
  
  For example: sum(5) = 5 + sum(4) = 5 + (4 + 3 + 2 + 1) = 15

**Pseudocode**:

.. code-block:: text

   Function sum_to_n(N):
       If N equals 1:                    ← BASE CASE
           Return 1
       Otherwise:                         ← RECURSIVE CASE
           Return N + sum_to_n(N - 1)    ← Small work + recursive call

.. note::

   Notice the pattern: The recursive case is always **"do a little work, then solve a smaller version of the problem."** This is the heart of recursive thinking--if you don't know the answer right now, ask a simpler version of the question then modify that answer.

Practice: Identifying Base and Recursive Cases
-----------------------------------------------

.. mchoice:: recursion_base_case_identify
   :answer_a: When N equals 10
   :answer_b: When N equals 0 or is negative
   :answer_c: When N equals 1
   :answer_d: There is no base case needed
   :correct: b
   :feedback_a: That's just one specific value, not the simplest case for ALL possible inputs.
   :feedback_b: Correct! We need to handle 0 (and negative numbers to be safe). This is where we stop.
   :feedback_c: Close, but what if someone calls countdown(0)? We need to handle that too!
   :feedback_d: Every recursive function MUST have a base case, or it will recurse forever!

   For the countdown function, what should the base case be?

.. mchoice:: recursion_infinite_check
   :answer_a: The function runs forever until you manually stop the program
   :answer_b: The function returns 0
   :answer_c: The computer runs out of memory and the program crashes
   :answer_d: Python automatically adds a base case for you
   :correct: c
   :feedback_a: Close, but modern computers have protections against infinite recursion.
   :feedback_b: Without a base case, the function never reaches a return statement!
   :feedback_c: Exactly! This is called "infinite recursion" or "stack overflow." We'll see why in a later lesson.
   :feedback_d: Python cannot guess what your base case should be—you must write it!

   What happens if you write a recursive function without a base case?

Putting It Together: Parsons Problem
-------------------------------------

.. parsonsprob:: recursion_sum_first_n
   :numbered: left
   :adaptive:

   Arrange the blocks to create a function that sums the first N positive integers (1 + 2 + ... + N).
   
   Remember the two rules:
   
   - Base case: What's the simplest case you can solve directly?
   - Recursive case: Break the problem into small work + a smaller problem
   -----
   def sum_to_n(n):
   =====
       if n == 1:
   =====
       if n == 0: #distractor
   =====
           return 1
   =====
           return 0 #distractor
   =====
       else:
   =====
           return n + sum_to_n(n - 1)
   =====
           return n + sum_to_n(n) #distractor
   =====
           return sum_to_n(n - 1) #distractor

Key Takeaways
-------------

Before moving on to see recursion in Python code, make sure you understand:

✅ **The leap of faith**: Trust that your function works for smaller cases

✅ **Base case**: The simplest version you solve directly (where recursion stops)

✅ **Recursive case**: Break the problem into "small work + smaller problem"

✅ **Infinite recursion**: Forgetting the base case causes the function to call itself forever until the program crashes

.. note::

   In the next lesson, we'll see these concepts in actual Python code with a classic example: the factorial function!