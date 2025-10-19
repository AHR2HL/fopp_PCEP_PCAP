..  Copyright (C)  Adam Roush.  Permission is granted to copy, distribute
    and/or modify this document under the terms of the GNU Free Documentation
    License, Version 1.3 or any later version published by the Free Software
    Foundation; with Invariant Sections being Forward, Prefaces, and
    Contributor List, no Front-Cover Texts, and no Back-Cover Texts.  A copy of
    the license is included in the section entitled "GNU Free Documentation
    License".

Installing Packages with pip
=============================

Python comes with a powerful standard library, but sometimes you need **more**. Want to build web applications? Analyze data? Create games? There's a package for that! In this lesson, you'll learn how to access **thousands of third-party packages** using ``pip``, Python's package installer.

What Is pip?
------------

**pip** (short for "Pip Installs Packages" or "Package Installer for Python") is Python's official package manager. It downloads and installs packages from the **Python Package Index (PyPI)**.

Think of pip as:

.. list-table:: pip Analogies
   :header-rows: 0
   :widths: 30 70

   * - **Like...**
     - **Explanation**
   * - 🏪 **An app store**
     - Browse and install software (packages) with one command
   * - 📦 **A delivery service**
     - Brings packages from PyPI's warehouse to your computer
   * - 🔧 **A package manager**
     - Installs, updates, and removes Python packages

.. note::

   **PyPI (Python Package Index):** The official repository of Python packages.

   - Website: https://pypi.org
   - Contains over **400,000 packages**
   - Free and open to all Python developers

Basic pip Commands
------------------

Let's explore the essential pip commands you'll use every day.

Checking pip Version
~~~~~~~~~~~~~~~~~~~~

First, verify pip is installed:

.. code-block:: bash

   pip --version

**Example output:**
   .. code-block:: text

      pip 23.2.1 from /usr/lib/python3.11/site-packages/pip (python 3.11)

.. note::

   **Different pip commands:**

   - ``pip`` — Usually works on most systems
   - ``pip3`` — Explicitly uses Python 3's pip
   - ``python -m pip`` — Guaranteed to use the correct Python version's pip

   **Best practice:** Use ``python -m pip`` to avoid ambiguity!

Installing a Package
~~~~~~~~~~~~~~~~~~~~

Install a package with ``pip install``:

.. code-block:: bash

   pip install requests

This command:

1. **Searches** PyPI for the package named "requests"
2. **Downloads** the package and its dependencies
3. **Installs** it so you can import it in your code

**After installation:**

.. code-block:: python

   import requests

   response = requests.get('https://api.github.com')
   print(response.status_code)  # 200

.. important::

   **Package names:**

   - Use lowercase: ``pip install numpy`` ✓
   - Not ``pip install NumPy`` (though this often works)
   - Check exact names on PyPI if unsure

Installing Specific Versions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Install a specific version of a package:

.. code-block:: bash

   # Install exact version
   pip install requests==2.28.0

   # Install minimum version
   pip install requests>=2.25.0

   # Install version range
   pip install requests>=2.25.0,<3.0.0

**Version specifiers:**

.. list-table:: Version Operators
   :header-rows: 1
   :widths: 20 80

   * - Operator
     - Meaning
   * - ``==2.28.0``
     - Exactly version 2.28.0
   * - ``>=2.25.0``
     - Version 2.25.0 or higher
   * - ``<3.0.0``
     - Any version below 3.0.0
   * - ``>=2.25.0,<3.0.0``
     - Between 2.25.0 (inclusive) and 3.0.0 (exclusive)

Upgrading a Package
~~~~~~~~~~~~~~~~~~~

Update an installed package to the latest version:

.. code-block:: bash

   pip install --upgrade requests

   # Or shorter:
   pip install -U requests

Uninstalling a Package
~~~~~~~~~~~~~~~~~~~~~~

Remove a package you no longer need:

