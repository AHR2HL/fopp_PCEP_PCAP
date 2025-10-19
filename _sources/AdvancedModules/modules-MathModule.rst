..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

The ``math`` Module
===================

.. index:: math module, ceil, floor, trunc, factorial, sqrt, hypot, pi, e

Python's ``math`` module provides access to mathematical functions and constants that go beyond basic arithmetic operators. While you can add, subtract, multiply, and divide with built-in operators, the ``math`` module gives you access to more specialized mathematical operations.

Why Use the ``math`` Module?
-----------------------------

The ``math`` module is part of Python's standard library, which means it's always available—you don't need to install anything extra. It's particularly useful when you need:

* **Precise rounding control** (ceiling, floor, truncation)
* **Mathematical calculations** (square roots, factorials, powers)
* **Mathematical constants** (π, e)
* **Trigonometric functions** (sine, cosine, tangent)

For the PCAP exam, you need to know the core functions we'll cover in this section.

Importing the ``math`` Module
------------------------------

Before you can use any function from the ``math`` module, you must import it:

.. activecode:: math_import_basic
   :nocodelens:

   import math

   # Now we can use math functions
   result = math.sqrt(16)
   print(result)
   print(f"The value of pi is {math.pi}")

You can also import specific functions:

.. activecode:: math_import_specific
   :nocodelens:

   from math import sqrt, pi

   # Now we can use them without the math. prefix
   result = sqrt(25)
   print(result)
   print(f"Pi is approximately {pi:.2f}")

For the PCAP exam, you should be comfortable with both import styles.

Rounding Functions
------------------

Python's ``math`` module provides three different ways to round numbers, each with a specific purpose.

``ceil()`` — Round Up
~~~~~~~~~~~~~~~~~~~~~

The ``ceil()`` function (short for "ceiling") always rounds **up** to the nearest integer, no matter how small the decimal part is.

.. activecode:: math_ceil_demo
   :nocodelens:

   import math

   print(math.ceil(3.1))    # 4
   print(math.ceil(3.9))    # 4
   print(math.ceil(-3.1))   # -3 (up toward zero)
   print(math.ceil(-3.9))   # -3

   # Practical example: calculating needed packages
   items = 47
   items_per_box = 10
   boxes_needed = math.ceil(items / items_per_box)
   print(f"You need {boxes_needed} boxes")  # 5 boxes

``floor()`` — Round Down
~~~~~~~~~~~~~~~~~~~~~~~~~

The ``floor()`` function always rounds **down** to the nearest integer.

.. activecode:: math_floor_demo
   :nocodelens:

   import math

   print(math.floor(3.1))    # 3
   print(math.floor(3.9))    # 3
   print(math.floor(-3.1))   # -4 (down, away from zero)
   print(math.floor(-3.9))   # -4

   # Practical example: calculating complete sets
   total_students = 47
   students_per_team = 10
   complete_teams = math.floor(total_students / students_per_team)
   print(f"You can make {complete_teams} complete teams")  # 4 teams

``trunc()`` — Truncate Toward Zero
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``trunc()`` function removes the decimal part entirely, always moving **toward zero**.

.. activecode:: math_trunc_demo
   :nocodelens:

   import math

   print(math.trunc(3.1))    # 3
   print(math.trunc(3.9))    # 3
   print(math.trunc(-3.1))   # -3 (toward zero)
   print(math.trunc(-3.9))   # -3 (toward zero)

   # Practical example: extracting whole units
   total_distance = 47.8  # miles
   whole_miles = math.trunc(total_distance)
   print(f"You traveled {whole_miles} complete miles")  # 47 miles

**Visual Comparison:**

.. list-table:: Rounding Function Comparison
   :header-rows: 1
   :widths: 20 20 20 20

   * - Value
     - ``ceil()``
     - ``floor()``
     - ``trunc()``
   * - 3.2
     - 4
     - 3
     - 3
   * - 3.9
     - 4
     - 3
     - 3
   * - -3.2
     - -3
     - -4
     - -3
   * - -3.9
     - -3
     - -4
     - -3

.. mchoice:: math_rounding_understanding
   :answer_a: ceil()
   :answer_b: floor()
   :answer_c: trunc()
   :answer_d: round()
   :correct: a
   :feedback_a: Correct! ceil() always rounds up, ensuring you have enough materials.
   :feedback_b: floor() would round down, potentially leaving you short.
   :feedback_c: trunc() would remove the decimal, potentially leaving you short.
   :feedback_d: round() might round down if the decimal is less than 0.5.

   You need to order paint cans for a project. Your calculation shows you need 7.3 cans. Which function ensures you order enough?

