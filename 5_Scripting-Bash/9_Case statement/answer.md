CASE STATEMENT - ANSWER
=======================

This guide covers bash case statements, process management (PID, kill),
background processes, and creating service-like scripts.


================================================================================
                        CASE STATEMENT SYNTAX
================================================================================

Basic case syntax:

    case $variable in
        pattern1)
            # commands for pattern1
            ;;
        pattern2)
            # commands for pattern2
            ;;
        pattern3|pattern4)
            # commands for pattern3 OR pattern4
            ;;
        *)
            # default case (if no patterns match)
            ;;
    esac

Important:
- Each pattern ends with )
- Each case block ends with ;;
- Multiple patterns can be combined with |
- * is the wildcard (default/catch-all case)
- esac closes the case statement (case spelled backwards)

================================================================================


TASK 1: Create my_service.sh script
-----------------------------------

Create the script:

    cd ~
    touch my_service.sh
    chmod +x my_service.sh

Edit the script:

    vim my_service.sh
    # or
    nano my_service.sh

Script content:

    #!/bin/bash

    # Function to start the service
    start_service() {
        echo "Service is started"
        sleep 9999
    }

    # Function to stop the service
    stop_service() {
        # Find PID of running my_service.sh processes (exclude current and grep)
        pid=$(pgrep -f "my_service.sh" | grep -v $$)

        if [[ -n "$pid" ]]; then
            kill $pid 2>/dev/null
            echo "Service is stopped"
        else
            echo "Service is not running"
        fi
    }

    # Main case statement
    case $1 in
        start)
            start_service
            ;;
        stop)
            stop_service
            ;;
        restart)
            stop_service
            start_service
            ;;
        *)
            echo "usage: my_service.sh [start|stop|restart]"
            ;;
    esac

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating my_service.sh                            |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: my_service.sh script content                      |
|  Expected: Complete script with case statement           |
+----------------------------------------------------------+

Alternative - Create using cat heredoc:

    cat > ~/my_service.sh << 'EOF'
    #!/bin/bash

    # Function to start the service
    start_service() {
        echo "Service is started"
        sleep 9999
    }

    # Function to stop the service
    stop_service() {
        # Find PID of running my_service.sh processes (exclude current and grep)
        pid=$(pgrep -f "my_service.sh" | grep -v $$)

        if [[ -n "$pid" ]]; then
            kill $pid 2>/dev/null
            echo "Service is stopped"
        else
            echo "Service is not running"
        fi
    }

    # Main case statement
    case $1 in
        start)
            start_service
            ;;
        stop)
            stop_service
            ;;
        restart)
            stop_service
            start_service
            ;;
        *)
            echo "usage: my_service.sh [start|stop|restart]"
            ;;
    esac
    EOF

    chmod +x ~/my_service.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: cat my_service.sh output                          |
|  Expected: Complete script displayed                     |
+----------------------------------------------------------+

Understanding the script:

    pgrep -f "my_service.sh"    # Find PIDs of processes matching name
    grep -v $$                   # Exclude current process ($$)
    kill $pid                    # Terminate process by PID
    2>/dev/null                  # Suppress error messages
    sleep 9999                   # Keep process running


TASK 2: Add service to PATH
---------------------------

Option 1 - Add home directory to PATH (temporary):

    export PATH="$PATH:$HOME"

Option 2 - Create bin directory and add to PATH:

    mkdir -p ~/bin
    mv ~/my_service.sh ~/bin/
    export PATH="$PATH:$HOME/bin"

Option 3 - Make permanent (add to ~/.bashrc):

    echo 'export PATH="$PATH:$HOME"' >> ~/.bashrc
    source ~/.bashrc

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Adding to PATH                                    |
|  Expected: export PATH command executed                  |
+----------------------------------------------------------+

Verify the script is in PATH:

    which my_service.sh

Expected output:
    /home/username/my_service.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: which my_service.sh output                        |
|  Expected: Full path to script                           |
+----------------------------------------------------------+


TASK 3: Run with start and restart in background
-------------------------------------------------

Running in background with &:

Start the service in background:

    my_service.sh start &

Expected output:
    [1] 45043
    Service is started

Note: [1] is the job number, 45043 is the PID

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: my_service.sh start &                             |
|  Expected: Job number, PID, and "Service is started"     |
+----------------------------------------------------------+

Verify the process is running:

    ps aux | grep my_service
    # or
    pgrep -f my_service.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: ps aux | grep my_service                          |
|  Expected: my_service.sh process with sleep 9999         |
+----------------------------------------------------------+

Run restart in background:

    my_service.sh restart &

Expected output:
    [1] 45863
    Service is stopped
    Service is started

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: my_service.sh restart &                           |
|  Expected: Stop message, then start message              |
+----------------------------------------------------------+


TASK 4: Run with stop and help arguments
----------------------------------------

First, start a service to stop:

    my_service.sh start &

Then stop it:

    my_service.sh stop

Expected output:
    Service is stopped
    [1]  + 44996 terminated  ./my_service.sh start

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: my_service.sh stop                                |
|  Expected: "Service is stopped" and terminated message   |
+----------------------------------------------------------+

Run with help (or any invalid argument):

    my_service.sh help

Expected output:
    usage: my_service.sh [start|stop|restart]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: my_service.sh help                                |
|  Expected: Usage information                             |
+----------------------------------------------------------+

Run with no arguments:

    my_service.sh

