# FINAL TASK - QUADRATIC EQUATION SOLVER - ANSWER

This guide provides a complete solution for the quadratic equation solver
with all required functions and detailed explanations.


## QUADRATIC EQUATION THEORY

Quadratic equation in standard form:
    ax^2 + bx + c = 0

Where:
    a = coefficient of x^2 (must not be 0)
    b = coefficient of x
    c = constant term

Discriminant formula:
    D = b^2 - 4ac

Roots formula:
    x = (-b +/- sqrt(D)) / (2a)

Cases based on discriminant:
    D > 0  ->  Two distinct real roots
    D = 0  ->  One real root (two equal roots)
    D < 0  ->  No real roots (complex roots)


## COMPLETE SOLUTION

Create a file named quadratic_solver.py:

```python
#!/usr/bin/env python3
"""
Quadratic Equation Solver
Solves equations in the form: ax^2 + bx + c = 0
"""

import math


def ask_value(message):
    """
    Ask user for input and return the value.
    Includes input validation for numeric values.
    """
    while True:
        try:
            value = float(input(message))
            return value
        except ValueError:
            print("Error: Please enter a valid number!")


def discriminant(a, b, c):
    """
    Calculate and return the discriminant.
    Formula: D = b^2 - 4ac
    """
    d = b**2 - 4*a*c
    return d


def roots(d, a, b, c):
    """
    Calculate and display the roots based on discriminant.
    """
    print("\n" + "="*40)
    print("RESULTS")
    print("="*40)

    if d > 0:
        # Two distinct real roots
        x1 = (-b + math.sqrt(d)) / (2*a)
        x2 = (-b - math.sqrt(d)) / (2*a)
        print(f"Discriminant: {d}")
        print(f"Two real roots:")
        print(f"  x1 = {x1}")
        print(f"  x2 = {x2}")
    elif d == 0:
        # One real root (double root)
        x = -b / (2*a)
        print(f"Discriminant: {d}")
        print(f"One real root (double root):")
        print(f"  x = {x}")
    else:
        # No real roots
        print(f"Discriminant: {d}")
        print("No real roots (discriminant is negative)")
        # Optional: Show complex roots
        real_part = -b / (2*a)
        imag_part = math.sqrt(abs(d)) / (2*a)
        print(f"Complex roots:")
        print(f"  x1 = {real_part} + {imag_part}i")
        print(f"  x2 = {real_part} - {imag_part}i")

    print("="*40 + "\n")


def solv_square(a, b, c):
    """
    Main solving function that uses discriminant and roots.
    """
    # Calculate discriminant
    d = discriminant(a, b, c)

    # Find and display roots
    roots(d, a, b, c)


def main():
    """
    Main function - entry point of the program.
    """
    print("\n" + "="*40)
    print("  QUADRATIC EQUATION SOLVER")
    print("  ax^2 + bx + c = 0")
    print("="*40 + "\n")

    # Get coefficients from user
    print("Enter the coefficients:\n")

    a = ask_value("Enter a (coefficient of x^2): ")

    # Validate that 'a' is not zero
    while a == 0:
        print("Error: 'a' cannot be zero (not a quadratic equation)")
        a = ask_value("Enter a (coefficient of x^2): ")

    b = ask_value("Enter b (coefficient of x): ")
    c = ask_value("Enter c (constant term): ")

    # Display the equation
    print(f"\nSolving: {a}x^2 + {b}x + {c} = 0")

    # Solve the equation
    solv_square(a, b, c)


# Run the program
if __name__ == "__main__":
    main()
```

> **Screenshot 1**
>
> Show: Complete quadratic_solver.py script
> Expected: All functions visible in editor


## FUNCTION BREAKDOWN

1. ask_value(message):

```python
def ask_value(message):
    while True:
        try:
            value = float(input(message))
            return value
        except ValueError:
            print("Error: Please enter a valid number!")
```

- Uses try/except for input validation
- Loops until valid number is entered
- Converts string input to float

> **Screenshot 2**
>
> Show: ask_value function
> Expected: Input validation with try/except

2. discriminant(a, b, c):

```python
def discriminant(a, b, c):
    d = b**2 - 4*a*c
    return d
```

- Simple calculation: D = b^2 - 4ac
- Returns the discriminant value

> **Screenshot 3**
>
> Show: discriminant function
> Expected: Formula implementation

3. roots(d, a, b, c):

```python
def roots(d, a, b, c):
    if d > 0:
        x1 = (-b + math.sqrt(d)) / (2*a)
        x2 = (-b - math.sqrt(d)) / (2*a)
        print(f"x1 = {x1}, x2 = {x2}")
    elif d == 0:
        x = -b / (2*a)
        print(f"x = {x}")
    else:
        print("No real roots")
```

- Handles all three cases (D>0, D=0, D<0)
- Uses math.sqrt() for square root
- Displays results to user

> **Screenshot 4**
>
> Show: roots function
> Expected: Three cases handled with if/elif/else

4. solv_square(a, b, c):

```python
def solv_square(a, b, c):
    d = discriminant(a, b, c)
    roots(d, a, b, c)
```

- Orchestrates the solving process
- Calls discriminant() and roots() functions

> **Screenshot 5**
>
> Show: solv_square function
> Expected: Calls to discriminant and roots

5. main():

```python
def main():
    print("QUADRATIC EQUATION SOLVER")
    a = ask_value("Enter a: ")
    # Validate a != 0
    b = ask_value("Enter b: ")
    c = ask_value("Enter c: ")
    solv_square(a, b, c)
```

- Entry point of the program
- Gets user input
- Calls solv_square()

> **Screenshot 6**
>
> Show: main function
> Expected: User prompts and function call