Mathematical Functions
----------------------

``factorial()`` — Calculate Factorials
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The factorial of a number *n* (written as *n!*) is the product of all positive integers from 1 to *n*.

.. math::

   n! = n \times (n-1) \times (n-2) \times ... \times 2 \times 1

.. activecode:: math_factorial_demo
   :nocodelens:

   import math

   print(math.factorial(5))   # 5! = 5 × 4 × 3 × 2 × 1 = 120
   print(math.factorial(0))   # 0! = 1 (by definition)
   print(math.factorial(10))  # 3628800

   # Practical example: calculating permutations
   # How many ways can 5 people sit in 5 chairs?
   arrangements = math.factorial(5)
   print(f"There are {arrangements} possible seating arrangements")

``sqrt()`` — Square Root
~~~~~~~~~~~~~~~~~~~~~~~~~

The ``sqrt()`` function calculates the square root of a number.

.. activecode:: math_sqrt_demo
   :nocodelens:

   import math

   print(math.sqrt(16))    # 4.0
   print(math.sqrt(2))     # 1.4142135623730951
   print(math.sqrt(100))   # 10.0

   # Practical example: Pythagorean theorem (one side)
   # If hypotenuse = 5 and one side = 3, find the other side
   hypotenuse = 5
   side_a = 3
   side_b = math.sqrt(hypotenuse**2 - side_a**2)
   print(f"The other side is {side_b}")  # 4.0

``hypot()`` — Hypotenuse Calculation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``hypot()`` function calculates the Euclidean distance (hypotenuse) given two sides of a right triangle. It's more accurate than calculating ``sqrt(x**2 + y**2)`` yourself.

.. activecode:: math_hypot_demo
   :nocodelens:

   import math

   # Find the hypotenuse of a triangle with sides 3 and 4
   side_a = 3
   side_b = 4
   hypotenuse = math.hypot(side_a, side_b)
   print(f"Hypotenuse: {hypotenuse}")  # 5.0

   # Practical example: distance between two points
   # Distance from (0, 0) to (3, 4)
   x = 3
   y = 4
   distance = math.hypot(x, y)
   print(f"Distance from origin: {distance}")  # 5.0

   # Distance between (1, 2) and (4, 6)
   x1, y1 = 1, 2
   x2, y2 = 4, 6
   distance = math.hypot(x2 - x1, y2 - y1)
   print(f"Distance between points: {distance}")  # 5.0

.. mchoice:: math_functions_understanding
   :answer_a: math.sqrt(a**2 + b**2)
   :answer_b: math.hypot(a, b)
   :answer_c: Both are equivalent and equally good
   :answer_d: math.factorial(a + b)
   :correct: b
   :feedback_a: This works, but hypot() is more accurate for very large or very small numbers.
   :feedback_b: Correct! hypot() is specifically designed for this calculation and handles edge cases better.
   :feedback_c: While they often give similar results, hypot() is more accurate and handles overflow better.
   :feedback_d: factorial() is not related to calculating distances or hypotenuses.

   Which function is the best choice for calculating the distance between two points?

Mathematical Constants
----------------------

The ``math`` module provides precise values for important mathematical constants.

``math.pi`` — The Constant π
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. activecode:: math_pi_demo
   :nocodelens:

   import math

   print(f"Pi: {math.pi}")  # 3.141592653589793

   # Calculate the area of a circle
   radius = 5
   area = math.pi * radius ** 2
   print(f"Area of circle with radius {radius}: {area:.2f}")

   # Calculate the circumference of a circle
   circumference = 2 * math.pi * radius
   print(f"Circumference: {circumference:.2f}")

``math.e`` — Euler's Number
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. activecode:: math_e_demo
   :nocodelens:

   import math

   print(f"Euler's number: {math.e}")  # 2.718281828459045

   # Calculate compound interest
   principal = 1000  # Initial amount
   rate = 0.05       # 5% interest
   time = 10         # 10 years

   # Continuous compounding: A = P * e^(rt)
   amount = principal * math.e ** (rate * time)
   print(f"Amount after {time} years: ${amount:.2f}")

