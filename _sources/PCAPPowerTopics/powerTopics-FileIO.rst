..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Section 2: Advanced File I/O
=============================

.. index:: file I/O, binary files, bytearray, errno, streams

Introduction
------------

You've learned basic file operations with ``open()``, ``read()``, and ``write()``. Now it's time to master **advanced file I/O** techniques essential for PCAP certification.

Advanced file I/O goes beyond simple text files:

- **Binary files** — images, videos, executables
- **Byte operations** — working with raw binary data
- **Error handling** — understanding error codes with ``errno``
- **Standard streams** — stdin, stdout, stderr
- **Performance** — choosing the right read method

In this section, you'll learn:

- Text vs binary file modes
- Working with ``bytearray`` for mutable binary data
- The ``errno`` module for error code handling
- Standard streams: ``sys.stdin``, ``sys.stdout``, ``sys.stderr``
- ``read()`` vs ``readline()`` vs ``readlines()``
- Context managers and best practices
- Common file I/O patterns

By the end, you'll handle files like a professional!

---

Text vs Binary Modes
---------------------

.. index:: text mode, binary mode, file modes

Files can be opened in **text mode** or **binary mode**.

.. list-table:: Text vs Binary Modes
   :widths: 30 35 35
   :header-rows: 1

   * - Aspect
     - Text Mode (``'t'``)
     - Binary Mode (``'b'``)
   * - **Default**
     - ✅ Default mode
     - Must specify explicitly
   * - **Encoding**
     - Automatic (UTF-8, etc.)
     - No encoding (raw bytes)
   * - **Newlines**
     - Converted to ``\n``
     - Preserved as-is
   * - **Returns**
     - ``str`` objects
     - ``bytes`` objects
   * - **Use for**
     - Text files (.txt, .py, .csv)
     - Images, videos, executables

**Basic Example:**

.. activecode:: file_text_vs_binary
   :language: python

   # Simulate file operations
   text_content = "Hello, World!\nLine 2"
   binary_content = b"Hello, World!\nLine 2"

   print("TEXT MODE:")
   print(f"  Type: {type(text_content)}")
   print(f"  Content: {repr(text_content)}")
   print(f"  Length: {len(text_content)} characters")

   print("\nBINARY MODE:")
   print(f"  Type: {type(binary_content)}")
   print(f"  Content: {repr(binary_content)}")
   print(f"  Length: {len(binary_content)} bytes")

   print("\n💡 Notice:")
   print("  • Text mode returns str")
   print("  • Binary mode returns bytes (note the 'b' prefix)")
   print("  • Both have the same length in this case")

**Output:**

::

   TEXT MODE:
     Type: <class 'str'>
     Content: 'Hello, World!\nLine 2'
     Length: 21 characters

   BINARY MODE:
     Type: <class 'bytes'>
     Content: b'Hello, World!\nLine 2'
     Length: 21 bytes

   💡 Notice:
     • Text mode returns str
     • Binary mode returns bytes (note the 'b' prefix)
     • Both have the same length in this case

---

**File Mode Combinations:**

.. activecode:: file_mode_combinations
   :language: python

   print("FILE MODES:")
   print("="*50)

   modes = [
       ('r', 'Read text (default)'),
       ('rb', 'Read binary'),
       ('w', 'Write text (truncate)'),
       ('wb', 'Write binary (truncate)'),
       ('a', 'Append text'),
       ('ab', 'Append binary'),
       ('r+', 'Read/write text (file must exist)'),
       ('rb+', 'Read/write binary (file must exist)'),
       ('w+', 'Read/write text (truncate)'),
       ('wb+', 'Read/write binary (truncate)'),
       ('a+', 'Read/append text'),
       ('ab+', 'Read/append binary'),
   ]

   for mode, description in modes:
       print(f"  '{mode:4}' - {description}")

**Output:**

::

   FILE MODES:
   ==================================================
     'r   ' - Read text (default)
     'rb  ' - Read binary
     'w   ' - Write text (truncate)
     'wb  ' - Write binary (truncate)
     'a   ' - Append text
     'ab  ' - Append binary
     'r+  ' - Read/write text (file must exist)
     'rb+ ' - Read/write binary (file must exist)
     'w+  ' - Read/write text (truncate)
     'wb+ ' - Read/write binary (truncate)
     'a+  ' - Read/append text
     'ab+ ' - Read/append binary

