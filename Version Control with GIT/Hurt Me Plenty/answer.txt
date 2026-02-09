HURT ME PLENTY - ANSWER
=======================

This guide covers Git tagging and rebasing. Rebasing is an alternative to merging
that creates a linear commit history by replaying commits on top of another branch.

PREREQUISITE: Complete "Bring It On" task first.


TASK 1: Create storm branch with first commit
---------------------------------------------

Step 1: Create and switch to storm branch

    git checkout -b storm

Expected output:
    Switched to a new branch 'storm'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating storm branch                             |
|  Expected: "Switched to a new branch 'storm'"            |
+----------------------------------------------------------+

Step 2: Verify current branch

    git branch

Expected output:
      feature
      main
    * storm

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: git branch output                                 |
|  Expected: storm branch marked with asterisk (*)         |
+----------------------------------------------------------+

Step 3: Create storm.txt with first two lines

    cat > storm.txt << 'EOF'
    Twenty ships with Norsemen braves
    Riding the northern wind
    EOF

    # Or use vim/nano

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Creating storm.txt                                |
|  Expected: Two lines of text                             |
+----------------------------------------------------------+

Step 4: Verify content

    cat storm.txt

Expected output:
    Twenty ships with Norsemen braves
    Riding the northern wind

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: cat storm.txt output                              |
|  Expected: First two lines displayed                     |
+----------------------------------------------------------+

Step 5: Commit storm.txt

    git add storm.txt
    git commit -m "add storm.txt with first verse"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: First commit on storm branch                      |
|  Expected: Commit successful                             |
+----------------------------------------------------------+


TASK 2: Add more lines and second commit
----------------------------------------

Step 1: Add two more lines to storm.txt

    cat >> storm.txt << 'EOF'
    They left their shores at early dawn
    As a red sun was rising in the east
    EOF

    # Or edit with vim/nano and append the lines

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Appending to storm.txt                            |
|  Expected: Adding two more lines                         |
+----------------------------------------------------------+

Step 2: Verify complete content

    cat storm.txt

Expected output:
    Twenty ships with Norsemen braves
    Riding the northern wind
    They left their shores at early dawn
    As a red sun was rising in the east

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: cat storm.txt with all 4 lines                    |
|  Expected: All four lines displayed                      |
+----------------------------------------------------------+

Step 3: Make second commit

    git add storm.txt
    git commit -m "add second verse to storm.txt"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Second commit on storm branch                     |
|  Expected: Commit successful                             |
+----------------------------------------------------------+

Step 4: View commit history on storm

    git log --oneline

Expected output:
    abc1234 (HEAD -> storm) add second verse to storm.txt
    def5678 add storm.txt with first verse
    ghi9012 merge feature: resolve arrows.txt conflict
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: git log on storm branch                           |
|  Expected: Two storm commits visible                     |
+----------------------------------------------------------+


TASK 3: Create pursuit.txt on master
------------------------------------

Step 1: Switch to master/main branch

    git checkout main

Expected output:
    Switched to branch 'main'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Switch to main branch                             |
|  Expected: "Switched to branch 'main'"                   |
+----------------------------------------------------------+

Step 2: Note that storm.txt is not on main

    ls -la
    # storm.txt should NOT be present (it's only on storm branch)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: ls on main branch                                 |
|  Expected: storm.txt NOT present                         |
+----------------------------------------------------------+

Step 3: Create pursuit.txt with 4 lines

    cat > pursuit.txt << 'EOF'
    The warming sun returns again
    And melts away the snow
    The sea is freed from icy chains
    Winter is letting go
    EOF

    # Or use vim/nano

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Creating pursuit.txt                              |
|  Expected: Four lines of text in editor                  |
+----------------------------------------------------------+

Step 4: Verify content

    cat pursuit.txt

Expected output:
    The warming sun returns again
    And melts away the snow
    The sea is freed from icy chains
    Winter is letting go

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: cat pursuit.txt output                            |
|  Expected: All four lines displayed                      |
+----------------------------------------------------------+

