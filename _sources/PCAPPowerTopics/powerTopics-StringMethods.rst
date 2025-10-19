..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 3: String Methods Mastery
==================================

.. index:: string methods, string manipulation, text processing

Introduction
------------

Strings are one of the most commonly used data types in Python. Mastering string methods is essential for PCAP certification and professional Python development.

Python provides **over 40 built-in string methods** for:

- **Case manipulation** — changing case patterns
- **Searching** — finding substrings
- **Validation** — checking string content
- **Modification** — altering strings
- **Splitting/Joining** — breaking apart and combining
- **Alignment** — formatting output
- **Testing** — checking patterns

In this section, you'll learn:

- Complete reference to all major string methods
- Practical examples for each method
- Common patterns and use cases
- Performance considerations
- Best practices

By the end, you'll be a string manipulation expert!

---

String Method Categories
-------------------------

.. index:: string method categories

**All string methods organized by category:**

.. list-table:: String Method Categories
   :widths: 30 70
   :header-rows: 1

   * - Category
     - Methods
   * - **Case Manipulation**
     - ``upper()``, ``lower()``, ``capitalize()``, ``title()``, ``swapcase()``, ``casefold()``
   * - **Search**
     - ``find()``, ``rfind()``, ``index()``, ``rindex()``, ``count()``
   * - **Validation**
     - ``isalpha()``, ``isdigit()``, ``isalnum()``, ``isspace()``, ``isupper()``, ``islower()``, ``istitle()``, ``isidentifier()``
   * - **Modification**
     - ``strip()``, ``lstrip()``, ``rstrip()``, ``replace()``
   * - **Splitting/Joining**
     - ``split()``, ``rsplit()``, ``splitlines()``, ``join()``, ``partition()``, ``rpartition()``
   * - **Alignment**
     - ``center()``, ``ljust()``, ``rjust()``, ``zfill()``
   * - **Testing**
     - ``startswith()``, ``endswith()``, ``in`` operator

.. important::
   **Key Point:** All string methods return a **new string** — strings are immutable!

   .. code-block:: python

      text = "hello"
      text.upper()  # Returns "HELLO" but doesn't change text
      print(text)   # Still "hello"

      text = text.upper()  # Must reassign to change
      print(text)   # Now "HELLO"

---

Case Manipulation Methods
--------------------------

.. index:: upper, lower, capitalize, title, swapcase, casefold

**Case manipulation methods change the case of characters.**

.. activecode:: string_case_methods
   :language: python

   text = "Hello World! Python is AWESOME."

   print("CASE MANIPULATION METHODS:")
   print("="*50)
   print(f"Original: '{text}'")
   print()

   # upper() - all uppercase
   print(f"upper():      '{text.upper()}'")

   # lower() - all lowercase
   print(f"lower():      '{text.lower()}'")

   # capitalize() - first char uppercase, rest lowercase
   print(f"capitalize(): '{text.capitalize()}'")

   # title() - first char of each word uppercase
   print(f"title():      '{text.title()}'")

   # swapcase() - swap upper/lower case
   print(f"swapcase():   '{text.swapcase()}'")

   # casefold() - aggressive lowercase (Unicode-aware)
   print(f"casefold():   '{text.casefold()}'")

   print("\n💡 Note: All return NEW strings (strings are immutable)")

**Output:**

::

   CASE MANIPULATION METHODS:
   ==================================================
   Original: 'Hello World! Python is AWESOME.'

   upper():      'HELLO WORLD! PYTHON IS AWESOME.'
   lower():      'hello world! python is awesome.'
   capitalize(): 'Hello world! python is awesome.'
   title():      'Hello World! Python Is Awesome.'
   swapcase():   'hELLO wORLD! pYTHON IS awesome.'
   casefold():   'hello world! python is awesome.'

   💡 Note: All return NEW strings (strings are immutable)

---

**lower() vs casefold():**

.. activecode:: string_lower_vs_casefold
   :language: python

   # For ASCII, they're the same
   text1 = "HELLO"
   print(f"ASCII text: '{text1}'")
   print(f"  lower():    '{text1.lower()}'")
   print(f"  casefold(): '{text1.casefold()}'")
   print(f"  Same? {text1.lower() == text1.casefold()}")

   # For Unicode, casefold() is more aggressive
   # German sharp-s (ß)
   text2 = "Straße"
   print(f"\nGerman text: '{text2}'")
   print(f"  lower():    '{text2.lower()}'")
   print(f"  casefold(): '{text2.casefold()}'")
   print(f"  Same? {text2.lower() == text2.casefold()}")

   print("\n💡 Use casefold() for case-insensitive comparisons!")
   print("   It handles international characters better.")

**Output:**

::

   ASCII text: 'HELLO'
     lower():    'hello'
     casefold(): 'hello'
     Same? True

   German text: 'Straße'
     lower():    'straße'
     casefold(): 'strasse'
     Same? False

   💡 Use casefold() for case-insensitive comparisons!
      It handles international characters better.