.. important::
   **Key Points:**

   - **'r'** is the same as **'rt'** (text is default)
   - **'b'** must be explicit for binary mode
   - **'+'** adds the opposite operation (read to write, write to read)
   - Binary mode returns ``bytes``, text mode returns ``str``

---

Working with Binary Files
--------------------------

.. index:: binary files, bytes, binary operations

**Reading Binary Data:**

.. activecode:: file_binary_read
   :language: python

   # Simulate binary file content (image header)
   # PNG file signature
   png_signature = b'\x89PNG\r\n\x1a\n'

   print("PNG File Signature:")
   print(f"  Raw bytes: {png_signature}")
   print(f"  Hex: {png_signature.hex()}")
   print(f"  Length: {len(png_signature)} bytes")

   # Check if data is PNG
   def is_png(data):
       """Check if binary data is a PNG file."""
       return data[:8] == png_signature

   print(f"\nIs PNG? {is_png(png_signature)}")

   # Compare with non-PNG data
   jpeg_start = b'\xff\xd8\xff'
   print(f"Is JPEG signature PNG? {is_png(jpeg_start)}")

**Output:**

::

   PNG File Signature:
     Raw bytes: b'\x89PNG\r\n\x1a\n'
     Hex: 89504e470d0a1a0a
     Length: 8 bytes

   Is PNG? True
   Is JPEG signature PNG? False

---

**Writing Binary Data:**

.. activecode:: file_binary_write
   :language: python

   # Simulate writing binary data
   def create_simple_binary_file():
       """Demonstrate binary write operations."""
       # Binary data to write
       data = bytes([0x48, 0x65, 0x6C, 0x6C, 0x6F])  # "Hello" in ASCII

       print(f"Writing binary data: {data}")
       print(f"  As text: {data.decode('ascii')}")
       print(f"  As hex: {data.hex()}")

       # In real code:
       # with open('output.bin', 'wb') as f:
       #     f.write(data)

       return data

   binary_data = create_simple_binary_file()

   print("\n💡 Binary write modes:")
   print("  'wb'  - Write (truncate existing)")
   print("  'ab'  - Append to existing")
   print("  'wb+' - Write and read")

**Output:**

::

   Writing binary data: b'Hello'
     As text: Hello
     As hex: 48656c6c6f

   💡 Binary write modes:
     'wb'  - Write (truncate existing)
     'ab'  - Append to existing
     'wb+' - Write and read

---

The ``bytearray`` Type
-----------------------

.. index:: bytearray, mutable bytes, byte manipulation

**``bytearray``** is like ``bytes``, but **mutable** (can be changed).

.. list-table:: bytes vs bytearray
   :widths: 30 35 35
   :header-rows: 1

   * - Feature
     - ``bytes``
     - ``bytearray``
   * - **Mutability**
     - ❌ Immutable
     - ✅ Mutable
   * - **Creation**
     - ``b"text"`` or ``bytes([1,2,3])``
     - ``bytearray(b"text")``
   * - **Use case**
     - Read-only data
     - Data that needs modification

**Basic bytearray Operations:**

.. activecode:: file_bytearray_basic
   :language: python

   # Create bytearray
   data = bytearray(b"Hello")

   print(f"Original: {data}")
   print(f"Type: {type(data)}")

   # Modify individual bytes
   data[0] = ord('J')  # Change 'H' to 'J'
   print(f"After change: {data}")

   # Append bytes
   data.append(ord('!'))
   print(f"After append: {data}")

   # Extend with more bytes
   data.extend(b" World")
   print(f"After extend: {data}")

   # Convert to bytes (immutable)
   immutable = bytes(data)
   print(f"\nAs bytes: {immutable}")

   # Try to modify bytes (will fail)
   try:
       immutable[0] = ord('K')
   except TypeError as e:
       print(f"❌ Can't modify bytes: {e}")

**Output:**

