# Forensics

## Git Version Control

### 🛠️ Tools

1. `$ git` (Kali Linux terminal command)
2. https://docs.github.com/en/get-started/git-basics/set-up-git (Git basics)

### Version Control (Easy) Write-Up

- *One of our employee's computer was compromised and we saw this backup file leave the network, but we couldn't find anything other than a simple README.md file in it. Help us found out what information the hackers got: "git_backup.zip"*

#### Git Logs, Git Show

1. Unzip "git_backup.zip" by typing: `$ unzip git_backup.zip` 
2. Change to "git_backup" directory by typing: `$ cd git_backup`
3. in the `~/git_backup` directory, list out ALL files/directories (+ hidden) by typing `$ ls -la`
4. you will see 20 total directories, subdirectories, and files → the `.git` directory is important

> The `.git` directory means that this is a git repository and we can use the `git` command to view and extract information.

5. `$ cd .git`
6. to check out the git log and see what commits have been created + view any users that are active on this repository, run: `$ git log` 

> Each commit is represented in a SHA1 hash with author/user of the commit, date, time, comment, etc.

7. to see a specific commit, run: `$ git show [hash]`

#### Git Branches

1. nothing useful in current branch, to switch branches: run `$ git branch` in `~/git_backup` directory to see the available branches in that repository
2. to switch branches, run: `$ git switch [branch name]`
