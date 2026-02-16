# IF STATEMENT - ANSWER

This guide covers bash if/else/elif statements, conditional expressions,
and practical scripting examples.


## IF STATEMENT SYNTAX

Basic if statement:

```bash
if [[ condition ]]; then
    # commands
fi
```

If-else:

```bash
if [[ condition ]]; then
    # commands if true
else
    # commands if false
fi
```

If-elif-else:

```bash
if [[ condition1 ]]; then
    # commands if condition1 is true
elif [[ condition2 ]]; then
    # commands if condition2 is true
else
    # commands if all conditions are false
fi
```

Important:
- Spaces after [[ and before ]] are REQUIRED
- Semicolon before 'then' is REQUIRED (or put 'then' on new line)
- 'fi' closes the if statement


## TASK 1: Create odd_even.sh script

Create the script:

```bash
cd ~
touch odd_even.sh
chmod +x odd_even.sh
```

Edit the script:

```bash
vim odd_even.sh
# or
nano odd_even.sh
```

Script content:

```bash
#!/bin/bash

# Get the length of the first argument
length=${#1}

# Check if length is odd or even using modulo
if (( length % 2 == 1 )); then
    echo "Odd"
else
    echo "Even"
fi
```

> **Screenshot 1**
> - Show: Creating odd_even.sh
> - Expected: touch and chmod commands

> **Screenshot 2**
> - Show: odd_even.sh script content
> - Expected: Complete script with if/else

Alternative - Create using cat heredoc:

```bash
cat > ~/odd_even.sh << 'EOF'
#!/bin/bash

# Get the length of the first argument
length=${#1}

# Check if length is odd or even using modulo
if (( length % 2 == 1 )); then
    echo "Odd"
else
    echo "Even"
fi
EOF

chmod +x ~/odd_even.sh
```

Understanding the script:

```bash
${#1}           # Length of first argument ($1)
(( ))           # Arithmetic evaluation
length % 2      # Modulo operation (remainder when divided by 2)
% 2 == 1        # If remainder is 1, number is odd
% 2 == 0        # If remainder is 0, number is even
```

> **Screenshot 3**
> - Show: cat odd_even.sh output
> - Expected: Complete script displayed


## TASK 2: Run odd_even.sh with "odd" and "even"

First run with "odd" (3 letters - odd):

```bash
./odd_even.sh odd
```

Expected output:
```
Odd
```

Explanation:
- "odd" has 3 letters
- 3 % 2 = 1 (odd)
- Outputs "Odd"

> **Screenshot 4**
> - Show: ./odd_even.sh odd
> - Expected: Odd

Second run with "even" (4 letters - even):

```bash
./odd_even.sh even
```

Expected output:
```
Even
```

Explanation:
- "even" has 4 letters
- 4 % 2 = 0 (even)
- Outputs "Even"

> **Screenshot 5**
> - Show: ./odd_even.sh even
> - Expected: Even

Additional test cases:

```bash
./odd_even.sh a        # 1 letter -> Odd
./odd_even.sh ab       # 2 letters -> Even
./odd_even.sh abc      # 3 letters -> Odd
./odd_even.sh hello    # 5 letters -> Odd
./odd_even.sh world    # 5 letters -> Odd
```

> **Screenshot 6**
> - Show: Multiple test runs of odd_even.sh
> - Expected: Correct Odd/Even for each


## TASK 3: Create my_script.sh

Create the script:

```bash
touch ~/my_script.sh
chmod +x ~/my_script.sh
```

Edit the script:

```bash
vim ~/my_script.sh
```

Script content:

```bash
#!/bin/bash

# Get number of arguments
num_args=$#

if [[ $num_args -lt 2 ]]; then
    # Less than 2 arguments: output the values
    echo "$@"
elif [[ $num_args -gt 2 && $num_args -lt 4 ]]; then
    # More than 2 but less than 4 (i.e., exactly 3): output last argument
    echo "${@: -1}"
else
    # 2 arguments OR 4+ arguments
    echo "Invalid number of arguments"
fi
```

