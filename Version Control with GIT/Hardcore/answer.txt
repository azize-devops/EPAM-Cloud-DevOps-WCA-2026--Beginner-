HARDCORE - ANSWER
=================

This guide covers Git remote repository management including changing remote URLs,
pushing to multiple repositories, and managing remote configurations.

PREREQUISITE: Complete "Hurt Me Plenty" task first.


TASK 1: Push all commits to GitHub
----------------------------------

Step 1: Check current branch and status

    git branch
    git status

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: git branch and status output                      |
|  Expected: Current branch shown, working tree clean      |
+----------------------------------------------------------+

Step 2: View current remote configuration

    git remote -v

Expected output:
    origin  git@github.com:username/my-favorite-song.git (fetch)
    origin  git@github.com:username/my-favorite-song.git (push)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: git remote -v output                              |
|  Expected: Origin URL for fetch and push                 |
+----------------------------------------------------------+

Step 3: Push all branches to GitHub

    # Push current branch
    git push origin main

    # Push storm branch
    git push origin storm

    # Or push all branches at once
    git push --all origin

    # Push tags as well
    git push origin --tags

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: git push --all origin output                      |
|  Expected: All branches pushed successfully              |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: git push origin --tags output                     |
|  Expected: Tags pushed (session1)                        |
+----------------------------------------------------------+

Step 4: Verify on GitHub

1. Go to https://github.com/username/my-favorite-song
2. Check branches dropdown - should see main, feature, storm
3. Check tags - should see session1
4. Verify all files are present

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: GitHub repository page                            |
|  Expected: All branches visible in dropdown              |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: GitHub repository files                           |
|  Expected: All files (song.txt, arrows.txt, etc.)        |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: GitHub tags page                                  |
|  Expected: session1 tag visible                          |
+----------------------------------------------------------+


TASK 2: Create a new repository on GitHub
-----------------------------------------

Step 1: Create new repository on GitHub

1. Go to https://github.com/new
2. Repository name: my-favorite-song-backup (or any different name)
3. Description: "Backup repository"
4. Select "Public" or "Private"
5. DO NOT initialize with README (keep empty)
6. Click "Create repository"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: GitHub new repository page                        |
|  Expected: New repository name entered                   |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: New repository created                            |
|  Expected: Empty repository with quick setup page        |
+----------------------------------------------------------+

Step 2: Note the new repository URL

SSH URL:
    git@github.com:username/my-favorite-song-backup.git

HTTPS URL:
    https://github.com/username/my-favorite-song-backup.git

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: New repository URL                                |
|  Expected: SSH or HTTPS URL displayed                    |
+----------------------------------------------------------+


TASK 3: Change remote to new repository
---------------------------------------

Step 1: View current remote

    git remote -v

