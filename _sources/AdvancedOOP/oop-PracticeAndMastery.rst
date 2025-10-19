..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".


Section 7: Practice and Mastery
================================

.. index:: OOP practice, OOP assessment, mastery

Introduction
------------

**Congratulations!** You've completed all instructional sections on Advanced OOP. Now it's time to **test your mastery** and solidify your understanding.

This comprehensive assessment section includes:

- **Vocabulary Review** — test your understanding of OOP terminology
- **Conceptual Understanding** — verify you understand how OOP works
- **Parsons Problems** — arrange code blocks to create working OOP solutions
- **Coding Challenges** — build OOP systems from scratch (10 challenges)
- **Debugging Challenges** — find and fix broken OOP code (5 challenges)
- **Self-Assessment Checklist** — comprehensive skills inventory
- **Quick Reference Guide** — syntax and patterns at a glance

Take your time, work through each section, and use this as a learning opportunity. **Good luck!** 🚀

---

Part 1: Vocabulary Review
--------------------------

.. index:: OOP vocabulary, OOP terminology

Test your understanding of OOP terminology:

.. mchoice:: oop_practice_vocab_introspection
   :answer_a: Examining objects at runtime to discover their properties
   :answer_b: Writing comments in your code
   :answer_c: Debugging with a debugger
   :answer_d: Looking at the source code
   :correct: a
   :feedback_a: Correct! Introspection is the ability to examine objects, attributes, and types at runtime.
   :feedback_b: Comments are documentation, not introspection.
   :feedback_c: Debugging tools use introspection, but that's not the definition.
   :feedback_d: Introspection happens at runtime, not by reading source code.

   What is **introspection** in Python?

.. mchoice:: oop_practice_vocab_mro
   :answer_a: Multiple Return Objects
   :answer_b: Method Resolution Order
   :answer_c: Memory Reference Optimization
   :answer_d: Module Runtime Organization
   :correct: b
   :feedback_a: That's not what MRO stands for.
   :feedback_b: Correct! MRO is Method Resolution Order—the order Python searches for methods in inheritance.
   :feedback_c: MRO isn't about memory optimization.
   :feedback_d: MRO is about method resolution, not modules.

   What does **MRO** stand for?

.. mchoice:: oop_practice_vocab_diamond_problem
   :answer_a: A problem with diamond-shaped objects
   :answer_b: When two parent classes share a common ancestor
   :answer_c: A memory allocation issue
   :answer_d: When a class has four parent classes
   :correct: b
   :feedback_a: The "diamond" refers to the inheritance structure, not object shapes.
   :feedback_b: Correct! The diamond problem occurs when a class inherits from two classes that share a common ancestor.
   :feedback_c: It's about inheritance ambiguity, not memory.
   :feedback_d: It's about shared ancestors, not the number of parents.

   What is the **diamond problem**?

.. mchoice:: oop_practice_vocab_mixin
   :answer_a: A class that combines multiple inheritance
   :answer_b: A small class that adds specific functionality through inheritance
   :answer_c: A function that mixes data types
   :answer_d: A method that calls multiple other methods
   :correct: b
   :feedback_a: That's any class with multiple inheritance, not specifically a mixin.
   :feedback_b: Correct! A mixin is a class designed to add specific capabilities without being standalone.
   :feedback_c: Mixins are classes, not functions.
   :feedback_d: That's just a regular method, not a mixin.

   What is a **mixin** class?

.. mchoice:: oop_practice_vocab_polymorphism
   :answer_a: Having multiple forms or implementations
   :answer_b: A class with many methods
   :answer_c: Inheritance from multiple parents
   :answer_d: A function that returns different types
   :correct: a
   :feedback_a: Correct! Polymorphism means "many forms"—treating different types uniformly through a common interface.
   :feedback_b: Having many methods is unrelated to polymorphism.
   :feedback_c: That's multiple inheritance, not polymorphism.
   :feedback_d: Polymorphism is about objects/methods, not return types specifically.

   What is **polymorphism**?

.. mchoice:: oop_practice_vocab_duck_typing
   :answer_a: Type checking based on class names
   :answer_b: Using objects based on their behavior, not type
   :answer_c: A typing system for databases
   :answer_d: Static type checking
   :correct: b
   :feedback_a: Duck typing doesn't care about class names!
   :feedback_b: Correct! "If it walks like a duck and quacks like a duck, it's a duck"—behavior over type.
   :feedback_c: Duck typing is unrelated to databases.
   :feedback_d: Duck typing is dynamic, not static.

   What is **duck typing**?

.. mchoice:: oop_practice_vocab_abc
   :answer_a: Always Be Coding
   :answer_b: Abstract Base Class
   :answer_c: Automatic Base Constructor
   :answer_d: Advanced Boolean Check
   :correct: b
   :feedback_a: That's motivational, not technical!
   :feedback_b: Correct! ABC stands for Abstract Base Class—a class with abstract methods that subclasses must implement.
   :feedback_c: That's not a Python concept.
   :feedback_d: That's not what ABC means.

   What does **ABC** stand for in Python OOP?

.. mchoice:: oop_practice_vocab_protocol
   :answer_a: A network communication standard
   :answer_b: A type hint that defines expected methods without inheritance
   :answer_c: A class initialization protocol
   :answer_d: A method calling convention
   :correct: b
   :feedback_a: That's network protocols, not Python Protocols.
   :feedback_b: Correct! Protocols enable structural subtyping—defining expected behavior without inheritance.
   :feedback_c: That's not what Protocol means in Python.
   :feedback_d: Protocols are about type structure, not calling conventions.

   What is a **Protocol** in Python 3.8+?

