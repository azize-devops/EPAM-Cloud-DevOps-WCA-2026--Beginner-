# VIRTUAL ENVIRONMENT - ANSWER

This guide covers Python virtual environments, package installation with pip,
and using the Python interactive shell.


## WHAT IS A VIRTUAL ENVIRONMENT?

A virtual environment is an isolated Python environment that allows you to:

- Install packages without affecting the system Python
- Have different package versions for different projects
- Keep project dependencies separate and organized
- Easily share project requirements with others

Diagram:

```
System Python
├── Global packages
│
├── Project A (venv_a)
│   └── Package X v1.0
│   └── Package Y v2.0
│
└── Project B (venv_b)
    └── Package X v2.0  (different version!)
    └── Package Z v1.0
```


## STEP 1: Open Terminal

Windows:
- Press Win+R, type "cmd" and press Enter
- Or open PowerShell
- Or open VS Code terminal (Ctrl+`)

macOS:
- Press Cmd+Space, type "Terminal" and press Enter

Linux:
- Press Ctrl+Alt+T
- Or find Terminal in applications

> **Screenshot 1**
>
> Show: Terminal opened
> Expected: Clean terminal window

Navigate to your project directory:

```bash
# Windows
cd Desktop

# macOS/Linux
cd ~/Desktop
```


## STEP 2: Create Virtual Environment

Create a virtual environment named "testenv":

```bash
python3 -m venv testenv

# On Windows, you might need:
python -m venv testenv
```

> **Screenshot 2**
>
> Show: Creating virtual environment
> Expected: python3 -m venv testenv command

Understanding the command:

```
python3         # Python interpreter
-m venv         # Run the venv module
testenv         # Name of the virtual environment folder
```

This creates a "testenv" folder with:

```
testenv/
├── bin/           # (Linux/macOS) or Scripts/ (Windows)
│   ├── activate   # Activation script
│   ├── pip        # Package installer
│   └── python     # Python interpreter
├── include/       # C headers
├── lib/           # Installed packages
└── pyvenv.cfg     # Configuration file
```

Verify the folder was created:

```bash
# Windows
dir testenv

# macOS/Linux
ls -la testenv
```

> **Screenshot 3**
>
> Show: testenv folder contents
> Expected: bin/Scripts, include, lib folders


## STEP 3: Activate Virtual Environment

Activate the virtual environment:

```bash
# Linux/macOS
source testenv/bin/activate

# Windows (Command Prompt)
testenv\Scripts\activate

# Windows (PowerShell)
testenv\Scripts\Activate.ps1

# Windows (Git Bash)
source testenv/Scripts/activate
```

> **Screenshot 4**
>
> Show: Activating virtual environment
> Expected: source testenv/bin/activate command

After activation, your prompt changes:

```bash
# Before
user@computer:~$

# After
(testenv) user@computer:~$
```

The (testenv) prefix indicates you're in the virtual environment.

> **Screenshot 5**
>
> Show: Prompt with (testenv) prefix
> Expected: Virtual environment name in prompt

Verify you're in the virtual environment:

```bash
which python    # Linux/macOS
where python    # Windows
```

Expected output (shows path inside testenv):
    /path/to/testenv/bin/python


## STEP 4: Install emoji Package

Install the emoji package using pip:

```bash
pip install emoji
```

> **Screenshot 6**
>
> Show: pip install emoji command
> Expected: Package download and installation

Expected output:

```
Collecting emoji
  Downloading emoji-2.x.x-py3-none-any.whl (xxx kB)
Installing collected packages: emoji
Successfully installed emoji-2.x.x
```

> **Screenshot 7**
>
> Show: Successful installation message
> Expected: "Successfully installed emoji-x.x.x"

Verify installation:

```bash
pip list
```

Expected output (shows installed packages):

```
Package    Version
---------- -------
emoji      2.x.x
pip        xx.x.x
setuptools xx.x.x
```

> **Screenshot 8**
>
> Show: pip list output
> Expected: emoji package in the list


## STEP 5: Enter Python Shell

Start the Python interactive shell:

```bash
python3
# or on Windows:
python
```

> **Screenshot 9**
>
> Show: Python shell started
> Expected: Python version and >>> prompt

You'll see the Python prompt:

```
Python 3.x.x (default, ...)
[GCC x.x.x] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

The >>> indicates you're in the Python interactive shell.


## STEP 6: Import emoji Module

In the Python shell, import the emoji module:

```python
>>> import emoji
```

> **Screenshot 10**
>
> Show: import emoji command
> Expected: No error, returns to >>> prompt

If successful, it returns to the >>> prompt with no output.
If there's an error, you'll see "ModuleNotFoundError".


## STEP 7: Print with Emoji

Execute the print command with emoji:

```python
>>> print(emoji.emojize("Hello, world! :thumbs_up:"))
```

> **Screenshot 11**
>
> Show: print command with emoji
> Expected: Command visible in Python shell

Expected output:

```
Hello, world! 👍
```

> **Screenshot 12**
>
> Show: Output with thumbs up emoji
> Expected: "Hello, world! 👍" displayed

Understanding the code:

```python
emoji.emojize()         # Function to convert emoji codes
"Hello, world!"         # Regular text
:thumbs_up:             # Emoji shortcode
print()                 # Display the result
```

Try more emojis:

```python
>>> print(emoji.emojize(":red_heart:"))
❤️

>>> print(emoji.emojize(":snake: Python is awesome! :fire:"))
🐍 Python is awesome! 🔥

>>> print(emoji.emojize(":check_mark_button: Task completed!"))
✅ Task completed!
```

> **Screenshot 13**
>
> Show: Multiple emoji examples
> Expected: Various emojis displayed


## EXIT AND DEACTIVATE

Exit Python shell:

```python
>>> exit()
# or press Ctrl+D (Linux/macOS) or Ctrl+Z then Enter (Windows)
```

Deactivate virtual environment:

```bash
deactivate
```

> **Screenshot 14**
>
> Show: Deactivating virtual environment
> Expected: Prompt returns to normal (no testenv prefix)

After deactivate, your prompt returns to normal:

```bash
(testenv) user@computer:~$    # Before
user@computer:~$              # After
```


## VIRTUAL ENVIRONMENT COMMANDS

| Command                              | Description                    |
|--------------------------------------|--------------------------------|
| python3 -m venv <name>               | Create virtual environment     |
| source <name>/bin/activate           | Activate (Linux/macOS)         |
| <name>\Scripts\activate              | Activate (Windows)             |
| deactivate                           | Deactivate environment         |
| pip install <package>                | Install a package              |
| pip uninstall <package>              | Remove a package               |
| pip list                             | List installed packages        |
| pip freeze                           | List packages with versions    |
| pip freeze > requirements.txt        | Save requirements to file      |
| pip install -r requirements.txt      | Install from requirements      |


## COMMON EMOJI SHORTCODES

| Shortcode          | Emoji | Description              |
|--------------------|-------|--------------------------|
| :thumbs_up:        | 👍    | Thumbs up                |
| :thumbs_down:      | 👎    | Thumbs down              |
| :red_heart:        | ❤️    | Red heart                |
| :fire:             | 🔥    | Fire                     |
| :star:             | ⭐    | Star                     |
| :check_mark:       | ✔️    | Check mark               |
| :snake:            | 🐍    | Snake (Python!)          |
| :rocket:           | 🚀    | Rocket                   |
| :warning:          | ⚠️    | Warning                  |
| :light_bulb:       | 💡    | Light bulb               |

Find more at: https://carpedm20.github.io/emoji/


## WHY USE VIRTUAL ENVIRONMENTS?

1. Dependency Isolation
   - Each project has its own packages
   - No conflicts between projects

2. Version Control
   - Different projects can use different package versions
   - Project A: Django 3.0
   - Project B: Django 4.0

3. Clean Development
   - Don't pollute system Python
   - Easy to delete and recreate

4. Reproducibility
   - pip freeze > requirements.txt
   - Others can recreate your environment

5. Testing
   - Test with different Python/package versions
   - Easy to create fresh environment


## TROUBLESHOOTING

Problem: "python3: command not found"
Solution: Use 'python' instead, or install Python 3

```bash
python -m venv testenv
```

Problem: "venv is not recognized"
Solution: Install python3-venv package (Linux)

```bash
sudo apt install python3-venv
```

Problem: "source: command not found" (Windows)
Solution: Use Windows activation command

```bash
testenv\Scripts\activate
```

Problem: PowerShell script execution disabled
Solution: Enable script execution

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

Problem: "ModuleNotFoundError: No module named 'emoji'"
Solution: Ensure venv is activated before pip install

```bash
source testenv/bin/activate
pip install emoji
```

Problem: Emoji not displaying correctly
Solution: Terminal may not support Unicode
    - Try a different terminal
    - Use Windows Terminal or iTerm2

Problem: pip install permission denied
Solution: You're not in virtual environment
    - Activate venv first
    - Never use sudo pip install

> **Screenshot 15**
>
> Show: Complete workflow from start to finish
> Expected: All steps visible in terminal


## SELF-REVIEW CHECKLIST

[ ] Terminal opened successfully
[ ] Virtual environment created with python3 -m venv testenv
[ ] Virtual environment activated (testenv in prompt)
[ ] emoji package installed with pip
[ ] Python shell entered successfully
[ ] import emoji executed without error
[ ] print(emoji.emojize("Hello, world! :thumbs_up:")) shows emoji
[ ] "Hello, world! 👍" displayed in console
[ ] Understand purpose of virtual environments
[ ] Know how to activate/deactivate venv