.. code-block:: bash

   pip uninstall requests

You'll be asked to confirm:

.. code-block:: text

   Proceed (Y/n)? y

Listing Installed Packages
---------------------------

See what packages are installed in your Python environment.

List All Packages
~~~~~~~~~~~~~~~~~

.. code-block:: bash

   pip list

**Example output:**

.. code-block:: text

   Package    Version
   ---------- -------
   numpy      1.24.3
   pandas     2.0.2
   pip        23.2.1
   requests   2.31.0
   setuptools 68.0.0

Show Package Details
~~~~~~~~~~~~~~~~~~~~

Get detailed information about a specific package:

.. code-block:: bash

   pip show requests

**Example output:**

.. code-block:: text

   Name: requests
   Version: 2.31.0
   Summary: Python HTTP for Humans.
   Home-page: https://requests.readthedocs.io
   Author: Kenneth Reitz
   License: Apache 2.0
   Location: /usr/lib/python3.11/site-packages
   Requires: charset-normalizer, idna, urllib3, certifi
   Required-by:

.. mchoice:: pip_commands_understanding_1
   :answer_a: pip install requests
   :answer_b: pip download requests
   :answer_c: pip get requests
   :answer_d: pip add requests
   :correct: a
   :feedback_a: Correct! 'pip install' is the command to install packages.
   :feedback_b: 'pip download' exists but only downloads without installing.
   :feedback_c: There is no 'pip get' command.
   :feedback_d: There is no 'pip add' command; use 'pip install'.

   Which command installs the ``requests`` package?

.. mchoice:: pip_commands_understanding_2
   :answer_a: pip install package==2.5.0
   :answer_b: pip install package=2.5.0
   :answer_c: pip install package:2.5.0
   :answer_d: pip install package-2.5.0
   :correct: a
   :feedback_a: Correct! Use double equals (==) to specify an exact version.
   :feedback_b: Single equals doesn't work; use double equals (==).
   :feedback_c: Colons are not used for version specification.
   :feedback_d: Hyphens don't specify versions in pip commands.

   How do you install version 2.5.0 of a package?

Requirements Files
------------------

What Is a Requirements File?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A **requirements file** (usually named ``requirements.txt``) lists all the packages your project needs. It's like a **shopping list** for pip.

**Why use requirements files?**

- ✅ **Share projects easily** — Others can install all dependencies at once
- ✅ **Reproducible environments** — Same packages, same versions
- ✅ **Version control** — Track dependency changes over time
- ✅ **Documentation** — Clear record of what your project needs

Creating a Requirements File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can create a requirements file manually or automatically.

**Manual creation (requirements.txt):**

.. code-block:: text

   requests>=2.28.0
   numpy==1.24.3
   pandas>=2.0.0,<3.0.0
   matplotlib

**Automatic creation:**

.. code-block:: bash

   pip freeze > requirements.txt

``pip freeze`` outputs all installed packages with exact versions:

.. code-block:: text

   certifi==2023.5.7
   charset-normalizer==3.1.0
   idna==3.4
   numpy==1.24.3
   pandas==2.0.2
   python-dateutil==2.8.2
   pytz==2023.3
   requests==2.31.0
   six==1.16.0
   urllib3==2.0.3

.. note::

   **``pip freeze`` vs. manual requirements:**

   - ``pip freeze`` — Lists **everything** installed (including dependencies)
   - Manual — List only your **direct dependencies**

   **Best practice:** Manually list only what you directly use, with flexible version ranges.

Installing from a Requirements File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Install all packages listed in a requirements file:

.. code-block:: bash

   pip install -r requirements.txt

This is equivalent to running ``pip install`` for each package individually.

.. important::

   **When to use requirements files:**

   - ✅ Every project should have one
   - ✅ Commit it to version control (Git)
   - ✅ Update it when you add/remove dependencies
   - ✅ Share it with collaborators

