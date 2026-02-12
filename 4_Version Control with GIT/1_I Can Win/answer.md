# I CAN WIN - ANSWER

This guide covers Git basics including installation, SSH key configuration,
repository management, commits, and synchronization with GitHub.


TASK 1: Install git and generate SSH keys
-----------------------------------------

Step 1: Install Git

On CentOS/RHEL:
    sudo yum install git -y

On Ubuntu/Debian:
    sudo apt install git -y

On Windows:
    Download from https://git-scm.com/download/win and run installer

Verify installation:
    git --version

Expected output:
    git version 2.x.x

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: git --version output                              |
|  Expected: Git version number displayed                  |
+----------------------------------------------------------+

Step 2: Generate SSH key pair

    ssh-keygen -t ed25519 -C "your_email@example.com"

    # Or using RSA:
    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

When prompted:
- Press Enter to accept default file location (~/.ssh/id_ed25519)
- Enter passphrase (optional, press Enter for none)

Expected output:
    Generating public/private ed25519 key pair.
    Enter file in which to save the key (/home/user/.ssh/id_ed25519):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /home/user/.ssh/id_ed25519
    Your public key has been saved in /home/user/.ssh/id_ed25519.pub

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: ssh-keygen command execution                      |
|  Expected: Key generation with fingerprint displayed     |
+----------------------------------------------------------+

Step 3: Start SSH agent and add key

    # Start SSH agent
    eval "$(ssh-agent -s)"

    # Add private key to agent
    ssh-add ~/.ssh/id_ed25519

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: SSH agent started and key added                   |
|  Expected: "Agent pid" and "Identity added" messages     |
+----------------------------------------------------------+

Step 4: Copy public key

    cat ~/.ssh/id_ed25519.pub

Copy the entire output (starts with ssh-ed25519...)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: cat public key output                             |
|  Expected: Public key content displayed                  |
+----------------------------------------------------------+

Step 5: Add SSH key to GitHub

1. Go to https://github.com/settings/keys
2. Click "New SSH key"
3. Title: Give a descriptive name (e.g., "My Laptop")
4. Key type: Authentication Key
5. Key: Paste your public key
6. Click "Add SSH key"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: GitHub SSH keys settings page                     |
|  Expected: "New SSH key" button visible                  |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Adding new SSH key on GitHub                      |
|  Expected: Title and Key fields filled                   |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: SSH key added successfully                        |
|  Expected: Key visible in SSH keys list                  |
+----------------------------------------------------------+

Step 6: Test SSH connection to GitHub

    ssh -T git@github.com

Expected output:
    Hi username! You've successfully authenticated, but GitHub does not provide shell access.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: ssh -T git@github.com output                      |
|  Expected: "Successfully authenticated" message          |
+----------------------------------------------------------+


TASK 2: Configure git user.name and user.email
----------------------------------------------

Set global Git configuration:

    git config --global user.name "Your Name"
    git config --global user.email "your_email@example.com"

Verify configuration:

    git config --global --list
    # or
    git config user.name
    git config user.email

Expected output:
    user.name=Your Name
    user.email=your_email@example.com

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: git config commands and verification              |
|  Expected: user.name and user.email displayed            |
+----------------------------------------------------------+

Optional - View config file:

    cat ~/.gitconfig

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: ~/.gitconfig file content                         |
|  Expected: [user] section with name and email            |
+----------------------------------------------------------+


TASK 3: Create repository on GitHub and clone locally
-----------------------------------------------------

Step 1: Create new repository on GitHub

1. Go to https://github.com/new
2. Repository name: my-favorite-song (or any name)
3. Description: (optional)
4. Select "Public" or "Private"
5. DO NOT initialize with README (to keep it empty)
6. Click "Create repository"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: GitHub new repository page                        |
|  Expected: Repository name and settings filled           |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Repository created successfully                   |
|  Expected: Quick setup page with clone URL               |
+----------------------------------------------------------+

Step 2: Clone repository locally

    # Using SSH (recommended)
    git clone git@github.com:username/my-favorite-song.git

    # Or using HTTPS
    git clone https://github.com/username/my-favorite-song.git

    # Navigate into the repository
    cd my-favorite-song

Expected output:
    Cloning into 'my-favorite-song'...
    warning: You appear to have cloned an empty repository.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: git clone command execution                       |
|  Expected: Repository cloned (empty repository warning)  |
+----------------------------------------------------------+

