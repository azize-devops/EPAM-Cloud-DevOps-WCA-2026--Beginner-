# CONDITIONAL OPERATORS - ANSWER

This guide covers bash conditional operators for string comparison,
integer comparison, and environment variable testing.


## UNDERSTANDING CONDITIONAL OPERATORS

Conditional operators test conditions and return exit codes:
- 0 = True (condition is met)
- 1 = False (condition is not met)

String Operators (inside [[ ]]):
- ==          Equal
- !=          Not equal
- <           Less than (alphabetically)
- >           Greater than (alphabetically)
- -z          String is empty (zero length)
- -n          String is not empty (non-zero length)

Integer Operators (inside [[ ]] or (( ))):
- -eq         Equal
- -ne         Not equal
- -gt         Greater than
- -ge         Greater than or equal
- -lt         Less than
- -le         Less than or equal


## TASK 1: Create the script

Create the script file:

```bash
cd ~
touch conditional_test.sh
chmod +x conditional_test.sh
```

Edit the script:

```bash
vim conditional_test.sh
# or
nano conditional_test.sh
```

Script content:

```bash
#!/bin/bash

# Check if 1st and 2nd string arguments are equal
[[ "$1" == "$2" ]]
echo $?

# Check if 1st string argument is longer than 2nd
[[ ${#1} -gt ${#2} ]]
echo $?

# Check if TEST variable is present in environment (non-zero length)
[[ -n "$TEST" ]]
echo $?

# Check if 3rd and 4th integer arguments are not equal
[[ $3 -ne $4 ]]
echo $?

# Check if 3rd integer argument is greater or equal to 4th
[[ $3 -ge $4 ]]
echo $?
```

> **Screenshot 1**
> - Show: Creating conditional_test.sh
> - Expected: touch and chmod commands

> **Screenshot 2**
> - Show: Script content in editor or via cat
> - Expected: Complete script with all 5 checks

Alternative - Create using cat heredoc:

```bash
cat > ~/conditional_test.sh << 'EOF'
#!/bin/bash

# Check if 1st and 2nd string arguments are equal
[[ "$1" == "$2" ]]
echo $?

# Check if 1st string argument is longer than 2nd
[[ ${#1} -gt ${#2} ]]
echo $?

# Check if TEST variable is present in environment (non-zero length)
[[ -n "$TEST" ]]
echo $?

# Check if 3rd and 4th integer arguments are not equal
[[ $3 -ne $4 ]]
echo $?

# Check if 3rd integer argument is greater or equal to 4th
[[ $3 -ge $4 ]]
echo $?
EOF

chmod +x ~/conditional_test.sh
```

> **Screenshot 3**
> - Show: cat conditional_test.sh output
> - Expected: Complete script displayed


## UNDERSTANDING THE SCRIPT LINE BY LINE

Line: [[ "$1" == "$2" ]]
- Compares first and second arguments as strings
- Returns 0 if equal, 1 if not equal

