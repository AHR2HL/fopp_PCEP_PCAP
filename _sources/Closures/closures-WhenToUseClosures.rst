..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 5: Advanced Techniques
===============================

.. index:: advanced closures, decorator chains, introspection, functional programming

Introduction
------------

You've mastered closure basics, state management, encapsulation, and practical patterns. Now it's time to explore **advanced techniques** that professional Python developers use.

This section covers the sophisticated aspects of closures:

- **Decorators with arguments** — creating configurable decorators
- **Decorator stacking** — combining multiple decorators
- **Class decorators** — ``@property``, ``@staticmethod``, ``@classmethod``
- **Closure introspection** — examining closure internals with ``__closure__``
- **Performance considerations** — memory usage and optimization
- **Functional programming** — closures in map/filter/reduce pipelines
- **Best practices** — when to use closures vs. classes

By the end, you'll have **master-level understanding** of closures and be able to use them in sophisticated, production-ready code!

---

Decorators with Arguments
--------------------------

.. index:: parametrized decorators, decorator arguments, decorator factories

So far, we've seen simple decorators like ``@timer``. But what if you want a decorator that takes arguments, like ``@repeat(3)`` or ``@retry(max_attempts=5)``?

**The Pattern: Decorator Factory**

To create a decorator that accepts arguments, you need **three layers** of functions:

1. **Outer function** — takes decorator arguments, returns a decorator
2. **Middle function** — the actual decorator, takes the function to decorate
3. **Inner function** — the wrapper that executes when the decorated function is called

.. activecode:: closure_adv_decorator_with_args
   :language: python
   :caption: Decorator with Arguments Pattern

   def repeat(times):
       """Outer: takes decorator arguments"""

       def decorator(func):
           """Middle: the actual decorator"""

           def wrapper(*args, **kwargs):
               """Inner: executes when function is called"""
               results = []
               for i in range(times):
                   print(f"  Execution {i+1}/{times}")
                   result = func(*args, **kwargs)
                   results.append(result)
               return results

           return wrapper
       return decorator

   # Usage
   @repeat(3)
   def greet(name):
       return f"Hello, {name}!"

   results = greet("Alice")
   print(f"\nResults: {results}")

   print("\n" + "="*40 + "\n")

   # What's actually happening:
   # Step 1: repeat(3) returns decorator
   # Step 2: decorator(greet) returns wrapper
   # Step 3: greet = wrapper (greet is now the wrapper function)
   # Step 4: greet("Alice") calls wrapper("Alice")

   # Manual version (equivalent):
   def say_hi(name):
       return f"Hi, {name}!"

   say_hi = repeat(3)(say_hi)  # Two function calls!
   results = say_hi("Bob")
   print(f"Results: {results}")

**Output:**

::

     Execution 1/3
     Execution 2/3
     Execution 3/3

   Results: ['Hello, Alice!', 'Hello, Alice!', 'Hello, Alice!']

   ========================================

     Execution 1/3
     Execution 2/3
     Execution 3/3
   Results: ['Hi, Bob!', 'Hi, Bob!', 'Hi, Bob!']

.. important::
   **Three Layers of Closures**

   - ``@repeat(3)`` calls ``repeat(3)`` → returns ``decorator``
   - ``decorator`` wraps the function → returns ``wrapper``
   - ``wrapper`` executes when you call the decorated function

   Each layer creates a closure that captures variables from its enclosing scope!

---

**Practical Example: Configurable Retry**

.. activecode:: closure_adv_retry_with_config
   :language: python
   :caption: Retry Decorator with Configuration

   import time
   import random

   def retry(max_attempts=3, delay=0, exceptions=(Exception,)):
       """Decorator that retries on failure

       Args:
           max_attempts: Maximum number of attempts
           delay: Seconds to wait between attempts
           exceptions: Tuple of exceptions to catch
       """

       def decorator(func):
           def wrapper(*args, **kwargs):
               for attempt in range(1, max_attempts + 1):
                   try:
                       return func(*args, **kwargs)
                   except exceptions as e:
                       if attempt == max_attempts:
                           print(f"❌ Failed after {max_attempts} attempts: {e}")
                           raise
                       print(f"⚠️  Attempt {attempt} failed: {e}. Retrying...")
                       if delay > 0:
                           time.sleep(delay)
           return wrapper
       return decorator

   # Use with different configurations
   @retry(max_attempts=5, delay=0.1, exceptions=(ValueError,))
   def flaky_function():
       if random.random() < 0.6:  # 60% failure rate
           raise ValueError("Random failure!")
       return "Success! 🎉"

   @retry(max_attempts=3, exceptions=(ZeroDivisionError,))
   def divide(a, b):
       return a / b

   # Test flaky function
   result = flaky_function()
   print(result)

   # Test divide (will succeed)
   print(f"\n10 / 2 = {divide(10, 2)}")

