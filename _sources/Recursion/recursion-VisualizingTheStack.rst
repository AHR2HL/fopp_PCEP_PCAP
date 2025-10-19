..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Visualizing the Call Stack
===========================

You've written recursive functions and traced their logic. Now let's see what Python **actually does** when it executes recursion. Understanding the **call stack** will give you superpowers for debugging and reasoning about recursive code!

What Is the Call Stack?
------------------------

When Python runs a program, it uses a **call stack** to keep track of function calls. Think of it like a stack of plates:

- When you call a function, Python adds a new "plate" (called a **stack frame**) to the top of the stack
- When a function finishes, Python removes its plate from the top
- The function that called it is right below, waiting to continue

.. image:: Figures/stack_of_plates.png
   :alt: Stack of plates metaphor
   :align: center

*(Imagine each plate has a note: "Function X with parameters Y, currently at line Z")*

**Stack frames** contain:

- The function's **parameters** (the values passed in)
- **Local variables** (variables created inside the function)
- A **return address** (where to go back to when done)

.. note::

   **Why "stack"?** Because it follows **LIFO** (Last In, First Out)—like a stack of plates, you can only add or remove from the top!

Visualizing Factorial Recursively
----------------------------------

Let's visualize ``factorial(4)`` step by step. Watch how the stack grows and shrinks:

.. code-block:: python

   def factorial(n):
       if n == 0:
           return 1
       else:
           return n * factorial(n - 1)

**Step 1: Call** ``factorial(4)``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=4)      │  ← Currently executing
   │ Waiting for result  │
   │ from factorial(3)   │
   └─────────────────────┘

**Step 2:** ``factorial(4)`` calls ``factorial(3)``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=3)      │  ← Currently executing
   │ Waiting for result  │
   │ from factorial(2)   │
   ├─────────────────────┤
   │ factorial(n=4)      │  ← PAUSED, waiting
   │ Will compute 4 * ?  │
   └─────────────────────┘

**Step 3:** ``factorial(3)`` calls ``factorial(2)``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=2)      │  ← Currently executing
   │ Waiting for result  │
   ├─────────────────────┤
   │ factorial(n=3)      │  ← PAUSED
   │ Will compute 3 * ?  │
   ├─────────────────────┤
   │ factorial(n=4)      │  ← PAUSED
   │ Will compute 4 * ?  │
   └─────────────────────┘

**Step 4:** ``factorial(2)`` calls ``factorial(1)``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=1)      │  ← Currently executing
   │ Waiting for result  │
   ├─────────────────────┤
   │ factorial(n=2)      │  ← PAUSED
   │ Will compute 2 * ?  │
   ├─────────────────────┤
   │ factorial(n=3)      │  ← PAUSED
   │ Will compute 3 * ?  │
   ├─────────────────────┤
   │ factorial(n=4)      │  ← PAUSED
   │ Will compute 4 * ?  │
   └─────────────────────┘

**Step 5:** ``factorial(1)`` calls ``factorial(0)`` — **BASE CASE!**

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=0)      │  ← BASE CASE!
   │ Returns 1           │  ← No more recursion!
   ├─────────────────────┤
   │ factorial(n=1)      │
   │ Will compute 1 * ?  │
   ├─────────────────────┤
   │ factorial(n=2)      │
   │ Will compute 2 * ?  │
   ├─────────────────────┤
   │ factorial(n=3)      │
   │ Will compute 3 * ?  │
   ├─────────────────────┤
   │ factorial(n=4)      │
   │ Will compute 4 * ?  │
   └─────────────────────┘

**Now the stack starts UNWINDING** (collapsing from the top):

**Step 6:** ``factorial(0)`` returns ``1``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=1)      │  ← Receives 1
   │ Computes 1 * 1 = 1  │  ← Returns 1
   ├─────────────────────┤
   │ factorial(n=2)      │
   ├─────────────────────┤
   │ factorial(n=3)      │
   ├─────────────────────┤
   │ factorial(n=4)      │
   └─────────────────────┘

**Step 7:** ``factorial(1)`` returns ``1``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=2)      │  ← Receives 1
   │ Computes 2 * 1 = 2  │  ← Returns 2
   ├─────────────────────┤
   │ factorial(n=3)      │
   ├─────────────────────┤
   │ factorial(n=4)      │
   └─────────────────────┘