---

Part 2: Conceptual Understanding
---------------------------------

.. index:: OOP concepts, OOP behavior

Test your understanding of how OOP works:

.. mchoice:: oop_practice_concept_hasattr
   :answer_a: True
   :answer_b: False
   :answer_c: None
   :answer_d: AttributeError
   :correct: a
   :feedback_a: Correct! hasattr returns True if the object has the attribute (including inherited ones).
   :feedback_b: Inherited attributes are found by hasattr.
   :feedback_c: hasattr returns True or False, never None.
   :feedback_d: hasattr doesn't raise errors; it returns False instead.

   If a class inherits a method from its parent, what does ``hasattr(instance, 'method')`` return?

.. mchoice:: oop_practice_concept_isinstance_inherit
   :answer_a: True
   :answer_b: False
   :answer_c: TypeError
   :answer_d: Depends on the MRO
   :correct: a
   :feedback_a: Correct! isinstance returns True for the entire inheritance chain.
   :feedback_b: isinstance respects inheritance!
   :feedback_c: No error is raised.
   :feedback_d: isinstance always returns True for any class in the inheritance chain.

   If ``Dog`` inherits from ``Animal``, what does ``isinstance(dog_instance, Animal)`` return?

.. mchoice:: oop_practice_concept_is_vs_eq
   :answer_a: They are identical
   :answer_b: is checks identity, == checks equality
   :answer_c: is is faster, but they check the same thing
   :answer_d: == can't be used with objects
   :correct: b
   :feedback_a: They check fundamentally different things!
   :feedback_b: Correct! 'is' checks if objects are the same in memory, '==' checks if values are equal.
   :feedback_c: They check different things, not just speed difference.
   :feedback_d: == works with objects (can be customized with __eq__).

   What's the difference between ``is`` and ``==``?

.. mchoice:: oop_practice_concept_super_mi
   :answer_a: Calls the direct parent class
   :answer_b: Calls all parent classes
   :answer_c: Calls the next class in the MRO
   :answer_d: Calls the base class (object)
   :correct: c
   :feedback_a: In multiple inheritance, it doesn't always call the direct parent!
   :feedback_b: It calls one class, which then calls the next via its own super().
   :feedback_c: Correct! super() calls the next class in the MRO, enabling cooperative inheritance.
   :feedback_d: It calls the next in MRO, not necessarily the ultimate base.

   In multiple inheritance, what does ``super()`` call?

.. mchoice:: oop_practice_concept_class_vs_instance_dict
   :answer_a: They contain the same attributes
   :answer_b: Class __dict__ has methods, instance __dict__ has instance attributes
   :answer_c: Instance __dict__ has methods, class __dict__ has class attributes
   :answer_d: They are always identical
   :correct: b
   :feedback_a: They contain different things!
   :feedback_b: Correct! Class __dict__ has class attributes and methods; instance __dict__ has only instance attributes.
   :feedback_c: You have it backwards!
   :feedback_d: They contain very different things.

   What's in a class's ``__dict__`` vs an instance's ``__dict__``?

.. mchoice:: oop_practice_concept_mro_order
   :answer_a: Random order
   :answer_b: Alphabetical by class name
   :answer_c: Left-to-right, depth-first, no duplicates
   :answer_d: Right-to-left, breadth-first
   :correct: c
   :feedback_a: MRO is deterministic!
   :feedback_b: Class names don't affect MRO.
   :feedback_c: Correct! C3 linearization ensures left-to-right, depth-first with each class appearing once.
   :feedback_d: It's left-to-right, not right-to-left.

   What order does Python's MRO follow?

.. mchoice:: oop_practice_concept_abc_instantiate
   :answer_a: Yes, like any class
   :answer_b: No, it raises TypeError
   :answer_c: Yes, but only if it has no abstract methods
   :answer_d: Only if you use a special syntax
   :correct: b
   :feedback_a: Abstract classes can't be instantiated!
   :feedback_b: Correct! You can't instantiate an ABC with abstract methods—it raises TypeError.
   :feedback_c: If it has no abstract methods, it's not truly abstract anymore.
   :feedback_d: There's no special syntax to instantiate ABCs.

   Can you instantiate an Abstract Base Class?

.. mchoice:: oop_practice_concept_none_check
   :answer_a: if x == None:
   :answer_b: if x is None:
   :answer_c: if not x:
   :answer_d: All are equally valid
   :correct: b
   :feedback_a: While this works, 'is None' is the Pythonic way.
   :feedback_b: Correct! Always use 'is None' for checking None—it's faster and can't be overridden.
   :feedback_c: This checks truthiness, not specifically None (0, [], "" are also falsy).
   :feedback_d: They're not equivalent—each checks different things.

   What's the correct way to check if a variable is None?

---

Part 3: Parsons Problems
-------------------------

.. index:: parsons problems, code arrangement

Arrange the code blocks in the correct order to create working OOP solutions:

**Problem 1: Basic Introspection**

.. parsonsprob:: oop_practice_parsons_introspection
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a function that safely gets an attribute with a default value.
   -----
   def safe_get_attr(obj, attr_name, default=None):
   =====
       if hasattr(obj, attr_name):
   =====
       if obj.hasattr(attr_name): #distractor
   =====
           return getattr(obj, attr_name)
   =====
           return obj.attr_name #distractor
   =====
       return default
   =====
       return None #distractor

**Problem 2: Identity Check**