**Output (varies due to randomness):**

::

   ⚠️  Attempt 1 failed: Random failure!. Retrying...
   ⚠️  Attempt 2 failed: Random failure!. Retrying...
   Success! 🎉

   10 / 2 = 5.0

---

**Practical Example: Configurable Cache**

.. activecode:: closure_adv_cache_with_config
   :language: python
   :caption: Cache Decorator with Size Limit

   def cache(max_size=None):
       """Decorator that caches results with optional size limit

       Args:
           max_size: Maximum cache size (None = unlimited)
       """

       def decorator(func):
           cache_dict = {}
           access_order = []  # Track access order for LRU

           def wrapper(*args):
               # Check cache
               if args in cache_dict:
                   # Move to end (most recently used)
                   access_order.remove(args)
                   access_order.append(args)
                   print(f"💾 Cache hit: {func.__name__}{args}")
                   return cache_dict[args]

               # Compute result
               print(f"⚙️  Computing: {func.__name__}{args}")
               result = func(*args)

               # Add to cache
               cache_dict[args] = result
               access_order.append(args)

               # Enforce size limit (LRU eviction)
               if max_size and len(cache_dict) > max_size:
                   evicted = access_order.pop(0)
                   del cache_dict[evicted]
                   print(f"🗑️  Evicted: {evicted}")

               return result

           return wrapper
       return decorator

   @cache(max_size=3)
   def expensive(x):
       return x ** 2

   # Fill cache
   print(expensive(1))
   print(expensive(2))
   print(expensive(3))

   # Cache hits
   print(expensive(1))
   print(expensive(2))

   # Overflow cache (should evict 3)
   print(expensive(4))

   # Try to access evicted item (cache miss)
   print(expensive(3))

**Output:**

::

   ⚙️  Computing: expensive(1,)
   1
   ⚙️  Computing: expensive(2,)
   4
   ⚙️  Computing: expensive(3,)
   9
   💾 Cache hit: expensive(1,)
   1
   💾 Cache hit: expensive(2,)
   4
   ⚙️  Computing: expensive(4,)
   🗑️  Evicted: (3,)
   16
   ⚙️  Computing: expensive(3,)
   9

.. note::
   **Python's Built-in LRU Cache**

   Python's standard library includes ``functools.lru_cache`` with this exact functionality:

   .. code-block:: python

      from functools import lru_cache

      @lru_cache(maxsize=128)
      def expensive_function(x):
          return x ** 2

   But understanding how it works with closures is valuable!

---

Decorator Stacking and Chains
------------------------------

.. index:: decorator stacking, decorator chains, multiple decorators

You can apply **multiple decorators** to a single function. They're applied **bottom-to-top** (closest to the function first).

**Basic Stacking**

.. activecode:: closure_adv_stacking_basic
   :language: python
   :caption: Stacking Multiple Decorators

   def bold(func):
       """Wrap result in **bold**"""
       def wrapper(*args, **kwargs):
           result = func(*args, **kwargs)
           return f"**{result}**"
       return wrapper

   def italic(func):
       """Wrap result in *italic*"""
       def wrapper(*args, **kwargs):
           result = func(*args, **kwargs)
           return f"*{result}*"
       return wrapper

   def uppercase(func):
       """Convert result to uppercase"""
       def wrapper(*args, **kwargs):
           result = func(*args, **kwargs)
           return result.upper()
       return wrapper

   # Stack decorators
   @bold
   @italic
   @uppercase
   def greet(name):
       return f"hello, {name}"

   print(greet("Alice"))

   # What's happening:
   # Step 1: greet = uppercase(greet)  -> returns "HELLO, ALICE"
   # Step 2: greet = italic(greet)     -> returns "*HELLO, ALICE*"
   # Step 3: greet = bold(greet)       -> returns "***HELLO, ALICE***"

**Output:**

::

   ***HELLO, ALICE***

.. important::
   **Decorator Application Order**

   Decorators are applied **bottom-to-top**:

   .. code-block:: python

      @decorator1
      @decorator2
      @decorator3
      def func():
          pass

      # Equivalent to:
      func = decorator1(decorator2(decorator3(func)))

   The decorator **closest to the function** is applied first!

---

**Practical Stacking: Timing + Logging + Caching**

