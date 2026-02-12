# BRING IT ON - ANSWER

This guide covers Git branching, .gitignore configuration, merging, and
conflict resolution. This task continues from the "I Can Win" exercise.

PREREQUISITE: Complete "I Can Win" task first. You should have a local
repository with song.txt and be connected to GitHub.


TASK 1: Add .gitignore file
---------------------------

Step 1: Create .gitignore file

    vim .gitignore
    # or
    nano .gitignore

Add the following content:

    # Ignore database files
    *.db

    # Ignore log files
    *.log

    # Ignore target directory
    target/

    # Ignore bin directory
    bin/

Save the file.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating .gitignore file                          |
|  Expected: Editor with ignore patterns                   |
+----------------------------------------------------------+

Step 2: Verify .gitignore content

    cat .gitignore

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: cat .gitignore output                             |
|  Expected: All ignore patterns displayed                 |
+----------------------------------------------------------+

Step 3: Test .gitignore (optional)

    # Create test files to verify they're ignored
    touch test.db test.log
    mkdir target bin
    touch target/app.jar bin/program

    # Check status - these should NOT appear
    git status

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: git status with ignored files                     |
|  Expected: Only .gitignore shown, not .db/.log/target/bin|
+----------------------------------------------------------+

Step 4: Commit .gitignore

    git add .gitignore
    git commit -m "add .gitignore file"
    git push

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Commit and push .gitignore                        |
|  Expected: Commit successful, pushed to remote           |
+----------------------------------------------------------+


TASK 2: Create feature branch with two commits
----------------------------------------------

Step 1: Create and switch to feature branch

    git checkout -b feature

    # Or using newer syntax:
    git switch -c feature

Expected output:
    Switched to a new branch 'feature'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Creating feature branch                           |
|  Expected: "Switched to a new branch 'feature'"          |
+----------------------------------------------------------+

Step 2: Verify current branch

    git branch

Expected output:
    * feature
      main

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: git branch output                                 |
|  Expected: feature branch marked with asterisk (*)       |
+----------------------------------------------------------+

Step 3: Make first commit on feature branch

    # Create or modify a file
    echo "Feature work - part 1" > feature1.txt
    git add feature1.txt
    git commit -m "add feature part 1"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: First commit on feature branch                    |
|  Expected: Commit message "add feature part 1"           |
+----------------------------------------------------------+

Step 4: Make second commit on feature branch

    echo "Feature work - part 2" > feature2.txt
    git add feature2.txt
    git commit -m "add feature part 2"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Second commit on feature branch                   |
|  Expected: Commit message "add feature part 2"           |
+----------------------------------------------------------+

Step 5: View commit history on feature

    git log --oneline

Expected output:
    abc1234 (HEAD -> feature) add feature part 2
    def5678 add feature part 1
    ghi9012 add .gitignore file
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: git log on feature branch                         |
|  Expected: Two new commits visible                       |
+----------------------------------------------------------+


TASK 3: Merge feature branch into master
----------------------------------------

Step 1: Switch to master/main branch

    git checkout main
    # or
    git checkout master

Expected output:
    Switched to branch 'main'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Switch to main branch                             |
|  Expected: "Switched to branch 'main'"                   |
+----------------------------------------------------------+

Step 2: Merge feature into main

    git merge feature

Expected output:
    Updating abc1234..def5678
    Fast-forward
     feature1.txt | 1 +
     feature2.txt | 1 +
     2 files changed, 2 insertions(+)
     create mode 100644 feature1.txt
     create mode 100644 feature2.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: git merge feature output                          |
|  Expected: Fast-forward merge, files added               |
+----------------------------------------------------------+

Step 3: Verify merge

    git log --oneline
    ls -la

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Log and files after merge                         |
|  Expected: Feature commits now in main, files present    |
+----------------------------------------------------------+


TASK 4: Create arrows.txt on feature branch
-------------------------------------------

Step 1: Switch back to feature branch

    git checkout feature

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Switch to feature branch                          |
|  Expected: "Switched to branch 'feature'"                |
+----------------------------------------------------------+

Step 2: Create arrows.txt with first two lines

    vim arrows.txt
    # or
    nano arrows.txt

    # Or using echo:
    cat > arrows.txt << 'EOF'
    The ship glides gently on the waves
    As day turns into night
    EOF

Content:
    The ship glides gently on the waves
    As day turns into night

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Creating arrows.txt on feature                    |
|  Expected: Two lines of text in editor                   |
+----------------------------------------------------------+

Step 3: Verify content

    cat arrows.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: cat arrows.txt output (feature)                   |
|  Expected: Two lines from step 4                         |
+----------------------------------------------------------+

Step 4: Commit arrows.txt on feature

    git add arrows.txt
    git commit -m "add arrows.txt with first verse"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: Commit arrows.txt on feature                      |