.. parsonsprob:: oop_practice_parsons_identity
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a function that checks if two objects are the same object (not just equal).
   -----
   def are_same_object(obj1, obj2):
   =====
       return obj1 is obj2
   =====
       return obj1 == obj2 #distractor
   =====
       return id(obj1) == id(obj2)
   =====
       return type(obj1) == type(obj2) #distractor

**Problem 3: Simple ABC**

.. parsonsprob:: oop_practice_parsons_abc
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create an abstract base class with one abstract method.
   -----
   from abc import ABC, abstractmethod
   =====
   import abc #distractor
   =====
   class Shape(ABC):
   =====
   class Shape: #distractor
   =====
       @abstractmethod
   =====
       @abstract #distractor
   =====
       def area(self):
   =====
           pass
   =====
           raise NotImplementedError #distractor

**Problem 4: Multiple Inheritance with super()**

.. parsonsprob:: oop_practice_parsons_super
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a class with multiple inheritance using super() correctly.
   -----
   class A:
       def __init__(self):
           print("A")
           super().__init__()
   =====
   class B:
       def __init__(self):
           print("B")
           super().__init__()
   =====
   class C(A, B):
   =====
   class C(A, B): #distractor
       def __init__(self):
           A.__init__(self)
           B.__init__(self)
   =====
       def __init__(self):
           print("C")
           super().__init__()

**Problem 5: Polymorphic Function**

.. parsonsprob:: oop_practice_parsons_polymorphic
   :language: python
   :adaptive:
   :numbered: left

   Arrange the blocks to create a polymorphic function that works with any object having a draw() method.
   -----
   def render_all(shapes):
   =====
       results = []
   =====
       for shape in shapes:
   =====
           if hasattr(shape, 'draw'):
   =====
           if isinstance(shape, Shape): #distractor
   =====
               results.append(shape.draw())
   =====
       return results

---

Part 4: Coding Challenges
--------------------------

.. index:: OOP coding challenges

Build these OOP systems from scratch! Each challenge tests different aspects of OOP mastery.

**Challenge 1: Class Inspector**

.. activecode:: oop_practice_code_inspector
   :language: python
   :autograde: unittest

   Create a function ``inspect_hierarchy(cls)`` that returns a dictionary with:
   - 'name': class name
   - 'bases': list of parent class names
   - 'mro': list of all classes in MRO (as names)
   - 'methods': list of public method names (no underscores)

   Example::

       class Animal:
           def speak(self): pass

       class Dog(Animal):
           def bark(self): pass

       result = inspect_hierarchy(Dog)
       # result['name'] == 'Dog'
       # result['bases'] == ['Animal']
       # 'Animal' in result['mro']
       # 'bark' in result['methods']

   ~~~~
   def inspect_hierarchy(cls):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_name(self):
           class TestClass:
               pass
           result = inspect_hierarchy(TestClass)
           self.assertEqual(result['name'], 'TestClass')

       def test_bases(self):
           class Parent:
               pass
           class Child(Parent):
               pass
           result = inspect_hierarchy(Child)
           self.assertEqual(result['bases'], ['Parent'])

       def test_mro_includes_object(self):
           class TestClass:
               pass
           result = inspect_hierarchy(TestClass)
           self.assertIn('object', result['mro'])

       def test_methods(self):
           class TestClass:
               def public_method(self):
                   pass
               def _private_method(self):
                   pass
           result = inspect_hierarchy(TestClass)
           self.assertIn('public_method', result['methods'])
           self.assertNotIn('_private_method', result['methods'])

   myTests().main()

.. reveal:: oop_practice_code_inspector_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def inspect_hierarchy(cls):
          return {
              'name': cls.__name__,
              'bases': [base.__name__ for base in cls.__bases__],
              'mro': [c.__name__ for c in cls.__mro__],
              'methods': [attr for attr in dir(cls)
                          if not attr.startswith('_') and callable(getattr(cls, attr))]
          }

---

**Challenge 2: Safe Attribute Manager**

.. activecode:: oop_practice_code_attr_manager
   :language: python
   :autograde: unittest

   Create a class ``SafeAttributeManager`` that:
   - Stores attributes safely
   - Has ``set(name, value)`` method to set attributes
   - Has ``get(name, default=None)`` method to get attributes
   - Has ``has(name)`` method to check if attribute exists
   - Has ``delete(name)`` method to delete attributes

   Example::

       manager = SafeAttributeManager()
       manager.set('name', 'Alice')
       manager.get('name')  # 'Alice'
       manager.has('name')  # True
       manager.get('age', 25)  # 25 (default)
       manager.delete('name')
       manager.has('name')  # False

   ~~~~
   class SafeAttributeManager:
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_set_and_get(self):
           manager = SafeAttributeManager()
           manager.set('test', 'value')
           self.assertEqual(manager.get('test'), 'value')

       def test_get_with_default(self):
           manager = SafeAttributeManager()
           self.assertEqual(manager.get('missing', 'default'), 'default')

       def test_has(self):
           manager = SafeAttributeManager()
           manager.set('exists', True)
           self.assertTrue(manager.has('exists'))
           self.assertFalse(manager.has('missing'))

       def test_delete(self):
           manager = SafeAttributeManager()
           manager.set('temp', 'value')
           manager.delete('temp')
           self.assertFalse(manager.has('temp'))

   myTests().main()

.. reveal:: oop_practice_code_attr_manager_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      class SafeAttributeManager:
          def __init__(self):
              self._attrs = {}

          def set(self, name, value):
              self._attrs[name] = value

          def get(self, name, default=None):
              return self._attrs.get(name, default)

          def has(self, name):
              return name in self._attrs

          def delete(self, name):
              if name in self._attrs:
                  del self._attrs[name]