---

**Practical Case Example:**

.. activecode:: string_case_practical
   :language: python

   def format_name(first, last):
       """Format a name properly."""
       # Capitalize first letter, rest lowercase
       return f"{first.capitalize()} {last.capitalize()}"

   def format_title(text):
       """Format a title in title case."""
       return text.title()

   def normalize_email(email):
       """Normalize email to lowercase."""
       return email.lower()

   # Test
   print("Name Formatting:")
   print(f"  {format_name('jOHN', 'dOE')}")
   print(f"  {format_name('JANE', 'SMITH')}")

   print("\nTitle Formatting:")
   print(f"  {format_title('the lord of the rings')}")
   print(f"  {format_title('HARRY POTTER')}")

   print("\nEmail Normalization:")
   print(f"  {normalize_email('User@Example.COM')}")
   print(f"  {normalize_email('ADMIN@SITE.ORG')}")

**Output:**

::

   Name Formatting:
     John Doe
     Jane Smith

   Title Formatting:
     The Lord Of The Rings
     Harry Potter

   Email Normalization:
     user@example.com
     admin@site.org

---

Search Methods
--------------

.. index:: find, rfind, index, rindex, count

**Search methods locate substrings within strings.**

.. list-table:: Search Methods
   :widths: 25 40 35
   :header-rows: 1

   * - Method
     - Returns
     - On Not Found
   * - ``find(sub)``
     - Index of first occurrence
     - Returns -1
   * - ``rfind(sub)``
     - Index of last occurrence
     - Returns -1
   * - ``index(sub)``
     - Index of first occurrence
     - Raises ValueError
   * - ``rindex(sub)``
     - Index of last occurrence
     - Raises ValueError
   * - ``count(sub)``
     - Number of occurrences
     - Returns 0

.. activecode:: string_search_methods
   :language: python

   text = "Python is awesome. Python is powerful. Python rocks!"

   print("SEARCH METHODS:")
   print("="*50)
   print(f"Text: '{text}'")
   print()

   # find() - first occurrence
   print(f"find('Python'):       {text.find('Python')}")
   print(f"find('awesome'):      {text.find('awesome')}")
   print(f"find('missing'):      {text.find('missing')}")  # -1

   # rfind() - last occurrence
   print(f"\nrfind('Python'):      {text.rfind('Python')}")
   print(f"rfind('is'):          {text.rfind('is')}")

   # count() - number of occurrences
   print(f"\ncount('Python'):      {text.count('Python')}")
   print(f"count('is'):          {text.count('is')}")
   print(f"count('o'):           {text.count('o')}")

   # index() - like find() but raises error
   print(f"\nindex('awesome'):     {text.index('awesome')}")
   try:
       print(f"index('missing'):     {text.index('missing')}")
   except ValueError as e:
       print(f"index('missing'):     ValueError: {e}")

**Output:**

::

   SEARCH METHODS:
   ==================================================
   Text: 'Python is awesome. Python is powerful. Python rocks!'

   find('Python'):       0
   find('awesome'):      10
   find('missing'):      -1

   rfind('Python'):      42
   rfind('is'):          29

   count('Python'):      3
   count('is'):          2
   count('o'):           7

   index('awesome'):     10
   index('missing'):     ValueError: substring not found

---

**Search with Start/End Position:**

.. activecode:: string_search_positions
   :language: python

   text = "abcabcabc"

   print(f"Text: '{text}'")
   print("="*50)

   # find(sub, start, end)
   print("Finding 'abc' at different positions:")
   print(f"  find('abc'):          {text.find('abc')}")
   print(f"  find('abc', 1):       {text.find('abc', 1)}")
   print(f"  find('abc', 4):       {text.find('abc', 4)}")
   print(f"  find('abc', 0, 3):    {text.find('abc', 0, 3)}")
   print(f"  find('abc', 0, 2):    {text.find('abc', 0, 2)}")  # -1

   print("\n💡 Syntax: find(substring, start, end)")
   print("   start: begin searching from this index")
   print("   end: stop searching at this index")

**Output:**

::

   Text: 'abcabcabc'
   ==================================================
   Finding 'abc' at different positions:
     find('abc'):          0
     find('abc', 1):       3
     find('abc', 4):       6
     find('abc', 0, 3):    0
     find('abc', 0, 2):    -1

   💡 Syntax: find(substring, start, end)
      start: begin searching from this index
      end: stop searching at this index

---

**Practical Search Example:**