|  Expected: Commit successful                             |
+----------------------------------------------------------+


TASK 5: Create arrows.txt on master branch
------------------------------------------

Step 1: Switch to master/main branch

    git checkout main

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: Switch to main branch                             |
|  Expected: "Switched to branch 'main'"                   |
+----------------------------------------------------------+

Step 2: Note that arrows.txt doesn't exist on main yet

    ls -la
    # arrows.txt should NOT be present

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: ls on main branch                                 |
|  Expected: arrows.txt NOT present                        |
+----------------------------------------------------------+

Step 3: Create arrows.txt with different content on main

    cat > arrows.txt << 'EOF'
    One thousand burning arrows
    Fill the starlit sky
    EOF

    # Or use vim/nano

Content:
    One thousand burning arrows
    Fill the starlit sky

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: Creating arrows.txt on main                       |
|  Expected: Two different lines in editor                 |
+----------------------------------------------------------+

Step 4: Verify content

    cat arrows.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: cat arrows.txt output (main)                      |
|  Expected: Two lines from step 5                         |
+----------------------------------------------------------+

Step 5: Commit arrows.txt on main

    git add arrows.txt
    git commit -m "add arrows.txt with second verse"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: Commit arrows.txt on main                         |
|  Expected: Commit successful                             |
+----------------------------------------------------------+


TASK 6: Merge feature into master and resolve conflict
------------------------------------------------------

Step 1: Attempt to merge feature into main

    git merge feature

Expected output (CONFLICT):
    Auto-merging arrows.txt
    CONFLICT (add/add): Merge conflict in arrows.txt
    Automatic merge failed; fix conflicts and then commit the result.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 22 <<<                  |
|                                                          |
|  Show: git merge feature with conflict                   |
|  Expected: CONFLICT message for arrows.txt               |
+----------------------------------------------------------+

Step 2: Check status

    git status

Expected output:
    On branch main
    You have unmerged paths.
      (fix conflicts and run "git commit")
      (use "git merge --abort" to abort the merge)

    Unmerged paths:
      (use "git add <file>..." to mark resolution)
            both added:      arrows.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 23 <<<                  |
|                                                          |
|  Show: git status during conflict                        |
|  Expected: "Unmerged paths" with arrows.txt              |
+----------------------------------------------------------+

Step 3: View the conflict in arrows.txt

    cat arrows.txt

You'll see conflict markers:
    <<<<<<< HEAD
    One thousand burning arrows
    Fill the starlit sky
    =======
    The ship glides gently on the waves
    As day turns into night
    >>>>>>> feature

+----------------------------------------------------------+
|                    >>> SCREENSHOT 24 <<<                  |
|                                                          |
|  Show: arrows.txt with conflict markers                  |
|  Expected: <<<<<<< HEAD, =======, >>>>>>> feature        |
+----------------------------------------------------------+

Step 4: Resolve the conflict

Edit arrows.txt to keep all 4 lines in the order they were added:
- First: lines from feature (step 4)
- Then: lines from main (step 5)

    vim arrows.txt
    # or
    nano arrows.txt

Final content (remove conflict markers, reorder lines):
    The ship glides gently on the waves
    As day turns into night
    One thousand burning arrows
    Fill the starlit sky

+----------------------------------------------------------+
|                    >>> SCREENSHOT 25 <<<                  |
|                                                          |
|  Show: Editing arrows.txt to resolve conflict            |
|  Expected: All 4 lines in correct order, no markers      |
+----------------------------------------------------------+

Step 5: Verify resolved content

    cat arrows.txt

Expected output:
    The ship glides gently on the waves
    As day turns into night
    One thousand burning arrows
    Fill the starlit sky

+----------------------------------------------------------+
|                    >>> SCREENSHOT 26 <<<                  |
|                                                          |
|  Show: cat arrows.txt after resolution                   |
|  Expected: All 4 lines in correct order                  |
+----------------------------------------------------------+

Step 6: Stage the resolved file

    git add arrows.txt
    git status

+----------------------------------------------------------+
|                    >>> SCREENSHOT 27 <<<                  |
|                                                          |
|  Show: git add and status after resolution               |
|  Expected: "All conflicts fixed" or ready to commit      |
+----------------------------------------------------------+

Step 7: Complete the merge commit

    git commit -m "merge feature: resolve arrows.txt conflict"

    # Or just:
    git commit
    # (Git will provide a default merge commit message)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 28 <<<                  |
|                                                          |
|  Show: Merge commit completed                            |
|  Expected: Merge commit successful                       |
+----------------------------------------------------------+

Step 8: Verify final state

    git log --oneline --graph

