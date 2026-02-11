HELLO, PYTHON! - ANSWER
=======================

This guide covers installing Visual Studio Code, Python extensions,
and running your first Python program.


================================================================================
                     STEP 1: DOWNLOAD VISUAL STUDIO CODE
================================================================================

1. Open your browser and go to:
   https://code.visualstudio.com/

2. Click the "Download" button for your operating system:
   - Windows: Download the .exe installer
   - macOS: Download the .dmg file
   - Linux: Download .deb or .rpm package

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: VS Code download page                             |
|  Expected: Download button visible                       |
+----------------------------------------------------------+

3. Run the installer and follow the installation wizard:
   - Accept the license agreement
   - Choose installation location
   - Select additional tasks (recommended):
     [x] Add "Open with Code" to context menu
     [x] Add to PATH

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: VS Code installation wizard                       |
|  Expected: Installation options selected                 |
+----------------------------------------------------------+

4. Complete the installation and launch VS Code.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: VS Code welcome screen                            |
|  Expected: Fresh VS Code installation                    |
+----------------------------------------------------------+


================================================================================
                     STEP 2: INSTALL PYTHON (if not installed)
================================================================================

Check if Python is installed:

    # Windows (Command Prompt or PowerShell)
    python --version

    # macOS/Linux
    python3 --version

If Python is not installed:

1. Go to: https://www.python.org/downloads/

2. Download the latest Python version

3. Run the installer:
   - [x] IMPORTANT: Check "Add Python to PATH"
   - Click "Install Now"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Python installer with "Add to PATH" checked       |
|  Expected: Checkbox selected before installation         |
+----------------------------------------------------------+

Verify installation:

    python --version
    # or
    python3 --version

Expected output:
    Python 3.x.x

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Python version check in terminal                  |
|  Expected: Python 3.x.x displayed                        |
+----------------------------------------------------------+


================================================================================
                     STEP 3: INSTALL VS CODE EXTENSIONS
================================================================================

Method 1: Install from VS Code

1. Open VS Code

2. Click the Extensions icon in the left sidebar (or press Ctrl+Shift+X)

3. Search for "Python" and install the Microsoft Python extension

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: VS Code Extensions marketplace                    |
|  Expected: Python extension search results               |
+----------------------------------------------------------+

4. Search for "Pylance" and install it

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Pylance extension installation                    |
|  Expected: Pylance extension page with Install button    |
+----------------------------------------------------------+

Method 2: Install from Marketplace links

1. Python extension:
   https://marketplace.visualstudio.com/items?itemName=ms-python.python
   - Click "Install" on the webpage
   - Allow browser to open VS Code
   - Confirm installation in VS Code

2. Pylance extension:
   https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance
   - Same process as above

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Both extensions installed                         |
|  Expected: Python and Pylance in installed extensions    |
+----------------------------------------------------------+


================================================================================
                     STEP 4: CREATE AND OPEN TEST.PY
================================================================================

Method 1: Create from VS Code

1. Open VS Code

2. Click File > New File (or press Ctrl+N)

3. Save the file as "test.py":
   - File > Save As (or Ctrl+S)
   - Choose location (e.g., Desktop or Documents)
   - Name: test.py
   - Save

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Save As dialog with test.py                       |
|  Expected: File name as test.py                          |
+----------------------------------------------------------+

Method 2: Create from terminal

    # Windows
    cd Desktop
    echo. > test.py
    code test.py

    # macOS/Linux
    cd ~/Desktop
    touch test.py
    code test.py

Method 3: Create from File Explorer

1. Navigate to your desired folder
2. Right-click > New > Text Document
3. Rename to "test.py" (change extension from .txt)
4. Right-click > Open with Code


================================================================================
                     STEP 5: WRITE THE CODE
================================================================================

In the test.py file, type:

    print("Hello, python!")

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: test.py with print statement                      |
|  Expected: Code visible in VS Code editor                |
+----------------------------------------------------------+