.. mchoice:: math_constants_understanding
   :answer_a: 3.14
   :answer_b: 22/7
   :answer_c: math.pi
   :answer_d: "pi"
   :correct: c
   :feedback_a: This is an approximation, but not as precise as math.pi.
   :feedback_b: This is a common approximation, but less accurate than math.pi.
   :feedback_c: Correct! math.pi provides the most precise value available in Python.
   :feedback_d: This is a string, not a number, and won't work in calculations.

   Which should you use for the most accurate circle calculations in Python?

Putting It All Together
------------------------

Let's combine several ``math`` functions in a practical example:

.. activecode:: math_comprehensive_example
   :nocodelens:

   import math

   # Problem: Calculate materials needed for a circular garden

   # Garden specifications
   radius_meters = 4.7
   post_spacing = 1.5  # meters between fence posts

   # Calculate circumference
   circumference = 2 * math.pi * radius_meters
   print(f"Circumference: {circumference:.2f} meters")

   # Calculate number of posts needed (round up)
   posts_needed = math.ceil(circumference / post_spacing)
   print(f"Fence posts needed: {posts_needed}")

   # Calculate area
   area = math.pi * radius_meters ** 2
   print(f"Area: {area:.2f} square meters")

   # Calculate bags of mulch (each covers 5 sq meters, round up)
   mulch_bags = math.ceil(area / 5)
   print(f"Mulch bags needed: {mulch_bags}")

Practice Problems
-----------------

.. activecode:: math_challenge_1
   :nocodelens:

   Write a function ``triangle_info(a, b)`` that takes two sides of a right triangle and returns a dictionary with:
   - The hypotenuse
   - The perimeter (sum of all three sides)
   - The area (using the formula: area = (a * b) / 2)

   Round all values to 2 decimal places.
   ~~~~
   import math

   def triangle_info(a, b):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_triangle_info(self):
           result = triangle_info(3, 4)
           self.assertAlmostEqual(result['hypotenuse'], 5.0, places=2)
           self.assertAlmostEqual(result['perimeter'], 12.0, places=2)
           self.assertAlmostEqual(result['area'], 6.0, places=2)

           result = triangle_info(5, 12)
           self.assertAlmostEqual(result['hypotenuse'], 13.0, places=2)
           self.assertAlmostEqual(result['perimeter'], 30.0, places=2)
           self.assertAlmostEqual(result['area'], 30.0, places=2)

   MyTests().main()

.. activecode:: math_challenge_2
   :nocodelens:

   Write a function ``boxes_needed(items, box_capacity)`` that calculates how many boxes are needed to pack a given number of items. Use the appropriate ``math`` function to ensure you have enough boxes.

   Also return how many items will be in the last (partially filled) box.
   Return a tuple: (total_boxes, items_in_last_box)
   ~~~~
   import math

   def boxes_needed(items, box_capacity):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_boxes_needed(self):
           self.assertEqual(boxes_needed(47, 10), (5, 7))
           self.assertEqual(boxes_needed(50, 10), (5, 10))
           self.assertEqual(boxes_needed(51, 10), (6, 1))
           self.assertEqual(boxes_needed(0, 10), (0, 0))

   MyTests().main()

Key Takeaways
-------------

.. admonition:: Summary

   * The ``math`` module provides essential mathematical functions and constants
   * **Rounding functions** each serve different purposes:

     * ``ceil()`` — Always round **up**
     * ``floor()`` — Always round **down**
     * ``trunc()`` — Remove decimals, move toward **zero**

   * **Key mathematical functions:**

     * ``factorial(n)`` — Calculate *n!*
     * ``sqrt(x)`` — Square root
     * ``hypot(x, y)`` — Hypotenuse/distance calculation

   * **Constants:** Use ``math.pi`` and ``math.e`` for precise values
   * Always ``import math`` before using these functions

----

What's Next?
------------

You've mastered the ``math`` module!

In the next lesson, you'll learn:

- **The ``platform`` module** for system information
- Extracting OS and hardware details
- Python implementation and version detection
- Cross-platform development awareness

.. note::

   **✅ Lesson 1 Complete!**

   **You've learned the ``math`` module:**

   - ✅ Import methods (``import math`` vs. ``from math import``)
   - ✅ Rounding functions (``ceil()``, ``floor()``, ``trunc()``)
   - ✅ Mathematical functions (``factorial()``, ``sqrt()``, ``hypot()``)
   - ✅ Mathematical constants (``math.pi``, ``math.e``)
   - ✅ Practical applications for real-world problems

   **Next:** Learn how to extract system and Python information with the ``platform`` module!