## RUNNING THE SCRIPT

Save the file and run:

```bash
python3 quadratic_solver.py
```

> **Screenshot 7**
>
> Show: Running the script
> Expected: python3 quadratic_solver.py command


## TEST CASE 1: Two Real Roots (D > 0)

Equation: x^2 - 5x + 6 = 0
Coefficients: a=1, b=-5, c=6

```
========================================
  QUADRATIC EQUATION SOLVER
  ax^2 + bx + c = 0
========================================

Enter the coefficients:

Enter a (coefficient of x^2): 1
Enter b (coefficient of x): -5
Enter c (constant term): 6

Solving: 1.0x^2 + -5.0x + 6.0 = 0

========================================
RESULTS
========================================
Discriminant: 1.0
Two real roots:
  x1 = 3.0
  x2 = 2.0
========================================
```

Verification: (x-2)(x-3) = x^2 - 5x + 6

> **Screenshot 8**
>
> Show: Test case with two real roots
> Expected: x1 = 3.0, x2 = 2.0


## TEST CASE 2: One Real Root (D = 0)

Equation: x^2 - 4x + 4 = 0
Coefficients: a=1, b=-4, c=4

```
Enter a (coefficient of x^2): 1
Enter b (coefficient of x): -4
Enter c (constant term): 4

Solving: 1.0x^2 + -4.0x + 4.0 = 0

========================================
RESULTS
========================================
Discriminant: 0.0
One real root (double root):
  x = 2.0
========================================
```

Verification: (x-2)^2 = x^2 - 4x + 4

> **Screenshot 9**
>
> Show: Test case with one real root
> Expected: x = 2.0 (double root)


## TEST CASE 3: No Real Roots (D < 0)

Equation: x^2 + x + 1 = 0
Coefficients: a=1, b=1, c=1

```
Enter a (coefficient of x^2): 1
Enter b (coefficient of x): 1
Enter c (constant term): 1

Solving: 1.0x^2 + 1.0x + 1.0 = 0

========================================
RESULTS
========================================
Discriminant: -3.0
No real roots (discriminant is negative)
Complex roots:
  x1 = -0.5 + 0.866i
  x2 = -0.5 - 0.866i
========================================
```

> **Screenshot 10**
>
> Show: Test case with no real roots
> Expected: Discriminant negative, complex roots shown


## TEST CASE 4: Input Validation

Test with invalid input:

```
Enter a (coefficient of x^2): abc
Error: Please enter a valid number!
Enter a (coefficient of x^2): 0
Error: 'a' cannot be zero (not a quadratic equation)
Enter a (coefficient of x^2): 1
```

> **Screenshot 11**
>
> Show: Input validation working
> Expected: Error messages for invalid input


## ALTERNATIVE SIMPLER VERSION

If you want a more compact version:

```python
import math

def ask_value(msg):
    return float(input(msg))

def discriminant(a, b, c):
    return b**2 - 4*a*c

def roots(d, a, b, c):
    if d > 0:
        print(f"x1 = {(-b + math.sqrt(d)) / (2*a)}")
        print(f"x2 = {(-b - math.sqrt(d)) / (2*a)}")
    elif d == 0:
        print(f"x = {-b / (2*a)}")
    else:
        print("No real roots")

def solv_square(a, b, c):
    roots(discriminant(a, b, c), a, b, c)

def main():
    a = ask_value("a: ")
    b = ask_value("b: ")
    c = ask_value("c: ")
    solv_square(a, b, c)

main()
```

> **Screenshot 12**
>
> Show: Compact version of the script
> Expected: Minimal but functional code


## FORMULAS REFERENCE

| Formula          | Expression              | Python Code              |
|------------------|-------------------------|--------------------------|
| Discriminant     | D = b^2 - 4ac           | d = b**2 - 4*a*c         |
| Root 1           | x1 = (-b + sqrt(D))/2a  | x1 = (-b + sqrt(d))/(2*a)|
| Root 2           | x2 = (-b - sqrt(D))/2a  | x2 = (-b - sqrt(d))/(2*a)|
| Single root      | x = -b / 2a             | x = -b / (2*a)           |


## TROUBLESHOOTING

Problem: "NameError: name 'math' is not defined"
Solution: Add import at the top

```python
import math
```

Problem: "ZeroDivisionError: division by zero"
Solution: Validate that 'a' is not zero

```python
if a == 0:
    print("Not a quadratic equation")
```

Problem: "ValueError: could not convert string to float"
Solution: Use try/except for input validation

```python
try:
    value = float(input(msg))
except ValueError:
    print("Invalid input")
```

Problem: "math domain error" with sqrt
Solution: Check if discriminant is negative before sqrt

```python
if d >= 0:
    math.sqrt(d)
```

Problem: Functions not being called
Solution: Make sure to call main() at the end

```python
if __name__ == "__main__":
    main()
```

> **Screenshot 13**
>
> Show: Final working program
> Expected: Complete successful run


## SELF-REVIEW CHECKLIST

[ ] Script file created (quadratic_solver.py)
[ ] import math statement included
[ ] ask_value(message) function implemented
[ ] Input validation added (try/except)
[ ] discriminant(a, b, c) function returns D = b^2 - 4ac
[ ] roots(d, a, b, c) function handles all three cases
[ ] solv_square(a, b, c) calls discriminant and roots
[ ] main() function gets user input
[ ] Validation: 'a' cannot be zero
[ ] User-friendly messages displayed
[ ] Script runs without errors
[ ] Test case D > 0 works (two roots)
[ ] Test case D = 0 works (one root)
[ ] Test case D < 0 works (no real roots)