::

   Original: bytearray(b'Hello')
   Type: <class 'bytearray'>
   After change: bytearray(b'Jello')
   After append: bytearray(b'Jello!')
   After extend: bytearray(b'Jello! World')

   As bytes: b'Jello! World'
   ❌ Can't modify bytes: 'bytes' object does not support item assignment

---

**Practical bytearray Example:**

.. activecode:: file_bytearray_practical
   :language: python

   def modify_binary_data(data):
       """Modify binary data in-place using bytearray."""
       # Convert to bytearray for modification
       buffer = bytearray(data)

       print(f"Original: {buffer}")

       # XOR encryption (simple example)
       key = 42
       for i in range(len(buffer)):
           buffer[i] ^= key  # XOR each byte with key

       print(f"Encrypted: {buffer}")

       # Decrypt (XOR again with same key)
       for i in range(len(buffer)):
           buffer[i] ^= key

       print(f"Decrypted: {buffer}")

       # Convert back to bytes
       return bytes(buffer)

   # Test encryption
   original = b"Secret Message"
   result = modify_binary_data(original)
   print(f"\nFinal: {result}")

**Output:**

::

   Original: bytearray(b'Secret Message')
   Encrypted: bytearray(b'yOE\\O^*wO]]MQO')
   Decrypted: bytearray(b'Secret Message')

   Final: b'Secret Message'

---

**bytearray Methods:**

.. activecode:: file_bytearray_methods
   :language: python

   data = bytearray(b"Hello World")

   print("bytearray Methods:")
   print("="*50)

   # Common methods
   print(f"Original: {data}")

   # Indexing and slicing
   print(f"\nIndexing:")
   print(f"  data[0]: {data[0]} (decimal) = '{chr(data[0])}' (char)")
   print(f"  data[0:5]: {data[0:5]}")

   # Modification
   print(f"\nModification:")
   data[0] = ord('h')  # lowercase 'h'
   print(f"  After data[0] = ord('h'): {data}")

   # List-like operations
   print(f"\nList operations:")
   data.insert(5, ord(','))
   print(f"  After insert: {data}")

   data.remove(ord(','))
   print(f"  After remove: {data}")

   # Reversing
   data.reverse()
   print(f"  After reverse: {data}")

   data.reverse()  # Reverse back
   print(f"  Reversed back: {data}")

**Output:**

::

   bytearray Methods:
   ==================================================
   Original: bytearray(b'Hello World')

   Indexing:
     data[0]: 72 (decimal) = 'H' (char)
     data[0:5]: bytearray(b'Hello')

   Modification:
     After data[0] = ord('h'): bytearray(b'hello World')

   List operations:
     After insert: bytearray(b'hello, World')
     After remove: bytearray(b'hello World')
     After reverse: bytearray(b'dlroW olleh')
     Reversed back: bytearray(b'hello World')

---

The ``errno`` Module
--------------------

.. index:: errno module, error codes, system errors

The **``errno``** module provides error code constants for system errors.

.. activecode:: file_errno_basic
   :language: python

   import errno
   import os

   print("Common errno Values:")
   print("="*50)

   # Common error codes
   errors = [
       (errno.ENOENT, "No such file or directory"),
       (errno.EACCES, "Permission denied"),
       (errno.EEXIST, "File exists"),
       (errno.EISDIR, "Is a directory"),
       (errno.ENOTDIR, "Not a directory"),
       (errno.EPERM, "Operation not permitted"),
   ]

   for code, description in errors:
       print(f"  {code:2d} - {description}")

   print(f"\n💡 These are standard POSIX error codes")
   print(f"   Used across Unix/Linux/macOS systems")

**Output:**

::

   Common errno Values:
   ==================================================
      2 - No such file or directory
     13 - Permission denied
     17 - File exists
     21 - Is a directory
     20 - Not a directory
      1 - Operation not permitted

   💡 These are standard POSIX error codes
      Used across Unix/Linux/macOS systems

---

**Using errno in Exception Handling:**