.. activecode:: string_search_practical
   :language: python

   def extract_domain(email):
       """Extract domain from email address."""
       at_pos = email.find('@')
       if at_pos == -1:
           return None
       return email[at_pos + 1:]

   def count_words(text, word):
       """Count occurrences of word (case-insensitive)."""
       return text.lower().count(word.lower())

   def find_all_positions(text, substring):
       """Find all positions of substring."""
       positions = []
       start = 0
       while True:
           pos = text.find(substring, start)
           if pos == -1:
               break
           positions.append(pos)
           start = pos + 1
       return positions

   # Test
   print("Extract Domain:")
   print(f"  {extract_domain('user@example.com')}")
   print(f"  {extract_domain('admin@site.org')}")

   print("\nCount Words:")
   text = "Python is great. python is powerful. PYTHON rocks!"
   print(f"  'python' appears {count_words(text, 'python')} times")

   print("\nFind All Positions:")
   text = "abcabcabc"
   print(f"  'abc' found at: {find_all_positions(text, 'abc')}")

**Output:**

::

   Extract Domain:
     example.com
     site.org

   Count Words:
     'python' appears 3 times

   Find All Positions:
     'abc' found at: [0, 3, 6]

---

Validation Methods
------------------

.. index:: isalpha, isdigit, isalnum, isspace, isupper, islower

**Validation methods check string content and return True/False.**

.. activecode:: string_validation_methods
   :language: python

   print("VALIDATION METHODS:")
   print("="*50)

   tests = [
       ("Hello", "text"),
       ("12345", "numbers"),
       ("Hello123", "mixed"),
       ("   ", "spaces"),
       ("UPPER", "uppercase"),
       ("lower", "lowercase"),
       ("Hello World", "title case"),
       ("_valid_id", "identifier"),
   ]

   print(f"{'String':<15} {'alpha':<7} {'digit':<7} {'alnum':<7} {'space':<7} {'upper':<7} {'lower':<7}")
   print("-" * 70)

   for string, desc in tests:
       print(f"{string:<15} "
             f"{str(string.isalpha()):<7} "
             f"{str(string.isdigit()):<7} "
             f"{str(string.isalnum()):<7} "
             f"{str(string.isspace()):<7} "
             f"{str(string.isupper()):<7} "
             f"{str(string.islower()):<7}")

**Output:**

::

   VALIDATION METHODS:
   ==================================================
   String          alpha   digit   alnum   space   upper   lower
   ----------------------------------------------------------------------
   Hello           True    False   True    False   False   False
   12345           False   True    True    False   False   False
   Hello123        False   False   True    False   False   False
                  False   False   False   True    False   False
   UPPER           True    False   True    False   True    False
   lower           True    False   True    False   False   True
   Hello World     False   False   False   False   False   False
   _valid_id       False   False   False   False   False   False

---

**Complete Validation Reference:**

.. activecode:: string_validation_complete
   :language: python

   print("COMPLETE VALIDATION METHODS:")
   print("="*50)

   strings = ["Hello123", "12345", "   ", "Hello World", "_name"]

   for s in strings:
       print(f"\nString: '{s}'")
       print(f"  isalpha():      {s.isalpha()}    # All letters?")
       print(f"  isdigit():      {s.isdigit()}    # All digits?")
       print(f"  isalnum():      {s.isalnum()}    # Letters or digits?")
       print(f"  isspace():      {s.isspace()}    # All whitespace?")
       print(f"  isupper():      {s.isupper()}    # All uppercase?")
       print(f"  islower():      {s.islower()}    # All lowercase?")
       print(f"  istitle():      {s.istitle()}    # Title case?")
       print(f"  isidentifier(): {s.isidentifier()} # Valid identifier?")

**Output:**

::

   COMPLETE VALIDATION METHODS:
   ==================================================

   String: 'Hello123'
     isalpha():      False    # All letters?
     isdigit():      False    # All digits?
     isalnum():      True    # Letters or digits?
     isspace():      False    # All whitespace?
     isupper():      False    # All uppercase?
     islower():      False    # All lowercase?
     istitle():      False    # Title case?
     isidentifier(): False # Valid identifier?

   String: '12345'
     isalpha():      False    # All letters?
     isdigit():      True    # All digits?
     isalnum():      True    # Letters or digits?
     isspace():      False    # All whitespace?
     isupper():      False    # All uppercase?
     islower():      False    # All lowercase?
     istitle():      False    # Title case?
     isidentifier(): False # Valid identifier?

   String: '   '
     isalpha():      False    # All letters?
     isdigit():      False    # All digits?
     isalnum():      False    # Letters or digits?
     isspace():      True    # All whitespace?
     isupper():      False    # All uppercase?
     islower():      False    # All lowercase?
     istitle():      False    # Title case?
     isidentifier(): False # Valid identifier?

   String: 'Hello World'
     isalpha():      False    # All letters?
     isdigit():      False    # All digits?
     isalnum():      False    # Letters or digits?
     isspace():      False    # All whitespace?
     isupper():      False    # All uppercase?
     islower():      False    # All lowercase?
     istitle():      True    # Title case?
     isidentifier(): False # Valid identifier?

   String: '_name'
     isalpha():      False    # All letters?
     isdigit():      False    # All digits?
     isalnum():      False    # Letters or digits?
     isspace():      False    # All whitespace?
     isupper():      False    # All uppercase?
     islower():      True    # All lowercase?
     istitle():      False    # Title case?
     isidentifier(): True # Valid identifier?

