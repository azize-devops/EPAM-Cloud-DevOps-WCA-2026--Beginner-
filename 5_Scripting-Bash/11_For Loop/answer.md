# FOR LOOP - ANSWER

This guide covers bash for loops, iterating over arguments,
arithmetic operations, and calculating sum/average.


## FOR LOOP SYNTAX

Basic for loop (C-style):

```bash
for (( i=0; i<10; i++ )); do
    echo $i
done
```

For loop with list:

```bash
for item in item1 item2 item3; do
    echo $item
done
```

For loop with range:

```bash
for i in {1..5}; do
    echo $i
done
```

For loop with arguments:

```bash
for arg in "$@"; do
    echo $arg
done
```

One-liner format:

```bash
for i in {1..5}; do echo $i; done
```


## TASK 1: Create the script

Create the script file:

```bash
cd ~
touch sum_avg.sh
chmod +x sum_avg.sh
```

Edit the script:

```bash
vim sum_avg.sh
# or
nano sum_avg.sh
```

Script content:

```bash
#!/bin/bash

# Initialize sum variable
sum=0

# Loop through all arguments and calculate sum
for num in "$@"; do
    sum=$((sum + num))
done

# Get the number of arguments
count=$#

# Calculate average (integer division)
avg=$((sum / count))

# Output results
echo "Sum: $sum"
echo "Args number: $count"
echo "Result: $avg"
```

> **Screenshot 1**
> - Show: Creating sum_avg.sh
> - Expected: touch and chmod commands

> **Screenshot 2**
> - Show: Script content in editor or via cat
> - Expected: Complete script with for loop

Alternative - Create using cat heredoc:

```bash
cat > ~/sum_avg.sh << 'EOF'
#!/bin/bash

# Initialize sum variable
sum=0

# Loop through all arguments and calculate sum
for num in "$@"; do
    sum=$((sum + num))
done

# Get the number of arguments
count=$#

# Calculate average (integer division)
avg=$((sum / count))

# Output results
echo "Sum: $sum"
echo "Args number: $count"
echo "Result: $avg"
EOF

chmod +x ~/sum_avg.sh
```

> **Screenshot 3**
> - Show: cat sum_avg.sh output
> - Expected: Complete script displayed


## UNDERSTANDING THE SCRIPT LINE BY LINE

Line: sum=0
- Initialize sum variable to zero
- Must have no spaces around =

Line: for num in "$@"; do
- "$@" = all arguments passed to script
- Loops through each argument, assigning to $num
- "do" starts the loop body

Line: sum=$((sum + num))
- $(( )) = arithmetic expansion
- Adds current number to running sum
- Assigns result back to sum

Line: done
- Ends the for loop

Line: count=$#
- $# = number of arguments

Line: avg=$((sum / count))
- Integer division (no decimals)
- 15 / 5 = 3

Line: echo "Sum: $sum"
- Outputs the total sum


## TASK 2: Execute with 1 2 3 4 5

Run the script:

```bash
./sum_avg.sh 1 2 3 4 5
```

Expected output:
```
Sum: 15
Args number: 5
Result: 3
```

> **Screenshot 4**
> - Show: ./sum_avg.sh 1 2 3 4 5
> - Expected: Sum: 15, Args number: 5, Result: 3

Calculation breakdown:

```
Arguments: 1 2 3 4 5

Loop iterations:
    Iteration 1: sum = 0 + 1 = 1
    Iteration 2: sum = 1 + 2 = 3
    Iteration 3: sum = 3 + 3 = 6
    Iteration 4: sum = 6 + 4 = 10
    Iteration 5: sum = 10 + 5 = 15

Final values:
    sum = 15
    count = 5
    avg = 15 / 5 = 3
```

> **Screenshot 5**
> - Show: Calculation breakdown explanation
> - Expected: Step by step calculation


## ADDITIONAL TEST CASES

Test with different arguments:

```bash
./sum_avg.sh 10 20 30
```

Expected output:
```
Sum: 60
Args number: 3
Result: 20
```

> **Screenshot 6**
> - Show: ./sum_avg.sh 10 20 30
> - Expected: Sum: 60, Args number: 3, Result: 20

Test with more arguments:

```bash
./sum_avg.sh 2 4 6 8 10 12
```

Expected output:
```
Sum: 42
Args number: 6
Result: 7
```

> **Screenshot 7**
> - Show: Multiple test runs
> - Expected: Correct calculations for each


## FOR LOOP VARIATIONS

C-style for loop:

```bash
for (( i=1; i<=5; i++ )); do
    echo "Number: $i"
done
```

Loop with range:

```bash
for i in {1..10}; do
    echo $i
done
```

Loop with step:

```bash
for i in {0..10..2}; do    # 0, 2, 4, 6, 8, 10
    echo $i
done
```