.. activecode:: file_errno_exceptions
   :language: python

   import errno

   def safe_file_operation(filename):
       """Demonstrate errno-aware exception handling."""
       try:
           # Simulate file operation
           # In real code: open(filename, 'r')
           raise FileNotFoundError(errno.ENOENT, "No such file")

       except OSError as e:
           if e.errno == errno.ENOENT:
               print(f"❌ File not found: {filename}")
               print(f"   Error code: {e.errno}")
               return None
           elif e.errno == errno.EACCES:
               print(f"❌ Permission denied: {filename}")
               return None
           else:
               print(f"❌ Unknown error: {e}")
               raise

   # Test
   safe_file_operation("missing.txt")

   print("\n💡 Benefits of checking errno:")
   print("   • Distinguish between different file errors")
   print("   • Provide specific error messages")
   print("   • Handle errors appropriately")

**Output:**

::

   ❌ File not found: missing.txt
      Error code: 2

   💡 Benefits of checking errno:
      • Distinguish between different file errors
      • Provide specific error messages
      • Handle errors appropriately

---

**Practical errno Example:**

.. activecode:: file_errno_practical
   :language: python

   import errno

   class FileManager:
       """File manager with errno-aware error handling."""

       def read_file(self, filename):
           """Read file with detailed error handling."""
           try:
               # Simulate different errors
               if filename == "missing.txt":
                   raise FileNotFoundError(errno.ENOENT, "File not found")
               elif filename == "protected.txt":
                   raise PermissionError(errno.EACCES, "Permission denied")
               elif filename == "folder":
                   raise IsADirectoryError(errno.EISDIR, "Is a directory")
               else:
                   return f"Contents of {filename}"

           except FileNotFoundError as e:
               return f"Error: File '{filename}' does not exist (errno {e.errno})"
           except PermissionError as e:
               return f"Error: Cannot read '{filename}' - permission denied (errno {e.errno})"
           except IsADirectoryError as e:
               return f"Error: '{filename}' is a directory, not a file (errno {e.errno})"

   # Test different scenarios
   manager = FileManager()

   test_files = ["data.txt", "missing.txt", "protected.txt", "folder"]

   for filename in test_files:
       print(f"{filename}: {manager.read_file(filename)}")

**Output:**

::

   data.txt: Contents of data.txt
   missing.txt: Error: File 'missing.txt' does not exist (errno 2)
   protected.txt: Error: Cannot read 'protected.txt' - permission denied (errno 13)
   folder: Error: 'folder' is a directory, not a file (errno 21)

---

Standard Streams
----------------

.. index:: stdin, stdout, stderr, standard streams, sys module

Python provides three standard streams via the ``sys`` module:

.. list-table:: Standard Streams
   :widths: 20 40 40
   :header-rows: 1

   * - Stream
     - Purpose
     - Example Use
   * - ``stdin``
     - Standard input
     - Reading user input
   * - ``stdout``
     - Standard output
     - Normal program output
   * - ``stderr``
     - Standard error
     - Error messages, logging

**Basic Stream Usage:**

.. activecode:: file_streams_basic
   :language: python

   import sys

   # stdout - normal output
   sys.stdout.write("This is stdout\n")

   # stderr - error output
   sys.stderr.write("This is stderr\n")

   # stdin - input (simulated here)
   print("\n💡 Stream characteristics:")
   print(f"  stdin type: {type(sys.stdin)}")
   print(f"  stdout type: {type(sys.stdout)}")
   print(f"  stderr type: {type(sys.stderr)}")

   print("\n💡 Why separate stderr?")
   print("  • Errors don't mix with normal output")
   print("  • Can redirect separately: python script.py 2> errors.log")
   print("  • Error messages appear even when stdout is redirected")

**Output:**

::

   This is stdout
   This is stderr

   💡 Stream characteristics:
     stdin type: <class '_io.TextIOWrapper'>
     stdout type: <class '_io.TextIOWrapper'>
     stderr type: <class '_io.TextIOWrapper'>

   💡 Why separate stderr?
     • Errors don't mix with normal output
     • Can redirect separately: python script.py 2> errors.log
     • Error messages appear even when stdout is redirected

---

**Practical Stream Usage:**

