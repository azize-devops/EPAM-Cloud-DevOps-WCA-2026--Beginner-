# Pipelines & Logical Operators - Task

## Goals

- Learn to use pipelines
- Learn to use `||` and `&&` operators

## Tasks

1. Write a one line command using `||` and `&&` operators that does the following:
   - Creates a folder named `Name_Surname`
   - If folder creation was successful, creates a file inside, called `my_file`
   - If file creation was successful, writes "Hello" into the file
   - If previous operation was successful, lists the contents of the file
   - If any of the operations fail, print "Something went wrong"

2. Run the command two times.

3. Write a command that outputs only users that have `/usr/bin/false` shell from `/etc/passwd` file and changes the shell to `/bin/bash`.

## Self-Review

- First command returns the following outputs:

**First run:**
```
Hello
```

**Second run:**
```
mkdir: Name_Surname: File exists
Something went wrong
```

- Second command returns similar output:

```
nobody:*:-2:-2:Unprivileged User:/var/empty:/bin/bash
daemon:*:1:1:System Services:/var/root:/bin/bash
...
```

## Helpful Materials

- https://www.poftut.com/linux-bash-pipe-command-usage-examples-redirection/
- https://devhints.io/bash