Loop over files:

```bash
for file in *.txt; do
    echo "File: $file"
done
```

Loop over command output:

```bash
for user in $(cat /etc/passwd | cut -d: -f1); do
    echo "User: $user"
done
```

Loop over array:

```bash
arr=(apple banana cherry)
for fruit in "${arr[@]}"; do
    echo $fruit
done
```


## ARITHMETIC IN BASH

| Method          | Syntax                  | Example                    |
|-----------------|-------------------------|----------------------------|
| $(( ))          | $((expression))         | result=$((5 + 3))          |
| let             | let var=expression      | let result=5+3             |
| expr            | $(expr expression)      | result=$(expr 5 + 3)       |
| declare -i      | declare -i var          | declare -i num; num=5+3    |

Operators:

| Operator | Description      | Example          |
|----------|------------------|------------------|
| +        | Addition         | $((a + b))       |
| -        | Subtraction      | $((a - b))       |
| *        | Multiplication   | $((a * b))       |
| /        | Division         | $((a / b))       |
| %        | Modulo           | $((a % b))       |
| **       | Exponentiation   | $((a ** b))      |
| ++       | Increment        | $((a++))         |
| --       | Decrement        | $((a--))         |

Examples:

```bash
a=10
b=3

echo $((a + b))     # 13
echo $((a - b))     # 7
echo $((a * b))     # 30
echo $((a / b))     # 3 (integer division)
echo $((a % b))     # 1 (remainder)
echo $((a ** 2))    # 100
```


## ITERATING OVER ARGUMENTS

| Variable | Description                           |
|----------|---------------------------------------|
| $@       | All arguments (separate words)        |
| $*       | All arguments (single word)           |
| $#       | Number of arguments                   |
| $1..$9   | Individual arguments                  |
| ${10}    | 10th argument (braces required)       |

Loop patterns:

```bash
# Using $@
for arg in "$@"; do
    echo $arg
done

# Using positional shift
while [[ $# -gt 0 ]]; do
    echo $1
    shift
done

# Using index
for (( i=1; i<=$#; i++ )); do
    echo "Arg $i: ${!i}"
done
```


## ALTERNATIVE SCRIPT VERSIONS

Version with floating-point average (using bc):

```bash
#!/bin/bash
sum=0
for num in "$@"; do
    sum=$((sum + num))
done
count=$#
avg=$(echo "scale=2; $sum / $count" | bc)

echo "Sum: $sum"
echo "Args number: $count"
echo "Result: $avg"
```

Version using while loop with shift:

```bash
#!/bin/bash
sum=0
count=$#

while [[ $# -gt 0 ]]; do
    sum=$((sum + $1))
    shift
done

avg=$((sum / count))

echo "Sum: $sum"
echo "Args number: $count"
echo "Result: $avg"
```

Version using C-style for loop:

```bash
#!/bin/bash
sum=0
count=$#
args=("$@")

for (( i=0; i<count; i++ )); do
    sum=$((sum + args[i]))
done

avg=$((sum / count))

echo "Sum: $sum"
echo "Args number: $count"
echo "Result: $avg"
```

> **Screenshot 8**
> - Show: Alternative script version
> - Expected: Different approach, same output


## TROUBLESHOOTING

Problem: "syntax error near unexpected token"
Solution: Check for proper spacing and syntax

```bash
for i in {1..5}; do    # Correct
for i in {1..5};do     # Missing space before 'do'
```

Problem: "integer expression expected"
Solution: Ensure arguments are numbers

```bash
./script.sh 1 2 3      # Correct
./script.sh one two    # Will fail
```

Problem: Division by zero
Solution: Check if arguments were passed

```bash
if [[ $# -eq 0 ]]; then
    echo "No arguments provided"
    exit 1
fi
```

Problem: Unexpected results with spaces in values
Solution: Quote variables properly

```bash
for arg in "$@"; do    # Correct (quoted)
for arg in $@; do      # May break on spaces
```

Problem: Loop not iterating
Solution: Check if $@ is empty

```bash
echo "Number of args: $#"
echo "Args: $@"
```

Problem: Decimal/float numbers not working
Solution: Bash only does integer math. Use bc:

```bash
echo "scale=2; 15 / 4" | bc    # 3.75
```


## SELF-REVIEW CHECKLIST

[ ] Script created and executable
[ ] Script uses for loop to iterate over arguments
[ ] Script calculates sum correctly using $((sum + num))
[ ] Script uses $# for argument count
[ ] Script calculates average (integer division)
[ ] Running with "1 2 3 4 5" outputs Sum: 15
[ ] Running with "1 2 3 4 5" outputs Args number: 5
[ ] Running with "1 2 3 4 5" outputs Result: 3
[ ] Understand "$@" for all arguments
[ ] Understand $(( )) for arithmetic