Verify you're in the repository:

    pwd
    ls -la
    git status

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Inside cloned repository                          |
|  Expected: .git folder present, git status works         |
+----------------------------------------------------------+


TASK 4: Create song.txt with half the lyrics
--------------------------------------------

Create song.txt file with first half of your favorite song:

    vim song.txt
    # or
    nano song.txt

Example content (first half of a song):

    I Can Win
    ---------

    When the world seems cold
    And you feel so alone
    Remember you are strong
    You can carry on

    [First half of lyrics...]

Save the file and verify:

    cat song.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Creating song.txt file                            |
|  Expected: Text editor with first half of lyrics         |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: cat song.txt output                               |
|  Expected: First half of song lyrics displayed           |
+----------------------------------------------------------+


TASK 5: Commit and push to GitHub
---------------------------------

Step 1: Check status

    git status

Expected output:
    On branch main
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            song.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: git status output                                 |
|  Expected: song.txt shown as untracked file              |
+----------------------------------------------------------+

Step 2: Stage the file

    git add song.txt
    # or add all files
    git add .

    git status

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: git add and status                                |
|  Expected: song.txt staged (green, ready to commit)      |
+----------------------------------------------------------+

Step 3: Make the commit

    git commit -m "add first half of my favorite song"

Expected output:
    [main (root-commit) abc1234] add first half of my favorite song
     1 file changed, X insertions(+)
     create mode 100644 song.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: git commit output                                 |
|  Expected: Commit created with specified message         |
+----------------------------------------------------------+

Step 4: Push to GitHub

    git push -u origin main
    # or if using master branch
    git push -u origin master

Expected output:
    Enumerating objects: 3, done.
    Counting objects: 100% (3/3), done.
    Writing objects: 100% (3/3), XXX bytes | XXX KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To github.com:username/my-favorite-song.git
     * [new branch]      main -> main
    Branch 'main' set up to track remote branch 'main' from 'origin'.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: git push output                                   |
|  Expected: Push successful, branch tracking set          |
+----------------------------------------------------------+


TASK 6: Verify song.txt on GitHub
---------------------------------

1. Go to your repository on GitHub: https://github.com/username/my-favorite-song
2. Click on song.txt file
3. Verify the first half of lyrics is there

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: GitHub repository page                            |
|  Expected: song.txt file visible in file list            |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 22 <<<                  |
|                                                          |
|  Show: song.txt content on GitHub                        |
|  Expected: First half of lyrics displayed                |
+----------------------------------------------------------+


TASK 7: Add second half via GitHub web interface
------------------------------------------------

Step 1: Edit file on GitHub

1. On GitHub, navigate to song.txt
2. Click the pencil icon (Edit this file)
3. Add the second half of the lyrics at the bottom

Example additional content:

    [Second half of lyrics...]

    Through every storm you'll find your way
    A brighter dawn, a better day
    Believe in yourself, you'll see
    The winner you were meant to be

    I Can Win!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 23 <<<                  |
|                                                          |
|  Show: GitHub edit interface                             |
|  Expected: Editing song.txt with pencil icon clicked     |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 24 <<<                  |
|                                                          |
|  Show: Adding second half of lyrics                      |
|  Expected: Complete lyrics in the editor                 |
+----------------------------------------------------------+

Step 2: Commit changes on GitHub

1. Scroll down to "Commit changes"
2. Commit message: "finish my song"
3. Select "Commit directly to the main branch"
4. Click "Commit changes"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 25 <<<                  |
|                                                          |
|  Show: GitHub commit dialog                              |
|  Expected: Commit message "finish my song" entered       |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 26 <<<                  |
|                                                          |
|  Show: Commit completed on GitHub                        |
|  Expected: File updated, commit visible in history       |
+----------------------------------------------------------+


TASK 8: Pull changes to local repository
----------------------------------------

Step 1: Pull from remote

    git pull

    # or explicitly
    git pull origin main

Expected output:
    remote: Enumerating objects: 5, done.
    remote: Counting objects: 100% (5/5), done.
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    Unpacking objects: 100% (3/3), done.
    From github.com:username/my-favorite-song
       abc1234..def5678  main       -> origin/main
    Updating abc1234..def5678
    Fast-forward
     song.txt | X +++
     1 file changed, X insertions(+)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 27 <<<                  |
|                                                          |
|  Show: git pull output                                   |
|  Expected: Changes pulled, Fast-forward update           |
+----------------------------------------------------------+