Understanding the code:

    print()             # Built-in function to output text
    "Hello, python!"    # String (text) to be printed
    print("...")        # Outputs the string to console

Save the file: Ctrl+S (or Cmd+S on macOS)


================================================================================
                     STEP 6: RUN THE SCRIPT
================================================================================

Method 1: Run Button (Recommended)

1. Look at the top-right corner of VS Code
2. Click the "Run" button (play icon ▶)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Run button location in VS Code                    |
|  Expected: Play button visible at top-right              |
+----------------------------------------------------------+

Method 2: Right-click menu

1. Right-click anywhere in the editor
2. Select "Run Python File in Terminal"

Method 3: Terminal command

1. Open terminal in VS Code: View > Terminal (or Ctrl+`)

2. Run the script:

    # Windows
    python test.py

    # macOS/Linux
    python3 test.py

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Terminal with python command                      |
|  Expected: python test.py command visible                |
+----------------------------------------------------------+

Method 4: Keyboard shortcut

- Press F5 to run with debugging
- Or Ctrl+F5 to run without debugging


================================================================================
                     EXPECTED OUTPUT
================================================================================

After running the script, you should see in the terminal:

    Hello, python!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Console output "Hello, python!"                   |
|  Expected: Output visible in VS Code terminal            |
+----------------------------------------------------------+


================================================================================
                     VS CODE PYTHON FEATURES
================================================================================

With Python and Pylance extensions installed, you get:

| Feature              | Description                              |
|----------------------|------------------------------------------|
| Syntax highlighting  | Code is colored for readability          |
| IntelliSense         | Auto-completion suggestions              |
| Linting              | Error detection as you type              |
| Debugging            | Step through code, set breakpoints       |
| Formatting           | Auto-format code (Shift+Alt+F)           |
| Run button           | One-click script execution               |
| Terminal             | Integrated terminal for commands         |

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: IntelliSense autocomplete in action               |
|  Expected: Dropdown with suggestions                     |
+----------------------------------------------------------+


================================================================================
                     TROUBLESHOOTING
================================================================================

Problem: "python is not recognized as a command"
Solution: Python not in PATH
    - Reinstall Python with "Add to PATH" checked
    - Or add manually to system environment variables

Problem: VS Code doesn't recognize Python
Solution: Select Python interpreter
    - Press Ctrl+Shift+P
    - Type "Python: Select Interpreter"
    - Choose your Python installation

Problem: Run button not visible
Solution: Ensure Python extension is installed
    - Check Extensions sidebar
    - Install Microsoft Python extension

Problem: "No module named..." error
Solution: Install the required module
    pip install module_name

Problem: Wrong Python version running
Solution: Select correct interpreter
    - Click Python version in bottom-left of VS Code
    - Select the correct Python installation

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Python interpreter selection                      |
|  Expected: List of available Python interpreters         |
+----------------------------------------------------------+


================================================================================
                     USEFUL VS CODE SHORTCUTS
================================================================================

| Shortcut           | Action                           |
|--------------------|----------------------------------|
| Ctrl+S             | Save file                        |
| Ctrl+N             | New file                         |
| Ctrl+`             | Toggle terminal                  |
| F5                 | Run with debugging               |
| Ctrl+F5            | Run without debugging            |
| Ctrl+Shift+P       | Command palette                  |
| Ctrl+Shift+X       | Extensions sidebar               |
| Ctrl+/             | Toggle comment                   |
| Shift+Alt+F        | Format document                  |


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Visual Studio Code installed
[ ] Python installed and in PATH
[ ] Python extension installed in VS Code
[ ] Pylance extension installed in VS Code
[ ] test.py file created
[ ] print("Hello, python!") code written
[ ] Script executed successfully
[ ] "Hello, python!" displayed in console
[ ] Understand how to run Python scripts in VS Code

================================================================================