**Step 8:** ``factorial(2)`` returns ``2``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=3)      │  ← Receives 2
   │ Computes 3 * 2 = 6  │  ← Returns 6
   ├─────────────────────┤
   │ factorial(n=4)      │
   └─────────────────────┘

**Step 9:** ``factorial(3)`` returns ``6``

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ factorial(n=4)      │  ← Receives 6
   │ Computes 4 * 6 = 24 │  ← Returns 24
   └─────────────────────┘

**Step 10:** ``factorial(4)`` returns ``24`` — **DONE!** Stack is empty.

.. important::

   **The Key Insight**: Each recursive call is **paused** (waiting on the stack) until the call it made finishes. The base case is what allows the stack to start unwinding!

Try It With Visualization
--------------------------

Run this code and watch the stack grow and shrink:

.. activecode:: visualize_factorial_stack
   :caption: Factorial with Stack Visualization

   def factorial(n, depth=0):
       indent = "  " * depth
       print(f"{indent}→ CALL factorial({n}) [Stack depth: {depth}]")
       
       if n == 0:
           print(f"{indent}← BASE CASE! Return 1")
           return 1
       else:
           result = n * factorial(n - 1, depth + 1)
           print(f"{indent}← RETURN {n} * factorial({n-1}) = {result}")
           return result
   
   print("=== Computing factorial(4) ===\n")
   answer = factorial(4)
   print(f"\n=== Final Answer: {answer} ===")

**Watch how:**
- The arrows move RIGHT (→) as we dive deeper (stack growing)
- The arrows move LEFT (←) as we return results (stack shrinking)
- The depth parameter shows how many frames are on the stack

What Is Stack Overflow?
------------------------

Remember infinite recursion from Lesson 2? Now you can see what **actually** happens!

