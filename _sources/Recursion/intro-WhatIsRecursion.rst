..  Copyright (C)  Brad Miller, David Ranum, Jeffrey Elkner, Peter Wentworth, Allen B. Downey, Chris
    Meyers, and Dario Mitchell.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

What Is Recursion?
==================

Imagine you're standing between two parallel mirrors. When you look into one mirror, you see your reflection—but that reflection also contains a reflection of you in the other mirror, which contains another reflection, and another, stretching into infinity. Each reflection is slightly smaller than the one before it, creating an endless tunnel of images.

This phenomenon is an everyday example of **recursion**: a situation where something is defined in terms of itself.

The Essence of Recursion
-------------------------

Recursion is a problem-solving technique where a solution depends on solutions to smaller instances of the same problem. Think of it like this:

**Opening a set of Russian nesting dolls (matryoshka)**

1. Open the largest doll
2. Inside, you find... another doll that needs opening
3. Open that doll
4. Inside, you find... another doll that needs opening
5. Continue until you reach the smallest doll (which doesn't open)

Notice the pattern? The process of "opening a matryoshka" involves doing the same action (opening) on progressively smaller dolls, until you reach one that's so small it can't be opened anymore.

**The key ingredients:**
- **Self-similarity**: Each doll-opening task is essentially the same
- **Getting smaller**: Each doll inside is smaller than the last
- **A stopping point**: Eventually you reach a doll that doesn't open (the *base case*)

Real-World Recursion
--------------------

Recursion appears everywhere in nature and daily life:

**Fractals in Nature**
- Tree branches split into smaller branches, which split into even smaller branches
- River systems divide into tributaries, which divide into smaller streams
- Coastlines have jagged edges, and zooming in reveals smaller jagged edges that look similar

**Organizational Hierarchies**
- A company CEO manages directors
- Each director manages managers
- Each manager manages team leads
- Each team lead manages individual contributors

To find information in this structure, you might ask: "Who can tell me about Project X?" If the CEO doesn't know, they ask their directors. If the directors don't know, they ask their managers. The question cascades down until someone knows the answer, then the response bubbles back up.

Why Learn Recursion?
--------------------

Recursion is a fundamental concept in computer science because:

1. **Some problems are naturally recursive**: Tasks like navigating folder structures, processing tree-like data, 'fill' in a paint program, or solving puzzles often have elegant recursive solutions
2. **It provides an alternative way of thinking**: Where loops repeat actions, recursion breaks problems into smaller versions of themselves
3. **It's essential for advanced topics**: Understanding recursion unlocks data structures (trees, graphs), algorithms (sorting, searching), and even how your Python code itself is executed

.. mchoice:: recursion_concept_check_1
   :answer_a: A technique where a problem is broken into completely different smaller problems
   :answer_b: A technique where a problem is solved by breaking it into smaller instances of the same problem
   :answer_c: A type of loop that repeats forever
   :answer_d: A way to make programs run faster
   :correct: b
   :feedback_a: Not quite. The smaller problems must be the SAME TYPE as the original problem.
   :feedback_b: Exactly! Recursion solves problems by reducing them to smaller versions of themselves.
   :feedback_c: While recursion involves repetition, it's not a loop—and it must have a stopping point!
   :feedback_d: Recursion isn't primarily about speed; it's about problem structure and elegance.

   What is recursion?

.. mchoice:: recursion_concept_check_2
   :answer_a: To make the program run infinitely
   :answer_b: To make the recursion faster
   :answer_c: To provide a stopping point where the problem is so simple it can be solved directly
   :answer_d: To handle errors in the program
   :correct: c
   :feedback_a: The opposite! The base case STOPS infinite recursion.
   :feedback_b: The base case is about correctness, not speed.
   :feedback_c: Perfect! The base case is the "smallest doll" that doesn't need further opening.
   :feedback_d: Base cases aren't for error handling—they're an essential part of the solution structure.

   In the Russian doll analogy, the smallest doll that doesn't open represents what's called a "base case" in recursion. What is the purpose of a base case?

.. note::

   In the next section, we'll see how these recursive concepts translate into Python code. But understanding the **idea** of recursion—breaking problems into smaller versions of themselves—is the foundation for everything that follows.