Expected output:
    usage: my_service.sh [start|stop|restart]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: my_service.sh (no args)                           |
|  Expected: Usage information                             |
+----------------------------------------------------------+


COMPLETE TEST SEQUENCE
----------------------

    # 1. Start service in background
    my_service.sh start &
    # Output: [1] PID, Service is started

    # 2. Check it's running
    ps aux | grep my_service

    # 3. Stop the service
    my_service.sh stop
    # Output: Service is stopped, [1] terminated

    # 4. Restart service in background
    my_service.sh restart &
    # Output: [1] PID, Service is stopped, Service is started

    # 5. Stop again
    my_service.sh stop

    # 6. Test help
    my_service.sh help
    # Output: usage: my_service.sh [start|stop|restart]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Complete test sequence                            |
|  Expected: All commands and outputs                      |
+----------------------------------------------------------+


================================================================================
                        CASE STATEMENT PATTERNS
================================================================================

| Pattern      | Description                              | Example          |
|--------------|------------------------------------------|------------------|
| word)        | Exact match                              | start)           |
| word1|word2) | Match word1 OR word2                     | start|begin)     |
| [abc])       | Match a, b, or c                         | [yY])            |
| [a-z])       | Match any lowercase letter               | [a-z])           |
| [0-9])       | Match any digit                          | [0-9])           |
| ?)           | Match any single character               | ?)               |
| *)           | Match anything (default case)            | *)               |
| *pattern*)   | Contains pattern                         | *error*)         |

Examples:

    case $input in
        [yY]|[yY][eE][sS])
            echo "Yes"
            ;;
        [nN]|[nN][oO])
            echo "No"
            ;;
        *)
            echo "Invalid input"
            ;;
    esac

================================================================================


================================================================================
                        PROCESS MANAGEMENT
================================================================================

| Command              | Description                              |
|----------------------|------------------------------------------|
| &                    | Run command in background                |
| jobs                 | List background jobs                     |
| fg                   | Bring job to foreground                  |
| fg %1                | Bring job 1 to foreground                |
| bg                   | Resume stopped job in background         |
| Ctrl+Z               | Suspend current job                      |
| Ctrl+C               | Terminate current job                    |
| kill PID             | Terminate process by PID                 |
| kill -9 PID          | Force kill process                       |
| killall name         | Kill all processes by name               |
| pkill pattern        | Kill processes matching pattern          |
| pgrep pattern        | Find PIDs matching pattern               |
| ps                   | List processes                           |
| ps aux               | List all processes (detailed)            |
| top / htop           | Interactive process viewer               |

================================================================================


================================================================================
                        FINDING PROCESS ID (PID)
================================================================================

| Method                   | Description                              |
|--------------------------|------------------------------------------|
| $$                       | Current script's PID                     |
| $!                       | Last background process PID              |
| pgrep -f "name"          | Find PID by process name                 |
| pidof program            | Find PID by program name                 |
| ps aux | grep name       | Find process info                        |
| cat /var/run/prog.pid    | Read PID from file (common for services) |

Example of PID file approach (more robust):

    #!/bin/bash
    PIDFILE="/tmp/my_service.pid"

    start_service() {
        if [[ -f "$PIDFILE" ]]; then
            echo "Service already running"
            return 1
        fi
        echo "Service is started"
        echo $$ > "$PIDFILE"
        sleep 9999
    }

    stop_service() {
        if [[ -f "$PIDFILE" ]]; then
            kill $(cat "$PIDFILE") 2>/dev/null
            rm "$PIDFILE"
            echo "Service is stopped"
        else
            echo "Service is not running"
        fi
    }

================================================================================


================================================================================
                        BACKGROUND PROCESSES
================================================================================

Running in background:

    command &              # Run in background
    nohup command &        # Run in background, immune to hangup

Job control:

    jobs                   # List jobs
    jobs -l                # List jobs with PIDs
    fg %1                  # Bring job 1 to foreground
    bg %1                  # Continue job 1 in background
    kill %1                # Kill job 1

Example session:

    $ my_service.sh start &
    [1] 12345
    Service is started

    $ jobs
    [1]+  Running    my_service.sh start &

    $ fg %1
    my_service.sh start
    ^C                     # Ctrl+C to stop

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "kill: no process found"
Solutions:
- Service might not be running
- Check with: ps aux | grep my_service
- Use pgrep -f "my_service.sh"

Problem: Service doesn't stop
Solutions:
- Use kill -9 for force kill
- Check correct PID is being used
- Multiple instances might be running

Problem: "command not found" after adding to PATH
Solutions:
- Verify PATH: echo $PATH
- Source bashrc: source ~/.bashrc
- Check file is executable: ls -la my_service.sh

Problem: Background process stops when terminal closes
Solution: Use nohup:
    nohup my_service.sh start &

Problem: Can't see output from background process
Solution: Redirect output:
    my_service.sh start > /tmp/service.log 2>&1 &

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] my_service.sh created and executable
[ ] Script uses case statement
[ ] start case outputs "Service is started" and sleeps
[ ] stop case finds and kills the process
[ ] restart case stops then starts
[ ] Default case (*) shows usage
[ ] Script added to PATH
[ ] my_service.sh start & runs in background
[ ] my_service.sh stop terminates the process
[ ] my_service.sh restart works correctly
[ ] my_service.sh help shows usage information
[ ] which my_service.sh shows correct path

================================================================================
