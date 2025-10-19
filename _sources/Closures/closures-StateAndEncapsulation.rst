..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 3: State and Encapsulation
===================================

.. index:: closure state, encapsulation, data privacy, closure vs class

Introduction
------------

You've learned how to create closures that maintain state using ``nonlocal``. But there's something special about closure state: **it's completely private**. No outside code can directly access or tamper with the variables captured in a closure.

This makes closures powerful tools for **encapsulation** — hiding implementation details and protecting data integrity.

In this section, you'll learn:

- How closures provide automatic data privacy
- Building "object-like" structures with closures
- The difference between closures and classes for state management
- Real-world scenarios where encapsulation matters
- When to use closures vs. classes

By the end, you'll understand why closures are often called "the poor man's objects" — and when they're actually the *better* choice!

---

The Privacy Problem
-------------------

.. index:: data privacy, variable access, global state

Let's start with a common problem: unprotected state.

.. activecode:: closure_no_privacy
   :language: python
   :caption: The Problem with Global State

   # Global counter (anyone can modify it!)
   counter = 0

   def increment():
       global counter
       counter += 1
       return counter

   # Normal usage
   print(f"Count: {increment()}")
   print(f"Count: {increment()}")

   # Oops! Someone directly modifies the counter
   counter = 1000000
   print(f"Count: {increment()}")  # Unexpected jump!

   # Or even worse
   counter = "HACKED"
   try:
       print(f"Count: {increment()}")
   except TypeError as e:
       print(f"❌ Error: {e}")

**Output:**

::

   Count: 1
   Count: 2
   Count: 1000001
   ❌ Error: unsupported operand type(s) for +=: 'str' and 'int'

The problem? **Anyone** can modify ``counter`` directly, breaking our increment logic.

---

Closures Provide Automatic Privacy
-----------------------------------

.. index:: closure privacy, protected state, data hiding

Now let's use a closure for the same counter:

.. activecode:: closure_with_privacy
   :language: python
   :caption: Closure Privacy in Action

   def make_counter():
       count = 0  # Private variable!

       def increment():
           nonlocal count
           count += 1
           return count

       return increment

   counter = make_counter()

   # Normal usage works
   print(f"Count: {counter()}")
   print(f"Count: {counter()}")
   print(f"Count: {counter()}")

   # Try to access count directly
   try:
       print(counter.count)
   except AttributeError:
       print("❌ Cannot access 'count' directly!")

   # Try to modify it
   try:
       counter.count = 1000000
       print(f"Count: {counter()}")  # Still works correctly!
   except:
       pass

**Output:**

::

   Count: 1
   Count: 2
   Count: 3
   ❌ Cannot access 'count' directly!
   Count: 4

**Key Insight:** There's **no way** to directly access or modify ``count`` from outside the closure. It's truly private!

.. important::
   **Closure Privacy Guarantee**

   Variables in the enclosing scope are **completely inaccessible** from outside the closure:

   - ✅ You can only interact through the returned function(s)
   - ✅ No accidental modifications
   - ✅ No type mismatches
   - ✅ Implementation details are hidden

---

Building Object-Like Structures
--------------------------------

.. index:: closure as object, object-oriented closure, stateful functions

Closures can mimic objects by returning multiple functions that operate on shared private state:

.. activecode:: closure_bank_account
   :language: python
   :caption: Bank Account Using Closures

   def make_bank_account(account_holder, initial_balance):
       # Private variables (completely inaccessible from outside)
       balance = initial_balance
       holder = account_holder
       transaction_count = 0

       def deposit(amount):
           nonlocal balance, transaction_count
           if amount <= 0:
               print("❌ Deposit amount must be positive")
               return balance
           balance += amount
           transaction_count += 1
           print(f"✅ Deposited ${amount:.2f}")
           return balance

       def withdraw(amount):
           nonlocal balance, transaction_count
           if amount <= 0:
               print("❌ Withdrawal amount must be positive")
               return balance
           if amount > balance:
               print(f"❌ Insufficient funds (balance: ${balance:.2f})")
               return balance
           balance -= amount
           transaction_count += 1
           print(f"✅ Withdrew ${amount:.2f}")
           return balance

       def get_balance():
           return balance

       def get_info():
           return {
               'holder': holder,
               'balance': balance,
               'transactions': transaction_count
           }

       # Return a dictionary of functions (like methods)
       return {
           'deposit': deposit,
           'withdraw': withdraw,
           'balance': get_balance,
           'info': get_info
       }

   # Create an account
   alice_account = make_bank_account("Alice", 1000)

   # Use the "methods"
   print(f"Starting balance: ${alice_account['balance']():.2f}")
   alice_account['deposit'](500)
   alice_account['withdraw'](200)
   alice_account['withdraw'](2000)  # Should fail

   print("\nAccount Info:")
   info = alice_account['info']()
   for key, value in info.items():
       print(f"  {key}: {value}")

   # Try to cheat
   print("\n🔒 Security check:")
   print(f"Can we access 'balance' directly? {hasattr(alice_account, 'balance')}")
   print(f"Can we modify transaction_count? No way to even try!")

**Output:**

::

   Starting balance: $1000.00
   ✅ Deposited $500.00
   ✅ Withdrew $200.00
   ❌ Insufficient funds (balance: $1300.00)

   Account Info:
     holder: Alice
     balance: 1300
     transactions: 2

   🔒 Security check:
   Can we access 'balance' directly? False
   Can we modify transaction_count? No way to even try!

This is **object-oriented programming** using closures! The private variables (``balance``, ``holder``, ``transaction_count``) are completely protected.

---

Closures vs. Classes
---------------------

.. index:: closure vs class, when to use closure, when to use class

Let's compare the same functionality using both approaches:

.. activecode:: closure_vs_class_comparison
   :language: python
   :caption: Closure vs. Class Side-by-Side

   # Using a CLASS
   class Counter:
       def __init__(self, start=0):
           self._count = start  # Convention: _ means "private" (but not enforced!)

       def increment(self):
           self._count += 1
           return self._count

       def reset(self):
           self._count = 0

   # Using a CLOSURE
   def make_counter(start=0):
       count = start  # Truly private!

       def increment():
           nonlocal count
           count += 1
           return count

       def reset():
           nonlocal count
           count = 0

       return increment, reset

   print("=== CLASS ===")
   c1 = Counter(10)
   print(c1.increment())
   print(c1.increment())
   print(c1._count)  # ❌ Can access "private" variable!
   c1._count = 1000  # ❌ Can modify it directly!
   print(c1.increment())

   print("\n=== CLOSURE ===")
   inc, reset = make_counter(10)
   print(inc())
   print(inc())
   # print(count)  # ❌ NameError: 'count' doesn't exist here
   # No way to access or modify count!
   print(inc())

**Output:**

::

   === CLASS ===
   11
   12
   12
   1001

   === CLOSURE ===
   11
   12
   13

