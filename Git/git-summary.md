<img src="https://git-scm.com/images/logos/downloads/Git-Logo-2Color.svg" width="250" height="250">


# Git Commands Summary <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->

- [1. Downloading \& Initialization](#1-downloading--initialization)
- [2. Setup Credentials](#2-setup-credentials)
- [3. Branches](#3-branches)
- [4. Stage \& Snapshot](#4-stage--snapshot)
- [5. Merging](#5-merging)
- [6. The .gitignore File](#6-the-gitignore-file)
- [7. Redo Commits](#7-redo-commits)
- [8. Temporary Commits](#8-temporary-commits)


<br>
<br>

****************
## 1. Downloading & Initialization
Initialize an existing directoryas a git repository
```bash
git init
```

Clone(download) a repository that already exists on GitHub, including all of the files, branches, and commits.
```bash
git clone [repo_url]
```

After using the `git init` command, link the local repository to an empty GitHub repository using the following command:
```bash
git remote add origin [url]
```


<br>
<br>

****************
## 2. Setup Credentials
Set a username
```bash
git config --global user.name "user_name"
```

Set an email address
```bash
git config --global user.email "user_email"
```


<br>
<br>

****************
## 3. Branches
To check which branch that is
```bash
git branch
```

Creates a new branch
```bash
git branch [branch_name]
```

Creates a new branch and switch to that branch using one command only
```bash
git checkout -b [branch_name]
```

Deletes the specific branch
```bash
git branch -d [branch_name]
```


<br>
<br>

****************
## 4. Stage & Snapshot
Show modified files in working directory, staged for your next commit
```bash
git status
```

Add a file aas it looks now to your next commit(stage)
```bash
git add [file_name]
```

Add all changed files to staging area
```bash
git add .
```

Commit your staged content as a new commit snapshot
```bash
git commit -m "commit_message"
```

Updates your current local working branch with all new commits from the corresponding remote branch on GitHub. `git pull` is a combination of `git fetch` and `git merge`
```bash
git pull origin [branch_name]
```

Uploads all local branch commits to GitHub
```bash
git push origin [branch_name]
```

Synchronize your local repository with the remote repository on GitHub.com
```bash
git fetch
```


<br>
<br>

****************
## 5. Merging
Merge [branch_name] into the current branch
```bash
git merge [branch_name]
```


<br>
<br>

****************
## 6. The .gitignore File
Sometimes it may be a good idea to exclude files from being tracked with Git. This is typically done in a special file named `.gitignore`.


<br>
<br>

****************
## 7. Redo Commits
Reset staging area to match most recent commit, but leave the working directory unchanged
```bash
git reset
```

Reset staging area and working directory to match most recent commit and overwrites all changes in the working directory
```bash
git reset --hard
```

Create new commit that undoes all of the changes made in, then apply it to the current branch
```bash
git revert [commit_hash]
```


<br>
<br>

****************
## 8. Temporary Commits
Put current changes from your working directory into stash for later use
```bash
git stash
```

Apply stored stash content into working directory, and clear stash
```bash
git stash pop
```

Delete a specific stash from all your previous stashes
```bash
git stash drop
```