Example Workflow
~~~~~~~~~~~~~~~~

Here's a typical workflow using requirements files:

.. code-block:: bash

   # 1. Start a new project
   mkdir myproject
   cd myproject

   # 2. Create virtual environment (covered next section)
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate

   # 3. Install packages as you need them
   pip install requests
   pip install pandas

   # 4. Generate requirements file
   pip freeze > requirements.txt

   # 5. Share project (include requirements.txt)
   # Others can now run:
   pip install -r requirements.txt

.. activecode:: requirements_file_simulation
   :nocodelens:

   # Simulating working with a requirements file

   def parse_requirements(requirements_text):
       """
       Parse a requirements.txt file content.
       Returns a list of (package, version) tuples.
       """
       packages = []
       for line in requirements_text.strip().split('\n'):
           line = line.strip()
           if line and not line.startswith('#'):
               # Simple parsing (handles == operator)
               if '==' in line:
                   package, version = line.split('==')
                   packages.append((package, version))
               else:
                   packages.append((line, 'latest'))
       return packages

   # Example requirements.txt content
   requirements = """
   requests==2.31.0
   numpy==1.24.3
   pandas>=2.0.0
   matplotlib
   # This is a comment
   """

   packages = parse_requirements(requirements)

   print("📦 Packages to install:")
   print("=" * 40)
   for package, version in packages:
       print(f"  • {package:15} {version}")

   print(f"\n✓ Total packages: {len(packages)}")

.. mchoice:: requirements_file_understanding
   :answer_a: pip install requirements.txt
   :answer_b: pip install -r requirements.txt
   :answer_c: pip load requirements.txt
   :answer_d: python requirements.txt
   :correct: b
   :feedback_a: Missing the -r flag to specify a requirements file.
   :feedback_b: Correct! Use -r (or --requirement) to install from a requirements file.
   :feedback_c: There is no 'pip load' command.
   :feedback_d: requirements.txt is not a Python script; use pip to process it.

   How do you install packages from a ``requirements.txt`` file?

Virtual Environments (Introduction)
------------------------------------

What Is a Virtual Environment?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A **virtual environment** is an isolated Python environment where you can install packages without affecting your system-wide Python installation.

**Metaphor:** Think of virtual environments like **separate apartments**:

- Each apartment (environment) has its own furniture (packages)
- Changes in one apartment don't affect others
- You can have different furniture (package versions) in each

**Why use virtual environments?**

1. **Isolation** — Different projects can use different package versions
2. **No conflicts** — Project A uses Django 3, Project B uses Django 4? No problem!
3. **Clean system** — Don't clutter your system Python installation
4. **Reproducibility** — Easy to recreate exact project environment

Creating a Virtual Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Python's built-in ``venv`` module creates virtual environments:

.. code-block:: bash

   # Create a virtual environment named 'venv'
   python -m venv venv

   # Or give it a custom name
   python -m venv myproject_env

This creates a directory (``venv``) containing:
- A copy of Python
- pip
- An isolated ``site-packages`` directory for packages

Activating a Virtual Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**On macOS/Linux:**

.. code-block:: bash

   source venv/bin/activate

**On Windows:**

.. code-block:: bash

   venv\Scripts\activate

**After activation:**
- Your prompt changes: ``(venv) $``
- ``pip install`` now installs to this environment only
- ``python`` uses this environment's Python

Deactivating a Virtual Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Return to your system Python:

.. code-block:: bash

   deactivate

Virtual Environment Workflow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Complete workflow for a new project:**

.. code-block:: bash

   # 1. Create project directory
   mkdir my_web_app
   cd my_web_app

   # 2. Create virtual environment
   python -m venv venv

   # 3. Activate it
   source venv/bin/activate  # macOS/Linux
   # venv\Scripts\activate   # Windows

   # 4. Install packages
   pip install flask
   pip install requests

   # 5. Save dependencies
   pip freeze > requirements.txt

   # 6. Work on your project...

   # 7. Deactivate when done
   deactivate

