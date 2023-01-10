<img src="assets/git-github-logo.svg" width="250" height="250">


# Git & GitHub <!-- omit from toc -->


## Table of Contents <!-- omit from toc -->

- [1. Git Installation \& Terminal Commands](#1-git-installation--terminal-commands)
  - [1.1. Configuring Git](#11-configuring-git)
  - [1.2. Terminal Basic Commands](#12-terminal-basic-commands)
- [2. The Very Basics of Git](#2-the-very-basics-of-git)
  - [2.1. First Git Commands](#21-first-git-commands)
  - [2.2. Committing](#22-committing)
  - [2.3. Amending Commits](#23-amending-commits)
  - [2.4. Some Basic Guidelines](#24-some-basic-guidelines)
  - [2.5. Ignoring Files](#25-ignoring-files)
- [3. Branches](#3-branches)
  - [3.1. Working With Branches](#31-working-with-branches)
  - [3.2. Merging Branches](#32-merging-branches)
- [4. Comparing Changes](#4-comparing-changes)
  - [4.1. Comparisons with Git Diff](#41-comparisons-with-git-diff)
  - [4.2. Chunk Header](#42-chunk-header)
- [5. Stashing](#5-stashing)
  - [5.1. Git Stash](#51-git-stash)
  - [5.2. Applying Specific Stashes](#52-applying-specific-stashes)
  - [5.3. Clearing the Stash](#53-clearing-the-stash)
- [6. Undoing Changes](#6-undoing-changes)
  - [6.1. Checkout](#61-checkout)
  - [6.2. Restore](#62-restore)
  - [6.3. Git Reset](#63-git-reset)
  - [6.4. Git Revert](#64-git-revert)
- [7. GitHub Basics](#7-github-basics)
  - [7.1. Cloning](#71-cloning)
  - [7.2. Configuring SSH keys](#72-configuring-ssh-keys)
  - [7.3. Get the Codes on GitHub](#73-get-the-codes-on-github)
  - [7.4. Remote](#74-remote)
  - [7.5. Pushing](#75-pushing)
- [8. Fetch \& Pull](#8-fetch--pull)
  - [8.1. Remote Branches](#81-remote-branches)
  - [8.2. Fetching](#82-fetching)
  - [8.3. Pulling](#83-pulling)
  - [8.4. Fetch vs Pull](#84-fetch-vs-pull)
- [9. Rebasing](#9-rebasing)
  - [9.1. Merging With Rebase](#91-merging-with-rebase)
  - [9.2. Cleaning Up History](#92-cleaning-up-history)
- [10. Tags](#10-tags)
  - [10.1. Leightweight Tags](#101-leightweight-tags)
  - [10.2. Annotated Tags](#102-annotated-tags)
  - [10.3. Tagging Previous Commits](#103-tagging-previous-commits)
  - [10.4. Forcing Tags](#104-forcing-tags)
  - [10.5. Deleting Tags](#105-deleting-tags)
  - [10.6. Pushing Tags](#106-pushing-tags)


<br>
<br>

****************

## 1. Git Installation & Terminal Commands

### 1.1. Configuring Git

If you are using a GUI, it should prompt you for your name and email the first time you open the app.

**Your name**
```bash
git config --global user.name "Tom Hulce"
```

**Your email**
```bash
git config --global user.email blah@blah.com
```

### 1.2. Terminal Basic Commands

| Command  | Name                    | Description                                                                                       |
| -------- | ----------------------- | ------------------------------------------------------------------------------------------------- |
| `ls`     | List                    | Use `/s` to list the contents of your current directory. Use `-a` after `ls` to show hidden files |
| `pwd`    | Print Working Directory | Prints the path to the working directory (where you currently are)                                |
| `cd`     | Change Directory        | Use `cd ..` to "back up" one directory                                                            |
| `touch`  | Touch                   | Use touch to create a file (or multiple)                                                          |
| `mkdir`  | Make Directory          | `mkdir` will create a new directory (or directories)                                              |
| `rm`     | rm                      | `rm` will delete a file or files. It permanently removes them                                     |
| `rm -rf` | rm -rf                  | Use this to delete a directory (r = recursive. f = force)                                         |


<br>
<br>

****************

## 2. The Very Basics of Git

### 2.1. First Git Commands

```bash
git status
```
Gives information on the current status of a git repository and its contents.

```bash
git init
```
Use this to create a new git repository. Before we can do anything git-related, we must initialize a repo first! 

DO NOT INIT A REPO INSIDE OF A REPO!: Before running `git init`, use `git status` to verify that you are not currently inside of a repo.

```bash
git log
```
Use this to see a log of previous commits and their hash values.

```bash
git log --oneline
```
`--oneline` is used to shorten the hash values and concise the log.


### 2.2. Committing

```bash
git add file1 file2

git add .
```
We use this command to stage changes to be committed. Use `git add` to add specific files to the staging area. Separate files with spaces to add multiple at once. Use `git add .` to stage all changes at once 


```bash
git commit
```
Running `git commit` will commit all staged changes. It also opens up a text editor and prompts you for a commit message.

```bash
git commit -m "my message"
```
The `-m` flag allows us to pass in an inline commit message, rather than launching a text editor.


### 2.3. Amending Commits

Suppose you just made a commit and then realized you forgot to include a file! Or, maybe you made a typo in the commit message that you want to correct.
Rather than making a brand new separate commit, you can "redo" the previous commit using the `--amend` option.

```bash
git commit -m "some commit"

git add forgotten_file

git commit --amend
```

### 2.4. Some Basic Guidelines

- Commit early and often

- Make commits atomic (group similar changes together, don't commit a million things at once)

- Write meaningful but concise commit messages


### 2.5. Ignoring Files

We can tell Git which files and directories to ignore in a given repository, using a `.gitignore` file. This is useful for files you know you NEVER want to commit, including:
- Secrets, API keys, credentials, etc.
- Operating System files (.DS_Store on Mac)
- Log files
- Dependencies & packages

Create a file called `.gitignore` in the root of a repository.

```bash
touch .gitignore
```

Inside the file, we can write patterns to tell Git which files & folders to ignore:

- `.DS_Store` will ignore files named .DS_Store
- `folderName/` will ignore an entire directory
- `*.log` will ignore any files with the .log extension


<br>
<br>

****************
## 3. Branches

### 3.1. Working With Branches

We'll often come across the term `HEAD` in Git.
`HEAD` is simply a pointer that refers to the current "location" in your repository. It points to a particular branch reference.

![image1](assets/image1.png)

```bash
git branch
```
Use git branch to view your existing branches. The default branch in every git repo is master, though you can configure this. 
Look for the `*` which indicates the branch you are currently on.

**Note:** Remember, branches are made based on the `HEAD`.

Use the `-v` flag with git branch to view more information about each branch:
```bash
git branch -v
```

```bash
git branch "branch-name"
```
Use this to make a new branch based upon the current HEAD
This just creates the branch. It **does not** switch you to that branch (the HEAD stays the same)

Use the `-d` flag to delete a branch:
```bash
git branch -d "branch-name"
```

Once you have created a new branch, use git switch "branch-name" to switch to it.
```bash
git switch "branch-name"
```

When you make a new commit, it exists only on your new branch, not on master.

Use git switch with the `-c` flag to create a new branch and switch to it all in one go:
```bash
git switch -c "branch-name"
```

Historically, we used `git checkout "branch-name"` to switch branches. This still works.
```bash
git checkout "branch-name"
```
The `checkout` command does a million additional things, so the decision was made to add a standalone switch command which is much simpler.


### 3.2. Merging Branches

We merge branches, not specific commits

We always merge to the current `HEAD` branch

To merge, follow these basic steps:

1. `switch` to or `checkout` the branch you want to merge the changes into (the receiving branch)
```bash
git switch "current-branch-name"
```

2. Use the `git merge` command to merge changes from a specific branch into the current branch (To merge the "specific-branch-name" into "current-branch-name")
```bash
git merge "specific-branch-name"
```

> What if we add a commit on master?
> 
> This happens all the time! Imagine one of your teammates merged in a new feature or change to master while you were working on a branch
> 
> ![image2](assets/image2.png)


> What happens when I try to merge?
> 
> Rather than performing a simple fast forward, git performs a "merge commit" We end up with a new commit on the master branch. Git will prompt you for a message.
> 
> ![image3](assets/image3.png)
> ![image4](assets/image4.png)


**Whenever you encounter merge conflicts, follow these steps to resolve them:**
1. Open up the file(s) with merge conflicts
2. Edit the file(s) to remove the conflicts. Decide which branch's content you want to keep in each conflict.  Or keep the content from both.
3. Remove the conflict "markers" in the document:
The content from the branch you are trying to merge from is displayed between the `=======` and  `>>>>>>>` symbols.
4. Add your changes and then make a commit!


<br>
<br>

****************
## 4. Comparing Changes

### 4.1. Comparisons with Git Diff

We can use the `git diff` command to view changes between commits, branches, files, our working directory, and more!  

We often use `git diff` alongside commands like `git status` and `git log`, to get a better picture of a repository and how it has changed over time.

```bash
git diff
```
Without additional options, `git diff` lists all the changes in our working directory that are NOT staged for the next commit.


`git diff HEAD` lists all changes in the working tree since your last commit.
```bash
git diff HEAD
```

If you want to compare changes in different commits use `git diff <COMMIT1>..<COMMIT2>`

If you want to compare changes in different branches use `git diff <BRANCH_NAME1>..<BRANCH_NAME2>`

`git diff --staged` or `--cached` will list the changes between the staging area and our last commit. ("Show me what will be included in my commit if I run `git commit` right now")
	
	
### 4.2. Chunk Header

Each chunk starts with the chunk header, found between @@ and @@.

Example:

`@@ -3,4 +3,5 @@`

From file a, 4 lines are extracted starting from line 3. From file b, 5 lines are extracted starting from line 3.


<br>
<br>

****************
## 5. Stashing

Imagine you're on master branch and you make new branch and switch to it. You do some new work, but don't make any commits. When you switch back to master:
1. Your changes come with you to the destination branch
2. Git won't let you switch if it detects potential conflicts

Git provides an easy way of stashing these uncommitted changes so that we can return to them later, without having to make unnecessary commits.

### 5.1. Git Stash

`git stash` is super useful command that helps you save changes that you are not yet ready to commit. You can stash changes and then come back to them later.

Running `git stash` will take all uncommitted changes (staged and unstaged) and stash them, reverting the changes in your working copy.

![image5](assets/image5.png)

```bash
git stash
```
**Note:** You can also use `git stash save` instead.

![image6](assets/image6.png)

Use `git stash pop` to remove the most recently stashed changes in your stash and re-apply them to your working copy.

```bash
git stash pop
```

You can use `git stash apply` to apply whatever is stashed away, without removing it from the stash. This can be useful if you want to apply stashed changes to multiple branches.

```bash
git stash apply
```

![image7](assets/image7.png)


If you have untracked files (files that you have never checked in to Git), they will not be included in the stash.

Fortunately, you can use the `-u` option to tell git stash to include those untracked files.

```bash
git stash -u
```

You can add multiple stashes onto the stack of stashes.  They will all be stashed in the order you added them.

```bash
git stash
# do some other stuff ...
git stash
# do some other stuff ...
git stash
```

**Note:** Run `git stash list` to view all stashes

```bash
git stash list

# stash@{0}: WIP on master: 049d078 Create index file
# stash@{1}: WIP on master: c264051 Revert "Add file_size"
# stash@{2}: WIP on master: 21d80a5 Add number to log
```

### 5.2. Applying Specific Stashes

Git assumes you want to apply the most recent stash when you run `git stash apply`, but you can also specify a particular stash like `git stash apply stash@{2}`

To delete a particular stash, you can use `git stash drop <stash-id>`

```bash
git stash drop stash@{2}
```

### 5.3. Clearing the Stash

To clear out all stashes, run `git stash clear`
```bash
git stash clear
```


<br>
<br>

****************
## 6. Undoing Changes

### 6.1. Checkout
We can use `git checkout` to create branches, switch to new branches, restore files, and undo history!

```bash
git checkout <commit-hash>
```
**Note:** You can use the `git log` command to view commit hashes. We just need the first 7 digits of a commit hash.

> **Example:**
>
> Suppose you want to go back to an old commit and make some new changes. With `git checkout <commit-hash>` you'll be in a detached HEAD state.
>
> ![image8](assets/image8.png)
>
> ```bash
> git checkout d8194d6
> ```
>
> ![image9](assets/image9.png)
> 
> While in detached HEAD, make a new branch and switch to it. Head is now back to pointing at a branch reference!
> 
> ```bash
> git switch -c newbranch
> ```
> 
> ![image10](assets/image9.png)
>
> Now on the new branch, make as many new commits as you want.
>
> ```bash
> git add .
> git commit -m "new commit"
> ````
>
> ![image11](assets/image10.png)

To reference previous commits using `git checkout` use the syntax below:

```bash
git checkout HEAD~1 # refers to the commit before HEAD (parent)
git checkout HEAD~2 # refers to 2 commit before HEAD (grandparent)
```

To discard any changes in that file, reverting back to the HEAD, you can use:
```bash
git checkout HEAD <filename>
```

There's another shorter option to revert a file:
```bash
git checkout -- <file>
```

Rather than typing HEAD, you can substitute `--` followed by the file(s) you want to restore.


### 6.2. Restore
`git restore` is a brand new Git command that helps with undoing operations.   

Suppose you've made some changes to a file since your last commit. You've saved the file but then realize you definitely do NOT want those changes anymore!
To restore the file to the contents in the HEAD: 
```bash
git restore <file-name>
```

**Note:** The above command is not "undoable". If you have uncommited changes in the file, they will be lost!

`git restore <file-name>` restores using HEAD as the default source, but we can change that using the `--source` option.

For example, `git restore --source HEAD~1 home.html` will restore the contents of home.html to its state from the commit prior to HEAD.  You can also use a particular commit hash as the source.

If you have accidentally added a file to your staging area with `git add` and you don't wish to include it in the next commit, you can use `git restore` to remove it from staging.

```bash
git restore --staged <file-name>
```

### 6.3. Git Reset
Suppose you've just made a couple of commits on the master branch, but you actually meant to make them on a separate branch instead.  To undo those commits, you can use git reset.
```bash
git reset <commit-hash>
```

`git reset <commit-hash>` will reset the repo back to a specific commit.  The commits are gone.


### 6.4. Git Revert
`git revert` is similar to `git reset` in that they both "undo" changes, but they accomplish it in different ways.

`git reset` actually moves the branch pointer backwards, eliminating commits.

`git revert` instead creates a brand new commit which reverses/undos the changes from a commit. Because it results in a new commit, you will be prompted to enter a commit message.
```bash
git revert <commit-hash>
```
> **Note:** If you want to reverse some commits that other people already have on their machines, you should use `git revert`. If you want to reverse commits that you haven't shared with others, use `git reset` and no one will ever know!
>
> ![image12](assets/image12.png)
>
> ```bash
> git reset HEAD~2
> ```
>
> ![image13](assets/image13.png)
>
> The branch pointer is moved back to an earlier commit, erasing the 2 later commits.
>
> ![image12](assets/image12.png)
>
> ```bash
> git revert 51494a6
> ```
>
> ![image14](assets/image14.png)
>
> This new commit reverses the changes from 51494a6.


<br>
<br>

****************
## 7. GitHub Basics

### 7.1. Cloning
To get a local copy of an existing repository, we can clone a remote repository hosted on GitHub or similar websites. All we need is a URL that we can tell Git to clone for use.
```bash
git clone <url>
```

Git will retrieve all the files associated with the repository and will copy them to your local machine. In addition, Git initializes a new repository on your machine, giving you access to the full Git history of the cloned project.

**Note:** Make sure you are not inside of a repo when you clone!

### 7.2. Configuring SSH keys
SSH key allows to be authenticated without having to enter our email and password every time we try to connect to GitHub.
To generate SSH key we must follow [these steps](https://docs.github.com/en/authentication/connecting-to-github-with-ssh):

**Note:** Before you generate an SSH key, you can check to see if you have any existing SSH keys.
```bash
ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist
```
Check the directory listing to see if you already have a public SSH key. By default, the filenames of supported public keys for GitHub are one of the following.
- id_rsa.pub
- id_ecdsa.pub
- id_ed25519.pub

### 7.3. Get the Codes on GitHub 

**Option 1: Existing Repo**

If you already have an existing repo locally that you want to get on GitHub:

1. Create a new repo on GitHub
2. Connect your local repo (add a remote)
3. Push up your changes to GitHub

**Option 2: Start From Scratch**

If you haven't begun work on your local repo, you can:

1. Create a brand new repo on GitHub
2. Clone it down to your machine
3. Do some work locally
4. Push up your changes to GitHub

### 7.4. Remote
Each remote is simply a URL where a hosted repository lives. To view any existing remotes for you repository, we can run:
```bash
git remote
```

Or adding a `-v` flag for more info
```bash
git remote -v
```

This just displays a list of remotes and if you haven't added any remotes yet, nothing will be shown.

To add a new remote, we need to provide both to Git.
```bash
git remote add <name> <url>
```

There are commands to rename and delete remotes if needed.
```bash
git remote rename <old> <new>
```
```bash
git remote remove <name>
```

### 7.5. Pushing
We need to specify the remote we want to push up to and the specific local branch we want to push up to that remote.
```bash
git push <remote> <branch>
```

If we want to push a local branch up to a remote branch of a different name, we could do:
```bash
git push <remote> <local-branch>:<remote-branch>
```


The `-u` option allows us to set the upstream of the branch we're pushing. You can think of this as a link connecting our local branch to a branch on GitHub. 
```bash
git push -u origin master
```

Running this, sets the upstream of the local master branch so that it tracks the master branch on the origin repo. So next time we can use the `git push` without specifying the \<remote\> and \<branch\> which will push our current branch to the upstream.


<br>
<br>

****************
## 8. Fetch & Pull

### 8.1. Remote Branches
To view the remote branches our local repository knows about.
```bash 
git branch -r
```
They follow this pattern `<remote>/<branch>`. 

> `origin/master` references the state of the master branch on the remote repo named origin.
 
Once you've cloned a repository that contains two branches: `master` and `temp`, we have all the data and Git history for the project at that moment in time. However, that does not mean it's all in the workspace! If you run `git branch -r` you'll see all remote branches. By default the `master` is connected to the local `master` branch but `origin/temp` is not available locally.

Run `git switch <remote-branch-name>` to create a new local branch from the remote branch of the same name. So, by running `git switch temp` you'll have a local `temp` branch which is connected to the remote branch `origin/temp`.

**Note:** It used to be slightly more complicated using `git checkout`:
```bash
git checkout --track origin/temp
```

### 8.2. Fetching
Fetching allows us to download changes from a remote repository, but those changes will not be automatically integrated into our working files. It lets you see what others have been working on, without having to merge those changes into your local repo.
```bash
git fetch <remote>
```
This command fetches branches and history from a specific remote repository. It only updates remote tracking branches.

**Note:** If not specified, `<remote>` defaults to `origin`

We can also fetch a specific branch from a remote:
```bash
git fetch <remote> <branch>
```

### 8.3. Pulling
We can use pulling to retrieve changes from a remote repository. Unlike fetch, pull actually updates our HEAD branch with whatever changes are retrieved from the remote.
```bash
git pull <remote> <branch>
```

> `git pull` = `git fetch` + `git merge`

If we run `git pull` without specifying a particular remote or branch to pull from, git assumes the following:
- remote will default to origin
- branch will default to whatever tracking connection is configured for your current branch.

**Note:** This behavior can be configured, and tracking connections can be changed manually. Most people dont mess with that stuff


### 8.4. Fetch vs Pull
| `git fetch`                                               | `git pull`                                                       |
| --------------------------------------------------------- | ---------------------------------------------------------------- |
| Gets changes from remote branch(es)                       | Gets changes from remote branch(es)                              |
| Updates the remote-tracking branches with the new changes | Updates the current branch with the new changes, merging them in |
| Does not merge changes onto your current HEAD branch      | Can result in merge conflicts                                    |
| Safe to do at anytime                                     | Not recommended if you have uncommitted changes!                 |


<br>
<br>

****************
## 9. Rebasing

There are two main ways to use `git rebase` command:
- as an alternative to merging
- as a cleanup tool

### 9.1. Merging With Rebase
Instead of using a merge commit, rebasing rewrites history by <u>creating new commits</u> for each of the original feature branch commits.

```bash
git switch feature

git rebase master
```

![image15](assets/image15.png)

**WARNING!**

Never rebase commits that have been shared with others. If you have already pushed commits up to Github...DO NOT rebase them unless you are positive no one on the team is using those commits.

### 9.2. Cleaning Up History

Running `git rebase` with the `-i` option will enter the interactive mode, which allows us to edit commits, add files, drop commits, etc. Note that we need to specify how far back we want to rewrite commits.

Also, notice that we are not rebasing onto another branch. Instead, we are rebasing a series of commits onto the HEAD they currently are based on.

```bash
git rebase -i HEAD~4
```

In our text editor, we'll see a list of commits alongside a list of commands that we can choose from. Here are a couple of the more commonly used commands:

- `pick` - use the commit
- `reword` - use the commit, but edit the commit message
- `edit` - use commit, but stop for amending
- `fixup` - use commit contents but meld it into previous commit and discard the commit message
- `drop` - remove commit


<br>
<br>

****************
## 10. Tags
Once a tag is created, it always refers to the same commit. It's just a label for a commit.

There are two types of Git tags we can use:
1. lightweight tags - They are just a name/label that points to a particular commit.
2. annotated tags - store extra meta data including the author's name and email, the date, and a tagging message (like a commit message)

To view the tags we use:
```bash
git tag
```

It will print a list of all the tags in the current repository. We can search for tags that match a particular pattern by using `git tag -l` and then passing in a wildcard pattern. For example, `git tag -l "*beta*"` will print a list of tags that include "beta" in their name.

To view the state of a repo at a particular tag, we can use:
```bash
git checkout <tag>
```

**Note:** This puts us in detached HEAD!

### 10.1. Leightweight Tags
To create a lightweight tag, use: 
```bash
git tag <tagname>   
```

By default, Git will create the tag referring to the commit that HEAD is referencing.

### 10.2. Annotated Tags

To create a new annotated tag, use:
```bash
git tag -a
```

Git will then open the default text editor and prompt you for additional information.  

Similar to `git commit`, we can also use the `-m` option to pass a message directly and forgo the opening of the text editor

### 10.3. Tagging Previous Commits
We can also tag an older commit by providing the commit hash:
```bash
git tag -a <tagname> <commit-hash>
```

### 10.4. Forcing Tags
Git will yell at us if we try to reuse a tag that is already referring to a commit. If we use the `-f` option, we can FORCE our tag through.
```bash
git tag -f <tagname>
```

### 10.5. Deleting Tags
To delete a tag, use:
```bash
git tag -d <tagname>
```

### 10.6. Pushing Tags
By default, the git push command doesn't transfer tags to remote servers. If you have a lot of tags that you want to push up at once, you can use the `--tags` option to the `git push` command. This will transfer all of your tags to the remote server that are not already there.
```bash
git push --tags
```


<br>
<br>

****************