---

**Practical Validation Example:**

.. activecode:: string_validation_practical
   :language: python

   def validate_username(username):
       """Validate username requirements."""
       errors = []

       if len(username) < 3:
           errors.append("Too short (min 3 characters)")

       if len(username) > 20:
           errors.append("Too long (max 20 characters)")

       if not username.isalnum() and '_' not in username:
           errors.append("Must contain only letters, numbers, or underscore")

       if username[0].isdigit():
           errors.append("Cannot start with a digit")

       return (len(errors) == 0, errors)

   def validate_password(password):
       """Validate password strength."""
       has_upper = any(c.isupper() for c in password)
       has_lower = any(c.islower() for c in password)
       has_digit = any(c.isdigit() for c in password)

       if len(password) < 8:
           return False, "Too short (min 8 characters)"

       if not (has_upper and has_lower and has_digit):
           return False, "Must have uppercase, lowercase, and digit"

       return True, "Valid password"

   # Test usernames
   print("Username Validation:")
   usernames = ["ab", "validuser", "user_123", "123user", "user@invalid"]

   for username in usernames:
       valid, errors = validate_username(username)
       status = "✅" if valid else "❌"
       print(f"  {status} '{username}': {', '.join(errors) if errors else 'Valid'}")

   # Test passwords
   print("\nPassword Validation:")
   passwords = ["short", "alllowercase", "NoDigits", "Valid123"]

   for password in passwords:
       valid, message = validate_password(password)
       status = "✅" if valid else "❌"
       print(f"  {status} '{password}': {message}")

**Output:**

::

   Username Validation:
     ❌ 'ab': Too short (min 3 characters)
     ✅ 'validuser': Valid
     ✅ 'user_123': Valid
     ❌ '123user': Cannot start with a digit
     ❌ 'user@invalid': Must contain only letters, numbers, or underscore

   Password Validation:
     ❌ 'short': Too short (min 8 characters)
     ❌ 'alllowercase': Must have uppercase, lowercase, and digit
     ❌ 'NoDigits': Must have uppercase, lowercase, and digit
     ✅ 'Valid123': Valid password

---

Modification Methods
--------------------

.. index:: strip, lstrip, rstrip, replace

**Modification methods alter strings and return new versions.**

.. activecode:: string_modification_methods
   :language: python

   text = "  Hello World!  "

   print("MODIFICATION METHODS:")
   print("="*50)
   print(f"Original: '{text}'")
   print()

   # strip() - remove whitespace from both ends
   print(f"strip():  '{text.strip()}'")

   # lstrip() - remove whitespace from left
   print(f"lstrip(): '{text.lstrip()}'")

   # rstrip() - remove whitespace from right
   print(f"rstrip(): '{text.rstrip()}'")

   # replace(old, new) - replace substring
   text2 = "Hello World!"
   print(f"\nOriginal: '{text2}'")
   print(f"replace('World', 'Python'): '{text2.replace('World', 'Python')}'")
   print(f"replace('o', '0'):          '{text2.replace('o', '0')}'")
   print(f"replace('l', 'L', 1):       '{text2.replace('l', 'L', 1)}'")

**Output:**

::

   MODIFICATION METHODS:
   ==================================================
   Original: '  Hello World!  '

   strip():  'Hello World!'
   lstrip(): 'Hello World!  '
   rstrip(): '  Hello World!'

   Original: 'Hello World!'
   replace('World', 'Python'): 'Hello Python!'
   replace('o', '0'):          'Hell0 W0rld!'
   replace('l', 'L', 1):       'HeLlo World!'

---

**Stripping Custom Characters:**

.. activecode:: string_strip_custom
   :language: python

   # strip() can remove any characters, not just whitespace

   print("STRIPPING CUSTOM CHARACTERS:")
   print("="*50)

   text = "***Hello***"
   print(f"Original: '{text}'")
   print(f"strip('*'): '{text.strip('*')}'")

   text = "...test..."
   print(f"\nOriginal: '{text}'")
   print(f"strip('.'): '{text.strip('.')}'")

   text = "xxxHello Worldxxx"
   print(f"\nOriginal: '{text}'")
   print(f"strip('x'): '{text.strip('x')}'")

   # Strip multiple characters
   text = "***...Hello...***"
   print(f"\nOriginal: '{text}'")
   print(f"strip('*.'): '{text.strip('*.')}'")

   print("\n💡 strip() removes ANY of the specified characters")
   print("   from both ends until it hits a different character")

**Output:**

