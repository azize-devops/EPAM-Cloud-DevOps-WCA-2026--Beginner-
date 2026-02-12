# NIGHTMARE! - ANSWER

Complete guide to performing all 4 previous tasks using ONLY the command line.
No GUI editors, file managers, or git GUI tools allowed!


================================================================================
                         ESSENTIAL COMMAND LINE TOOLS
================================================================================

CREATING FILES:

1. Using echo:
    echo "single line" > file.txt           # Create/overwrite file
    echo "another line" >> file.txt         # Append to file

2. Using cat with heredoc:
    cat > file.txt << 'EOF'
    Line 1
    Line 2
    Line 3
    EOF

3. Using cat to append:
    cat >> file.txt << 'EOF'
    More lines
    EOF

4. Using vi/vim:
    vi file.txt
    # Press 'i' to enter insert mode
    # Type your content
    # Press Esc to exit insert mode
    # Type :wq to save and quit
    # Type :q! to quit without saving

VIEWING FILES:
    cat file.txt                # View entire file
    head -n 5 file.txt          # View first 5 lines
    tail -n 5 file.txt          # View last 5 lines
    less file.txt               # Scrollable view (q to quit)

NAVIGATION:
    pwd                         # Print current directory
    cd /path/to/dir             # Change directory
    cd ..                       # Go up one level
    cd ~                        # Go to home directory
    ls                          # List files
    ls -la                      # List all files with details

================================================================================


================================================================================
                    PART 1: I CAN WIN (Command Line Only)
================================================================================

Step 1: Verify Git installation

    git --version

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: git --version in terminal                         |
|  Expected: Git version displayed                         |
+----------------------------------------------------------+

Step 2: Generate SSH keys (command line)

    ssh-keygen -t ed25519 -C "your_email@example.com"
    # Press Enter for default location
    # Press Enter for no passphrase (or enter one)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: ssh-keygen execution                              |
|  Expected: Key generated message                         |
+----------------------------------------------------------+

Step 3: Start SSH agent and add key

    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519

Step 4: Display public key (copy for GitHub)

    cat ~/.ssh/id_ed25519.pub

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: cat public key output                             |
|  Expected: Public key to copy to GitHub                  |
+----------------------------------------------------------+

Step 5: Configure Git (command line)

    git config --global user.name "Your Name"
    git config --global user.email "your_email@example.com"
    git config --global --list

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: git config commands                               |
|  Expected: Configuration set and displayed               |
+----------------------------------------------------------+

Step 6: Clone repository (after creating on GitHub via browser)

    cd ~
    git clone git@github.com:username/nightmare-challenge.git
    cd nightmare-challenge

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: git clone in terminal                             |
|  Expected: Repository cloned                             |
+----------------------------------------------------------+

Step 7: Create song.txt using cat heredoc

    cat > song.txt << 'EOF'
    I Can Win
    ---------

    When the world seems cold
    And you feel so alone
    Remember you are strong
    You can carry on
    EOF

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Creating song.txt with heredoc                    |
|  Expected: File created via command line                 |
+----------------------------------------------------------+

Step 8: Verify file content

    cat song.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: cat song.txt output                               |
|  Expected: First half of lyrics displayed                |
+----------------------------------------------------------+

Step 9: Commit and push

    git add song.txt
    git commit -m "add first half of my favorite song"
    git push -u origin main

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: git add, commit, push                             |
|  Expected: All commands successful                       |
+----------------------------------------------------------+

Step 10: After adding second half on GitHub, pull

    git pull

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: git pull result                                   |
|  Expected: Changes pulled from GitHub                    |
+----------------------------------------------------------+

Step 11: Verify complete content

    cat song.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Complete song.txt content                         |
|  Expected: Both halves of lyrics                         |
+----------------------------------------------------------+


================================================================================
                   PART 2: BRING IT ON (Command Line Only)
================================================================================

Step 1: Create .gitignore using cat

    cat > .gitignore << 'EOF'
    *.db
    *.log
    target/
    bin/
    EOF

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Creating .gitignore with heredoc                  |
|  Expected: Command executed                              |
+----------------------------------------------------------+

Step 2: Verify .gitignore

    cat .gitignore

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: cat .gitignore                                    |
|  Expected: Ignore patterns displayed                     |
+----------------------------------------------------------+

Step 3: Commit .gitignore

    git add .gitignore
    git commit -m "add .gitignore file"

Step 4: Create feature branch

    git checkout -b feature

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Creating feature branch                           |
|  Expected: Switched to new branch                        |
+----------------------------------------------------------+

Step 5: Create files and make two commits on feature

    echo "Feature work - part 1" > feature1.txt
    git add feature1.txt
    git commit -m "add feature part 1"

    echo "Feature work - part 2" > feature2.txt
    git add feature2.txt
    git commit -m "add feature part 2"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Two commits on feature branch                     |
|  Expected: Both commits successful                       |
+----------------------------------------------------------+

Step 6: Merge feature into main

    git checkout main
    git merge feature

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Merge feature into main                           |
|  Expected: Fast-forward merge                            |
+----------------------------------------------------------+