.. activecode:: closure_adv_stacking_practical
   :language: python
   :caption: Combining Timing, Logging, and Caching

   import time

   def timer(func):
       """Time function execution"""
       def wrapper(*args, **kwargs):
           start = time.time()
           result = func(*args, **kwargs)
           elapsed = time.time() - start
           print(f"⏱️  {func.__name__} took {elapsed:.4f}s")
           return result
       return wrapper

   def logger(func):
       """Log function calls"""
       def wrapper(*args, **kwargs):
           print(f"📞 Calling {func.__name__}{args}")
           result = func(*args, **kwargs)
           print(f"📤 {func.__name__} returned {result}")
           return result
       return wrapper

   def memoize(func):
       """Cache results"""
       cache = {}
       def wrapper(*args):
           if args in cache:
               print(f"💾 Cache hit!")
               return cache[args]
           result = func(*args)
           cache[args] = result
           return result
       return wrapper

   @timer
   @logger
   @memoize
   def fibonacci(n):
       """Compute Fibonacci (slow without memoization)"""
       if n <= 1:
           return n
       return fibonacci(n - 1) + fibonacci(n - 2)

   print("First call to fibonacci(5):")
   print(fibonacci(5))
   print("\n" + "="*40 + "\n")

   print("Second call to fibonacci(5):")
   print(fibonacci(5))

**Output:**

::

   First call to fibonacci(5):
   📞 Calling fibonacci(5,)
   📞 Calling fibonacci(4,)
   📞 Calling fibonacci(3,)
   📞 Calling fibonacci(2,)
   📞 Calling fibonacci(1,)
   📤 fibonacci returned 1
   📞 Calling fibonacci(0,)
   📤 fibonacci returned 0
   📤 fibonacci returned 1
   💾 Cache hit!
   📤 fibonacci returned 2
   💾 Cache hit!
   📤 fibonacci returned 3
   💾 Cache hit!
   📤 fibonacci returned 5
   ⏱️  fibonacci took 0.0001s
   5

   ========================================

   Second call to fibonacci(5):
   📞 Calling fibonacci(5,)
   💾 Cache hit!
   📤 fibonacci returned 5
   ⏱️  fibonacci took 0.0000s
   5

The decorators work together: memoization speeds it up, logger shows what's happening, timer measures total time!

---

Class Decorators
----------------

.. index:: class decorators, @property, @staticmethod, @classmethod

Python has built-in class decorators that use closures internally:

**@property — Computed Attributes**

.. activecode:: closure_adv_property_decorator
   :language: python
   :caption: @property Decorator

   class Circle:
       def __init__(self, radius):
           self._radius = radius

       @property
       def radius(self):
           """Get radius"""
           return self._radius

       @radius.setter
       def radius(self, value):
           """Set radius with validation"""
           if value <= 0:
               raise ValueError("Radius must be positive")
           self._radius = value

       @property
       def diameter(self):
           """Computed property"""
           return self._radius * 2

       @property
       def area(self):
           """Computed property"""
           return 3.14159 * self._radius ** 2

   # Use like attributes (but they're actually methods!)
   c = Circle(5)
   print(f"Radius: {c.radius}")
   print(f"Diameter: {c.diameter}")
   print(f"Area: {c.area:.2f}")

   # Setter works too
   c.radius = 10
   print(f"\nAfter changing radius to 10:")
   print(f"Diameter: {c.diameter}")
   print(f"Area: {c.area:.2f}")

   # Validation
   try:
       c.radius = -5
   except ValueError as e:
       print(f"\n❌ {e}")

**Output:**

::

   Radius: 5
   Diameter: 10
   Area: 78.54

   After changing radius to 10:
   Diameter: 20
   Area: 314.16

   ❌ Radius must be positive

``@property`` creates a closure that stores the getter, setter, and deleter methods!

---

**@staticmethod and @classmethod**

.. activecode:: closure_adv_class_method_decorators
   :language: python
   :caption: @staticmethod and @classmethod

   class MathUtils:
       pi = 3.14159

       @staticmethod
       def add(a, b):
           """Static method: no access to class or instance"""
           return a + b

       @classmethod
       def circle_area(cls, radius):
           """Class method: receives class as first argument"""
           return cls.pi * radius ** 2

       def instance_method(self):
           """Regular instance method"""
           return "I need an instance!"

   # Static method: call without instance
   print(MathUtils.add(5, 3))

   # Class method: receives the class
   print(f"Area: {MathUtils.circle_area(5):.2f}")

   # Instance method: needs an instance
   obj = MathUtils()
   print(obj.instance_method())

   # Can also call on instance
   print(obj.add(10, 20))

**Output:**

::

   8
   Area: 78.54
   I need an instance!
   30

---

Closure Introspection
----------------------

.. index:: closure introspection, __closure__, cell_contents, debugging closures