::

   STRIPPING CUSTOM CHARACTERS:
   ==================================================
   Original: '***Hello***'
   strip('*'): 'Hello'

   Original: '...test...'
   strip('.'): 'test'

   Original: 'xxxHello Worldxxx'
   strip('x'): 'Hello World'

   Original: '***...Hello...***'
   strip('*.'): 'Hello'

   💡 strip() removes ANY of the specified characters
      from both ends until it hits a different character

---

**Practical Modification Example:**

.. activecode:: string_modification_practical
   :language: python

   def clean_input(text):
       """Clean user input."""
       # Remove whitespace
       cleaned = text.strip()
       # Normalize whitespace
       cleaned = ' '.join(cleaned.split())
       return cleaned

   def sanitize_filename(filename):
       """Make filename safe."""
       # Replace invalid characters
       invalid_chars = '<>:"/\\|?*'
       safe = filename
       for char in invalid_chars:
           safe = safe.replace(char, '_')
       return safe.strip()

   def mask_credit_card(number):
       """Mask credit card number."""
       # Remove spaces/dashes
       clean = number.replace(' ', '').replace('-', '')
       # Mask all but last 4 digits
       if len(clean) >= 4:
           return '*' * (len(clean) - 4) + clean[-4:]
       return clean

   # Test
   print("Clean Input:")
   print(f"  '{clean_input('  hello   world  ')}'")
   print(f"  '{clean_input('\\n  test  \\t')}'")

   print("\nSanitize Filename:")
   print(f"  '{sanitize_filename('my<file>:name.txt')}'")
   print(f"  '{sanitize_filename('report|2024.pdf')}'")

   print("\nMask Credit Card:")
   print(f"  '{mask_credit_card('1234 5678 9012 3456')}'")
   print(f"  '{mask_credit_card('1234-5678-9012-3456')}'")

**Output:**

::

   Clean Input:
     'hello world'
     'test'

   Sanitize Filename:
     'my_file__name.txt'
     'report_2024.pdf'

   Mask Credit Card:
     '************3456'
     '************3456'

---

Splitting and Joining
----------------------

.. index:: split, rsplit, splitlines, join, partition, rpartition

**Splitting breaks strings apart; joining combines them.**

.. activecode:: string_split_join
   :language: python

   text = "apple,banana,cherry,date"

   print("SPLITTING AND JOINING:")
   print("="*50)
   print(f"Original: '{text}'")
   print()

   # split() - split by delimiter
   parts = text.split(',')
   print(f"split(','): {parts}")

   # join() - join with delimiter
   joined = ' | '.join(parts)
   print(f"' | '.join(parts): '{joined}'")

   # split with max splits
   text2 = "one two three four five"
   print(f"\nOriginal: '{text2}'")
   print(f"split(' '):    {text2.split(' ')}")
   print(f"split(' ', 2): {text2.split(' ', 2)}")

   # rsplit() - split from right
   print(f"rsplit(' ', 2): {text2.rsplit(' ', 2)}")

**Output:**

::

   SPLITTING AND JOINING:
   ==================================================
   Original: 'apple,banana,cherry,date'

   split(','): ['apple', 'banana', 'cherry', 'date']
   ' | '.join(parts): 'apple | banana | cherry | date'

   Original: 'one two three four five'
   split(' '):    ['one', 'two', 'three', 'four', 'five']
   split(' ', 2): ['one', 'two', 'three four five']
   rsplit(' ', 2): ['one two three', 'four', 'five']

---

**splitlines() for Line Breaks:**

.. activecode:: string_splitlines
   :language: python

   text = """Line 1
   Line 2
   Line 3"""

   print("SPLITLINES:")
   print("="*50)
   print(f"Text: {repr(text)}")
   print()

   # splitlines() - split on line breaks
   lines = text.splitlines()
   print(f"splitlines(): {lines}")

   # splitlines(keepends=True) - keep line breaks
   lines_with_breaks = text.splitlines(keepends=True)
   print(f"splitlines(True): {lines_with_breaks}")

   print("\n💡 splitlines() handles all line break types:")
   print("   \\n (Linux/Mac), \\r\\n (Windows), \\r (old Mac)")

**Output:**

::

   SPLITLINES:
   ==================================================
   Text: 'Line 1\nLine 2\nLine 3'

   splitlines(): ['Line 1', 'Line 2', 'Line 3']
   splitlines(True): ['Line 1\n', 'Line 2\n', 'Line 3']

   💡 splitlines() handles all line break types:
      \n (Linux/Mac), \r\n (Windows), \r (old Mac)

---

**partition() and rpartition():**