Step 7: Create arrows.txt on feature branch

    git checkout feature

    cat > arrows.txt << 'EOF'
    The ship glides gently on the waves
    As day turns into night
    EOF

    git add arrows.txt
    git commit -m "add arrows.txt with first verse"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: Creating arrows.txt on feature                    |
|  Expected: File created and committed                    |
+----------------------------------------------------------+

Step 8: Create different arrows.txt on main

    git checkout main

    cat > arrows.txt << 'EOF'
    One thousand burning arrows
    Fill the starlit sky
    EOF

    git add arrows.txt
    git commit -m "add arrows.txt with second verse"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: Creating arrows.txt on main                       |
|  Expected: Different content committed                   |
+----------------------------------------------------------+

Step 9: Merge and resolve conflict

    git merge feature
    # CONFLICT will occur

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Merge conflict                                    |
|  Expected: CONFLICT message                              |
+----------------------------------------------------------+

Step 10: View conflict markers

    cat arrows.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: Conflict markers in file                          |
|  Expected: <<<<<<< HEAD, =======, >>>>>>>                |
+----------------------------------------------------------+

Step 11: Resolve conflict using cat (overwrite with correct content)

    cat > arrows.txt << 'EOF'
    The ship glides gently on the waves
    As day turns into night
    One thousand burning arrows
    Fill the starlit sky
    EOF

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: Resolving conflict with cat heredoc               |
|  Expected: All 4 lines, no markers                       |
+----------------------------------------------------------+

Step 12: Complete merge

    git add arrows.txt
    git commit -m "merge feature: resolve arrows.txt conflict"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: Merge commit completed                            |
|  Expected: Merge successful                              |
+----------------------------------------------------------+


================================================================================
                  PART 3: HURT ME PLENTY (Command Line Only)
================================================================================

Step 1: Create storm branch

    git checkout -b storm

Step 2: Create storm.txt with first verse

    cat > storm.txt << 'EOF'
    Twenty ships with Norsemen braves
    Riding the northern wind
    EOF

    git add storm.txt
    git commit -m "add storm.txt with first verse"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 22 <<<                  |
|                                                          |
|  Show: Creating storm.txt on storm branch                |
|  Expected: First commit on storm                         |
+----------------------------------------------------------+

Step 3: Add second verse using cat append

    cat >> storm.txt << 'EOF'
    They left their shores at early dawn
    As a red sun was rising in the east
    EOF

    git add storm.txt
    git commit -m "add second verse to storm.txt"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 23 <<<                  |
|                                                          |
|  Show: Appending to storm.txt                            |
|  Expected: Second commit on storm                        |
+----------------------------------------------------------+

Step 4: Verify storm.txt content

    cat storm.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 24 <<<                  |
|                                                          |
|  Show: Complete storm.txt                                |
|  Expected: All 4 lines                                   |
+----------------------------------------------------------+

Step 5: Create pursuit.txt on main

    git checkout main

    cat > pursuit.txt << 'EOF'
    The warming sun returns again
    And melts away the snow
    The sea is freed from icy chains
    Winter is letting go
    EOF

    git add pursuit.txt
    git commit -m "add pursuit.txt"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 25 <<<                  |
|                                                          |
|  Show: Creating pursuit.txt on main                      |
|  Expected: Commit successful                             |
+----------------------------------------------------------+

Step 6: Create tag

    git tag session1
    git tag

+----------------------------------------------------------+
|                    >>> SCREENSHOT 26 <<<                  |
|                                                          |
|  Show: Creating and listing tag                          |
|  Expected: session1 tag created                          |
+----------------------------------------------------------+

Step 7: Rebase storm on main

    git checkout storm
    git rebase main

+----------------------------------------------------------+
|                    >>> SCREENSHOT 27 <<<                  |
|                                                          |
|  Show: Rebase storm on main                              |
|  Expected: Successfully rebased                          |
+----------------------------------------------------------+

Step 8: Verify linear history

    git log --oneline --graph

+----------------------------------------------------------+
|                    >>> SCREENSHOT 28 <<<                  |
|                                                          |
|  Show: Git log after rebase                              |
|  Expected: Linear history                                |
+----------------------------------------------------------+


================================================================================
                    PART 4: HARDCORE (Command Line Only)
================================================================================

Step 1: Push all branches and tags

    git checkout main
    git push --all origin
    git push origin --tags

+----------------------------------------------------------+
|                    >>> SCREENSHOT 29 <<<                  |
|                                                          |
|  Show: Push all to GitHub                                |
|  Expected: All branches and tags pushed                  |
+----------------------------------------------------------+

Step 2: View current remote

    git remote -v

+----------------------------------------------------------+
|                    >>> SCREENSHOT 30 <<<                  |
|                                                          |
|  Show: Current remote URL                                |
|  Expected: Original repository URL                       |
+----------------------------------------------------------+

Step 3: Change remote to new repository (created via browser)

    git remote set-url origin git@github.com:username/nightmare-backup.git
    git remote -v

+----------------------------------------------------------+
|                    >>> SCREENSHOT 31 <<<                  |
|                                                          |
|  Show: Change remote URL                                 |
|  Expected: New repository URL                            |
+----------------------------------------------------------+