---

**Challenge 3: Polymorphic Shape System**

.. activecode:: oop_practice_code_shapes
   :language: python
   :autograde: unittest

   Create an ABC ``Shape`` with abstract methods ``area()`` and ``perimeter()``.
   Then create ``Rectangle`` and ``Circle`` classes that implement these methods.

   Example::

       rect = Rectangle(5, 10)
       rect.area()  # 50
       rect.perimeter()  # 30

       circle = Circle(7)
       circle.area()  # ~153.94
       circle.perimeter()  # ~43.98

   ~~~~
   from abc import ABC, abstractmethod

   class Shape(ABC):
       # Your code here
       pass

   class Rectangle(Shape):
       # Your code here
       pass

   class Circle(Shape):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_cant_instantiate_shape(self):
           with self.assertRaises(TypeError):
               Shape()

       def test_rectangle_area(self):
           rect = Rectangle(5, 10)
           self.assertEqual(rect.area(), 50)

       def test_rectangle_perimeter(self):
           rect = Rectangle(5, 10)
           self.assertEqual(rect.perimeter(), 30)

       def test_circle_area(self):
           circle = Circle(7)
           self.assertAlmostEqual(circle.area(), 153.94, places=1)

       def test_circle_perimeter(self):
           circle = Circle(7)
           self.assertAlmostEqual(circle.perimeter(), 43.98, places=1)

   myTests().main()

.. reveal:: oop_practice_code_shapes_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      from abc import ABC, abstractmethod

      class Shape(ABC):
          @abstractmethod
          def area(self):
              pass

          @abstractmethod
          def perimeter(self):
              pass

      class Rectangle(Shape):
          def __init__(self, width, height):
              self.width = width
              self.height = height

          def area(self):
              return self.width * self.height

          def perimeter(self):
              return 2 * (self.width + self.height)

      class Circle(Shape):
          def __init__(self, radius):
              self.radius = radius

          def area(self):
              return 3.14159 * self.radius ** 2

          def perimeter(self):
              return 2 * 3.14159 * self.radius

---

**Challenge 4: Mixin Composition**

.. activecode:: oop_practice_code_mixins
   :language: python
   :autograde: unittest

   Create two mixins:
   - ``ReprMixin``: provides __repr__ that shows class name and attributes
   - ``EqualityMixin``: provides __eq__ that compares __dict__

   Then create a ``Point`` class that uses both mixins.

   Example::

       p1 = Point(3, 4)
       p2 = Point(3, 4)
       repr(p1)  # "Point(x=3, y=4)"
       p1 == p2  # True

   ~~~~
   class ReprMixin:
       # Your code here
       pass

   class EqualityMixin:
       # Your code here
       pass

   class Point(ReprMixin, EqualityMixin):
       def __init__(self, x, y):
           self.x = x
           self.y = y

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_repr(self):
           p = Point(3, 4)
           self.assertIn('Point', repr(p))
           self.assertIn('3', repr(p))
           self.assertIn('4', repr(p))

       def test_equality_true(self):
           p1 = Point(3, 4)
           p2 = Point(3, 4)
           self.assertEqual(p1, p2)

       def test_equality_false(self):
           p1 = Point(3, 4)
           p2 = Point(5, 6)
           self.assertNotEqual(p1, p2)

   myTests().main()

.. reveal:: oop_practice_code_mixins_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      class ReprMixin:
          def __repr__(self):
              class_name = self.__class__.__name__
              attrs = ', '.join(f"{k}={v!r}" for k, v in self.__dict__.items())
              return f"{class_name}({attrs})"

      class EqualityMixin:
          def __eq__(self, other):
              if not isinstance(other, self.__class__):
                  return False
              return self.__dict__ == other.__dict__

      class Point(ReprMixin, EqualityMixin):
          def __init__(self, x, y):
              self.x = x
              self.y = y

---

**Challenge 5: MRO Analyzer**

.. activecode:: oop_practice_code_mro_analyzer
   :language: python
   :autograde: unittest

   Create a function ``find_method_source(cls, method_name)`` that returns the class name where a method is defined in the MRO.

   Example::

       class A:
           def method(self): pass

       class B(A):
           pass

       class C(B):
           def method(self): pass

       find_method_source(B, 'method')  # 'A'
       find_method_source(C, 'method')  # 'C'
       find_method_source(A, 'missing')  # None

   ~~~~
   def find_method_source(cls, method_name):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_direct_method(self):
           class TestClass:
               def test_method(self):
                   pass
           result = find_method_source(TestClass, 'test_method')
           self.assertEqual(result, 'TestClass')

       def test_inherited_method(self):
           class Parent:
               def parent_method(self):
                   pass
           class Child(Parent):
               pass
           result = find_method_source(Child, 'parent_method')
           self.assertEqual(result, 'Parent')

       def test_overridden_method(self):
           class Parent:
               def method(self):
                   pass
           class Child(Parent):
               def method(self):
                   pass
           result = find_method_source(Child, 'method')
           self.assertEqual(result, 'Child')

       def test_missing_method(self):
           class TestClass:
               pass
           result = find_method_source(TestClass, 'missing')
           self.assertIsNone(result)

   myTests().main()

.. reveal:: oop_practice_code_mro_analyzer_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def find_method_source(cls, method_name):
          for c in cls.__mro__:
              if method_name in c.__dict__:
                  return c.__name__
          return None

---

**Challenge 6: Identity vs Equality Checker**

