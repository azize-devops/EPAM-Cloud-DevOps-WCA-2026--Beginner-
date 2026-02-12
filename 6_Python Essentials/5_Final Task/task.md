# FINAL TASK - QUADRATIC EQUATION SOLVER

GOALS:
------

- Test your Python skills with a practical challenge
- Implement a function that finds roots of a quadratic equation


TASK DESCRIPTION:
-----------------

Create a script that solves quadratic equations in classic form:
    ax^2 + bx + c = 0

Where a, b, c are entered from user input.
Don't forget to implement user-friendly messages!


REQUIRED FUNCTIONS:
-------------------

1. main()
   - Program entry point
   - Ask for all required data

2. ask_value(message)
   - Ask user for input
   - Return the variable
   - Good to have: input validation

3. discriminant(a, b, c)
   - Calculate and return discriminant
   - Formula: D = b^2 - 4ac

4. roots(d, a, b, c)
   - Show all acceptable roots on screen
   - Handle cases: D > 0, D = 0, D < 0

5. solv_square(a, b, c)
   - Contains discriminant and roots functions inside
   - Orchestrates the solving process


SELF-REVIEW:
------------

- Script runs without errors
- User can enter a, b, c values
- Roots are correctly calculated and displayed
- All required functions are implemented
- User-friendly messages are shown


HELPFUL MATERIALS:
------------------

https://en.wikipedia.org/wiki/Quadratic_equation
https://docs.python.org/3/library/math.html