Step 2: Verify complete lyrics locally

    cat song.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 28 <<<                  |
|                                                          |
|  Show: cat song.txt after pull                           |
|  Expected: Complete song lyrics (both halves)            |
+----------------------------------------------------------+

Step 3: Check git log

    git log --oneline

Expected output:
    def5678 (HEAD -> main, origin/main) finish my song
    abc1234 add first half of my favorite song

+----------------------------------------------------------+
|                    >>> SCREENSHOT 29 <<<                  |
|                                                          |
|  Show: git log --oneline output                          |
|  Expected: Both commits visible with messages            |
+----------------------------------------------------------+

    # Detailed log
    git log

+----------------------------------------------------------+
|                    >>> SCREENSHOT 30 <<<                  |
|                                                          |
|  Show: git log (detailed) output                         |
|  Expected: Full commit details including author, date    |
+----------------------------------------------------------+


================================================================================
                           GIT COMMANDS REFERENCE
================================================================================

| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| git init                         | Initialize new repository                |
| git clone <url>                  | Clone remote repository                  |
| git status                       | Show working tree status                 |
| git add <file>                   | Stage file for commit                    |
| git add .                        | Stage all changes                        |
| git commit -m "message"          | Commit staged changes                    |
| git push                         | Push commits to remote                   |
| git push -u origin main          | Push and set upstream branch             |
| git pull                         | Fetch and merge remote changes           |
| git fetch                        | Fetch remote changes without merge       |
| git log                          | Show commit history                      |
| git log --oneline                | Show compact commit history              |
| git diff                         | Show unstaged changes                    |
| git diff --staged                | Show staged changes                      |
| git branch                       | List branches                            |
| git checkout <branch>            | Switch to branch                         |
| git remote -v                    | Show remote repositories                 |

================================================================================


================================================================================
                           SSH KEY COMMANDS
================================================================================

| Command                              | Description                          |
|--------------------------------------|--------------------------------------|
| ssh-keygen -t ed25519 -C "email"     | Generate Ed25519 key                 |
| ssh-keygen -t rsa -b 4096 -C "email" | Generate RSA key                     |
| eval "$(ssh-agent -s)"               | Start SSH agent                      |
| ssh-add ~/.ssh/id_ed25519            | Add key to agent                     |
| ssh -T git@github.com                | Test GitHub SSH connection           |
| cat ~/.ssh/id_ed25519.pub            | Display public key                   |

================================================================================


================================================================================
                           GIT CONFIG OPTIONS
================================================================================

| Command                                    | Description                    |
|-------------------------------------------|--------------------------------|
| git config --global user.name "Name"      | Set global username            |
| git config --global user.email "email"    | Set global email               |
| git config --global --list                | List global config             |
| git config --local --list                 | List repo-specific config      |
| git config --global core.editor "vim"     | Set default editor             |
| git config --global init.defaultBranch main | Set default branch name      |

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Permission denied (publickey)
Solutions:
- Verify SSH key is added to GitHub
- Check ssh-agent is running: eval "$(ssh-agent -s)"
- Add key to agent: ssh-add ~/.ssh/id_ed25519
- Test connection: ssh -T git@github.com

Problem: Repository not found
Solutions:
- Check repository URL is correct
- Verify you have access to the repository
- Use SSH URL instead of HTTPS or vice versa

Problem: Updates were rejected (non-fast-forward)
Solutions:
- Pull first: git pull origin main
- Then push: git push origin main

Problem: fatal: not a git repository
Solutions:
- Make sure you're in the repository directory
- Run git init if starting fresh
- Clone the repository if it exists remotely

Problem: Nothing to commit, working tree clean
Solutions:
- Make sure you saved your file changes
- Check git status to see file states
- Verify you're in the correct directory

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Git installed and version verified
[ ] SSH key pair generated
[ ] Public key added to GitHub account
[ ] SSH connection to GitHub tested successfully
[ ] Git user.name configured
[ ] Git user.email configured
[ ] Repository created on GitHub
[ ] Repository cloned locally
[ ] song.txt created with first half of lyrics
[ ] First commit made with message "add first half of my favorite song"
[ ] Changes pushed to GitHub
[ ] song.txt visible on GitHub with first half
[ ] Second half added via GitHub web interface
[ ] Commit "finish my song" made on GitHub
[ ] git pull executed locally
[ ] Complete lyrics visible in local song.txt
[ ] Both commits visible in git log

================================================================================

