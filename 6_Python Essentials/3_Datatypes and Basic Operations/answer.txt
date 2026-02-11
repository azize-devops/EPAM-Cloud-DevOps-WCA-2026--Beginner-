DATATYPES AND BASIC OPERATIONS - ANSWER
=======================================

This guide covers Python functions, data types, basic arithmetic operations,
and using the Python interactive shell for function definitions.


================================================================================
                     PYTHON DATA TYPES OVERVIEW
================================================================================

| Type      | Description              | Example                    |
|-----------|--------------------------|----------------------------|
| int       | Integer numbers          | 42, -7, 0                  |
| float     | Decimal numbers          | 3.14, -0.5, 2.0            |
| str       | Text strings             | "hello", 'world'           |
| bool      | Boolean values           | True, False                |
| list      | Ordered collection       | [1, 2, 3]                  |
| tuple     | Immutable collection     | (1, 2, 3)                  |
| dict      | Key-value pairs          | {"name": "John", "age": 30}|
| None      | Null value               | None                       |

================================================================================


STEP 1: Open Terminal
---------------------

Open your terminal:
- Windows: Command Prompt, PowerShell, or VS Code terminal
- macOS: Terminal app
- Linux: Terminal (Ctrl+Alt+T)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Terminal opened                                   |
|  Expected: Clean terminal window                         |
+----------------------------------------------------------+


STEP 2: Start Python Shell
--------------------------

Option A: Using virtual environment (recommended):

    # Activate virtual environment first
    source testenv/bin/activate    # Linux/macOS
    testenv\Scripts\activate       # Windows

    # Then start Python
    python3

Option B: Direct Python shell:

    python3
    # or on Windows:
    python

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Python shell started                              |
|  Expected: Python version and >>> prompt                 |
+----------------------------------------------------------+

You should see:

    Python 3.x.x (default, ...)
    Type "help", "copyright", "credits" or "license" for more information.
    >>>


STEP 3: Define the Function - Line 1
------------------------------------

Type the function definition:

    >>> def sum(a, b):

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: def sum(a, b): entered                            |
|  Expected: Prompt changes to ... (continuation)          |
+----------------------------------------------------------+

After pressing Enter, the prompt changes to "..." indicating
Python is waiting for the function body:

    >>> def sum(a, b):
    ...

Understanding the syntax:

    def         # Keyword to define a function
    sum         # Function name
    (a, b)      # Parameters (inputs)
    :           # Colon indicates start of function body


STEP 4: Add the Function Body
-----------------------------

Type the print statement WITH INDENTATION (use Tab or 4 spaces):

    ...     print(a + b)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: print(a + b) with indentation                     |
|  Expected: Indented line after ...                       |
+----------------------------------------------------------+

IMPORTANT: Indentation is crucial in Python!
- Use Tab key or 4 spaces
- The indentation defines the code block

What it looks like:

    >>> def sum(a, b):
    ...     print(a + b)
    ...

Understanding the code:

    print()     # Built-in function to display output
    a + b       # Addition operation
    a, b        # Variables received as parameters


STEP 5: Complete the Function Definition
-----------------------------------------

Press Enter on an empty line to complete the function:

    ...     print(a + b)
    ...                      <- Just press Enter here (empty line)
    >>>

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Empty line pressed, back to >>> prompt            |
|  Expected: Function defined, prompt returns to >>>       |
+----------------------------------------------------------+

The prompt returns to >>> indicating the function is now defined
and ready to use.


STEP 6: Call the Function
-------------------------

Call the sum function with arguments 2 and 4:

    >>> sum(2, 4)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: sum(2, 4) command                                 |
|  Expected: Function call visible                         |
+----------------------------------------------------------+

Expected output:

    >>> sum(2, 4)
    6

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Output showing 6                                  |
|  Expected: Number 6 displayed                            |
+----------------------------------------------------------+


COMPLETE SESSION
----------------

Here's the complete interaction:

    >>> def sum(a, b):
    ...     print(a + b)
    ...
    >>> sum(2, 4)
    6

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Complete session from def to output               |
|  Expected: All steps visible with result 6               |
+----------------------------------------------------------+


TRY MORE EXAMPLES
-----------------

Test with different values:

    >>> sum(10, 20)
    30

    >>> sum(100, 50)
    150

    >>> sum(-5, 10)
    5

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Multiple sum function calls                       |
|  Expected: Different results for different inputs        |
+----------------------------------------------------------+

The + operator also works with strings (concatenation):

    >>> sum("Hello, ", "World!")
    Hello, World!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: sum with strings                                  |
