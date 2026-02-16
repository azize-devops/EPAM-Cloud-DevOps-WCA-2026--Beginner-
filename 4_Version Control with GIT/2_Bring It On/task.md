# Bring It On - Task

> **Note:** This task is performed immediately after the previous one (I Can Win).

## Tasks

1. Add a `.gitignore` file to the project and configure it to hide files with the extension `.db`, `.log` and directories with the names `target` or `bin`.

2. Create a feature branch and add two commits to it.

3. Merge the feature branch in master.

4. Return to feature and create the `arrows.txt` file with the following contents:

   ```
   The ship glides gently on the waves
   As day turns into night
   ```

   Make a commit.

5. Go to master. Create the `arrows.txt` file there and add the following text:

   ```
   One thousand burning arrows
   Fill the starlit sky
   ```

   Make a commit.

6. Merge feature in master resolving the conflict: save all 4 lines in `arrows.txt` file in the order they were added in steps 4 and 5.

## Self-Review

- `.gitignore` file correctly ignores `.db`, `.log` files and `target/`, `bin/` directories
- Feature branch created with two commits
- Feature branch merged into master successfully
- Merge conflict resolved with all 4 lines preserved in correct order
- `arrows.txt` contains:
  ```
  The ship glides gently on the waves
  As day turns into night
  One thousand burning arrows
  Fill the starlit sky
  ```

## Helpful Materials

- https://www.atlassian.com/git/tutorials/using-branches
- https://www.atlassian.com/git/tutorials/using-branches/merge-conflicts
- https://git-scm.com/docs/gitignore
