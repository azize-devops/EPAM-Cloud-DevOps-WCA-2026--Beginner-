# Conditional Operators - Task

## Goals

- Write conditional statements

## Tasks

1. Create a script file in your home directory that does the following:
   - Checks if 1st and 2nd string arguments are equal and outputs the exit code
   - Checks if 1st string argument is longer than 2nd string argument and outputs the exit code
   - Checks if variable `TEST` is present in the environment (has non-zero length) and outputs the exit code
   - Checks if 3rd and 4th integer arguments are not equal and outputs the exit code
   - Checks if 3rd integer argument is greater or equal to 4th integer argument and outputs the exit code

2. Run your script with `"hi world 7 9"` arguments.

3. Add `TEST` environment variable with `"123"` value.

4. Re-run the script with `"hello hello 10 7"` arguments.

## Self-Review

- First script run should return the following values:

```
1
1
1
0
1
```

- Second script run should return the following values:

```
0
0
0
0
0
```

## Helpful Materials

- https://tldp.org/LDP/abs/html/testconstructs.html
- https://devhints.io/bash