Note the current URL (you'll need it later to restore):
    origin  git@github.com:username/my-favorite-song.git (fetch)
    origin  git@github.com:username/my-favorite-song.git (push)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Current remote URL (before change)                |
|  Expected: Original repository URL                       |
+----------------------------------------------------------+

Step 2: Change remote URL to new repository

    git remote set-url origin git@github.com:username/my-favorite-song-backup.git

    # Or for HTTPS:
    git remote set-url origin https://github.com/username/my-favorite-song-backup.git

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: git remote set-url command                        |
|  Expected: Command executed (no output on success)       |
+----------------------------------------------------------+

Step 3: Verify remote was changed

    git remote -v

Expected output:
    origin  git@github.com:username/my-favorite-song-backup.git (fetch)
    origin  git@github.com:username/my-favorite-song-backup.git (push)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: git remote -v after change                        |
|  Expected: New repository URL for both fetch and push    |
+----------------------------------------------------------+

Alternative Method - Set fetch and push URLs separately:

    # Set fetch URL
    git remote set-url origin git@github.com:username/my-favorite-song-backup.git

    # Set push URL (if different)
    git remote set-url --push origin git@github.com:username/my-favorite-song-backup.git


TASK 4: Push to new repository and verify
-----------------------------------------

Step 1: Push all branches to new repository

    git push --all origin

Expected output:
    Enumerating objects: XX, done.
    Counting objects: 100% (XX/XX), done.
    Delta compression using up to X threads
    Compressing objects: 100% (XX/XX), done.
    Writing objects: 100% (XX/XX), X.XX KiB | X.XX MiB/s, done.
    Total XX (delta X), reused XX (delta X)
    To github.com:username/my-favorite-song-backup.git
     * [new branch]      main -> main
     * [new branch]      feature -> feature
     * [new branch]      storm -> storm

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: git push --all origin to new repo                 |
|  Expected: All branches pushed to new repository         |
+----------------------------------------------------------+

Step 2: Push tags to new repository

    git push origin --tags

Expected output:
    Total 0 (delta 0), reused 0 (delta 0)
    To github.com:username/my-favorite-song-backup.git
     * [new tag]         session1 -> session1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: git push origin --tags to new repo                |
|  Expected: Tags pushed to new repository                 |
+----------------------------------------------------------+

Step 3: Verify on GitHub - Second Repository

1. Go to https://github.com/username/my-favorite-song-backup
2. Verify all branches are present
3. Verify all files are present
4. Verify tags are present

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: Second repository on GitHub                       |
|  Expected: All branches visible                          |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: Second repository files                           |
|  Expected: Same files as first repository                |
+----------------------------------------------------------+

Step 4: Compare both repositories

Open both repositories side by side:
- https://github.com/username/my-favorite-song
- https://github.com/username/my-favorite-song-backup

They should have identical:
- Branches (main, feature, storm)
- Files (song.txt, arrows.txt, storm.txt, pursuit.txt, etc.)
- Commit history
- Tags (session1)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Both repositories side by side                    |
|  Expected: Identical content in both repos               |
+----------------------------------------------------------+


TASK 5: Restore remote to original repository
---------------------------------------------

Step 1: Change remote URL back to original repository

    git remote set-url origin git@github.com:username/my-favorite-song.git

    # Or for HTTPS:
    git remote set-url origin https://github.com/username/my-favorite-song.git

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: Restoring original remote URL                     |
|  Expected: Command executed                              |
+----------------------------------------------------------+

Step 2: Verify remote is restored

    git remote -v

Expected output:
    origin  git@github.com:username/my-favorite-song.git (fetch)
    origin  git@github.com:username/my-favorite-song.git (push)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: git remote -v after restoration                   |
|  Expected: Original repository URL restored              |
+----------------------------------------------------------+

Step 3: Test connection to original repository

    git fetch origin

    # Or test push (if you have changes)
    git push origin main

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: git fetch origin                                  |
|  Expected: Fetch successful from original repo           |
+----------------------------------------------------------+

Step 4: Verify everything works

    git remote show origin

Expected output:
    * remote origin
      Fetch URL: git@github.com:username/my-favorite-song.git
      Push  URL: git@github.com:username/my-favorite-song.git
      HEAD branch: main
      Remote branches:
        feature tracked
        main    tracked
        storm   tracked
      Local branches configured for 'git pull':
        main  merges with remote main
        storm merges with remote storm
      Local refs configured for 'git push':
        main  pushes to main  (up to date)
        storm pushes to storm (up to date)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 22 <<<                  |
|                                                          |
|  Show: git remote show origin output                     |
|  Expected: Full remote details showing original repo     |
+----------------------------------------------------------+


================================================================================
                        GIT REMOTE COMMANDS REFERENCE
================================================================================

| Command                              | Description                          |
|--------------------------------------|--------------------------------------|
| git remote                           | List remote names                    |
| git remote -v                        | List remotes with URLs               |
| git remote add <name> <url>          | Add new remote                       |
| git remote remove <name>             | Remove remote                        |
| git remote rename <old> <new>        | Rename remote                        |
| git remote set-url <name> <url>      | Change remote URL                    |
| git remote set-url --push <name> <url>| Change push URL only               |
| git remote show <name>               | Show remote details                  |
| git remote prune <name>              | Remove stale remote branches         |
| git remote update                    | Fetch all remotes                    |

================================================================================


================================================================================
                        WORKING WITH MULTIPLE REMOTES
================================================================================

You can have multiple remotes configured:

    # Add second remote
    git remote add backup git@github.com:username/backup-repo.git

    # View all remotes
    git remote -v
    # Output:
    # origin  git@github.com:username/main-repo.git (fetch)
    # origin  git@github.com:username/main-repo.git (push)
    # backup  git@github.com:username/backup-repo.git (fetch)
    # backup  git@github.com:username/backup-repo.git (push)

    # Push to specific remote
    git push origin main
    git push backup main

    # Fetch from specific remote
    git fetch origin
    git fetch backup

    # Push to all remotes
    git remote | xargs -L1 git push --all

================================================================================


================================================================================
                    DIFFERENT FETCH AND PUSH URLs
================================================================================

You can configure different URLs for fetch and push:

    # Set main URL (affects fetch)
    git remote set-url origin git@github.com:username/repo.git

    # Set different push URL
    git remote set-url --push origin git@github.com:other/repo.git

    # Verify
    git remote -v
    # Output:
    # origin  git@github.com:username/repo.git (fetch)
    # origin  git@github.com:other/repo.git (push)

Use case: Fetch from upstream, push to your fork.

================================================================================


================================================================================
                           PUSH OPTIONS
================================================================================

| Command                              | Description                          |
|--------------------------------------|--------------------------------------|
| git push                             | Push current branch                  |
| git push origin main                 | Push main to origin                  |
| git push --all origin                | Push all branches                    |
| git push origin --tags               | Push all tags                        |
| git push --mirror origin             | Push everything (branches + tags)    |
| git push -u origin main              | Push and set upstream                |
| git push --force origin main         | Force push (dangerous!)              |
| git push --force-with-lease          | Safer force push                     |
| git push --delete origin branch      | Delete remote branch                 |

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Permission denied (publickey)"
Solutions:
- Verify SSH key is added to new repository's GitHub account
- Test SSH: ssh -T git@github.com
- Check correct SSH key is being used

Problem: "Repository not found"
Solutions:
- Verify URL is correct
- Check repository exists on GitHub
- Ensure you have access to the repository

Problem: "Remote origin already exists"
Solution: Use set-url instead of add:
    git remote set-url origin <new-url>

Problem: "Updates were rejected" on push to new repo
Solutions:
- If new repo is empty, use: git push -u origin main
- If new repo has commits, use: git push --force origin main
  (WARNING: Only if you're sure!)

Problem: Forgot original remote URL
Solutions:
- Check GitHub for the repository URL
- Look in .git/config file
- Check git reflog for fetch/push history

Problem: Want to push to multiple remotes at once
Solution: Add multiple push URLs:
    git remote set-url --add --push origin git@github.com:user/repo2.git

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Current remote URL noted (original repository)
[ ] All branches pushed to original GitHub repository
[ ] All tags pushed to original GitHub repository
[ ] Original repository verified on GitHub
[ ] New repository created on GitHub (empty)
[ ] New repository URL noted
[ ] Remote URL changed to new repository (git remote set-url)
[ ] git remote -v shows new repository URL
[ ] All branches pushed to new repository
[ ] All tags pushed to new repository
[ ] New repository verified on GitHub
[ ] Both repositories have identical content
[ ] Remote URL restored to original repository
[ ] git remote -v shows original repository URL
[ ] Connection to original repository verified (git fetch)

================================================================================
