# UNTIL LOOP - ANSWER

This guide covers bash until loops, file size checking, file manipulation,
and the difference between while and until loops.


## UNTIL LOOP SYNTAX

Basic until loop:

```bash
until [[ condition ]]; do
    # commands (run until condition becomes TRUE)
done
```

Key difference from while:
- while: runs WHILE condition is TRUE
- until: runs UNTIL condition becomes TRUE (while it's FALSE)

Comparison:

```bash
# These are equivalent:
while [[ $x -lt 10 ]]; do ... done
until [[ $x -ge 10 ]]; do ... done
```


## TASK 1: Create the script

Create the script file:

```bash
cd ~
touch double_file.sh
chmod +x double_file.sh
```

Edit the script:

```bash
vim double_file.sh
# or
nano double_file.sh
```

Script content:

```bash
#!/bin/bash

# Read filename from user
read -p "Enter filename: " filename

# Check if file exists
if [[ ! -f "$filename" ]]; then
    echo "File not found: $filename"
    exit 1
fi

# Target size in KB (1024 KB = 1 MB)
target_size=1024

# Get current file size in KB
filesize=$(du -k "$filename" | awk '{print $1}')

# Loop until file size exceeds target
until [[ $filesize -gt $target_size ]]; do
    # Combine file with itself (double it)
    cat "$filename" "$filename" > temp_file
    mv temp_file "$filename"

    # Get new file size
    filesize=$(du -k "$filename" | awk '{print $1}')

    # Output current size
    echo "Filesize: $filesize"
done

echo "Final size: $filesize KB (exceeded $target_size KB)"
```

> **Screenshot 1**
> - Show: Creating double_file.sh
> - Expected: touch and chmod commands

> **Screenshot 2**
> - Show: Script content in editor or via cat
> - Expected: Complete script with until loop

Alternative - Create using cat heredoc:

```bash
cat > ~/double_file.sh << 'EOF'
#!/bin/bash

# Read filename from user
read -p "Enter filename: " filename

# Check if file exists
if [[ ! -f "$filename" ]]; then
    echo "File not found: $filename"
    exit 1
fi

# Target size in KB (1024 KB = 1 MB)
target_size=1024

# Get current file size in KB
filesize=$(du -k "$filename" | awk '{print $1}')

# Loop until file size exceeds target
until [[ $filesize -gt $target_size ]]; do
    # Combine file with itself (double it)
    cat "$filename" "$filename" > temp_file
    mv temp_file "$filename"

    # Get new file size
    filesize=$(du -k "$filename" | awk '{print $1}')

    # Output current size
    echo "Filesize: $filesize"
done

echo "Final size: $filesize KB (exceeded $target_size KB)"
EOF

chmod +x ~/double_file.sh
```

> **Screenshot 3**
> - Show: cat double_file.sh output
> - Expected: Complete script displayed


## UNDERSTANDING THE SCRIPT LINE BY LINE

Line: read -p "Enter filename: " filename
- Prompts user to enter a filename
- Stores input in $filename variable

Line: if [[ ! -f "$filename" ]]; then
- Checks if file exists (-f tests for regular file)
- ! negates the condition (if NOT a file)

Line: target_size=1024
- Sets target size to 1024 KB (1 MB)

Line: filesize=$(du -k "$filename" | awk '{print $1}')
- du -k: shows disk usage in KB
- awk '{print $1}': extracts just the number

Line: until [[ $filesize -gt $target_size ]]; do
- Loops UNTIL filesize is GREATER THAN target
- -gt = greater than

Line: cat "$filename" "$filename" > temp_file
- Concatenates file with itself
- Writes to temporary file (can't write to same file being read)

Line: mv temp_file "$filename"
- Renames temp file back to original filename
- Effectively doubles the file size

Line: filesize=$(du -k "$filename" | awk '{print $1}')
- Updates filesize variable with new size

Line: done
- Ends the until loop


## TASK 2: Create the test file

Create a 4KB random file:

```bash
head -c 4KB /dev/urandom > file.txt
```

Verify the file was created:

```bash
ls -la file.txt
```

Expected output:
```
-rw-r--r-- 1 user user 4096 Jan 15 12:00 file.txt
```

Check file size in KB:

```bash
du -k file.txt
```

Expected output:
```
4       file.txt
```

> **Screenshot 4**
> - Show: Creating file.txt with head command
> - Expected: head -c 4KB /dev/urandom > file.txt

> **Screenshot 5**
> - Show: Verifying file.txt size
> - Expected: ls -la and du -k output

Understanding the command:

```bash
head -c 4KB     # Output first 4KB of data
/dev/urandom    # Source of random data
> file.txt      # Redirect to file
```


## TASK 3: Execute the script

Run the script:

```bash
./double_file.sh
```

When prompted, enter the filename:

```
Enter filename: file.txt
```

Expected output:
```
Filesize: 8
Filesize: 16
Filesize: 32
Filesize: 64
Filesize: 128
Filesize: 256
Filesize: 512
Filesize: 1024
Filesize: 2048
Final size: 2048 KB (exceeded 1024 KB)
```

> **Screenshot 6**
> - Show: Running ./double_file.sh
> - Expected: Enter filename prompt

> **Screenshot 7**
> - Show: Script output with file sizes
> - Expected: Doubling sizes until > 1024 KB

Size progression breakdown:

```
Iteration 1:  4 KB  +  4 KB  =   8 KB
Iteration 2:  8 KB  +  8 KB  =  16 KB
Iteration 3: 16 KB  + 16 KB  =  32 KB
Iteration 4: 32 KB  + 32 KB  =  64 KB
Iteration 5: 64 KB  + 64 KB  = 128 KB
Iteration 6: 128 KB + 128 KB = 256 KB
Iteration 7: 256 KB + 256 KB = 512 KB
Iteration 8: 512 KB + 512 KB = 1024 KB
Iteration 9: 1024 KB + 1024 KB = 2048 KB (> 1024, loop ends)
```

> **Screenshot 8**
> - Show: Size progression explanation
> - Expected: Doubling pattern visible

Verify final file size:

```bash
du -k file.txt
```

Expected output:
```
2048    file.txt
```

```bash
# Or approximately 2 MB
ls -lh file.txt
```

> **Screenshot 9**
> - Show: Final file size verification
> - Expected: ~2048 KB or 2 MB


## WHILE VS UNTIL COMPARISON

| Aspect     | while                    | until                    |
|------------|--------------------------|--------------------------|
| Condition  | Runs while TRUE          | Runs while FALSE         |
| Stops when | Condition becomes FALSE  | Condition becomes TRUE   |
| Equivalent | while [[ A ]]            | until [[ ! A ]]          |

Examples:

```bash
# Count up to 5 with WHILE
i=1
while [[ $i -le 5 ]]; do
    echo $i
    ((i++))
done

# Count up to 5 with UNTIL
i=1
until [[ $i -gt 5 ]]; do
    echo $i
    ((i++))
done

# Both produce: 1 2 3 4 5
```

When to use which:
- while: "Keep going AS LONG AS this is true"
- until: "Keep going UNTIL this becomes true"


## FILE SIZE COMMANDS

| Command              | Description                    | Output        |
|----------------------|--------------------------------|---------------|
| du -k file           | Size in KB                     | 1024 file     |
| du -m file           | Size in MB                     | 1 file        |
| du -h file           | Human readable                 | 1.0M file     |
| ls -l file           | Size in bytes                  | 1048576       |
| ls -lh file          | Human readable                 | 1.0M          |
| stat -c %s file      | Size in bytes (Linux)          | 1048576       |
| wc -c < file         | Size in bytes                  | 1048576       |

Examples:

```bash
# Get just the size number in KB
du -k file.txt | awk '{print $1}'

# Get size in bytes
stat -c %s file.txt

# Human readable size
ls -lh file.txt | awk '{print $5}'
```


## /dev/urandom AND /dev/random

| Device       | Description                              |
|--------------|------------------------------------------|
| /dev/urandom | Unlimited pseudo-random data (faster)    |
| /dev/random  | True random data (may block if depleted) |
| /dev/zero    | Infinite stream of zero bytes            |
| /dev/null    | Discards all data written to it          |

Creating files with specific sizes:

```bash
# 1 KB file with random data
head -c 1KB /dev/urandom > file.bin

# 1 MB file with zeros
head -c 1MB /dev/zero > zeros.bin

# 100 bytes of random data
head -c 100 /dev/urandom > small.bin

# Using dd command
dd if=/dev/urandom of=file.bin bs=1K count=4
```


## ALTERNATIVE SCRIPT VERSIONS

Version with argument instead of input:

```bash
#!/bin/bash

filename=$1
target_size=1024

if [[ ! -f "$filename" ]]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

filesize=$(du -k "$filename" | awk '{print $1}')

until [[ $filesize -gt $target_size ]]; do
    cat "$filename" "$filename" > temp_file
    mv temp_file "$filename"
    filesize=$(du -k "$filename" | awk '{print $1}')
    echo "Filesize: $filesize"
done
```

Usage:

```bash
./double_file.sh file.txt
```

Version with configurable target size:

```bash
#!/bin/bash

read -p "Enter filename: " filename
read -p "Enter target size (KB): " target_size

filesize=$(du -k "$filename" | awk '{print $1}')

until [[ $filesize -gt $target_size ]]; do
    cat "$filename" "$filename" > temp_file
    mv temp_file "$filename"
    filesize=$(du -k "$filename" | awk '{print $1}')
    echo "Filesize: $filesize KB"
done
```

> **Screenshot 10**
> - Show: Alternative script version
> - Expected: Different approach, same result


## TROUBLESHOOTING

Problem: "No such file or directory"
Solution: Ensure file exists before running

```bash
ls -la file.txt
head -c 4KB /dev/urandom > file.txt
```

Problem: "Permission denied"
Solution: Make script executable

```bash
chmod +x double_file.sh
```

Problem: File size not updating
Solution: Ensure temp file logic is correct

```bash
cat file file > temp    # Write to temp first
mv temp file            # Then rename
```

Problem: Infinite loop
Solution: Check condition logic

```bash
until [[ $size -gt 1024 ]]    # > not >=
# Make sure filesize is actually increasing
```

Problem: du shows different values
Solution: du reports disk blocks, actual size may vary

```bash
# Use stat for exact byte count
stat -c %s filename
```

Problem: /dev/urandom not found (Windows/WSL)
Solution: Use alternative method

```bash
# In Windows Git Bash:
dd if=/dev/urandom of=file.txt bs=1024 count=4
```


## SELF-REVIEW CHECKLIST

[ ] Script created and executable
[ ] Script reads filename from user input
[ ] Script checks if file exists
[ ] Script uses until loop correctly
[ ] File is doubled using cat + temp file
[ ] File size is checked with du -k
[ ] Loop stops when size exceeds 1024 KB
[ ] Test file created with head -c 4KB /dev/urandom
[ ] Script outputs correct size progression
[ ] Understand difference between while and until