Line: [[ ${#1} -gt ${#2} ]]
- ${#1} = length of first argument
- ${#2} = length of second argument
- -gt = greater than (numeric comparison)
- Returns 0 if first is longer, 1 if not

Line: [[ -n "$TEST" ]]
- -n tests if string has non-zero length
- Checks if TEST environment variable exists and has a value
- Returns 0 if TEST has value, 1 if empty or unset

Line: [[ $3 -ne $4 ]]
- -ne = not equal (numeric comparison)
- Returns 0 if not equal, 1 if equal

Line: [[ $3 -ge $4 ]]
- -ge = greater than or equal (numeric comparison)
- Returns 0 if $3 >= $4, 1 if $3 < $4


## TASK 2: Run script with "hi world 7 9"

Run the script:

```bash
./conditional_test.sh hi world 7 9
```

Expected output:
```
1
1
1
0
1
```

> **Screenshot 4**
> - Show: ./conditional_test.sh hi world 7 9
> - Expected: 1, 1, 1, 0, 1 (each on new line)

Output explanation:

```
1    <- "hi" == "world"? NO (strings not equal)
1    <- "hi"(2) > "world"(5)? NO (2 is not greater than 5)
1    <- TEST has value? NO (TEST not set yet)
0    <- 7 != 9? YES (7 is not equal to 9)
1    <- 7 >= 9? NO (7 is less than 9)
```

> **Screenshot 5**
> - Show: First run output with explanation
> - Expected: Each exit code explained


## TASK 3: Add TEST environment variable

Set the TEST variable:

```bash
export TEST="123"
```

Verify the variable is set:

```bash
echo $TEST
```

Expected output:
```
123
```

> **Screenshot 6**
> - Show: export TEST="123" and echo $TEST
> - Expected: Variable set and displayed

Alternative verification:

```bash
env | grep TEST
```

Expected output:
```
TEST=123
```

> **Screenshot 7**
> - Show: env | grep TEST
> - Expected: TEST=123


## TASK 4: Re-run script with "hello hello 10 7"

Run the script again:

```bash
./conditional_test.sh hello hello 10 7
```

Expected output:
```
0
1
0
0
0
```

> **Screenshot 8**
> - Show: ./conditional_test.sh hello hello 10 7
> - Expected: 0, 1, 0, 0, 0 (each on new line)

Output explanation:

```
0    <- "hello" == "hello"? YES (strings are equal)
1    <- "hello"(5) > "hello"(5)? NO (equal length, not greater)
0    <- TEST has value? YES (TEST="123")
0    <- 10 != 7? YES (10 is not equal to 7)
0    <- 10 >= 7? YES (10 is greater than 7)
```

> **Screenshot 9**
> - Show: Second run output with explanation
> - Expected: Each exit code explained


## COMPARISON OF BOTH RUNS

| Check                    | "hi world 7 9" | "hello hello 10 7" |
|--------------------------|----------------|--------------------|
| $1 == $2 (string equal)  | 1 (hi!=world)  | 0 (hello==hello)   |
| ${#1} > ${#2} (longer)   | 1 (2 not > 5)  | 1 (5 not > 5)      |
| -n $TEST (exists)        | 1 (not set)    | 0 (TEST=123)       |
| $3 -ne $4 (not equal)    | 0 (7 != 9)     | 0 (10 != 7)        |
| $3 -ge $4 (>= than)      | 1 (7 < 9)      | 0 (10 >= 7)        |

> **Screenshot 10**
> - Show: Both runs side by side or in sequence
> - Expected: Clear comparison of results


## STRING COMPARISON OPERATORS

| Operator | Description                    | Example               |
|----------|--------------------------------|-----------------------|
| ==       | Equal                          | [[ "$a" == "$b" ]]    |
| !=       | Not equal                      | [[ "$a" != "$b" ]]    |
| <        | Less than (alphabetically)     | [[ "$a" < "$b" ]]     |
| >        | Greater than (alphabetically)  | [[ "$a" > "$b" ]]     |
| -z       | String is empty                | [[ -z "$a" ]]         |
| -n       | String is not empty            | [[ -n "$a" ]]         |

Examples:

```bash
[[ "abc" == "abc" ]]; echo $?    # 0 (true)
[[ "abc" == "xyz" ]]; echo $?    # 1 (false)
[[ -z "" ]]; echo $?             # 0 (empty string)
[[ -n "hello" ]]; echo $?        # 0 (non-empty)
```


## INTEGER COMPARISON OPERATORS

| Operator | Description              | Example              |
|----------|--------------------------|----------------------|
| -eq      | Equal                    | [[ $a -eq $b ]]      |
| -ne      | Not equal                | [[ $a -ne $b ]]      |
| -gt      | Greater than             | [[ $a -gt $b ]]      |
| -ge      | Greater than or equal    | [[ $a -ge $b ]]      |
| -lt      | Less than                | [[ $a -lt $b ]]      |
| -le      | Less than or equal       | [[ $a -le $b ]]      |

Examples:

```bash
[[ 5 -eq 5 ]]; echo $?     # 0 (true)
[[ 5 -ne 3 ]]; echo $?     # 0 (true)
[[ 10 -gt 5 ]]; echo $?    # 0 (true)
[[ 5 -ge 5 ]]; echo $?     # 0 (true, equal counts)
[[ 3 -lt 7 ]]; echo $?     # 0 (true)
[[ 5 -le 5 ]]; echo $?     # 0 (true, equal counts)
```


## STRING LENGTH

| Method       | Description                    | Example              |
|--------------|--------------------------------|----------------------|
| ${#var}      | Length of variable             | ${#mystring}         |
| ${#1}        | Length of first argument       | ${#1}                |
| expr length  | Using expr command             | expr length "$str"   |

Examples:

```bash
str="hello"
echo ${#str}                 # 5

# Compare lengths
[[ ${#str1} -gt ${#str2} ]]  # str1 longer than str2?
```


## ENVIRONMENT VARIABLE TESTING

| Test         | Description                              |
|--------------|------------------------------------------|
| -n "$VAR"    | VAR is set and has non-zero length       |
| -z "$VAR"    | VAR is empty or unset                    |
| -v VAR       | VAR is set (even if empty) [bash 4.2+]   |

Examples:

```bash
# Check if variable exists and has value
[[ -n "$HOME" ]]; echo $?     # 0 (HOME is set)
[[ -n "$UNDEFINED" ]]; echo $?  # 1 (not set)

# Check if variable is empty
[[ -z "$UNDEFINED" ]]; echo $?  # 0 (empty/unset)

# Setting and checking
export MYVAR="value"
[[ -n "$MYVAR" ]]; echo $?    # 0
```


## EXIT CODES REFERENCE

| Code | Meaning                              |
|------|--------------------------------------|
| 0    | Success / True / Condition met       |
| 1    | Failure / False / Condition not met  |

Important:
- In bash, 0 means TRUE (success)
- Non-zero means FALSE (failure)
- This is opposite of many programming languages!

Checking exit code:

```bash
command
echo $?              # Shows exit code of previous command

# Or capture it
command
result=$?
echo "Exit code: $result"
```


## TROUBLESHOOTING

Problem: "unary operator expected"
Solution: Quote your variables

```bash
[[ "$var" == "value" ]]    # Correct
[[ $var == value ]]        # May fail if var is empty
```

Problem: String comparison instead of integer
Solution: Use correct operators

```bash
[[ $a -eq $b ]]    # Integer comparison
[[ "$a" == "$b" ]] # String comparison
```

Problem: TEST variable not found after setting
Solution: Use export to add to environment

```bash
TEST="123"         # Only shell variable
export TEST="123"  # Environment variable (visible to scripts)
```

Problem: Getting wrong exit codes
Solution: Remember bash logic

```bash
0 = TRUE  (condition satisfied)
1 = FALSE (condition not satisfied)
```

Problem: Length comparison not working
Solution: Use ${#var} syntax with numeric operators

```bash
[[ ${#str1} -gt ${#str2} ]]    # Correct
[[ "$str1" > "$str2" ]]        # Alphabetical, not length!
```


## SELF-REVIEW CHECKLIST

[ ] Script created and executable
[ ] Script checks string equality with ==
[ ] Script checks string length with ${#var}
[ ] Script checks environment variable with -n
[ ] Script checks integer inequality with -ne
[ ] Script checks integer >= with -ge
[ ] First run (hi world 7 9) returns: 1, 1, 1, 0, 1
[ ] TEST environment variable set with export
[ ] Second run (hello hello 10 7) returns: 0, 1, 0, 0, 0
[ ] Understand difference between string and integer operators
[ ] Understand that 0 = true, 1 = false in bash
