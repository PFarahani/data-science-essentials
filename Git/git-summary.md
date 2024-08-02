<img src="https://git-scm.com/images/logos/downloads/Git-Logo-2Color.svg" width="250" height="250">

# Git Commands Summary <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->

- [1. Downloading \& Initialization](#1-downloading--initialization)
- [2. Setup Credentials](#2-setup-credentials)
- [3. Local Changes](#3-local-changes)
- [4. Commit History](#4-commit-history)
- [5. Branches \& Tags](#5-branches--tags)
- [6. Update \& Publish](#6-update--publish)
- [7. Merge \& Rebase](#7-merge--rebase)
- [8. Undo](#8-undo)
- [9. Best Practices](#9-best-practices)
- [10. Help \& Documentation](#10-help--documentation)


---
## 1. Downloading & Initialization

Initialize an existing directory as a git repository
```bash
git init
```

Clone (download) a repository that already exists on GitHub, including all of the files, branches, and commits.
```bash
git clone [repo_url]
```

After using the `git init` command, link the local repository to an empty GitHub repository using the following command:
```bash
git remote add origin [url]
```


---
## 2. Setup Credentials

Set a username
```bash
git config --global user.name "user_name"
```

Set an email address
```bash
git config --global user.email "user_email"
```


---
## 3. Local Changes

Show modified files in working directory, staged for your next commit
```bash
git status
```

Changes to tracked files
```bash
git diff
```

Add a file as it looks now to your next commit (stage)
```bash
git add [file_name]
```

Add some changes in `<file>` to the next commit
```bash
git add -p <file>
```

Add all changed files to staging area
```bash
git add .
```

Commit all local changes in tracked files
```bash
git commit -a
```

Commit previously staged changes
```bash
git commit
```

Commit your staged content as a new commit snapshot
```bash
git commit -m "commit_message"
```

Change the last commit (Don't amend published commits!)
```bash
git commit --amend
```

Updates your current local working branch with all new commits from the corresponding remote branch on GitHub. `git pull` is a combination of `git fetch` and `git merge`
```bash
git pull origin [branch_name]
```

Synchronize your local repository with the remote repository on GitHub.com
```bash
git fetch
```

Uploads all local branch commits to GitHub
```bash
git push origin [branch_name]
```


---
## 4. Commit History

Show all commits, starting with the newest
```bash
git log
```

Show changes over time for a specific file
```bash
git log -p <file>
```

Who changed what and when in `<file>`
```bash
git blame <file>
```


---
## 5. Branches & Tags

To check which branch you are on
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

Creates a new branch based on a specific revision
```bash
git branch [new-branch-name] [revision_hash]
```

Switch to a different branch
```bash
git switch [other-branch]
git checkout [other-branch]
```

Deletes the specific branch
```bash
git branch -d [branch_name]
```

Deletes a branch even if it has unmerged changes (force delete)
```bash
git branch -D [branch_name]
```

Delete a remote branch
```bash
git push origin --delete [branch-name]
```

List all existing branches
```bash
git branch -av
```

Switch HEAD branch
```bash
git checkout [branch]
```

Create a new tracking branch based on a remote branch
```bash
git checkout --track [remote/branch]
```

Mark the current commit with a tag
```bash
git tag [tag-name]
```

Rename a branch
```bash
git branch -m [new-name]
```

Rename a branch with the current name specified
```bash
git branch -m [current-name] [new-name]
```

Compare branches
```bash
git log [main]..[feature-branch]
```


---
## 6. Update & Publish

List all currently configured remotes
```bash
git remote -v
```

Show information about a remote
```bash
git remote show [remote]
```

Add new remote repository, named `<remote>`
```bash
git remote add [shortname] [url]
```

Download all changes from `<remote>`, but don’t integrate into HEAD
```bash
git fetch [remote]
```

Publish local changes on a remote
```bash
git push [remote] [branch]
```

Delete a branch on the remote
```bash
git branch -dr [remote/branch]
```

Publish your tags
```bash
git push --tags
```

Publish an existing local branch to a remote
```bash
git push -u origin [local-branch]
```


---
## 7. Merge & Rebase

Merge [branch_name] into the current branch
```bash
git merge [branch_name]
```

Rebase your current HEAD onto `<branch>` (Don't rebase published commits!)
```bash
git rebase [branch]
```

Abort a rebase
```bash
git rebase --abort
```

Continue a rebase after resolving conflicts
```bash
git rebase --continue
```

Use your configured merge tool to solve conflicts
```bash
git mergetool
```

Use your editor to manually solve conflicts and mark file as resolved
```bash
git add [resolved-file]
```

Remove a file from the staging area
```bash
git rm [resolved-file]
```


---
## 8. Undo

Discard all local changes in your working directory
```bash
git reset --hard HEAD
```

Discard local changes in a specific file
```bash
git checkout HEAD [file]
```

Revert a commit (by producing a new commit with contrary changes)
```bash
git revert [commit]
```

Reset your HEAD pointer to a previous commit and discard all changes since then
```bash
git reset --hard [commit]
```

Reset your HEAD pointer to a previous commit and preserve all changes as unstaged changes
```bash
git reset [commit]
```

Reset your HEAD pointer to a previous commit and preserve uncommitted local changes
```bash
git reset --keep [commit]
```


---
## 9. Best Practices

**Commit Related Changes**

A commit should be a wrapper for related changes. For example, fixing two different bugs should produce two separate commits. Small commits make it easier for other developers to understand the changes and roll them back if something went wrong. With tools like the staging area and the ability to stage only parts of a file, Git makes it easy to create very granular commits.

**Commit Often**

Committing often keeps your commits small and helps you commit only related changes. It allows you to share your code more frequently with others, making it easier to integrate changes regularly and avoid merge conflicts. Avoid having few large commits and sharing them rarely, as it makes conflict resolution harder.

**Do Not Commit Half-Done Work**

You should only commit code when it’s completed. This doesn’t mean you have to complete a whole, large feature before committing. Split the feature’s implementation into logical chunks and commit early and often. Avoid committing just to have something in the repository before leaving the office. Consider using Git’s "Stash" feature instead.

**Test Code Before You Commit**

Resist the temptation to commit something that you "think" is completed. Test it thoroughly to ensure it’s complete and has no side effects. Having your code tested is crucial, especially when pushing or sharing your code with others.

**Write Good Commit Messages**

Begin your message with a short summary of your changes (up to 50 characters). Separate it from the body with a blank line. The body should provide detailed answers to:
- What was the motivation for the change?
- How does it differ from the previous implementation?

Use the imperative, present tense ("change", not "changed" or "changes") to be consistent with generated messages from commands like `git merge`.

**Version Control is Not a Backup System**

Having your files backed up on a remote server is a nice side effect, but don’t use your VCS as a backup system. Pay attention to committing semantically—don’t just cram in files.

**Use Branches**

Branches are one of Git’s most powerful features. Use branches extensively in your development workflows for new features, bug fixes, ideas, etc. They help you avoid mixing up different lines of development.

**Agree on a Workflow**

Git offers many workflows (long-running branches, topic branches, merge or rebase, git-flow, etc.). Choose one based on your project, overall development, deployment workflows, and team preferences. Agree on a common workflow that everyone follows.


---
## 10. Help & Documentation

Get help on the command line
```bash
git help [command]
```