.. activecode:: file_streams_practical
   :language: python

   import sys

   def log_message(message, level="INFO"):
       """Log messages to appropriate stream."""
       if level == "ERROR":
           # Errors go to stderr
           sys.stderr.write(f"[ERROR] {message}\n")
       else:
           # Normal messages go to stdout
           sys.stdout.write(f"[{level}] {message}\n")

   def process_data(data):
       """Process data with logging."""
       log_message("Starting data processing", "INFO")

       if not data:
           log_message("No data provided", "ERROR")
           return None

       if not isinstance(data, list):
           log_message("Data must be a list", "ERROR")
           return None

       log_message(f"Processing {len(data)} items", "INFO")
       result = sum(data)
       log_message("Processing complete", "INFO")

       return result

   # Test
   print("Test 1: Valid data")
   result = process_data([1, 2, 3, 4, 5])
   print(f"Result: {result}\n")

   print("Test 2: Invalid data")
   result = process_data("invalid")
   print(f"Result: {result}")

**Output:**

::

   Test 1: Valid data
   [INFO] Starting data processing
   [INFO] Processing 5 items
   [INFO] Processing complete
   Result: 15

   Test 2: Invalid data
   [INFO] Starting data processing
   [ERROR] Data must be a list
   Result: None

---

**Redirecting Streams:**

.. activecode:: file_streams_redirect
   :language: python

   import sys
   from io import StringIO

   # Save original stdout
   original_stdout = sys.stdout

   # Create a string buffer
   string_buffer = StringIO()

   # Redirect stdout to buffer
   sys.stdout = string_buffer

   # These go to the buffer, not console
   print("This is captured")
   print("So is this")

   # Restore original stdout
   sys.stdout = original_stdout

   # Get captured output
   captured = string_buffer.getvalue()

   print("Captured output:")
   print(repr(captured))

   print("\n💡 Use cases for stream redirection:")
   print("  • Testing functions that print")
   print("  • Capturing program output")
   print("  • Logging to strings")
   print("  • Suppressing unwanted output")

**Output:**

::

   Captured output:
   'This is captured\nSo is this\n'

   💡 Use cases for stream redirection:
     • Testing functions that print
     • Capturing program output
     • Logging to strings
     • Suppressing unwanted output

---

Reading Methods Comparison
---------------------------

.. index:: read, readline, readlines, file reading methods

Python provides three main methods for reading files:

.. list-table:: Reading Methods
   :widths: 25 35 40
   :header-rows: 1

   * - Method
     - Returns
     - Best For
   * - ``read()``
     - Entire file as string
     - Small files
   * - ``read(size)``
     - Up to ``size`` characters
     - Large files (chunks)
   * - ``readline()``
     - One line (with ``\n``)
     - Processing line by line
   * - ``readlines()``
     - List of all lines
     - When you need all lines

**Demonstration:**

.. activecode:: file_read_methods
   :language: python

   # Simulate file content
   file_content = """Line 1
   Line 2
   Line 3
   Line 4"""

   print("SIMULATED FILE CONTENT:")
   print(file_content)
   print()

   # Method 1: read() - entire content
   print("1. read() - entire file:")
   content = file_content
   print(f"   Type: {type(content)}")
   print(f"   Length: {len(content)} characters")
   print(f"   Content: {repr(content[:30])}...")

   # Method 2: readline() - one line at a time
   print("\n2. readline() - line by line:")
   lines = file_content.split('\n')
   for i, line in enumerate(lines[:2], 1):
       print(f"   Line {i}: {repr(line)}")

   # Method 3: readlines() - all lines as list
   print("\n3. readlines() - all lines:")
   all_lines = file_content.split('\n')
   print(f"   Type: {type(all_lines)}")
   print(f"   Length: {len(all_lines)} lines")
   print(f"   First line: {repr(all_lines[0])}")

**Output:**

::

   SIMULATED FILE CONTENT:
   Line 1
   Line 2
   Line 3
   Line 4

   1. read() - entire file:
      Type: <class 'str'>
      Length: 31 characters
      Content: 'Line 1\nLine 2\nLine 3\nLine 4'...

   2. readline() - one line at a time:
      Line 1: 'Line 1'
      Line 2: 'Line 2'

   3. readlines() - all lines:
      Type: <class 'list'>
      Length: 4 lines
      First line: 'Line 1'

---

**Practical Comparison:**