|  Expected: "Hello, World!" output                        |
+----------------------------------------------------------+


================================================================================
                     BASIC ARITHMETIC OPERATORS
================================================================================

| Operator | Description      | Example      | Result    |
|----------|------------------|--------------|-----------|
| +        | Addition         | 5 + 3        | 8         |
| -        | Subtraction      | 5 - 3        | 2         |
| *        | Multiplication   | 5 * 3        | 15        |
| /        | Division         | 5 / 3        | 1.666...  |
| //       | Floor division   | 5 // 3       | 1         |
| %        | Modulo           | 5 % 3        | 2         |
| **       | Exponentiation   | 5 ** 3       | 125       |

Examples in Python shell:

    >>> 10 + 5
    15
    >>> 10 - 5
    5
    >>> 10 * 5
    50
    >>> 10 / 5
    2.0
    >>> 10 // 3
    3
    >>> 10 % 3
    1
    >>> 2 ** 10
    1024

================================================================================


================================================================================
                     FUNCTION SYNTAX IN PYTHON
================================================================================

Basic function structure:

    def function_name(parameters):
        # Function body (indented)
        # Do something
        return result  # Optional

Examples:

    # Function with no parameters
    def greet():
        print("Hello!")

    # Function with parameters
    def greet_person(name):
        print("Hello, " + name + "!")

    # Function with return value
    def add(a, b):
        return a + b

    # Function with default parameter
    def greet_default(name="World"):
        print("Hello, " + name + "!")

Using the functions:

    >>> greet()
    Hello!

    >>> greet_person("Alice")
    Hello, Alice!

    >>> result = add(5, 3)
    >>> print(result)
    8

    >>> greet_default()
    Hello, World!

    >>> greet_default("Python")
    Hello, Python!

================================================================================


================================================================================
                     PRINT VS RETURN
================================================================================

print() - Displays output to console:

    def sum_print(a, b):
        print(a + b)

    >>> sum_print(2, 4)
    6
    >>> result = sum_print(2, 4)
    6
    >>> print(result)
    None                # Can't use the value!

return - Returns value for further use:

    def sum_return(a, b):
        return a + b

    >>> sum_return(2, 4)
    6
    >>> result = sum_return(2, 4)
    >>> print(result)
    6                   # Value is stored!
    >>> result * 2
    12                  # Can use for calculations!

Best practice: Use return for values, print for display.

================================================================================


================================================================================
                     TYPE CHECKING
================================================================================

Check the type of a value:

    >>> type(42)
    <class 'int'>

    >>> type(3.14)
    <class 'float'>

    >>> type("hello")
    <class 'str'>

    >>> type(True)
    <class 'bool'>

    >>> type([1, 2, 3])
    <class 'list'>

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: type() function examples                          |
|  Expected: Different type outputs                        |
+----------------------------------------------------------+


================================================================================
                     ALTERNATIVE: USING A .PY FILE
================================================================================

Instead of the interactive shell, you can create a file:

sum_function.py:

    def sum(a, b):
        print(a + b)

    # Call the function
    sum(2, 4)

Run from terminal:

    python3 sum_function.py

Output:

    6

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Running .py file                                  |
|  Expected: Same result as interactive shell              |
+----------------------------------------------------------+


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "IndentationError: expected an indented block"
Solution: Add indentation (Tab or 4 spaces) before print
    >>> def sum(a, b):
    ...     print(a + b)    # <-- Must be indented!

Problem: "SyntaxError: invalid syntax"
Solution: Check for missing colon after def line
    def sum(a, b):    # <-- Colon is required!

Problem: Function defined but nothing happens
Solution: You need to call the function
    >>> sum(2, 4)     # <-- Call the function!

Problem: "NameError: name 'sum' is not defined"
Solution: Define the function first before calling it

Problem: Prompt stuck at "..."
Solution: Either:
    - Add indented code and press Enter twice
    - Press Ctrl+C to cancel and start over

Problem: Getting None as output
Reason: print() returns None, function also returns None
    - This is normal behavior for print-based functions

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Common error and fix                              |
|  Expected: Error message and corrected code              |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Terminal opened successfully
[ ] Python shell started (python3)
[ ] Function defined with def sum(a, b):
[ ] Proper indentation used for print(a + b)
[ ] Empty line pressed to complete function
[ ] Function called with sum(2, 4)
[ ] Output shows 6
[ ] Understand function definition syntax
[ ] Understand basic arithmetic operators
[ ] Understand difference between print and return

================================================================================
