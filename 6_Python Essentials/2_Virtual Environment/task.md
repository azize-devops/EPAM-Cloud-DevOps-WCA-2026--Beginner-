# Virtual Environment - Task

## Goals

- Learn to create and use Python virtual environments
- Learn to install Python packages with pip

## Tasks

1. Open your terminal.

2. Create virtual environment by entering:
   ```bash
   python3 -m venv testenv
   ```

3. Enter the virtual environment by entering:
   - Linux/macOS:
     ```bash
     source testenv/bin/activate
     ```
   - Windows:
     ```bash
     testenv\Scripts\activate
     ```

4. Install emoji python module by executing:
   ```bash
   pip install emoji
   ```

5. Enter python shell by executing:
   ```bash
   python3
   ```

6. Execute command:
   ```python
   import emoji
   ```

7. Execute command:
   ```python
   print(emoji.emojize("Hello, world! :thumbs_up:"))
   ```

## Self-Review

- You should see "Hello, world!" with a thumbs up emoji at the end:
  ```
  Hello, world! 👍
  ```

## Helpful Materials

- https://docs.python.org/3/library/venv.html
- https://pypi.org/project/emoji/