Python lets you **inspect closures** at runtime to see captured variables!

**The __closure__ Attribute**

.. activecode:: closure_adv_introspection_basic
   :language: python
   :caption: Inspecting Closures

   def make_counter(start=0, step=1):
       count = start

       def increment():
           nonlocal count
           count += step
           return count

       return increment

   counter = make_counter(10, 5)

   # Inspect the closure
   print(f"Is it a closure? {counter.__closure__ is not None}")
   print(f"Number of captured variables: {len(counter.__closure__)}")

   # Examine each captured variable
   for i, cell in enumerate(counter.__closure__):
       print(f"Variable {i}: {cell.cell_contents}")

   print("\nAfter calling counter():")
   result = counter()
   print(f"Result: {result}")

   # Check captured variables again
   print("\nCaptured variables after call:")
   for i, cell in enumerate(counter.__closure__):
       print(f"Variable {i}: {cell.cell_contents}")

**Output:**

::

   Is it a closure? True
   Number of captured variables: 2
   Variable 0: 10
   Variable 1: 5

   After calling counter():
   Result: 15

   Captured variables after call:
   Variable 0: 15
   Variable 1: 5

.. note::
   **Closure Cells**

   - ``__closure__`` is a tuple of ``cell`` objects
   - Each ``cell`` wraps a captured variable
   - Access the value with ``cell.cell_contents``
   - ``__closure__`` is ``None`` for non-closures

---

**Introspection for Debugging**

.. activecode:: closure_adv_introspection_debugging
   :language: python
   :caption: Debugging Tool for Closures

   def inspect_closure(func):
       """Inspect a closure's captured variables"""
       print(f"🔍 Inspecting: {func.__name__}")

       if func.__closure__ is None:
           print("  ❌ Not a closure!")
           return

       print(f"  ✅ Closure with {len(func.__closure__)} captured variable(s):")

       # Get variable names from code object
       var_names = func.__code__.co_freevars

       for name, cell in zip(var_names, func.__closure__):
           print(f"     - {name} = {cell.cell_contents}")

   # Test with various closures
   def make_adder(x):
       def add(y):
           return x + y
       return add

   def make_greeter(greeting, punctuation):
       def greet(name):
           return f"{greeting}, {name}{punctuation}"
       return greet

   add_five = make_adder(5)
   enthusiastic = make_greeter("Hello", "!!!")

   inspect_closure(add_five)
   print()
   inspect_closure(enthusiastic)

   # Not a closure
   def regular_function():
       return "I'm not a closure"

   print()
   inspect_closure(regular_function)

**Output:**

::

   🔍 Inspecting: add
     ✅ Closure with 1 captured variable(s):
        - x = 5

   🔍 Inspecting: greet
     ✅ Closure with 2 captured variable(s):
        - greeting = Hello
        - punctuation = !!!

   🔍 Inspecting: regular_function
     ❌ Not a closure!

---

Performance Considerations
---------------------------

.. index:: closure performance, memory usage, optimization

Closures have performance implications you should understand:

**Memory Usage**

.. activecode:: closure_adv_memory_usage
   :language: python
   :caption: Closure Memory Overhead

   import sys

   # Regular function
   def regular_add(x, y):
       return x + y

   # Closure
   def make_adder(x):
       def add(y):
           return x + y
       return add

   add_five = make_adder(5)

   # Compare sizes
   print("Memory usage:")
   print(f"  Regular function: {sys.getsizeof(regular_add)} bytes")
   print(f"  Closure:          {sys.getsizeof(add_five)} bytes")

   # The closure also stores the cell
   if add_five.__closure__:
       cell_size = sum(sys.getsizeof(cell) for cell in add_five.__closure__)
       print(f"  Closure cells:    {cell_size} bytes")
       print(f"  Total closure:    {sys.getsizeof(add_five) + cell_size} bytes")

**Output:**

::

   Memory usage:
     Regular function: 136 bytes
     Closure:          136 bytes
     Closure cells:    48 bytes
     Total closure:    184 bytes

Closures have overhead for storing captured variables, but it's usually negligible!

---

**Performance: Closures vs. Classes**

.. activecode:: closure_adv_performance_comparison
   :language: python
   :caption: Speed Comparison

   import time

   # Closure implementation
   def make_counter_closure():
       count = 0
       def increment():
           nonlocal count
           count += 1
           return count
       return increment

   # Class implementation
   class Counter:
       def __init__(self):
           self.count = 0
       def increment(self):
           self.count += 1
           return self.count

   # Benchmark
   iterations = 1000000

   # Test closure
   counter_closure = make_counter_closure()
   start = time.time()
   for _ in range(iterations):
       counter_closure()
   closure_time = time.time() - start

   # Test class
   counter_class = Counter()
   start = time.time()
   for _ in range(iterations):
       counter_class.increment()
   class_time = time.time() - start

   print(f"Performance ({iterations:,} iterations):")
   print(f"  Closure: {closure_time:.4f} seconds")
   print(f"  Class:   {class_time:.4f} seconds")
   print(f"  Winner:  {'Closure' if closure_time < class_time else 'Class'} "
         f"(~{max(closure_time, class_time) / min(closure_time, class_time):.1f}x faster)")