.. activecode:: string_partition
   :language: python

   email = "user@example.com"

   print("PARTITION METHODS:")
   print("="*50)
   print(f"Email: '{email}'")
   print()

   # partition() - split at first occurrence
   before, sep, after = email.partition('@')
   print(f"partition('@'):")
   print(f"  Before: '{before}'")
   print(f"  Separator: '{sep}'")
   print(f"  After: '{after}'")

   # rpartition() - split at last occurrence
   path = "/home/user/documents/file.txt"
   print(f"\nPath: '{path}'")
   before, sep, after = path.rpartition('/')
   print(f"rpartition('/'):")
   print(f"  Directory: '{before}'")
   print(f"  Separator: '{sep}'")
   print(f"  Filename: '{after}'")

   print("\n💡 partition returns 3-tuple: (before, separator, after)")
   print("   If separator not found: (original, '', '')")

**Output:**

::

   PARTITION METHODS:
   ==================================================
   Email: 'user@example.com'

   partition('@'):
     Before: 'user'
     Separator: '@'
     After: 'example.com'

   Path: '/home/user/documents/file.txt'
   rpartition('/'):
     Directory: '/home/user/documents'
     Separator: '/'
     Filename: 'file.txt'

   💡 partition returns 3-tuple: (before, separator, after)
      If separator not found: (original, '', '')

---

**Practical Splitting Example:**

.. activecode:: string_split_practical
   :language: python

   def parse_csv_line(line):
       """Parse CSV line into fields."""
       return [field.strip() for field in line.split(',')]

   def parse_key_value(line):
       """Parse key=value format."""
       if '=' not in line:
           return None, None
       key, _, value = line.partition('=')
       return key.strip(), value.strip()

   def get_file_extension(filename):
       """Extract file extension."""
       _, _, ext = filename.rpartition('.')
       return ext if ext else None

   # Test
   print("Parse CSV:")
   csv = "John, Doe, 30, Engineer"
   print(f"  {parse_csv_line(csv)}")

   print("\nParse Key-Value:")
   configs = ["name=Alice", "age=25", "invalid line"]
   for config in configs:
       key, value = parse_key_value(config)
       print(f"  '{config}' → key='{key}', value='{value}'")

   print("\nGet Extension:")
   files = ["document.pdf", "image.png", "archive.tar.gz", "noextension"]
   for file in files:
       ext = get_file_extension(file)
       print(f"  '{file}' → '{ext}'")

**Output:**

::

   Parse CSV:
     ['John', 'Doe', '30', 'Engineer']

   Parse Key-Value:
     'name=Alice' → key='name', value='Alice'
     'age=25' → key='age', value='25'
     'invalid line' → key='None', value='None'

   Get Extension:
     'document.pdf' → 'pdf'
     'image.png' → 'png'
     'archive.tar.gz' → 'gz'
     'noextension' → 'None'

---

Alignment Methods
-----------------

.. index:: center, ljust, rjust, zfill

**Alignment methods pad strings to a specified width.**

.. activecode:: string_alignment_methods
   :language: python

   text = "Python"

   print("ALIGNMENT METHODS:")
   print("="*50)
   print(f"Original: '{text}' (length {len(text)})")
   print()

   width = 20

   # center() - center align
   print(f"center({width}):      '{text.center(width)}'")
   print(f"center({width}, '*'): '{text.center(width, '*')}'")

   # ljust() - left justify
   print(f"\nljust({width}):       '{text.ljust(width)}'")
   print(f"ljust({width}, '-'):  '{text.ljust(width, '-')}'")

   # rjust() - right justify
   print(f"\nrjust({width}):       '{text.rjust(width)}'")
   print(f"rjust({width}, '.'):  '{text.rjust(width, '.')}'")

   # zfill() - pad with zeros
   number = "42"
   print(f"\nNumber: '{number}'")
   print(f"zfill(5): '{number.zfill(5)}'")
   print(f"zfill(8): '{number.zfill(8)}'")

**Output:**

::

   ALIGNMENT METHODS:
   ==================================================
   Original: 'Python' (length 6)

   center(20):      '       Python       '
   center(20, '*'): '*******Python*******'

   ljust(20):       'Python              '
   ljust(20, '-'):  'Python--------------'

   rjust(20):       '              Python'
   rjust(20, '.'):  '..............Python'

   Number: '42'
   zfill(5): '00042'
   zfill(8): '00000042'

---

**Practical Alignment Example:**