Expected output:
    *   abc1234 (HEAD -> main) merge feature: resolve arrows.txt conflict
    |\
    | * def5678 (feature) add arrows.txt with first verse
    * | ghi9012 add arrows.txt with second verse
    |/
    * jkl3456 add feature part 2
    * mno7890 add feature part 1
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 29 <<<                  |
|                                                          |
|  Show: git log --oneline --graph                         |
|  Expected: Merge commit visible with branch structure    |
+----------------------------------------------------------+

    cat arrows.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 30 <<<                  |
|                                                          |
|  Show: Final arrows.txt content                          |
|  Expected: All 4 lines preserved in correct order        |
+----------------------------------------------------------+

Step 9: Push to remote (optional)

    git push origin main

+----------------------------------------------------------+
|                    >>> SCREENSHOT 31 <<<                  |
|                                                          |
|  Show: Push to remote after merge                        |
|  Expected: Push successful                               |
+----------------------------------------------------------+


================================================================================
                           GIT BRANCHING COMMANDS
================================================================================

| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| git branch                       | List local branches                      |
| git branch -a                    | List all branches (local + remote)       |
| git branch <name>                | Create new branch                        |
| git checkout <branch>            | Switch to branch                         |
| git checkout -b <name>           | Create and switch to new branch          |
| git switch <branch>              | Switch to branch (newer syntax)          |
| git switch -c <name>             | Create and switch (newer syntax)         |
| git merge <branch>               | Merge branch into current branch         |
| git merge --abort                | Abort merge in progress                  |
| git branch -d <name>             | Delete branch (if merged)                |
| git branch -D <name>             | Force delete branch                      |
| git log --graph                  | Show commit graph                        |
| git log --oneline --graph        | Compact commit graph                     |

================================================================================


================================================================================
                           .GITIGNORE PATTERNS
================================================================================

| Pattern          | Description                                          |
|------------------|------------------------------------------------------|
| *.log            | Ignore all .log files                                |
| *.db             | Ignore all .db files                                 |
| target/          | Ignore directory named target                        |
| bin/             | Ignore directory named bin                           |
| /build           | Ignore build in root only                            |
| **/logs          | Ignore logs directories anywhere                     |
| !important.log   | Don't ignore important.log (exception)               |
| *.txt            | Ignore all .txt files                                |
| doc/*.pdf        | Ignore PDFs in doc directory                         |

Common .gitignore entries:
    # IDE files
    .idea/
    .vscode/
    *.swp

    # Build outputs
    target/
    bin/
    build/
    dist/

    # Dependencies
    node_modules/
    vendor/

    # Logs and databases
    *.log
    *.db
    *.sqlite

    # OS files
    .DS_Store
    Thumbs.db

================================================================================


================================================================================
                        MERGE CONFLICT RESOLUTION
================================================================================

Conflict markers explained:

    <<<<<<< HEAD
    (content from current branch - main)
    =======
    (content from merging branch - feature)
    >>>>>>> feature

Steps to resolve:
1. Open conflicted file
2. Find conflict markers (<<<<<<<, =======, >>>>>>>)
3. Decide what content to keep
4. Remove ALL conflict markers
5. Save the file
6. git add <file>
7. git commit

Resolution strategies:
- Keep current (HEAD) only
- Keep incoming (feature) only
- Keep both (combine content)
- Rewrite entirely

Tools for conflict resolution:
    git mergetool          # Use configured merge tool
    git diff               # See differences
    git checkout --ours    # Keep current branch version
    git checkout --theirs  # Keep merging branch version

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Already on branch" when trying to create
Solution: Use git checkout -b <name> to create AND switch

Problem: Cannot delete branch (not fully merged)
Solution: Use git branch -D <name> to force delete

Problem: Merge conflict - don't know how to resolve
Solution: Use git merge --abort to cancel and try again

Problem: Wrong content after merge
Solution: Check git log, use git revert or git reset if needed

Problem: .gitignore not working for existing files
Solution: Files already tracked need to be removed from index:
    git rm --cached <file>
    Then commit the change

Problem: Accidentally committed to wrong branch
Solution:
    git checkout correct-branch
    git cherry-pick <commit-hash>
    git checkout wrong-branch
    git reset HEAD~1

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] .gitignore file created
[ ] .gitignore contains *.db pattern
[ ] .gitignore contains *.log pattern
[ ] .gitignore contains target/ pattern
[ ] .gitignore contains bin/ pattern
[ ] .gitignore committed to repository
[ ] Feature branch created
[ ] First commit made on feature branch
[ ] Second commit made on feature branch
[ ] Feature merged into main (first merge)
[ ] arrows.txt created on feature with first two lines
[ ] arrows.txt committed on feature
[ ] arrows.txt created on main with different two lines
[ ] arrows.txt committed on main
[ ] Merge attempted - conflict detected
[ ] Conflict markers visible in arrows.txt
[ ] Conflict resolved - all 4 lines preserved
[ ] Lines in correct order (feature lines first, then main lines)
[ ] Merge commit completed successfully
[ ] git log --graph shows merge structure

================================================================================