.. note::

   **Best Practice:**

   - ✅ Create a virtual environment for **every project**
   - ✅ Name it ``venv`` or ``.venv`` (standard names)
   - ✅ Add ``venv/`` to ``.gitignore`` (don't commit it)
   - ✅ Commit ``requirements.txt`` instead

.. important::

   **Virtual environments are NOT portable!**

   Don't try to copy a virtual environment to another computer. Instead:

   1. Share ``requirements.txt``
   2. Others create their own virtual environment
   3. Install from ``requirements.txt``

.. activecode:: venv_workflow_demo
   :nocodelens:

   # Simulating virtual environment workflow

   class VirtualEnvironment:
       """Simulate a virtual environment."""

       def __init__(self, name):
           self.name = name
           self.active = False
           self.packages = {}

       def activate(self):
           """Activate the virtual environment."""
           self.active = True
           print(f"({self.name}) $ Environment activated")

       def deactivate(self):
           """Deactivate the virtual environment."""
           self.active = False
           print(f"$ Environment deactivated")

       def install(self, package, version="latest"):
           """Install a package."""
           if not self.active:
               print("⚠️  Warning: No virtual environment active!")
               print("   Package will install to system Python.")
               return

           self.packages[package] = version
           print(f"({self.name}) $ pip install {package}")
           print(f"✓ Successfully installed {package} {version}")

       def list_packages(self):
           """List installed packages."""
           if not self.packages:
               print("No packages installed.")
               return

           print(f"\n📦 Packages in {self.name}:")
           print("=" * 40)
           for package, version in self.packages.items():
               print(f"  {package:15} {version}")

   # Demonstrate workflow
   print("🚀 Virtual Environment Workflow Demo\n")

   # Create and activate environment
   venv = VirtualEnvironment("myproject_env")
   venv.activate()

   # Install packages
   venv.install("requests", "2.31.0")
   venv.install("numpy", "1.24.3")

   # List packages
   venv.list_packages()

   # Deactivate
   print()
   venv.deactivate()

.. mchoice:: virtual_environment_understanding_1
   :answer_a: To make Python run faster
   :answer_b: To isolate project dependencies
   :answer_c: To save disk space
   :answer_d: To compile Python code
   :correct: b
   :feedback_a: Virtual environments don't affect performance.
   :feedback_b: Correct! Virtual environments isolate dependencies so projects don't interfere with each other.
   :feedback_c: Virtual environments actually use more disk space (one Python copy per environment).
   :feedback_d: Virtual environments don't compile code; they isolate packages.

   What is the main purpose of a virtual environment?

.. mchoice:: virtual_environment_understanding_2
   :answer_a: python -m venv myenv
   :answer_b: pip install venv myenv
   :answer_c: create venv myenv
   :answer_d: python venv myenv
   :correct: a
   :feedback_a: Correct! Use 'python -m venv' followed by the environment name.
   :feedback_b: venv is not installed with pip; it's built into Python.
   :feedback_c: This is not valid Python or pip syntax.
   :feedback_d: Missing the '-m' flag to run venv as a module.

   How do you create a virtual environment named ``myenv``?

Best Practices for Package Management
--------------------------------------

Here are professional tips for managing Python packages:

1. Always Use Virtual Environments
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # ✓ GOOD: Use virtual environment
   python -m venv venv
   source venv/bin/activate
   pip install requests

   # ✗ BAD: Install to system Python
   pip install requests  # Without virtual environment

2. Pin Important Versions
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   # ✓ GOOD: Specific versions for critical packages
   django==4.2.1
   requests>=2.28.0,<3.0.0

   # ⚠️ FLEXIBLE: Allow updates for minor packages
   python-dotenv>=1.0.0

3. Keep Requirements Files Updated
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # After adding a new package
   pip install new-package
   pip freeze > requirements.txt  # Update requirements

4. Check Package Documentation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Before installing, check the package's:
- Documentation on PyPI
- GitHub repository
- Installation instructions
- Dependencies

5. Use ``python -m pip``
~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # ✓ GOOD: Explicit Python version
   python -m pip install requests
   python3 -m pip install requests

   # ⚠️ WORKS: But less explicit
   pip install requests

Common Packages to Know
-----------------------

Here are some popular packages you'll encounter:

.. list-table:: Popular Python Packages
   :header-rows: 1
   :widths: 20 30 50

   * - Package
     - Category
     - Purpose
   * - ``requests``
     - Web
     - HTTP requests made easy
   * - ``flask``
     - Web
     - Lightweight web framework
   * - ``django``
     - Web
     - Full-featured web framework
   * - ``numpy``
     - Data Science
     - Numerical computing
   * - ``pandas``
     - Data Science
     - Data manipulation and analysis
   * - ``matplotlib``
     - Data Science
     - Data visualization
   * - ``pytest``
     - Testing
     - Testing framework
   * - ``black``
     - Development
     - Code formatter
   * - ``pillow``
     - Images
     - Image processing

.. activecode:: package_info_demo
   :nocodelens:

   # Simulate checking package information

   packages_db = {
       'requests': {
           'version': '2.31.0',
           'description': 'Python HTTP for Humans',
           'downloads': '500M+ monthly',
           'category': 'Web'
       },
       'numpy': {
           'version': '1.24.3',
           'description': 'Fundamental package for scientific computing',
           'downloads': '200M+ monthly',
           'category': 'Data Science'
       },
       'flask': {
           'version': '2.3.2',
           'description': 'Lightweight web framework',
           'downloads': '50M+ monthly',
           'category': 'Web'
       }
   }

   def show_package_info(package_name):
       """Display information about a package."""
       if package_name not in packages_db:
           print(f"Package '{package_name}' not found in database.")
           return

       info = packages_db[package_name]
       print(f"📦 {package_name}")
       print("=" * 50)
       print(f"Version:     {info['version']}")
       print(f"Description: {info['description']}")
       print(f"Category:    {info['category']}")
       print(f"Downloads:   {info['downloads']}")
       print()

   # Show info for popular packages
   for package in ['requests', 'numpy', 'flask']:
       show_package_info(package)

Practice Problems
-----------------

.. mchoice:: pip_workflow_scenario
   :answer_a: pip install -r requirements.txt
   :answer_b: pip install requirements.txt
   :answer_c: python requirements.txt
   :answer_d: pip freeze requirements.txt
   :correct: a
   :feedback_a: Correct! Use 'pip install -r requirements.txt' to install all dependencies.
   :feedback_b: Missing the -r flag; this won't work.
   :feedback_c: requirements.txt is not a Python script.
   :feedback_d: pip freeze generates requirements, it doesn't install from them.

   Your colleague shares a project with a ``requirements.txt`` file. How do you install all dependencies?

.. mchoice:: version_specifier_understanding
   :answer_a: Install exactly version 2.5.0
   :answer_b: Install version 2.5.0 or higher
   :answer_c: Install any version below 2.5.0
   :answer_d: Install the latest version
   :correct: b
   :feedback_a: That would be ==2.5.0 (double equals for exact version).
   :feedback_b: Correct! >= means 'greater than or equal to', so 2.5.0 or any higher version.
   :feedback_c: That would be <2.5.0 (less than operator).
   :feedback_d: Just the package name installs latest; >= specifies a minimum.

   What does ``package>=2.5.0`` mean in a requirements file?

.. parsonsprob:: venv_workflow_parsons
   :numbered: left
   :adaptive:

   Arrange the commands in the correct order to set up a new Python project with a virtual environment.
   -----
   mkdir myproject
   =====
   cd myproject
   =====
   python -m venv venv
   =====
   source venv/bin/activate
   =====
   pip install requests
   =====
   pip freeze > requirements.txt

.. activecode:: requirements_generator_challenge
   :nocodelens:

   Write a function ``format_requirement(package, version)`` that formats
   a package and version into a requirements.txt line.

   Rules:
   - If version is "latest", return just the package name
   - Otherwise, return "package==version"
   ~~~~
   def format_requirement(package, version):
       # Your code here
       pass

   # Test cases
   print(format_requirement("requests", "2.31.0"))  # Should print: requests==2.31.0
   print(format_requirement("flask", "latest"))     # Should print: flask

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_format_requirement_with_version(self):
           self.assertEqual(format_requirement("numpy", "1.24.3"), "numpy==1.24.3")

       def test_format_requirement_latest(self):
           self.assertEqual(format_requirement("pandas", "latest"), "pandas")

       def test_format_requirement_another(self):
           self.assertEqual(format_requirement("requests", "2.31.0"), "requests==2.31.0")

   MyTests().main()

.. activecode:: package_lister_challenge
   :nocodelens:

   Create a function ``simulate_pip_list(packages_dict)`` that takes a dictionary
   of packages (keys) and versions (values), and prints them in a formatted
   table like 'pip list' does.

   Format: Left-align package names in 15 characters, then show version.
   ~~~~
   def simulate_pip_list(packages_dict):
       # Your code here
       pass

   # Test data
   packages = {
       'numpy': '1.24.3',
       'requests': '2.31.0',
       'flask': '2.3.2'
   }

   simulate_pip_list(packages)
   # Expected output format:
   # numpy          1.24.3
   # requests       2.31.0
   # flask          2.3.2

   ====
   from unittest.gui import TestCaseGui

   class MyTests(TestCaseGui):
       def test_simulate_pip_list(self):
           # Just verify the function runs without errors
           packages = {'test': '1.0.0'}
           try:
               simulate_pip_list(packages)
               self.assertTrue(True)
           except Exception as e:
               self.fail(f"Function raised exception: {e}")

   MyTests().main()

Key Takeaways
-------------

.. admonition:: Summary

   * **pip** is Python's package installer
   * **PyPI** (pypi.org) hosts over 400,000 packages

   * **Essential pip commands:**

     * ``pip install <package>`` — Install a package
     * ``pip install <package>==<version>`` — Install specific version
     * ``pip install -U <package>`` — Upgrade a package
     * ``pip uninstall <package>`` — Remove a package
     * ``pip list`` — Show installed packages
     * ``pip show <package>`` — Show package details
     * ``pip freeze > requirements.txt`` — Save dependencies
     * ``pip install -r requirements.txt`` — Install from requirements file

   * **Virtual environments:**

     * Create: ``python -m venv venv``
     * Activate: ``source venv/bin/activate`` (macOS/Linux) or ``venv\Scripts\activate`` (Windows)
     * Deactivate: ``deactivate``

   * **Best practices:**

     * ✅ Always use virtual environments
     * ✅ Keep requirements.txt updated
     * ✅ Use ``python -m pip`` for clarity
     * ✅ Pin critical package versions

----

What's Next?
------------

You've mastered pip and package management!

In the final lesson of this chapter, you'll consolidate everything:

- **Nested packages** and complex package structures
- **Public vs. private** variables in modules (``_`` and ``__`` prefixes)
- **Practice problems** bringing together all concepts
- **Real-world scenarios** and best practices

.. note::

   **✅ Lesson 6 Complete!**

   **You've learned package management with pip:**

   - ✅ Understanding pip and PyPI
   - ✅ Installing, upgrading, and uninstalling packages
   - ✅ Working with requirements files (``requirements.txt``)
   - ✅ Creating and using virtual environments
   - ✅ Best practices for dependency management
   - ✅ Professional Python project workflow

   **Next:** Master nested packages and final practice to complete Chapter 26!