.. activecode:: string_alignment_practical
   :language: python

   def format_table(data):
       """Format data as aligned table."""
       # Calculate column widths
       widths = [max(len(str(row[i])) for row in data) for i in range(len(data[0]))]

       # Print header
       header = data[0]
       print(" | ".join(str(header[i]).ljust(widths[i]) for i in range(len(header))))
       print("-" * (sum(widths) + 3 * (len(widths) - 1)))

       # Print rows
       for row in data[1:]:
           print(" | ".join(str(row[i]).ljust(widths[i]) for i in range(len(row))))

   def format_invoice(items):
       """Format invoice with prices."""
       print("INVOICE")
       print("="*40)
       for item, price in items:
           print(f"{item.ljust(30)} ${str(price).rjust(6)}")
       print("="*40)
       total = sum(price for _, price in items)
       print(f"{'TOTAL'.ljust(30)} ${str(total).rjust(6)}")

   # Test table
   print("Table Formatting:")
   data = [
       ["Name", "Age", "City"],
       ["Alice", "30", "NYC"],
       ["Bob", "25", "LA"],
       ["Charlie", "35", "Chicago"]
   ]
   format_table(data)

   # Test invoice
   print("\n\nInvoice Formatting:")
   items = [
       ("Widget", 19.99),
       ("Gadget", 29.99),
       ("Gizmo", 9.99)
   ]
   format_invoice(items)

**Output:**

::

   Table Formatting:
   Name    | Age | City
   -------------------------
   Alice   | 30  | NYC
   Bob     | 25  | LA
   Charlie | 35  | Chicago


   Invoice Formatting:
   ========================================
   INVOICE
   ========================================
   Widget                         $ 19.99
   Gadget                         $ 29.99
   Gizmo                          $  9.99
   ========================================
   TOTAL                          $ 59.97

---

Testing Methods
---------------

.. index:: startswith, endswith, in operator

**Testing methods check string patterns.**

.. activecode:: string_testing_methods
   :language: python

   filename = "report.pdf"
   url = "https://example.com"
   text = "Hello World"

   print("TESTING METHODS:")
   print("="*50)

   # startswith()
   print(f"'{filename}' starts with 'report': {filename.startswith('report')}")
   print(f"'{url}' starts with 'https': {url.startswith('https')}")

   # endswith()
   print(f"\n'{filename}' ends with '.pdf': {filename.endswith('.pdf')}")
   print(f"'{filename}' ends with '.txt': {filename.endswith('.txt')}")

   # Multiple options (tuple)
   print(f"\n'{filename}' ends with ('.pdf', '.doc'): {filename.endswith(('.pdf', '.doc'))}")
   print(f"'{url}' starts with ('http://', 'https://'): {url.startswith(('http://', 'https://'))}")

   # in operator
   print(f"\n'World' in '{text}': {'World' in text}")
   print(f"'Python' in '{text}': {'Python' in text}")

**Output:**

::

   TESTING METHODS:
   ==================================================
   'report.pdf' starts with 'report': True
   'https://example.com' starts with 'https': True

   'report.pdf' ends with '.pdf': True
   'report.pdf' ends with '.txt': False

   'report.pdf' ends with ('.pdf', '.doc'): True
   'https://example.com' starts with ('http://', 'https://'): True

   'World' in 'Hello World': True
   'Python' in 'Hello World': False

---

**Practical Testing Example:**

.. activecode:: string_testing_practical
   :language: python

   def is_image_file(filename):
       """Check if file is an image."""
       image_extensions = ('.jpg', '.jpeg', '.png', '.gif', '.bmp')
       return filename.lower().endswith(image_extensions)

   def is_secure_url(url):
       """Check if URL is secure (HTTPS)."""
       return url.lower().startswith('https://')

   def contains_profanity(text):
       """Check if text contains banned words."""
       banned = ['badword1', 'badword2', 'badword3']
       text_lower = text.lower()
       return any(word in text_lower for word in banned)

   # Test
   print("Image File Check:")
   files = ["photo.jpg", "document.pdf", "image.PNG", "video.mp4"]
   for file in files:
       status = "✅" if is_image_file(file) else "❌"
       print(f"  {status} {file}")

   print("\nSecure URL Check:")
   urls = ["https://secure.com", "http://unsecure.com", "ftp://server.com"]
   for url in urls:
       status = "✅" if is_secure_url(url) else "❌"
       print(f"  {status} {url}")

   print("\nProfanity Check:")
   comments = ["This is fine", "Contains badword1 here", "Clean comment"]
   for comment in comments:
       status = "❌" if contains_profanity(comment) else "✅"
       print(f"  {status} {comment}")

**Output:**

::

   Image File Check:
     ✅ photo.jpg
     ❌ document.pdf
     ✅ image.PNG
     ❌ video.mp4

   Secure URL Check:
     ✅ https://secure.com
     ❌ http://unsecure.com
     ❌ ftp://server.com

   Profanity Check:
     ✅ This is fine
     ❌ Contains badword1 here
     ✅ Clean comment

---

Check Your Understanding
-------------------------

.. mchoice:: string_immutable
   :answer_a: Strings can be modified in place
   :answer_b: String methods return new strings
   :answer_c: String methods modify the original
   :answer_d: Strings are mutable
   :correct: b
   :feedback_a: Strings are immutable—they cannot be modified in place!
   :feedback_b: Correct! String methods always return NEW strings; the original is unchanged.
   :feedback_c: String methods never modify the original (strings are immutable).
   :feedback_d: Strings are immutable, not mutable!

   What happens when you call a string method like ``upper()``?