Step 4: Push to new repository

    git push --all origin
    git push origin --tags

+----------------------------------------------------------+
|                    >>> SCREENSHOT 32 <<<                  |
|                                                          |
|  Show: Push to new repository                            |
|  Expected: All content pushed                            |
+----------------------------------------------------------+

Step 5: Restore original remote

    git remote set-url origin git@github.com:username/nightmare-challenge.git
    git remote -v

+----------------------------------------------------------+
|                    >>> SCREENSHOT 33 <<<                  |
|                                                          |
|  Show: Restore original remote                           |
|  Expected: Original URL restored                         |
+----------------------------------------------------------+

Step 6: Verify connection

    git fetch origin
    git remote show origin

+----------------------------------------------------------+
|                    >>> SCREENSHOT 34 <<<                  |
|                                                          |
|  Show: Verify remote connection                          |
|  Expected: Fetch successful                              |
+----------------------------------------------------------+


================================================================================
                           VI/VIM QUICK REFERENCE
================================================================================

STARTING VI:
    vi filename.txt              # Open or create file

MODES:
    i                            # Enter INSERT mode (type text)
    Esc                          # Exit INSERT mode (back to NORMAL)
    v                            # Enter VISUAL mode (select text)

NAVIGATION (NORMAL mode):
    h, j, k, l                   # Left, down, up, right
    w                            # Next word
    b                            # Previous word
    0                            # Beginning of line
    $                            # End of line
    gg                           # Beginning of file
    G                            # End of file
    :n                           # Go to line n

EDITING (NORMAL mode):
    x                            # Delete character
    dd                           # Delete line
    yy                           # Copy (yank) line
    p                            # Paste after cursor
    P                            # Paste before cursor
    u                            # Undo
    Ctrl+r                       # Redo

SAVING AND QUITTING:
    :w                           # Save
    :q                           # Quit
    :wq                          # Save and quit
    :q!                          # Quit without saving
    :wq!                         # Force save and quit
    ZZ                           # Save and quit (shortcut)

SEARCH:
    /pattern                     # Search forward
    ?pattern                     # Search backward
    n                            # Next match
    N                            # Previous match

================================================================================


================================================================================
                      BASH COMMAND LINE TIPS
================================================================================

FILE OPERATIONS:
    touch file.txt               # Create empty file
    mkdir -p dir1/dir2           # Create nested directories
    rm file.txt                  # Remove file
    rm -r directory              # Remove directory recursively
    cp source dest               # Copy file
    mv source dest               # Move/rename file

VIEWING/SEARCHING:
    cat file.txt                 # Display file
    less file.txt                # Scrollable view
    head -n 10 file.txt          # First 10 lines
    tail -n 10 file.txt          # Last 10 lines
    grep "pattern" file.txt      # Search in file
    find . -name "*.txt"         # Find files

REDIRECTION:
    command > file               # Output to file (overwrite)
    command >> file              # Output to file (append)
    command 2>&1                 # Redirect stderr to stdout
    command < file               # Input from file

PIPES:
    cat file | grep pattern      # Pipe output to grep
    ls -la | less                # Pipe to less for scrolling

HISTORY:
    history                      # Show command history
    !n                           # Run command number n
    !!                           # Run last command
    Ctrl+r                       # Search history

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: vi/vim feels unfamiliar
Solution: Practice basic commands:
    - Press 'i' to type, Esc to stop typing
    - Type :wq to save and exit
    - Type :q! to exit without saving

Problem: Heredoc not working
Solution: Make sure no spaces after << and EOF:
    cat > file.txt << 'EOF'
    content here
    EOF

Problem: Accidentally created wrong file
Solution: Remove it and start over:
    rm wrong-file.txt

Problem: Lost in directory structure
Solution: Use pwd and ls:
    pwd
    ls -la
    cd ~   # Go home

Problem: Can't see hidden files (.gitignore)
Solution: Use ls -la (show all files)

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

GENERAL:
[ ] All tasks completed via MINGW64/Git Bash terminal only
[ ] No GUI text editors used (Notepad, VS Code, etc.)
[ ] No file managers used (Windows Explorer, etc.)
[ ] No git gui or gitk used
[ ] Browser used ONLY for GitHub web operations

PART 1 - I CAN WIN:
[ ] SSH keys generated via command line
[ ] Git configured via command line
[ ] Repository cloned via command line
[ ] song.txt created using echo/cat/vi
[ ] Commits and push done via command line
[ ] Pull done via command line

PART 2 - BRING IT ON:
[ ] .gitignore created using echo/cat/vi
[ ] Feature branch created via command line
[ ] Files created and committed via command line
[ ] Merge performed via command line
[ ] Conflict resolved using echo/cat/vi (no GUI editor)

PART 3 - HURT ME PLENTY:
[ ] storm.txt created using echo/cat/vi
[ ] Content appended using >> or cat heredoc
[ ] Tag created via command line
[ ] Rebase performed via command line

PART 4 - HARDCORE:
[ ] Remote viewed and changed via command line
[ ] Push to multiple repos via command line
[ ] Remote restored via command line

================================================================================