Step 5: Commit pursuit.txt

    git add pursuit.txt
    git commit -m "add pursuit.txt"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Commit pursuit.txt on main                        |
|  Expected: Commit successful                             |
+----------------------------------------------------------+


TASK 4: Create session1 tag and switch to storm
-----------------------------------------------

Step 1: Create tag on current commit

    git tag session1

    # Or with annotation (recommended):
    git tag -a session1 -m "End of session 1"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Creating session1 tag                             |
|  Expected: Tag created (no output for lightweight tag)   |
+----------------------------------------------------------+

Step 2: Verify tag was created

    git tag

Expected output:
    session1

    # Or view with details:
    git show session1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: git tag output                                    |
|  Expected: session1 tag listed                           |
+----------------------------------------------------------+

Step 3: View commit history with tag

    git log --oneline

Expected output:
    abc1234 (HEAD -> main, tag: session1) add pursuit.txt
    def5678 merge feature: resolve arrows.txt conflict
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: git log showing tag                               |
|  Expected: session1 tag visible next to commit           |
+----------------------------------------------------------+

Step 4: Switch to storm branch

    git checkout storm

Expected output:
    Switched to branch 'storm'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Switch to storm branch                            |
|  Expected: "Switched to branch 'storm'"                  |
+----------------------------------------------------------+


TASK 5: Rebase storm branch on master
-------------------------------------

UNDERSTANDING REBASE:

Before rebase (current state):

    main:    A---B---C---D (pursuit.txt commit, tag: session1)
                  \
    storm:         E---F (storm.txt commits)

After rebase:

    main:    A---B---C---D (pursuit.txt commit, tag: session1)
                          \
    storm:                 E'---F' (storm.txt commits replayed)

Step 1: View current history before rebase

    git log --oneline --all --graph

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: git log --graph before rebase                     |
|  Expected: Diverging branches visible                    |
+----------------------------------------------------------+

Step 2: Perform rebase

    git rebase main

Expected output:
    Successfully rebased and updated refs/heads/storm.

    # Or if there are no conflicts:
    First, rewinding head to replay your work on top of it...
    Applying: add storm.txt with first verse
    Applying: add second verse to storm.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: git rebase main output                            |
|  Expected: "Successfully rebased" message                |
+----------------------------------------------------------+

Step 3: Verify rebase result

    git log --oneline

Expected output:
    abc1234 (HEAD -> storm) add second verse to storm.txt
    def5678 add storm.txt with first verse
    ghi9012 (main, tag: session1) add pursuit.txt
    jkl3456 merge feature: resolve arrows.txt conflict
    ...

NOTE: The storm commits now appear AFTER the pursuit.txt commit!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: git log after rebase                              |
|  Expected: Storm commits after pursuit.txt commit        |
|            Linear history (no branching)                 |
+----------------------------------------------------------+

Step 4: View graph after rebase

    git log --oneline --all --graph

Expected output:
    * abc1234 (HEAD -> storm) add second verse to storm.txt
    * def5678 add storm.txt with first verse
    * ghi9012 (main, tag: session1) add pursuit.txt
    * jkl3456 merge feature: resolve arrows.txt conflict
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 22 <<<                  |
|                                                          |
|  Show: git log --graph after rebase                      |
|  Expected: Linear history, storm on top of main          |
+----------------------------------------------------------+

Step 5: Verify pursuit.txt is now accessible on storm

    ls -la
    cat pursuit.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 23 <<<                  |
|                                                          |
|  Show: pursuit.txt accessible on storm branch            |
|  Expected: pursuit.txt present and readable              |
+----------------------------------------------------------+

Step 6: Verify storm.txt still present

    cat storm.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 24 <<<                  |
|                                                          |
|  Show: storm.txt content after rebase                    |
|  Expected: All 4 lines of storm.txt intact               |
+----------------------------------------------------------+