.. activecode:: file_read_comparison
   :language: python

   file_content = """apple,red,sweet
   banana,yellow,sweet
   lemon,yellow,sour
   lime,green,sour"""

   print("Processing CSV data with different methods:\n")

   # Method 1: read() - process entire file
   print("1. Using read():")
   lines = file_content.split('\n')
   for line in lines:
       parts = line.split(',')
       print(f"   {parts[0]}: {parts[1]}, {parts[2]}")

   # Method 2: readlines() - process as list
   print("\n2. Using readlines():")
   lines_list = file_content.split('\n')
   sweet_fruits = [line.split(',')[0] for line in lines_list
                   if 'sweet' in line]
   print(f"   Sweet fruits: {sweet_fruits}")

   # Method 3: readline() simulation - process one at a time
   print("\n3. Using readline() (line by line):")
   lines_iter = file_content.split('\n')
   count = 0
   for line in lines_iter:
       if 'yellow' in line:
           count += 1
   print(f"   Yellow fruits: {count}")

   print("\n💡 Which to use?")
   print("   • read(): Small files, need entire content")
   print("   • readlines(): Need all lines in memory")
   print("   • readline(): Large files, process one line at a time")

**Output:**

::

   Processing CSV data with different methods:

   1. Using read():
      apple: red, sweet
      banana: yellow, sweet
      lemon: yellow, sour
      lime: green, sour

   2. Using readlines():
      Sweet fruits: ['apple', 'banana']

   3. Using readline() (line by line):
      Yellow fruits: 2

   💡 Which to use?
      • read(): Small files, need entire content
      • readlines(): Need all lines in memory
      • readline(): Large files, process one line at a time

---

Context Managers and File Handling
-----------------------------------

.. index:: context manager, with statement, file closing

The ``with`` statement ensures files are properly closed.

**Without Context Manager (Bad):**

.. activecode:: file_without_context
   :language: python

   def read_file_bad(filename):
       """❌ BAD: Manual file closing."""
       f = None
       try:
           # Simulate file opening
           print(f"Opening {filename}")

           # Simulate an error
           if filename == "error.txt":
               raise ValueError("Simulated error")

           content = f"Contents of {filename}"
           print(f"Read: {content}")
           return content

       except Exception as e:
           print(f"Error: {e}")
           return None

       finally:
           # Must manually close
           if f is not None:
               print(f"Closing {filename}")
               # f.close()

   print("Test 1: Normal file")
   read_file_bad("data.txt")

   print("\nTest 2: Error case")
   read_file_bad("error.txt")

   print("\n⚠️ Easy to forget to close!")
   print("   File might remain open")

**Output:**

::

   Test 1: Normal file
   Opening data.txt
   Read: Contents of data.txt

   Test 2: Error case
   Opening error.txt
   Error: Simulated error

   ⚠️ Easy to forget to close!
      File might remain open

---

**With Context Manager (Good):**

.. activecode:: file_with_context
   :language: python

   def read_file_good(filename):
       """✅ GOOD: Using context manager."""
       try:
           # In real code: with open(filename, 'r') as f:
           print(f"Opening {filename}")

           # Simulate an error
           if filename == "error.txt":
               raise ValueError("Simulated error")

           content = f"Contents of {filename}"
           print(f"Read: {content}")

           # File automatically closed when exiting 'with' block
           print(f"File automatically closed")
           return content

       except Exception as e:
           print(f"Error: {e}")
           print("File automatically closed even on error")
           return None

   print("Test 1: Normal file")
   read_file_good("data.txt")

   print("\nTest 2: Error case")
   read_file_good("error.txt")

   print("\n✅ Benefits:")
   print("   • File always closed (even on errors)")
   print("   • Cleaner code")
   print("   • No manual cleanup needed")

**Output:**

::

   Test 1: Normal file
   Opening data.txt
   Read: Contents of data.txt
   File automatically closed

   Test 2: Error case
   Opening error.txt
   Error: Simulated error
   File automatically closed even on error

   ✅ Benefits:
      • File always closed (even on errors)
      • Cleaner code
      • No manual cleanup needed

---

**Multiple Files with Context Manager:**