.. list-table:: Closures vs. Classes for State Management
   :widths: 25 35 40
   :header-rows: 1

   * - Aspect
     - Closures
     - Classes
   * - **Privacy**
     - 🔒 True privacy (enforced by language)
     - 🔓 Convention-based (``_name``, but not enforced)
   * - **Complexity**
     - ✅ Simple for small state
     - 📚 More boilerplate (``__init__``, ``self``)
   * - **Inheritance**
     - ❌ Not possible
     - ✅ Full inheritance support
   * - **Multiple Instances**
     - ✅ Yes (call outer function multiple times)
     - ✅ Yes (instantiate class multiple times)
   * - **Introspection**
     - ❌ Limited (can't see state)
     - ✅ Easy (``__dict__``, ``dir()``)
   * - **Best For**
     - Small, focused state; function factories
     - Complex objects; inheritance hierarchies

---

Real-World Encapsulation Patterns
----------------------------------

.. index:: encapsulation patterns, validator pattern, configuration pattern

**Pattern 1: Input Validation with Protected State**

.. activecode:: closure_validator_pattern
   :language: python
   :caption: Protected State with Validation

   def make_age_tracker():
       age = 0  # Private, always valid

       def set_age(new_age):
           nonlocal age
           if not isinstance(new_age, int):
               print("❌ Age must be an integer")
               return False
           if new_age < 0 or new_age > 150:
               print("❌ Age must be between 0 and 150")
               return False
           age = new_age
           print(f"✅ Age set to {age}")
           return True

       def get_age():
           return age

       def birthday():
           nonlocal age
           age += 1
           print(f"🎂 Happy Birthday! Now {age} years old")
           return age

       return set_age, get_age, birthday

   set_age, get_age, birthday = make_age_tracker()

   # Valid operations
   set_age(25)
   birthday()
   print(f"Current age: {get_age()}")

   # Invalid operations (all blocked!)
   set_age(-5)
   set_age(200)
   set_age("thirty")

**Output:**

::

   ✅ Age set to 25
   🎂 Happy Birthday! Now 26 years old
   Current age: 26
   ❌ Age must be between 0 and 150
   ❌ Age must be between 0 and 150
   ❌ Age must be an integer

The ``age`` variable is **guaranteed** to always be valid because it's protected by the closure!

---

**Pattern 2: Configuration with Immutable Settings**

.. activecode:: closure_config_pattern_immutable
   :language: python
   :caption: Immutable Configuration

   def create_api_client(api_key, base_url):
       # Configuration is captured and cannot be changed

       def get(endpoint):
           url = f"{base_url}/{endpoint}"
           # In real code, this would make an HTTP request
           return f"GET {url} with key {api_key[:5]}..."

       def post(endpoint, data):
           url = f"{base_url}/{endpoint}"
           return f"POST {url} with key {api_key[:5]}... | Data: {data}"

       def get_config():
           # Only allow reading config, not modifying
           return {
               'base_url': base_url,
               'api_key': api_key[:5] + '...'  # Don't expose full key
           }

       return get, post, get_config

   # Create client
   get, post, get_config = create_api_client("SECRET123456", "https://api.example.com")

   # Use the client
   print(get("users"))
   print(post("users", {'name': 'Alice'}))

   # Can view config (safely)
   print(f"\nConfig: {get_config()}")

   # Cannot modify api_key or base_url - they're locked in!
   print("\n🔒 Configuration is immutable and api_key is hidden!")

**Output:**

::

   GET https://api.example.com/users with key SECRE...
   POST https://api.example.com/users with key SECRE... | Data: {'name': 'Alice'}

   Config: {'base_url': 'https://api.example.com', 'api_key': 'SECRE...'}

   🔒 Configuration is immutable and api_key is hidden!

---

**Pattern 3: Stateful Iterators**

.. activecode:: closure_iterator_pattern
   :language: python
   :caption: Custom Iterator with State

   def make_fibonacci_iterator():
       a, b = 0, 1
       count = 0

       def next_fib():
           nonlocal a, b, count
           result = a
           a, b = b, a + b
           count += 1
           return result

       def get_count():
           return count

       def reset():
           nonlocal a, b, count
           a, b, count = 0, 1, 0

       return next_fib, get_count, reset

   fib, count, reset = make_fibonacci_iterator()

   # Generate first 10 Fibonacci numbers
   fibs = [fib() for _ in range(10)]
   print(f"First 10 Fibonacci numbers: {fibs}")
   print(f"Generated {count()} numbers")

   # Reset and generate more
   reset()
   print(f"After reset: {[fib() for _ in range(5)]}")

**Output:**

::

   First 10 Fibonacci numbers: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
   Generated 10 numbers
   After reset: [0, 1, 1, 2, 3]

---

When to Use Closures for Encapsulation
---------------------------------------

.. index:: closure use cases, when to use closures

.. list-table:: Decision Guide: Closures vs. Classes
   :widths: 50 50
   :header-rows: 1

   * - **Use Closures When:**
     - **Use Classes When:**
   * - You need simple state (1-5 variables)
     - You need complex state with many attributes
   * - You want guaranteed data privacy
     - You need inheritance or polymorphism
   * - You're creating function factories
     - You're building a type hierarchy
   * - State is only modified through functions
     - You need extensive introspection
   * - Configuration is immutable
     - You need operator overloading (``__add__``, etc.)
   * - You want minimal boilerplate
     - Multiple developers need to understand the code

**Rule of Thumb:** If you can solve it with a closure in <20 lines and don't need inheritance, consider closures first!

---

Practice Challenges
--------------------

**Challenge 1: Build a Secret Box**

.. activecode:: closure_practice_secret_box
   :language: python
   :caption: Challenge - Secret Box

   def make_secret_box(initial_secret):
       """
       Create a "box" that stores a secret.

       Returns three functions:
       - get_secret(password): returns secret if password is "opensesame"
       - set_secret(new_secret, password): changes secret if password correct
       - change_password(old_pass, new_pass): updates password

       The password should start as "opensesame"
       """
       # TODO: Your code here
       pass

   # Test your code (uncomment when ready):
   # get, set_secret, change_pass = make_secret_box("treasure")
   #
   # print(get("opensesame"))        # Should print: treasure
   # print(get("wrong"))              # Should print: Access denied!
   # set_secret("gold", "opensesame")
   # print(get("opensesame"))        # Should print: gold
   # change_pass("opensesame", "newpass")
   # print(get("newpass"))            # Should print: gold

.. reveal:: closure_practice_secret_box_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_secret_box(initial_secret):
          secret = initial_secret
          password = "opensesame"

          def get_secret(pwd):
              if pwd == password:
                  return secret
              return "Access denied!"

          def set_secret(new_secret, pwd):
              nonlocal secret
              if pwd == password:
                  secret = new_secret
                  return "Secret updated!"
              return "Access denied!"

          def change_password(old_pwd, new_pwd):
              nonlocal password
              if old_pwd == password:
                  password = new_pwd
                  return "Password changed!"
              return "Access denied!"

          return get_secret, set_secret, change_password

---

**Challenge 2: Build a Retry Mechanism**

.. activecode:: closure_practice_retry
   :language: python
   :caption: Challenge - Retry Mechanism

   def make_retry_wrapper(max_retries):
       """
       Create a closure that wraps a function and automatically
       retries it if it raises an exception.

       Should track:
       - Total attempts made
       - Total failures
       - Total successes

       Returns: wrapped_function, get_stats
       """
       # TODO: Your code here
       pass

   # Test your code (uncomment when ready):
   # wrap, stats = make_retry_wrapper(3)
   #
   # attempts = 0
   # def unreliable_function():
   #     global attempts
   #     attempts += 1
   #     if attempts < 3:
   #         raise ValueError("Not yet!")
   #     return "Success!"
   #
   # wrapped = wrap(unreliable_function)
   # print(wrapped())  # Should retry and eventually succeed
   # print(stats())    # Show statistics

.. reveal:: closure_practice_retry_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def make_retry_wrapper(max_retries):
          total_attempts = 0
          total_failures = 0
          total_successes = 0

          def wrap(func):
              def wrapper(*args, **kwargs):
                  nonlocal total_attempts, total_failures, total_successes

                  for attempt in range(max_retries):
                      try:
                          total_attempts += 1
                          result = func(*args, **kwargs)
                          total_successes += 1
                          return result
                      except Exception as e:
                          if attempt == max_retries - 1:
                              total_failures += 1
                              raise
                          continue
              return wrapper

          def get_stats():
              return {
                  'attempts': total_attempts,
                  'failures': total_failures,
                  'successes': total_successes
              }

          return wrap, get_stats

---

Check Your Understanding
-------------------------

.. mchoice:: closure_privacy_level
   :answer_a: Variables can be read but not modified from outside
   :answer_b: Variables are completely inaccessible from outside the closure
   :answer_c: Variables can be accessed using special Python introspection
   :answer_d: Variables are stored in a hidden global namespace
   :correct: b
   :feedback_a: They can't even be read directly! Only through returned functions.
   :feedback_b: Correct! Closure variables are truly private - no outside access at all.
   :feedback_c: Even introspection can't directly access closure variables.
   :feedback_d: They're stored in the closure's local scope, not globally.

   What level of privacy do closure variables have?

.. mchoice:: closure_vs_class_privacy
   :answer_a: Classes provide better privacy than closures
   :answer_b: Closures and classes have the same privacy level
   :answer_c: Closures provide true privacy; classes only have convention-based privacy
   :answer_d: Neither closures nor classes provide any privacy
   :correct: c
   :feedback_a: Actually, closures provide STRONGER privacy!
   :feedback_b: Closures enforce privacy; classes use conventions (like _name).
   :feedback_c: Correct! Closures have language-enforced privacy; class "privacy" is just convention.
   :feedback_d: Closures definitely provide privacy!

   How does closure privacy compare to class privacy in Python?

.. mchoice:: closure_when_to_use
   :answer_a: Always use closures instead of classes
   :answer_b: Use closures for simple state, classes for complex objects with inheritance
   :answer_c: Never use closures; always use classes
   :answer_d: Use closures only for mathematical functions
   :correct: b
   :feedback_a: Classes are better when you need inheritance or complex state.
   :feedback_b: Correct! Each has its place - closures for simple state, classes for complex objects.
   :feedback_c: Closures are great for many scenarios!
   :feedback_d: Closures are useful far beyond just math!

   When should you use closures for state management?

.. mchoice:: closure_multiple_instances
   :answer_a: You can only create one closure per outer function
   :answer_b: Each call to the outer function creates an independent closure with its own state
   :answer_c: All closures share the same enclosing variables
   :answer_d: Closures don't support multiple instances
   :correct: b
   :feedback_a: You can create as many closures as you want!
   :feedback_b: Correct! Each outer function call creates a new, independent closure.
   :feedback_c: Each closure has its OWN copy of enclosing variables.
   :feedback_d: Multiple independent closures are fully supported!

   Can you create multiple independent closures from the same outer function?

---

Key Takeaways
-------------

.. important::
   **Section 3 Summary: State and Encapsulation**

   ✅ **True Privacy:**
      - Closure variables are **completely inaccessible** from outside
      - No way to read or modify them except through returned functions
      - Unlike classes, privacy is **enforced by the language**

   ✅ **Object-Like Behavior:**
      - Closures can mimic objects by returning multiple functions
      - Shared private state between returned functions
      - No boilerplate (no ``__init__``, no ``self``)

   ✅ **Closures vs. Classes:**

      **Closures Win:**
      - Simpler syntax for small state
      - True, enforced privacy
      - Less boilerplate

      **Classes Win:**
      - Inheritance and polymorphism
      - Complex state with many attributes
      - Better introspection
      - Team familiarity

   ✅ **Practical Patterns:**
      - **Validators:** Protected state with validation
      - **Configuration:** Immutable settings
      - **Iterators:** Stateful iteration with hidden state
      - **Security:** Password protection, access control

   ✅ **Decision Guide:**
      - Simple state (1-5 variables)? → Closure
      - Need inheritance? → Class
      - Want guaranteed privacy? → Closure
      - Complex object hierarchy? → Class

---

What's Next?
------------

You now understand how closures provide encapsulation and state management. But closures really shine when you apply them to **real-world problems** — creating decorators, callbacks, event handlers, and functional programming patterns.

**In Section 4: Practical Patterns**, you'll learn:

- Using closures as decorators (timing, logging, caching)
- Event handlers and callbacks with closures
- Partial application and currying
- Memoization and optimization patterns
- Functional programming with closures
- Real-world examples from popular libraries

Ready to see closures in action solving real problems? Let's go! 🚀

---

.. note::
   **✅ Section 3 Complete!**

   You've learned:
   - [✓] How closures provide true data privacy
   - [✓] Building object-like structures with closures
   - [✓] Closures vs. classes comparison
   - [✓] Real-world encapsulation patterns
   - [✓] When to choose closures over classes

   **Ready for practical applications?** → Continue to Section 4!