**Output (varies by system):**

::

   Performance (1,000,000 iterations):
     Closure: 0.0842 seconds
     Class:   0.0895 seconds
     Winner:  Closure (~1.1x faster)

Closures are typically **slightly faster** than classes for simple state, but the difference is minimal!

.. important::
   **When to Use Closures vs. Classes**

   **Use Closures when:**
   - Simple state (1-3 variables)
   - Single primary operation
   - Need true privacy
   - Creating function factories
   - Working with decorators

   **Use Classes when:**
   - Complex state (many attributes)
   - Multiple related methods
   - Need inheritance
   - Need clear structure and readability
   - Building reusable components

---

Functional Programming Patterns
--------------------------------

.. index:: functional programming, map, filter, reduce, lambda with closures

Closures enable functional programming patterns in Python:

**Map/Filter with Closures**

.. activecode:: closure_adv_functional_map_filter
   :language: python
   :caption: Closures in Map/Filter

   # Traditional functions
   numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

   # Using lambda (anonymous closures!)
   doubled = list(map(lambda x: x * 2, numbers))
   print(f"Doubled: {doubled}")

   evens = list(filter(lambda x: x % 2 == 0, numbers))
   print(f"Evens: {evens}")

   # Using closure factories for more flexibility
   def make_multiplier(factor):
       return lambda x: x * factor

   def make_divisibility_checker(divisor):
       return lambda x: x % divisor == 0

   # Create specialized functions
   triple = make_multiplier(3)
   divisible_by_3 = make_divisibility_checker(3)

   tripled = list(map(triple, numbers))
   print(f"\nTripled: {tripled}")

   div_by_3 = list(filter(divisible_by_3, numbers))
   print(f"Divisible by 3: {div_by_3}")

**Output:**