.. activecode:: oop_practice_code_identity_equality
   :language: python
   :autograde: unittest

   Create a function ``compare_objects(obj1, obj2)`` that returns a dictionary:
   - 'identical': True if same object (is)
   - 'equal': True if equal values (==)
   - 'same_type': True if same type
   - 'same_id': True if same id

   Example::

       a = [1, 2, 3]
       b = [1, 2, 3]
       c = a

       compare_objects(a, b)
       # {'identical': False, 'equal': True, 'same_type': True, 'same_id': False}

       compare_objects(a, c)
       # {'identical': True, 'equal': True, 'same_type': True, 'same_id': True}

   ~~~~
   def compare_objects(obj1, obj2):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_identical_objects(self):
           a = [1, 2, 3]
           b = a
           result = compare_objects(a, b)
           self.assertTrue(result['identical'])
           self.assertTrue(result['equal'])
           self.assertTrue(result['same_type'])
           self.assertTrue(result['same_id'])

       def test_equal_not_identical(self):
           a = [1, 2, 3]
           b = [1, 2, 3]
           result = compare_objects(a, b)
           self.assertFalse(result['identical'])
           self.assertTrue(result['equal'])
           self.assertTrue(result['same_type'])
           self.assertFalse(result['same_id'])

       def test_different_objects(self):
           a = [1, 2, 3]
           b = "hello"
           result = compare_objects(a, b)
           self.assertFalse(result['identical'])
           self.assertFalse(result['equal'])
           self.assertFalse(result['same_type'])

   myTests().main()

.. reveal:: oop_practice_code_identity_equality_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def compare_objects(obj1, obj2):
          return {
              'identical': obj1 is obj2,
              'equal': obj1 == obj2,
              'same_type': type(obj1) == type(obj2),
              'same_id': id(obj1) == id(obj2)
          }

---

**Challenge 7: Multiple Inheritance Diamond**

.. activecode:: oop_practice_code_diamond
   :language: python
   :autograde: unittest

   Create a diamond inheritance structure with cooperative __init__ methods.
   All classes should use super() and print their names when initialized.

   Structure::

          Base
          /  \
         A    B
          \  /
           C

   Example::

       c = C()
       # Prints: Base, A, B, C (in MRO order)

   ~~~~
   class Base:
       # Your code here
       pass

   class A(Base):
       # Your code here
       pass

   class B(Base):
       # Your code here
       pass

   class C(A, B):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_mro_order(self):
           mro_names = [cls.__name__ for cls in C.__mro__]
           self.assertEqual(mro_names[:4], ['C', 'A', 'B', 'Base'])

       def test_can_instantiate(self):
           c = C()
           self.assertIsInstance(c, C)
           self.assertIsInstance(c, A)
           self.assertIsInstance(c, B)
           self.assertIsInstance(c, Base)

   myTests().main()

.. reveal:: oop_practice_code_diamond_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      class Base:
          def __init__(self):
              print("Base")
              super().__init__()

      class A(Base):
          def __init__(self):
              print("A")
              super().__init__()

      class B(Base):
          def __init__(self):
              print("B")
              super().__init__()

      class C(A, B):
          def __init__(self):
              print("C")
              super().__init__()

---

**Challenge 8: Duck Typing Function**

.. activecode:: oop_practice_code_duck_typing
   :language: python
   :autograde: unittest

   Create a function ``process_drawable(obj)`` that:
   - If obj has a draw() method, call it and return the result
   - Otherwise, return "Object cannot be drawn"

   Don't use isinstance or type checking—use duck typing!

   Example::

       class Circle:
           def draw(self):
               return "Drawing circle"

       class Square:
           def draw(self):
               return "Drawing square"

       class Point:
           pass

       process_drawable(Circle())  # "Drawing circle"
       process_drawable(Point())   # "Object cannot be drawn"

   ~~~~
   def process_drawable(obj):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_with_draw_method(self):
           class Drawable:
               def draw(self):
                   return "Drawing"
           result = process_drawable(Drawable())
           self.assertEqual(result, "Drawing")

       def test_without_draw_method(self):
           class NotDrawable:
               pass
           result = process_drawable(NotDrawable())
           self.assertEqual(result, "Object cannot be drawn")

       def test_with_different_draw(self):
           class CustomDrawable:
               def draw(self):
                   return "Custom drawing"
           result = process_drawable(CustomDrawable())
           self.assertEqual(result, "Custom drawing")

   myTests().main()

.. reveal:: oop_practice_code_duck_typing_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def process_drawable(obj):
          try:
              return obj.draw()
          except AttributeError:
              return "Object cannot be drawn"

---

**Challenge 9: Class Decorator**

.. activecode:: oop_practice_code_class_decorator
   :language: python
   :autograde: unittest

   Create a class decorator ``@countable`` that adds:
   - A class variable ``instance_count`` (starts at 0)
   - Increments ``instance_count`` in __init__
   - A class method ``get_count()`` that returns the count

   Example::

       @countable
       class MyClass:
           pass

       obj1 = MyClass()
       obj2 = MyClass()
       MyClass.get_count()  # 2

   ~~~~
   def countable(cls):
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_count_increments(self):
           @countable
           class TestClass:
               pass

           obj1 = TestClass()
           self.assertEqual(TestClass.get_count(), 1)
           obj2 = TestClass()
           self.assertEqual(TestClass.get_count(), 2)

       def test_independent_counts(self):
           @countable
           class Class1:
               pass

           @countable
           class Class2:
               pass

           Class1()
           Class2()
           Class2()

           self.assertEqual(Class1.get_count(), 1)
           self.assertEqual(Class2.get_count(), 2)

   myTests().main()