.. mchoice:: string_find_vs_index
   :answer_a: They are identical
   :answer_b: find() returns -1, index() raises ValueError
   :answer_c: find() raises error, index() returns -1
   :answer_d: index() is faster
   :correct: b
   :feedback_a: They behave differently when substring is not found!
   :feedback_b: Correct! find() returns -1 when not found; index() raises ValueError.
   :feedback_c: You have it backwards!
   :feedback_d: Performance is similar; the difference is error handling.

   What's the difference between ``find()`` and ``index()`` when a substring is not found?

.. mchoice:: string_strip_behavior
   :answer_a: Removes from the middle
   :answer_b: Removes from both ends only
   :answer_c: Removes all occurrences
   :answer_d: Converts to lowercase
   :correct: b
   :feedback_a: strip() only removes from the ends, not the middle.
   :feedback_b: Correct! strip() removes specified characters from the beginning and end only.
   :feedback_c: It only removes from the ends, not all occurrences.
   :feedback_d: strip() doesn't change case.

   What does ``strip()`` do?

.. mchoice:: string_join_syntax
   :answer_a: join(separator, list)
   :answer_b: list.join(separator)
   :answer_c: separator.join(list)
   :answer_d: join(list, separator)
   :correct: c
   :feedback_a: That's not the Python syntax.
   :feedback_b: Lists don't have a join() method.
   :feedback_c: Correct! The separator string calls join() with the list: separator.join(list).
   :feedback_d: That's not the Python syntax.

   What's the correct syntax for ``join()``?

.. mchoice:: string_casefold_vs_lower
   :answer_a: They are identical
   :answer_b: casefold() is for ASCII only
   :answer_c: casefold() is more aggressive for Unicode
   :answer_d: lower() is better for comparisons
   :correct: c
   :feedback_a: They're different for Unicode characters!
   :feedback_b: Actually, casefold() is better for Unicode!
   :feedback_c: Correct! casefold() is more aggressive and handles Unicode better for comparisons.
   :feedback_d: casefold() is actually better for case-insensitive comparisons.

   What's the difference between ``lower()`` and ``casefold()``?

---

Key Takeaways
-------------

.. important::
   **Section 3 Summary: String Methods Mastery**

   ✅ **Strings are Immutable:**
      - All methods return NEW strings
      - Original string is never modified
      - Must reassign: ``text = text.upper()``

   ✅ **Case Manipulation:**
      ``upper()``, ``lower()``, ``capitalize()``, ``title()``, ``swapcase()``, ``casefold()``

   ✅ **Search Methods:**
      - ``find()`` / ``rfind()`` — return -1 if not found
      - ``index()`` / ``rindex()`` — raise ValueError if not found
      - ``count()`` — count occurrences

   ✅ **Validation Methods:**
      ``isalpha()``, ``isdigit()``, ``isalnum()``, ``isspace()``,
      ``isupper()``, ``islower()``, ``istitle()``, ``isidentifier()``

   ✅ **Modification Methods:**
      - ``strip()`` / ``lstrip()`` / ``rstrip()`` — remove characters from ends
      - ``replace(old, new)`` — replace substrings

   ✅ **Splitting and Joining:**
      - ``split()`` — break into list
      - ``join()`` — combine list into string
      - ``partition()`` / ``rpartition()`` — split at delimiter
      - ``splitlines()`` — split on line breaks

   ✅ **Alignment:**
      ``center()``, ``ljust()``, ``rjust()``, ``zfill()``

   ✅ **Testing:**
      ``startswith()``, ``endswith()``, ``in`` operator

   ✅ **Best Practices:**
      - Use ``casefold()`` for case-insensitive comparisons
      - Use ``find()`` when -1 is acceptable
      - Use ``index()`` when you want exceptions
      - Remember strings are immutable!

---

What's Next?
------------

You've completed all instructional content for PCAP Power Topics! Now it's time to test your mastery with comprehensive practice.

**In Section 4: Practice and Mastery**, you'll:

- Test your knowledge with MCQs
- Solve Parsons problems
- Build complete solutions with coding challenges
- Debug broken code
- Review all concepts with a checklist
- Use the quick reference guide

Ready to prove your mastery of the final chapter? Let's finish strong! 🚀

---

.. note::
   **✅ Section 3 Complete!**

   You've learned:
   - [✓] Complete string method reference
   - [✓] Case manipulation (6 methods)
   - [✓] Search methods (5 methods)
   - [✓] Validation methods (8+ methods)
   - [✓] Modification methods (4 methods)
   - [✓] Splitting and joining (6 methods)
   - [✓] Alignment methods (4 methods)
   - [✓] Testing methods (3 approaches)
   - [✓] Practical examples for all categories

   **Ready for the final practice section?** → Continue to Section 4!