::

   Doubled: [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
   Evens: [2, 4, 6, 8, 10]

   Tripled: [3, 6, 9, 12, 15, 18, 21, 24, 27, 30]
   Divisible by 3: [3, 6, 9]

---

**Reduce with Closures**

.. activecode:: closure_adv_functional_reduce
   :language: python
   :caption: Closures with functools.reduce

   from functools import reduce

   numbers = [1, 2, 3, 4, 5]

   # Built-in reduce patterns
   total = reduce(lambda acc, x: acc + x, numbers)
   print(f"Sum: {total}")

   product = reduce(lambda acc, x: acc * x, numbers)
   print(f"Product: {product}")

   # Custom reducer with closure
   def make_custom_reducer(operation, initial=0):
       """Factory that creates custom reducers"""
       def reducer(acc, x):
           return operation(acc, x)
       return reducer, initial

   # Create custom reducers
   max_reducer, _ = make_custom_reducer(lambda a, b: a if a > b else b)
   concat_reducer, _ = make_custom_reducer(lambda a, b: f"{a},{b}")

   maximum = reduce(max_reducer, numbers)
   print(f"\nMaximum: {maximum}")

   words = ['apple', 'banana', 'cherry']
   concatenated = reduce(concat_reducer, words)
   print(f"Concatenated: {concatenated}")

**Output:**

::

   Sum: 15
   Product: 120

   Maximum: 5
   Concatenated: apple,banana,cherry

---

**Pipeline Pattern**

.. activecode:: closure_adv_functional_pipeline
   :language: python
   :caption: Function Pipelines with Closures

   def compose(*functions):
       """Compose functions right-to-left: compose(f, g, h)(x) = f(g(h(x)))"""
       def inner(arg):
           result = arg
           for func in reversed(functions):
               result = func(result)
           return result
       return inner

   # Individual operations
   def double(x):
       return x * 2

   def add_ten(x):
       return x + 10

   def square(x):
       return x ** 2

   # Create pipeline
   pipeline = compose(square, add_ten, double)

   # Test: double(5) = 10, then 10 + 10 = 20, then 20^2 = 400
   result = pipeline(5)
   print(f"Pipeline result: {result}")

   # More complex example: data processing pipeline
   def make_transformer(operation, description):
       """Factory for labeled transformations"""
       def transform(data):
           print(f"  {description}")
           return operation(data)
       return transform

   # Build data processing pipeline
   data_pipeline = compose(
       make_transformer(lambda data: [x for x in data if x > 0], "Filter positives"),
       make_transformer(lambda data: [x ** 2 for x in data], "Square values"),
       make_transformer(lambda data: sorted(data), "Sort")
   )

   print("\nData processing:")
   input_data = [-2, 5, -1, 3, 8, -3, 1]
   output = data_pipeline(input_data)
   print(f"Input:  {input_data}")
   print(f"Output: {output}")

**Output:**

::

   Pipeline result: 400

   Data processing:
     Sort
     Square values
     Filter positives
   Input:  [-2, 5, -1, 3, 8, -3, 1]
   Output: [1, 9, 25, 64]

---

Best Practices and Patterns
----------------------------

.. index:: closure best practices, when to use closures

**Checklist: When to Use Closures**

.. code-block:: text

   ✅ Use closures when:
      □ Creating decorators
      □ Need true private state (not accessible from outside)
      □ Building function factories
      □ Simple state (1-3 variables)
      □ Single primary operation
      □ Callbacks need context
      □ Functional programming patterns

   ⚠️  Consider alternatives when:
      □ Complex state (many variables)
      □ Multiple related methods needed
      □ Need inheritance or polymorphism
      □ Team unfamiliar with closures
      □ Need clear OOP structure

---

**Common Pitfalls**

.. activecode:: closure_adv_pitfalls
   :language: python
   :caption: Common Closure Pitfalls

   # PITFALL 1: Loop variable capture
   print("❌ WRONG: Loop variable capture")
   functions = []
   for i in range(3):
       def func():
           return i  # All will see final i!
       functions.append(func)

   print([f() for f in functions])  # All return 2!

   # FIX: Use default argument
   print("\n✅ RIGHT: Default argument")
   functions = []
   for i in range(3):
       def func(x=i):  # Capture current value
           return x
       functions.append(func)

   print([f() for f in functions])  # Returns [0, 1, 2]

   # PITFALL 2: Forgetting nonlocal
   print("\n❌ WRONG: Missing nonlocal")
   def make_counter():
       count = 0
       def increment():
           count += 1  # Error! Can't modify without nonlocal
           return count
       return increment

   try:
       counter = make_counter()
       counter()
   except UnboundLocalError as e:
       print(f"Error: {e}")

   # FIX: Use nonlocal
   print("\n✅ RIGHT: Using nonlocal")
   def make_counter():
       count = 0
       def increment():
           nonlocal count
           count += 1
           return count
       return increment

   counter = make_counter()
   print(counter())  # Works!

**Output:**

::

   ❌ WRONG: Loop variable capture
   [2, 2, 2]

   ✅ RIGHT: Default argument
   [0, 1, 2]

   ❌ WRONG: Missing nonlocal
   Error: local variable 'count' referenced before assignment

   ✅ RIGHT: Using nonlocal
   1

---

**Code Style Guidelines**

.. code-block:: python

   # ✅ GOOD: Clear naming
   def make_validator(min_value, max_value):
       """Clear purpose and good names"""
       def validate(value):
           return min_value <= value <= max_value
       return validate

   # ❌ BAD: Unclear naming
   def f(a, b):
       def g(c):
           return a <= c <= b
       return g

   # ✅ GOOD: Docstrings
   def rate_limit(calls, period):
       """
       Rate limiter decorator.

       Args:
           calls: Maximum calls allowed
           period: Time period in seconds

       Returns:
           Decorator function
       """
       def decorator(func):
           # Implementation...
           pass
       return decorator

   # ✅ GOOD: Comments for complex closures
   def memoize_with_ttl(ttl):
       def decorator(func):
           cache = {}  # {args: (result, timestamp)}

           def wrapper(*args):
               # Check if cached value is still valid
               if args in cache:
                   result, timestamp = cache[args]
                   if time.time() - timestamp < ttl:
                       return result  # Use cached

               # Compute and cache
               result = func(*args)
               cache[args] = (result, time.time())
               return result

           return wrapper
       return decorator

---

Practice Challenges
--------------------

**Challenge 1: Build a Chaining Decorator**

.. activecode:: closure_adv_practice_chaining
   :language: python
   :caption: Challenge - Method Chaining with Closures

   def chainable(func):
       """
       Decorator that makes a function chainable.
       The function should return self to allow method chaining.

       Example:
           @chainable
           def add_item(self, item):
               self.items.append(item)
               # return self automatically added!
       """
       # TODO: Your code here
       pass

   # Test code (uncomment when ready):
   # class Builder:
   #     def __init__(self):
   #         self.items = []
   #
   #     @chainable
   #     def add(self, item):
   #         self.items.append(item)
   #
   #     @chainable
   #     def remove(self, item):
   #         self.items.remove(item)
   #
   #     def build(self):
   #         return self.items.copy()
   #
   # result = Builder().add(1).add(2).add(3).remove(2).build()
   # print(result)  # Should print: [1, 3]

.. reveal:: closure_adv_practice_chaining_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def chainable(func):
          def wrapper(self, *args, **kwargs):
              func(self, *args, **kwargs)
              return self  # Always return self for chaining
          return wrapper

---

**Challenge 2: Build a Throttle Decorator**

.. activecode:: closure_adv_practice_throttle
   :language: python
   :caption: Challenge - Throttle Decorator

   import time

   def throttle(min_interval):
       """
       Decorator that prevents a function from being called
       more frequently than min_interval seconds.

       If called too soon, should print a message and skip execution.

       Example: @throttle(2) allows calls at most every 2 seconds
       """
       # TODO: Your code here
       # Hint: Store the timestamp of the last successful call
       pass

   # Test code (uncomment when ready):
   # @throttle(1.0)
   # def save_file():
   #     print("💾 File saved!")
   #
   # save_file()  # Works
   # save_file()  # Too soon, skipped
   # time.sleep(1.1)
   # save_file()  # Works again

.. reveal:: closure_adv_practice_throttle_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def throttle(min_interval):
          def decorator(func):
              last_called = [0]  # Use list for mutable default

              def wrapper(*args, **kwargs):
                  current = time.time()
                  elapsed = current - last_called[0]

                  if elapsed < min_interval:
                      print(f"⏳ Throttled! Wait {min_interval - elapsed:.1f}s")
                      return None

                  last_called[0] = current
                  return func(*args, **kwargs)

              return wrapper
          return decorator

---

**Challenge 3: Build a Call Counter with Reset**

.. activecode:: closure_adv_practice_call_counter
   :language: python
   :caption: Challenge - Call Counter with Reset

   def counted(func):
       """
       Decorator that counts function calls.
       Adds a 'call_count' attribute and 'reset_count()' method to the function.

       Example:
           @counted
           def greet():
               return "Hello!"

           greet()
           greet()
           print(greet.call_count)  # 2
           greet.reset_count()
           print(greet.call_count)  # 0
       """
       # TODO: Your code here
       pass

   # Test code (uncomment when ready):
   # @counted
   # def say_hello():
   #     return "Hello!"
   #
   # say_hello()
   # say_hello()
   # say_hello()
   # print(f"Calls: {say_hello.call_count}")
   # say_hello.reset_count()
   # say_hello()
   # print(f"Calls after reset: {say_hello.call_count}")

.. reveal:: closure_adv_practice_call_counter_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def counted(func):
          count = [0]  # Use list for mutability

          def wrapper(*args, **kwargs):
              count[0] += 1
              return func(*args, **kwargs)

          def reset():
              count[0] = 0

          wrapper.call_count = property(lambda self: count[0])
          wrapper.reset_count = reset

          # Simpler approach:
          # wrapper.reset_count = lambda: count.__setitem__(0, 0)

          return wrapper

      # Or even simpler with attributes:
      def counted(func):
          def wrapper(*args, **kwargs):
              wrapper.call_count += 1
              return func(*args, **kwargs)

          wrapper.call_count = 0
          wrapper.reset_count = lambda: setattr(wrapper, 'call_count', 0)

          return wrapper

---

Check Your Understanding
-------------------------

.. mchoice:: closure_adv_decorator_args
   :answer_a: One layer: the decorator function
   :answer_b: Two layers: decorator and wrapper
   :answer_c: Three layers: factory, decorator, and wrapper
   :answer_d: It depends on how many arguments the decorator takes
   :correct: c
   :feedback_a: A decorator with arguments needs more layers!
   :feedback_b: You need an outer layer to receive the decorator arguments.
   :feedback_c: Correct! Factory (takes args) → Decorator (takes func) → Wrapper (replaces func).
   :feedback_d: It's always three layers for decorators with arguments.

   How many function layers do you need for a decorator that takes arguments like ``@retry(max_attempts=3)``?

.. mchoice:: closure_adv_nested_decorators
   :answer_a: Top to bottom (farthest from function first)
   :answer_b: Bottom to top (closest to function first)
   :answer_c: Randomly, depending on Python version
   :answer_d: All at once, in parallel
   :correct: b
   :feedback_a: The opposite! Decorators apply from bottom to top.
   :feedback_b: Correct! The decorator closest to the function is applied first.
   :feedback_c: Decorator order is deterministic and consistent.
   :feedback_d: Decorators are applied sequentially, not in parallel.

   When multiple decorators are stacked, in what order are they applied?

.. mchoice:: closure_adv_introspection
   :answer_a: __closure__ and cell_contents
   :answer_b: __dict__ and __slots__
   :answer_c: __code__ and __name__
   :answer_d: __vars__ and __locals__
   :correct: a
   :feedback_a: Correct! __closure__ contains cells, each with cell_contents.
   :feedback_b: These are for general object attributes, not closures specifically.
   :feedback_c: These provide code and name info, but not captured variables.
   :feedback_d: __vars__ doesn't exist, and __locals__ isn't for closures.

   Which attributes let you inspect captured variables in a closure?

.. mchoice:: closure_adv_memory
   :answer_a: Closures always use significantly more memory than classes
   :answer_b: Classes always use significantly more memory than closures
   :answer_c: Closures and classes have similar memory usage for simple state
   :answer_d: Closures can't be compared to classes in terms of memory
   :correct: c
   :feedback_a: Closures actually have slightly less overhead than classes.
   :feedback_b: The difference is small, not significant.
   :feedback_c: Correct! For simple state, memory usage is comparable (closures slightly smaller).
   :feedback_d: They can and should be compared when choosing between them!

   What's the memory overhead of closures compared to classes?

.. mchoice:: closure_adv_functional
   :answer_a: Closures can't be used with map() and filter()
   :answer_b: Lambda functions are closures when they capture variables
   :answer_c: Functional programming in Python doesn't use closures
   :answer_d: Only named functions can be closures, not lambdas
   :correct: b
   :feedback_a: Closures work perfectly with map() and filter()!
   :feedback_b: Correct! Lambdas are anonymous functions and can be closures if they capture variables.
   :feedback_c: Closures are fundamental to functional programming!
   :feedback_d: Both lambda and named functions can be closures.

   How do closures relate to functional programming in Python?

---

Key Takeaways
-------------

.. important::
   **Section 5 Summary: Advanced Techniques**

   ✅ **Decorators with Arguments**
      - Need **three layers**: factory (args) → decorator (func) → wrapper (execution)
      - Pattern: ``@decorator(arg)`` calls ``decorator(arg)`` first, then decorates
      - Examples: ``@retry(max=3)``, ``@cache(size=100)``, ``@rate_limit(calls=5)``

   ✅ **Decorator Stacking**
      - Applied **bottom-to-top** (closest to function first)
      - Each decorator wraps the result of the previous one
      - Combine timing + logging + caching for powerful effects

   ✅ **Class Decorators**
      - ``@property`` — computed attributes with getters/setters
      - ``@staticmethod`` — no class or instance access
      - ``@classmethod`` — receives class as first argument
      - All use closures internally!

   ✅ **Closure Introspection**
      - ``func.__closure__`` — tuple of cell objects
      - ``cell.cell_contents`` — the captured value
      - ``func.__code__.co_freevars`` — variable names
      - Useful for debugging and understanding closure behavior

   ✅ **Performance**
      - Closures have small memory overhead (cells)
      - Slightly faster than classes for simple operations
      - **Use closures for:** simple state, single operation, privacy
      - **Use classes for:** complex state, multiple methods, inheritance

   ✅ **Functional Programming**
      - Closures enable map/filter/reduce patterns
      - Lambda functions are closures when they capture variables
      - Function composition and pipelines
      - Factory pattern for creating specialized functions

   ✅ **Best Practices**
      - Clear naming and docstrings
      - Avoid loop variable capture pitfall (use default arguments)
      - Remember ``nonlocal`` for modifying captured variables
      - Choose closures vs. classes based on complexity

---

What's Next?
------------

**Congratulations! You've completed the Closures and Advanced Functions chapter!** 🎉

You now have **expert-level knowledge** of:

- ✅ Closure fundamentals (definition, free variables, scope)
- ✅ State management and the ``nonlocal`` keyword
- ✅ Encapsulation and data privacy
- ✅ Practical patterns (decorators, callbacks, factories, memoization)
- ✅ Advanced techniques (introspection, performance, functional programming)

**You can now:**

- Build sophisticated decorators with arguments
- Create function factories for code reuse
- Optimize performance with memoization
- Use closures for true data privacy
- Recognize closure patterns in professional Python libraries
- Make informed decisions about when to use closures vs. classes

**Next in your PCAP journey:**

- **Chapter 29: PCAP Power Topics** — Advanced exception handling, file I/O, and string methods
- **Real-world projects** — Apply your closure skills to build production-ready applications!

Keep coding, and remember: closures are a **superpower** in Python! 🚀