.. reveal:: oop_practice_code_class_decorator_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      def countable(cls):
          cls.instance_count = 0

          original_init = cls.__init__

          def new_init(self, *args, **kwargs):
              cls.instance_count += 1
              original_init(self, *args, **kwargs)

          cls.__init__ = new_init
          cls.get_count = classmethod(lambda c: c.instance_count)

          return cls

---

**Challenge 10: Polymorphic Container**

.. activecode:: oop_practice_code_polymorphic_container
   :language: python
   :autograde: unittest

   Create a ``ProcessorContainer`` class that:
   - Stores a list of objects
   - Has ``add(obj)`` method to add objects
   - Has ``process_all()`` method that calls process() on each object and returns results
   - Uses duck typing (no type checking!)

   Example::

       class DataProcessor:
           def process(self):
               return "Processing data"

       class ImageProcessor:
           def process(self):
               return "Processing image"

       container = ProcessorContainer()
       container.add(DataProcessor())
       container.add(ImageProcessor())
       results = container.process_all()  # ["Processing data", "Processing image"]

   ~~~~
   class ProcessorContainer:
       # Your code here
       pass

   ====
   from unittest.gui import TestCaseGui

   class myTests(TestCaseGui):
       def test_add_and_process(self):
           class TestProcessor:
               def process(self):
                   return "test"

           container = ProcessorContainer()
           container.add(TestProcessor())
           results = container.process_all()

           self.assertEqual(results, ["test"])

       def test_multiple_processors(self):
           class Proc1:
               def process(self):
                   return "one"

           class Proc2:
               def process(self):
                   return "two"

           container = ProcessorContainer()
           container.add(Proc1())
           container.add(Proc2())
           results = container.process_all()

           self.assertEqual(results, ["one", "two"])

       def test_empty_container(self):
           container = ProcessorContainer()
           results = container.process_all()
           self.assertEqual(results, [])

   myTests().main()

.. reveal:: oop_practice_code_polymorphic_container_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   .. code-block:: python

      class ProcessorContainer:
          def __init__(self):
              self.processors = []

          def add(self, obj):
              self.processors.append(obj)

          def process_all(self):
              results = []
              for processor in self.processors:
                  results.append(processor.process())
              return results

---

Part 5: Debugging Challenges
-----------------------------

.. index:: debugging OOP, OOP errors

Find and fix the bugs in these broken OOP systems!

**Debug 1: Broken Introspection**

.. activecode:: oop_practice_debug_introspection
   :language: python

   This code tries to safely access attributes but has bugs. Fix it!
   ~~~~
   def get_all_attributes(obj):
       """Get all public attributes of an object."""
       attributes = {}

       for attr in dir(obj):
           if not attr.startswith('_'):
               attributes[attr] = getattr(obj, attr)  # Bug here!

       return attributes

   class Person:
       def __init__(self, name):
           self.name = name

       def greet(self):
           return f"Hello, {self.name}"

   person = Person("Alice")
   attrs = get_all_attributes(person)

   # Should only get 'name', not methods!
   print(f"Attributes: {attrs}")

.. reveal:: oop_practice_debug_introspection_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** The function includes methods, not just attributes.

   **Fix:** Check if the attribute is callable before adding it:

   .. code-block:: python

      def get_all_attributes(obj):
          """Get all public attributes of an object."""
          attributes = {}

          for attr in dir(obj):
              if not attr.startswith('_'):
                  value = getattr(obj, attr)
                  if not callable(value):  # Filter out methods
                      attributes[attr] = value

          return attributes

---

**Debug 2: Identity Confusion**

.. activecode:: oop_practice_debug_identity
   :language: python

   This code has identity vs equality confusion. Fix it!
   ~~~~
   def remove_duplicates(items):
       """Remove duplicate items from list."""
       unique = []

       for item in items:
           # Check if item already in unique list
           is_duplicate = False
           for existing in unique:
               if item is existing:  # Bug here!
                   is_duplicate = True
                   break

           if not is_duplicate:
               unique.append(item)

       return unique

   # Test
   numbers = [1, 2, 2, 3, 3, 3, 4]
   result = remove_duplicates(numbers)
   print(f"Result: {result}")  # Should be [1, 2, 3, 4]

.. reveal:: oop_practice_debug_identity_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** Using ``is`` instead of ``==`` for value comparison.

   **Fix:** Use ``==`` to compare values:

   .. code-block:: python

      def remove_duplicates(items):
          """Remove duplicate items from list."""
          unique = []

          for item in items:
              # Check if item already in unique list
              is_duplicate = False
              for existing in unique:
                  if item == existing:  # Use == for value comparison
                      is_duplicate = True
                      break

              if not is_duplicate:
                  unique.append(item)

          return unique

      # Or simply:
      def remove_duplicates(items):
          unique = []
          for item in items:
              if item not in unique:
                  unique.append(item)
          return unique

---

**Debug 3: Multiple Inheritance Gone Wrong**

.. activecode:: oop_practice_debug_mi
   :language: python

   This multiple inheritance doesn't work correctly. Fix it!
   ~~~~
   class A:
       def __init__(self):
           print("A")

   class B:
       def __init__(self):
           print("B")

   class C(A, B):
       def __init__(self):
           print("C")
           A.__init__(self)  # Bug here!
           B.__init__(self)  # Bug here!

   print("Creating C:")
   c = C()
   print(f"MRO: {[cls.__name__ for cls in C.__mro__]}")
   print("\n⚠️ Only A and B printed, not object!")