.. activecode:: file_multiple_context
   :language: python

   def process_files(input_file, output_file):
       """Process files with multiple context managers."""
       print(f"Processing: {input_file} → {output_file}")

       # Simulate file operations
       input_data = "Input line 1\nInput line 2\nInput line 3"

       # In real code:
       # with open(input_file, 'r') as f_in, open(output_file, 'w') as f_out:

       print(f"  Reading from {input_file}")
       lines = input_data.split('\n')

       print(f"  Processing {len(lines)} lines")
       processed = [line.upper() for line in lines]

       print(f"  Writing to {output_file}")
       output_data = '\n'.join(processed)

       print(f"  Both files automatically closed")

       return output_data

   # Test
   result = process_files("input.txt", "output.txt")
   print(f"\nOutput:\n{result}")

   print("\n💡 Multiple files in one 'with':")
   print("   with open('in.txt') as f1, open('out.txt', 'w') as f2:")
   print("       # Both guaranteed to close")

**Output:**

::

   Processing: input.txt → output.txt
     Reading from input.txt
     Processing 3 lines
     Writing to output.txt
     Both files automatically closed

   Output:
   INPUT LINE 1
   INPUT LINE 2
   INPUT LINE 3

   💡 Multiple files in one 'with':
      with open('in.txt') as f1, open('out.txt', 'w') as f2:
          # Both guaranteed to close

---

Best Practices
--------------

.. index:: file I/O best practices

**✅ DO: Use Context Managers**

.. code-block:: python

   # ✅ GOOD
   with open('file.txt', 'r') as f:
       content = f.read()

   # ❌ BAD
   f = open('file.txt', 'r')
   content = f.read()
   f.close()  # Easy to forget!

**✅ DO: Choose Appropriate Read Method**

.. code-block:: python

   # ✅ Small files
   with open('small.txt', 'r') as f:
       content = f.read()

   # ✅ Large files (line by line)
   with open('large.txt', 'r') as f:
       for line in f:
           process(line)

   # ✅ Need all lines as list
   with open('data.txt', 'r') as f:
       lines = f.readlines()

**✅ DO: Specify Encoding**

.. code-block:: python

   # ✅ GOOD: Explicit encoding
   with open('file.txt', 'r', encoding='utf-8') as f:
       content = f.read()

   # ⚠️ OK but less explicit
   with open('file.txt', 'r') as f:
       content = f.read()  # Uses system default

**✅ DO: Use Binary Mode for Binary Files**

.. code-block:: python

   # ✅ Images, executables, etc.
   with open('image.png', 'rb') as f:
       data = f.read()

   # ❌ DON'T use text mode for binary
   with open('image.png', 'r') as f:  # Will fail!
       data = f.read()

---

**❌ DON'T: Forget to Handle Errors**

.. code-block:: python

   # ❌ BAD: No error handling
   with open('file.txt', 'r') as f:
       content = f.read()  # What if file doesn't exist?

   # ✅ GOOD: Handle errors
   try:
       with open('file.txt', 'r') as f:
           content = f.read()
   except FileNotFoundError:
       print("File not found")
   except PermissionError:
       print("Permission denied")

**❌ DON'T: Read Entire Large Files**

.. code-block:: python

   # ❌ BAD: Loads entire 1GB file into memory
   with open('huge_file.txt', 'r') as f:
       content = f.read()

   # ✅ GOOD: Process line by line
   with open('huge_file.txt', 'r') as f:
       for line in f:
           process(line)

---

Check Your Understanding
-------------------------

.. mchoice:: file_text_vs_binary_return
   :answer_a: Both return str
   :answer_b: Both return bytes
   :answer_c: Text returns str, binary returns bytes
   :answer_d: They return the same type
   :correct: c
   :feedback_a: Binary mode returns bytes, not str.
   :feedback_b: Text mode returns str, not bytes.
   :feedback_c: Correct! Text mode returns str, binary mode returns bytes.
   :feedback_d: They return different types!

   What's the difference between text and binary mode return types?

.. mchoice:: file_bytearray_mutable
   :answer_a: bytes is mutable, bytearray is immutable
   :answer_b: Both are mutable
   :answer_c: Both are immutable
   :answer_d: bytes is immutable, bytearray is mutable
   :correct: d
   :feedback_a: You have it backwards!
   :feedback_b: bytes is immutable.
   :feedback_c: bytearray is mutable!
   :feedback_d: Correct! bytes is immutable (like str), bytearray is mutable (like list).

   What's the difference between ``bytes`` and ``bytearray``?