If you forget the base case (or it's never reached), the stack keeps growing forever:

.. code-block:: text

   STACK:
   ┌─────────────────────┐
   │ countdown(-9998)    │
   ├─────────────────────┤
   │ countdown(-9997)    │
   ├─────────────────────┤
   │ countdown(-9996)    │
   ├─────────────────────┤
   │ ...thousands more...│
   ├─────────────────────┤
   │ countdown(3)        │
   ├─────────────────────┤
   │ countdown(4)        │
   └─────────────────────┘
   
   💥 RecursionError: maximum recursion depth exceeded!

Python has a limit (usually around 1000 stack frames) to prevent your computer from running out of memory. When you hit this limit, you get a **RecursionError**.

.. activecode:: stack_overflow_demo
   :caption: Stack Overflow Example (Be Careful!)

   def bad_countdown(n):
       print(f"Counting: {n}")
       return bad_countdown(n - 1)  # ← No base case!
   
   # Uncomment to see stack overflow (it will crash!)
   # bad_countdown(5)
   
   print("Recursion with no base case will crash!")
   print("Python's recursion limit protects your computer from freezing.")

.. warning::

   **Stack overflow** happens when:
   
   - You forget the base case
   - Your recursive case doesn't make the problem simpler
   - The input is so large that even valid recursion exceeds the limit
   
   Always verify: *"Does every recursive call eventually reach the base case?"*

Check Your Understanding
-------------------------

.. mchoice:: stack_lifo_order
   :answer_a: First In, First Out (like a queue)
   :answer_b: Last In, First Out (like a stack of plates)
   :answer_c: Random order
   :answer_d: Alphabetical order
   :correct: b
   :feedback_a: That's a queue! Stacks work the opposite way.
   :feedback_b: Exactly! The most recent function call is on top and finishes first.
   :feedback_c: No, the order is very predictable—always the top of the stack.
   :feedback_d: Function calls don't execute in alphabetical order!

   What order does the call stack follow?

.. mchoice:: stack_when_unwind
   :answer_a: As soon as the first function is called
   :answer_b: When the base case is reached
   :answer_c: When the user presses a key
   :answer_d: Randomly during execution
   :correct: b
   :feedback_a: The stack grows first as we make recursive calls!
   :feedback_b: Correct! The base case stops adding to the stack, allowing it to start returning values.
   :feedback_c: No, it's automatic based on the program logic, not user input.
   :feedback_d: The unwinding follows a predictable pattern once the base case is hit.

   When does the call stack start "unwinding" (returning values)?

.. dragndrop:: stack_frame_contents
   :match_1: Parameters|||Values passed to the function (e.g., n=4)
   :match_2: Local variables|||Variables created inside the function
   :match_3: Return address|||Where to continue after function finishes
   :match_4: Stack frame|||The "plate" containing all function information

   Match each term to its description:

Practice: Tracing the Stack
----------------------------

Here's a function that computes the sum of digits in a number:

.. code-block:: python

   def sum_digits(n):
       if n < 10:
           return n
       else:
           return (n % 10) + sum_digits(n // 10)

.. mchoice:: stack_sum_digits_depth
   :answer_a: 2 stack frames
   :answer_b: 3 stack frames
   :answer_c: 4 stack frames
   :answer_d: 5 stack frames
   :correct: b
   :feedback_a: Count more carefully: sum_digits(123), sum_digits(12), sum_digits(1).
   :feedback_b: Correct! sum_digits(123) → sum_digits(12) → sum_digits(1) [base case] = 3 frames maximum.
   :feedback_c: Too many! Remember, sum_digits(1) is the base case (less than 10), so it doesn't recurse further.
   :feedback_d: Way too many! Trace through the calls step by step.

   What is the MAXIMUM number of stack frames when calling ``sum_digits(123)``?

.. mchoice:: stack_sum_digits_return_order
   :answer_a: 123, then 12, then 1
   :answer_b: 1, then 12, then 123
   :answer_c: 1, then 3, then 6
   :answer_d: All at the same time
   :correct: c
   :feedback_a: Those are the values of n, not the return values!
   :feedback_b: The function doesn't return n—it processes the digits.
   :feedback_c: Correct! sum_digits(1) returns 1, sum_digits(12) returns 3 (2+1), sum_digits(123) returns 6 (3+3).
   :feedback_d: Functions return one at a time as the stack unwinds.

   In what order do the return values come back when calling ``sum_digits(123)``?

Visualize Any Recursive Function
---------------------------------

Want to see the stack for ANY Python code? Use **Python Tutor**!

.. raw:: html

   <div style="border: 2px solid #4CAF50; padding: 15px; margin: 20px 0; background-color: #f9f9f9;">
   <h4 style="margin-top: 0;">🔍 Python Tutor: Your Stack Visualization Superpower</h4>
   <p><strong>Python Tutor</strong> (<a href="https://pythontutor.com" target="_blank">pythontutor.com</a>) 
   is a free tool that shows you:</p>
   <ul>
       <li>Each stack frame as it's created</li>
       <li>Variable values at each step</li>
       <li>The exact line currently executing</li>
       <li>How the stack grows and shrinks</li>
   </ul>
   <p><strong>How to use it:</strong></p>
   <ol>
       <li>Go to <a href="https://pythontutor.com/visualize.html" target="_blank">pythontutor.com/visualize.html</a></li>
       <li>Paste your recursive function</li>
       <li>Click "Visualize Execution"</li>
       <li>Step through with the "Next" button</li>
   </ol>
   <p>Try it with <code>factorial(4)</code> or any recursive function from this chapter!</p>
   </div>

Practice with the Stack
------------------------

.. activecode:: stack_practice_string_reverse
   :caption: Trace the Stack Yourself

   Add print statements to show the stack depth as this function executes.
   Watch how deep the stack gets for different string lengths.
   ~~~~
   def reverse_string(s, depth=0):
       indent = "  " * depth
       # Add your print statement here to show when the function is called
       
       if len(s) <= 1:
           # Add your print statement here to show the base case
           return s
       else:
           result = reverse_string(s[1:], depth + 1) + s[0]
           # Add your print statement here to show what's being returned
           return result
   
   print("=== Reversing 'hello' ===\n")
   print(reverse_string("hello"))

Key Takeaways
-------------

You now have X-ray vision into recursive execution!

✅ **Call stack**: Python's way of tracking function calls (LIFO - Last In, First Out)

✅ **Stack frames**: Each function call gets its own frame with parameters, local variables, and return address

✅ **Stack growth**: Each recursive call adds a frame to the top

✅ **Stack unwinding**: When the base case is reached, frames return values and are removed

✅ **Stack overflow**: Happens when recursion never reaches the base case (or goes too deep)

✅ **Maximum depth**: Python limits recursion depth (usually ~1000) to protect your computer

.. note::

   **Pro Tip**: When debugging recursion, you don't need to trace every stack frame manually. Just verify:
   
   1. Base case is correct
   2. Recursive calls make progress toward the base case
   3. Combination step is correct
   
   The stack will handle the rest!

In the next lesson, we'll explore common recursive patterns so you can recognize when recursion is the right tool for a problem!