.. reveal:: oop_practice_debug_mi_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** Direct parent calls instead of cooperative super().

   **Fix:** Use super() in all classes:

   .. code-block:: python

      class A:
          def __init__(self):
              print("A")
              super().__init__()  # Call next in MRO

      class B:
          def __init__(self):
              print("B")
              super().__init__()  # Call next in MRO

      class C(A, B):
          def __init__(self):
              print("C")
              super().__init__()  # Start the chain

---

**Debug 4: Abstract Base Class Error**

.. activecode:: oop_practice_debug_abc
   :language: python

   This ABC implementation has bugs. Fix it!
   ~~~~
   from abc import ABC

   class Animal(ABC):
       def speak(self):  # Bug here! Missing @abstractmethod
           pass

       def move(self):  # Bug here! Missing @abstractmethod
           pass

   # This should fail but doesn't!
   class BrokenDog(Animal):
       pass

   try:
       dog = BrokenDog()
       print("❌ Created BrokenDog without implementing methods!")
   except TypeError as e:
       print(f"✅ Correctly prevented: {e}")

.. reveal:: oop_practice_debug_abc_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** Missing ``@abstractmethod`` decorator.

   **Fix:** Add ``@abstractmethod`` to abstract methods:

   .. code-block:: python

      from abc import ABC, abstractmethod

      class Animal(ABC):
          @abstractmethod  # Add decorator
          def speak(self):
              pass

          @abstractmethod  # Add decorator
          def move(self):
              pass

      # Now this correctly fails
      class BrokenDog(Animal):
          pass

      try:
          dog = BrokenDog()
      except TypeError as e:
          print(f"✅ Correctly prevented: {e}")

---

**Debug 5: Mixin Order Bug**

.. activecode:: oop_practice_debug_mixin_order
   :language: python

   The mixin order is wrong, causing method resolution issues. Fix it!
   ~~~~
   class Base:
       def process(self):
           return "Base"

   class LogMixin:
       def process(self):
           print("Logging...")
           return super().process()

   class CacheMixin:
       def process(self):
           print("Caching...")
           return super().process()

   # Bug: Wrong order! Mixins should come before Base
   class Processor(Base, LogMixin, CacheMixin):
       pass

   p = Processor()
   result = p.process()
   print(f"Result: {result}")
   print(f"\n⚠️ Mixins never executed!")

.. reveal:: oop_practice_debug_mixin_order_solution
   :showtitle: Show Solution
   :hidetitle: Hide Solution

   **Problem:** Base class comes before mixins in inheritance list.

   **Fix:** Put mixins before base class:

   .. code-block:: python

      class Base:
          def process(self):
              return "Base"

      class LogMixin:
          def process(self):
              print("Logging...")
              return super().process()

      class CacheMixin:
          def process(self):
              print("Caching...")
              return super().process()

      # Correct: Mixins before Base
      class Processor(LogMixin, CacheMixin, Base):
          pass

      p = Processor()
      result = p.process()
      print(f"Result: {result}")

---

Part 6: Self-Assessment Checklist
----------------------------------

.. index:: OOP skills checklist

Check off the skills you've mastered:

**Introspection**

.. code-block:: text

   □ I can use hasattr() to check for attributes
   □ I can use getattr() with default values
   □ I can use setattr() to dynamically set attributes
   □ I can use delattr() to remove attributes
   □ I can use dir() to list all attributes
   □ I understand the difference between __dict__ and vars()

**Type Checking**

.. code-block:: text

   □ I can use isinstance() for instance checking
   □ I can use issubclass() for class relationships
   □ I understand why isinstance() is better than type()
   □ I know when to use type checking vs duck typing
   □ I understand Python's EAFP philosophy

**Identity and Equality**

.. code-block:: text

   □ I understand the difference between is and ==
   □ I know when to use is (None checks, singletons)
   □ I know when to use == (value comparisons)
   □ I can use id() to check object identity
   □ I understand Python's object interning
   □ I can implement custom __eq__ methods

**Class Introspection**

.. code-block:: text

   □ I can examine __dict__ for class and instance attributes
   □ I can use __module__ to find where classes are defined
   □ I can use __bases__ to find direct parent classes
   □ I can use __mro__ to see method resolution order
   □ I understand the difference between class and instance attributes

**Multiple Inheritance**

.. code-block:: text

   □ I understand what multiple inheritance is
   □ I know about the diamond problem
   □ I understand how Python's MRO solves the diamond problem
   □ I can use super() correctly in multiple inheritance
   □ I know the C3 linearization algorithm rules
   □ I can create and use mixin classes
   □ I understand when to use multiple inheritance

**MRO and Polymorphism**

.. code-block:: text

   □ I understand C3 linearization properties
   □ I can predict MRO for complex hierarchies
   □ I understand different types of polymorphism
   □ I know the difference between duck typing and inheritance polymorphism
   □ I can create abstract base classes with @abstractmethod
   □ I understand Protocols for structural subtyping
   □ I can implement polymorphic design patterns

**Best Practices**

.. code-block:: text

   □ I use isinstance() instead of type()
   □ I use is None for None checks
   □ I use super() consistently in inheritance
   □ I name mixins with "Mixin" suffix
   □ I put mixins before base classes
   □ I avoid deep diamond hierarchies
   □ I prefer duck typing over excessive type checking

---

Part 7: Quick Reference Guide
------------------------------

.. index:: OOP reference, OOP cheat sheet

**Introspection Functions**

.. code-block:: python

   # Check if attribute exists
   hasattr(obj, 'attr')

   # Get attribute with default
   getattr(obj, 'attr', default)

   # Set attribute dynamically
   setattr(obj, 'attr', value)

   # Delete attribute
   delattr(obj, 'attr')

   # List all attributes
   dir(obj)

   # Get attribute dictionary
   vars(obj)  # Same as obj.__dict__