> **Screenshot 7**
> - Show: Creating my_script.sh
> - Expected: touch and chmod commands

> **Screenshot 8**
> - Show: my_script.sh script content
> - Expected: Complete script with if/elif/else

Alternative - Create using cat heredoc:

```bash
cat > ~/my_script.sh << 'EOF'
#!/bin/bash

# Get number of arguments
num_args=$#

if [[ $num_args -lt 2 ]]; then
    # Less than 2 arguments: output the values
    echo "$@"
elif [[ $num_args -gt 2 && $num_args -lt 4 ]]; then
    # More than 2 but less than 4 (i.e., exactly 3): output last argument
    echo "${@: -1}"
else
    # 2 arguments OR 4+ arguments
    echo "Invalid number of arguments"
fi
EOF

chmod +x ~/my_script.sh
```

Understanding the script:

```bash
$#              # Number of arguments
-lt 2           # Less than 2 (0 or 1 arguments)
-gt 2           # Greater than 2
-lt 4           # Less than 4
&&              # Logical AND
"$@"            # All arguments
${@: -1}        # Last argument (using array slicing)
```

Logic table:
- 0 args: less than 2 -> output args (empty)
- 1 arg:  less than 2 -> output args
- 2 args: NOT less than 2, NOT (>2 and <4) -> Invalid
- 3 args: greater than 2 AND less than 4 -> output last
- 4+ args: NOT less than 2, NOT (>2 and <4) -> Invalid

> **Screenshot 9**
> - Show: cat my_script.sh output
> - Expected: Complete script displayed


## TASK 4: Run my_script.sh with various arguments

Run 1: With "hello" (1 argument)

```bash
./my_script.sh hello
```

Expected output:
```
hello
```

Explanation: 1 < 2, so output all arguments

> **Screenshot 10**
> - Show: ./my_script.sh hello
> - Expected: hello

Run 2: With "hello world" (2 arguments)

```bash
./my_script.sh hello world
```

Expected output:
```
Invalid number of arguments
```

Explanation: 2 is NOT less than 2, and NOT (>2 and <4), so Invalid

> **Screenshot 11**
> - Show: ./my_script.sh hello world
> - Expected: Invalid number of arguments

Run 3: With "pie is lie" (3 arguments)

```bash
./my_script.sh pie is lie
```

Expected output:
```
lie
```

Explanation: 3 > 2 AND 3 < 4, so output last argument (lie)

> **Screenshot 12**
> - Show: ./my_script.sh pie is lie
> - Expected: lie

Run 4: With "keep calm and procrastinate" (4 arguments)

```bash
./my_script.sh keep calm and procrastinate
```

Expected output:
```
Invalid number of arguments
```

Explanation: 4 is NOT (>2 and <4), so Invalid

> **Screenshot 13**
> - Show: ./my_script.sh keep calm and procrastinate
> - Expected: Invalid number of arguments

Summary of all runs:

```bash
./my_script.sh hello                      # hello
./my_script.sh hello world                # Invalid number of arguments
./my_script.sh pie is lie                 # lie
./my_script.sh keep calm and procrastinate # Invalid number of arguments
```

> **Screenshot 14**
> - Show: All four runs together
> - Expected: All expected outputs


## IF STATEMENT SYNTAX VARIATIONS

Using [[ ]] (recommended for bash):

```bash
if [[ $var == "value" ]]; then
    echo "Match"
fi
```

Using [ ] (POSIX compatible):

```bash
if [ "$var" = "value" ]; then
    echo "Match"
fi
```

Using (( )) for arithmetic:

```bash
if (( num > 10 )); then
    echo "Greater than 10"
fi
```

Using command exit status:

```bash
if grep -q "pattern" file.txt; then
    echo "Pattern found"
fi
```

One-line if:

```bash
[[ $var == "value" ]] && echo "Match"

# Or with else
[[ $var == "value" ]] && echo "Match" || echo "No match"
```


## COMPARISON OPERATORS

Numeric (use with [[ ]] or [ ]):

| Operator | Description              |
|----------|--------------------------|
| -eq      | Equal                    |
| -ne      | Not equal                |
| -lt      | Less than                |
| -le      | Less than or equal       |
| -gt      | Greater than             |
| -ge      | Greater than or equal    |

String (use with [[ ]]):

| Operator | Description              |
|----------|--------------------------|
| ==       | Equal                    |
| !=       | Not equal                |
| <        | Less than (alphabetical) |
| >        | Greater than             |
| -z       | String is empty          |
| -n       | String is not empty      |

Logical:

| Operator | Description              |
|----------|--------------------------|
| &&       | AND                      |
| \|\|     | OR                       |
| !        | NOT                      |


## FILE TEST OPERATORS

| Operator | Description                              |
|----------|------------------------------------------|
| -e file  | File exists                              |
| -f file  | Is a regular file                        |
| -d file  | Is a directory                           |
| -r file  | Is readable                              |
| -w file  | Is writable                              |
| -x file  | Is executable                            |
| -s file  | File size > 0                            |
| -L file  | Is a symbolic link                       |

Example:

```bash
if [[ -f "/etc/passwd" ]]; then
    echo "File exists"
fi

if [[ -d "$HOME" ]]; then
    echo "Home directory exists"
fi
```


## GETTING STRING LENGTH

| Method           | Description                              |
|------------------|------------------------------------------|
| ${#var}          | Length of variable                       |
| ${#1}            | Length of first argument                 |
| expr length "$s" | Using expr command                       |
| echo -n "$s" \| wc -c | Using wc command                    |

Example:

```bash
str="hello"
echo ${#str}        # 5

# For argument
length=${#1}
echo $length
```


## GETTING LAST ARGUMENT

| Method           | Description                              |
|------------------|------------------------------------------|
| ${@: -1}         | Array slicing (space before -)           |
| ${!#}            | Indirect reference to last position      |
| $BASH_ARGV       | In functions (reversed order)            |

Example:

```bash
#!/bin/bash
echo "Last argument: ${@: -1}"
echo "Last argument: ${!#}"
```


## TROUBLESHOOTING

Problem: "[[: not found"
Solution: Use bash, not sh

```bash
#!/bin/bash (not #!/bin/sh)
```

Problem: "unary operator expected"
Solution: Quote variables

```bash
[[ "$var" == "value" ]]
```

Problem: "too many arguments"
Solution: Quote variables with spaces

```bash
[[ "$var" == "value" ]]
```

Problem: Arithmetic comparison not working
Solution: Use -eq, -lt, etc. OR use (( ))

```bash
[[ $a -eq $b ]]    # Correct
(( a == b ))       # Also correct
[[ $a == $b ]]     # String comparison!
```

Problem: && and || not working in [ ]
Solution: Use -a and -o in [ ], or use [[ ]]

```bash
[ $a -lt 5 -a $b -gt 2 ]    # In [ ]
[[ $a -lt 5 && $b -gt 2 ]]  # In [[ ]]
```


## SELF-REVIEW CHECKLIST

[ ] odd_even.sh created and executable
[ ] odd_even.sh uses ${#1} to get argument length
[ ] odd_even.sh uses modulo (%) to check odd/even
[ ] ./odd_even.sh odd outputs "Odd"
[ ] ./odd_even.sh even outputs "Even"
[ ] my_script.sh created and executable
[ ] my_script.sh uses $# for argument count
[ ] my_script.sh has if/elif/else structure
[ ] ./my_script.sh hello outputs "hello"
[ ] ./my_script.sh hello world outputs "Invalid number of arguments"
[ ] ./my_script.sh pie is lie outputs "lie"
[ ] ./my_script.sh keep calm and procrastinate outputs "Invalid..."