================================================================================
                           GIT TAG COMMANDS
================================================================================

| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| git tag                          | List all tags                            |
| git tag <name>                   | Create lightweight tag                   |
| git tag -a <name> -m "msg"       | Create annotated tag                     |
| git tag -d <name>                | Delete local tag                         |
| git push origin <tag>            | Push specific tag to remote              |
| git push origin --tags           | Push all tags to remote                  |
| git show <tag>                   | Show tag details                         |
| git checkout <tag>               | Checkout specific tag (detached HEAD)    |
| git tag -l "v1.*"                | List tags matching pattern               |

Tag types:
- Lightweight: Just a pointer to a commit (git tag name)
- Annotated: Full object with message, author, date (git tag -a name -m "msg")

================================================================================


================================================================================
                           GIT REBASE COMMANDS
================================================================================

| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| git rebase <branch>              | Rebase current branch onto <branch>      |
| git rebase main                  | Rebase current branch onto main          |
| git rebase --continue            | Continue after resolving conflict        |
| git rebase --abort               | Abort rebase and return to original      |
| git rebase --skip                | Skip current commit during rebase        |
| git rebase -i HEAD~3             | Interactive rebase (last 3 commits)      |
| git rebase -i <commit>           | Interactive rebase from commit           |

================================================================================


================================================================================
                        MERGE VS REBASE COMPARISON
================================================================================

MERGE:
- Creates a merge commit
- Preserves complete history
- Non-destructive operation
- Shows when branches were integrated
- Better for shared/public branches

    main:    A---B---C---M
                  \     /
    feature:       D---E

REBASE:
- Rewrites commit history
- Creates linear history
- Cleaner project history
- Commits get new hashes
- Better for local/private branches

    main:    A---B---C
                      \
    feature:           D'---E'

GOLDEN RULE: Never rebase commits that have been pushed to a shared repository!

================================================================================


================================================================================
                        HANDLING REBASE CONFLICTS
================================================================================

If conflicts occur during rebase:

1. Git will pause and show conflict:
    CONFLICT (content): Merge conflict in file.txt
    error: could not apply abc1234... commit message

2. Fix the conflict in the file (remove markers)

3. Stage the resolved file:
    git add file.txt

4. Continue the rebase:
    git rebase --continue

5. Or abort if needed:
    git rebase --abort

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Cannot rebase: You have unstaged changes"
Solution: Commit or stash changes first:
    git stash
    git rebase main
    git stash pop

Problem: "Cannot rebase: Your index contains uncommitted changes"
Solution: Commit changes first:
    git add .
    git commit -m "WIP"

Problem: Rebase conflict won't resolve
Solution: Use --abort and try merge instead:
    git rebase --abort
    git merge main

Problem: Want to undo a rebase
Solution: Use reflog to find pre-rebase state:
    git reflog
    git reset --hard HEAD@{n}

Problem: Tag not showing in git log
Solution: Use --decorate flag:
    git log --oneline --decorate

Problem: Pushed branch, now rebase causes issues
Solution: After rebasing a pushed branch, force push:
    git push --force-with-lease origin storm
    (WARNING: Only do this if you're the only one using the branch!)

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Storm branch created from main
[ ] storm.txt created with first two lines
[ ] First commit made on storm branch
[ ] Two more lines added to storm.txt
[ ] Second commit made on storm branch
[ ] Switched to main branch
[ ] pursuit.txt created with four lines
[ ] pursuit.txt committed on main
[ ] session1 tag created on pursuit.txt commit
[ ] Tag visible in git tag output
[ ] Tag visible in git log
[ ] Switched to storm branch
[ ] git rebase main executed successfully
[ ] Storm commits now appear after pursuit.txt commit
[ ] git log shows linear history
[ ] pursuit.txt accessible on storm branch after rebase
[ ] storm.txt still intact after rebase

================================================================================