---

**Type Checking**

.. code-block:: python

   # Check instance type
   isinstance(obj, Class)
   isinstance(obj, (Class1, Class2))  # Multiple types

   # Check class relationship
   issubclass(SubClass, ParentClass)

   # Get object type
   type(obj)

   # Get object ID
   id(obj)

---

**Identity vs Equality**

.. code-block:: python

   # Identity (same object)
   obj1 is obj2
   obj1 is not obj2
   id(obj1) == id(obj2)

   # Equality (same value)
   obj1 == obj2
   obj1 != obj2

   # Always use 'is' for None
   if value is None:
       pass

   # Use == for value comparisons
   if value == expected:
       pass

---

**Class Introspection**

.. code-block:: python

   # Class attributes and methods
   MyClass.__dict__

   # Instance attributes only
   instance.__dict__

   # Module where class is defined
   MyClass.__module__

   # Direct parent classes
   MyClass.__bases__

   # Method Resolution Order
   MyClass.__mro__
   MyClass.mro()  # Same thing

---

**Multiple Inheritance**

.. code-block:: python

   # Basic multiple inheritance
   class Child(Parent1, Parent2):
       pass

   # Cooperative inheritance with super()
   class A:
       def __init__(self):
           super().__init__()

   class B(A):
       def __init__(self):
           super().__init__()

   class C(A):
       def __init__(self):
           super().__init__()

   class D(B, C):
       def __init__(self):
           super().__init__()

---

**Abstract Base Classes**

.. code-block:: python

   from abc import ABC, abstractmethod

   class Shape(ABC):
       @abstractmethod
       def area(self):
           pass

       @abstractmethod
       def perimeter(self):
           pass

   class Rectangle(Shape):
       def area(self):
           return self.width * self.height

       def perimeter(self):
           return 2 * (self.width + self.height)

---

**Protocols (Python 3.8+)**

.. code-block:: python

   from typing import Protocol

   class Drawable(Protocol):
       def draw(self) -> str:
           ...

   # No inheritance needed!
   class Circle:
       def draw(self) -> str:
           return "Drawing circle"

   def render(obj: Drawable):
       print(obj.draw())

---

**Mixin Pattern**

.. code-block:: python

   class LoggerMixin:
       def log(self, message):
           print(f"[LOG] {message}")

   class TimestampMixin:
       def get_timestamp(self):
           from datetime import datetime
           return datetime.now()

   # Mixins before base class
   class MyClass(LoggerMixin, TimestampMixin, BaseClass):
       pass

---

**Common Patterns**

.. code-block:: python

   # Safe attribute access
   value = getattr(obj, 'attr', default)

   # Check before access
   if hasattr(obj, 'method'):
       obj.method()

   # Duck typing (EAFP)
   try:
       obj.method()
   except AttributeError:
       handle_error()

   # Polymorphic function
   def process(obj):
       return obj.process()  # Works with any object having process()

---

**Decision Guides**

.. code-block:: text

   Use isinstance() over type():
   ✓ isinstance() respects inheritance
   ✓ isinstance() checks multiple types
   ✓ type() only checks exact type

   Use 'is' for:
   ✓ None checks (if x is None:)
   ✓ Singleton checks
   ✓ Identity verification

   Use '==' for:
   ✓ Value comparisons
   ✓ String comparisons
   ✓ Number comparisons
   ✓ Collection comparisons

   Use Duck Typing when:
   ✓ Working with flexible interfaces
   ✓ You don't control the types
   ✓ Maximum flexibility needed

   Use Type Checking when:
   ✓ Different behavior for different types
   ✓ Input validation required
   ✓ Clear contracts needed

---

Lesson Complete!
----------------

.. important::
   **🎉 Congratulations! You've completed Chapter 28: Advanced OOP Mastery!**

   You've mastered:
   - ✅ Object and class introspection (hasattr, getattr, dir, __dict__)
   - ✅ Type checking (isinstance, issubclass)
   - ✅ Identity vs equality (is vs ==, id())
   - ✅ Class structure inspection (__module__, __bases__, __mro__)
   - ✅ Multiple inheritance and the diamond problem
   - ✅ MRO and C3 linearization
   - ✅ Polymorphism (duck typing, inheritance, protocols)
   - ✅ Abstract base classes
   - ✅ Mixin pattern
   - ✅ 10 coding challenges
   - ✅ 5 debugging challenges
   - ✅ Comprehensive self-assessment

   **You're now an Advanced OOP master!** 🚀

---

What's Next?
------------

With Chapter 28 complete, you're ready for the final chapter!

**Chapter 29: PCAP Power Topics**
- Part 1: Advanced Exception Handling
- Part 2: Advanced File I/O
- Part 3: String Methods Mastery

**Continue your PCAP journey!** These final topics will complete your PCAP preparation. 💪

---

.. note::
   **✅ Chapter 28: Advanced OOP Mastery - COMPLETE!**

   You've completed all 7 sections:
   1. Object Introspection
   2. Type Checking
   3. Identity vs Equality
   4. Class Introspection
   5. Multiple Inheritance and the Diamond Problem
   6. MRO and Advanced Polymorphism
   7. Practice and Mastery

   **Total Content:**
   - 60+ interactive examples
   - 5 Parsons problems
   - 10 coding challenges
   - 5 debugging challenges
   - 40+ MCQs
   - Comprehensive reference guide

   **PCAP OOP topics: COMPLETE!** 🎯