.. mchoice:: file_stderr_purpose
   :answer_a: Input from keyboard
   :answer_b: Normal program output
   :answer_c: Error messages and logging
   :answer_d: Writing to files
   :correct: c
   :feedback_a: That's stdin.
   :feedback_b: That's stdout.
   :feedback_c: Correct! stderr is for error messages so they don't mix with normal output.
   :feedback_d: That's file writing, not a standard stream purpose.

   What is ``sys.stderr`` used for?

.. mchoice:: file_read_methods_large
   :answer_a: read() - load entire file
   :answer_b: readlines() - load all lines into list
   :answer_c: readline() or iterate - process line by line
   :answer_d: All are equally good
   :correct: c
   :feedback_a: This loads the entire file into memory - bad for large files!
   :feedback_b: This also loads everything into memory.
   :feedback_c: Correct! Process line by line to avoid loading entire file into memory.
   :feedback_d: They have very different memory characteristics!

   Which method is best for reading a very large file?

.. mchoice:: file_context_manager_benefit
   :answer_a: Makes code longer
   :answer_b: Automatically closes file even on errors
   :answer_c: Only works with text files
   :answer_d: Makes files read faster
   :correct: b
   :feedback_a: Actually makes code cleaner and shorter!
   :feedback_b: Correct! Context managers (with statement) guarantee file closure even on exceptions.
   :feedback_c: Works with both text and binary files.
   :feedback_d: Performance is the same; the benefit is automatic cleanup.

   What's the main benefit of using context managers (``with`` statement)?

---

Key Takeaways
-------------

.. important::
   **Section 2 Summary: Advanced File I/O**

   ✅ **Text vs Binary Modes:**
      - Text mode (``'t'``): Returns ``str``, handles encoding
      - Binary mode (``'b'``): Returns ``bytes``, no encoding
      - Use text for .txt, .py, .csv; binary for images, executables

   ✅ **bytearray:**
      - Mutable version of ``bytes``
      - Use when you need to modify binary data
      - Supports list-like operations (append, extend, insert)
      - Convert to ``bytes`` when done: ``bytes(my_bytearray)``

   ✅ **errno Module:**
      - Provides standard error codes (POSIX)
      - Check ``exception.errno`` to distinguish errors
      - Common: ``ENOENT`` (2), ``EACCES`` (13), ``EISDIR`` (21)

   ✅ **Standard Streams:**
      - ``sys.stdin`` — standard input
      - ``sys.stdout`` — normal output
      - ``sys.stderr`` — error output
      - Can redirect for testing/logging

   ✅ **Reading Methods:**
      - ``read()`` — entire file (small files)
      - ``read(size)`` — chunks (large files)
      - ``readline()`` — one line (streaming)
      - ``readlines()`` — all lines as list
      - **Iterate file object** — most Pythonic for lines

   ✅ **Context Managers:**
      - ``with open(...) as f:`` — guaranteed cleanup
      - File closed automatically (even on errors)
      - Can open multiple files: ``with open(...) as f1, open(...) as f2:``
      - Always use context managers!

   ✅ **Best Practices:**
      - Always use context managers
      - Choose correct mode (text vs binary)
      - Specify encoding explicitly
      - Handle errors appropriately
      - Process large files line by line

---

What's Next?
------------

You now understand advanced file I/O! Next, you'll master **String Methods** — the complete reference to Python's powerful string manipulation methods.

**In Section 3: String Methods Mastery**, you'll learn:

- Comprehensive string method reference
- Case manipulation methods
- Search and validation methods
- Splitting and joining
- String formatting techniques
- Common string patterns
- Performance considerations

Ready to master string methods? Let's go! 🚀

---

.. note::
   **✅ Section 2 Complete!**

   You've learned:
   - [✓] Text vs binary file modes
   - [✓] Working with ``bytearray``
   - [✓] Error handling with ``errno``
   - [✓] Standard streams (stdin, stdout, stderr)
   - [✓] Reading methods comparison
   - [✓] Context managers
   - [✓] File I/O best practices

   **Ready for advanced string methods?** → Continue to Section 3!