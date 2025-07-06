---
title: "GIT"
date: 2025-02-07T15:25:39+05:30
publishdate: 2025-02-07
lastmod: 2025-02-07
draft: false
tags: [git]
categories: [git]
---
### Installation
------------
~~~
sudo apt install git
~~~

### Configuration 
---------------
*configuration files:*
~~~
system ---- /etc/gitconfig 				-- Entire system
global ---- ~/.gitconfig or ~/.config/git/config	-- All repositories of the user
local ---- Inside repository .git/config		-- Just the repository
~~~

*Command:*
~~~
git config

git config --system
git config --global
git config --local

### Example
git config --global user.name "Narayan"
git config --global user.email narayanbala@gmail.com
git config --global credential.helper cache
git config --global init.defaultBranch main
git config --global color.ui auto
git config --global core.editor vim
~~~

### Display all the settings 
~~~
git config --list 

$ git config --list

user.name=Narayan
user.email=narayanbala@gmail.com
credential.helper=cache
color.ui=auto
init.defaultbranch=main
core.editor=vim
~~~

### Location of the settings
~~~
git config --list --show-origin

file:/home/narayan/.gitconfig   user.name=Narayan
file:/home/narayan/.gitconfig   user.email=narayanbala@gmail.com
file:/home/narayan/.gitconfig   credential.helper=cache
file:/home/narayan/.gitconfig   color.ui=auto
file:/home/narayan/.gitconfig   init.defaultbranch=main

$ git config user.name
Narayan
~~~

### Initializing a repository
-------------------------
~~~
### New Repository
git init

### Existing Repository
git clone https://github.com/narayanbyes/dotfiles
~~~

### Updating Repository
----------------------
~~~
### Adding new files or already added(modified) files to staging
git add .

### Committing 
git commit -m "Updated config files"

### Committing already tracked files in one step and remove files from the index
### that have been removed from the working tree
git commit -am "Updated config files"
~~~

### Checking status of files
-------------------------
~~~
$ git status 

On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working tree clean

This means you have a clean working directory; in other words, none of your tracked files are modified. 
Git also doesn’t see any untracked files, or they would be listed here. 

Let’s say you add a new file to your project, a simple README file. If the file didn’t exist before, and you run git status, 
you see your untracked file like so:

$ echo 'My Project' > README
$ git status

On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README

nothing added to commit but untracked files present (use "git add" to track)

You can see that your new README file is untracked, because it’s under the “Untracked files” heading in your status output. 
Untracked basically means that Git sees a file you didn’t have in the previous snapshot (commit), and which hasn’t yet been staged; 
Git won’t start including it in your commit snapshots until you explicitly tell it to do so.

git add README

If you run your status command again, you can see that your README file is now tracked and staged to be committed:

$ git status

On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README

~~~

### Ignoring files
----------------
Create a .gitignore file


### View changed files 
--------------------
~~~
Command: git diff
This command compares what is in your working directory with what is in your staging area
To see what you’ve changed but not yet staged, type git diff with no other arguments.

$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's


Command: git diff --staged
This command compares your staged changes to your last commit:
If you want to see what you’ve staged that will go into your next commit.

$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
~~~

### Skip the staging area and directly commit already tracked files
---------------------------------------------------------------
Adding the -a option to the git commit command makes Git automatically stage every file that is already tracked 
before doing the commit, letting you skip the git add part
~~~
$ git commit -a -m 'Add new benchmarks'
[master 83e38c7] Add new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)

Note: for new files that are not tracked, they need to be explicitly added using git add 
~~~

### Removing Files
---------------------
To remove a file from Git, you have to remove it from your tracked files (more accurately, remove it from your staging area) 
and then commit. 
~~~
$ rm PROJECTS.md

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")

$ git commit -am "Removed PROJECTS.md"

	or

$ git rm PROJECTS.md
rm 'PROJECTS.md'

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md

The next time you commit, the file will be gone and no longer tracked.

$ git commit -m "Removed PROJECTS.md"
~~~

### Viewing Commit history
----------------------
~~~
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    Change version number

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    Remove unnecessary test

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    Initial commit

Limiting logs
You can also limit the number of log entries displayed, such as using -2 to show only the last two entries.
$ git log -2

or duration
$ git log --since=2.weeks

One of the more helpful options is -p or --patch, which shows the difference (the patch output) introduced in each commit.
$ git log -p -2

If you want to see some abbreviated stats for each commit, you can use the --stat option:
$ git log --stat

Another really useful option is --pretty.
$ git log --pretty=oneline
$ git log --pretty=format:"%h - %an, %ar : %s"
~~~

### Amending a Commit
---------------------
When you commit too early and possibly forget to add some files, or you mess up your commit message. If you want to redo that commit,
make the additional changes you forgot, stage them, and commit again using the --amend option:
~~~
Command: git commit --amend -m "msg" 
if -m meessage is not specified it uses the message from the commit we are amending.

for example

$ git commit -m 'Initial commit'
$ git add forgotten_file
$ git commit --amend

You end up with a single commit — the second commit replaces the results of the first.
~~~

### Undoing things 
--------------------
*Unstaging a Staged File*
~~~
Command: git reset 

For example, let’s say you’ve changed two files and want to commit them as two separate changes, but you accidentally 
type git add * and staged them both. How can you unstage one of the two? The git status command reminds you:

$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
    modified:   CONTRIBUTING.md

Right below the “Changes to be committed” text, it says use git reset HEAD <file>…to unstage

$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M	CONTRIBUTING.md

$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md

Git version 2.23.0 introduced a new command: git restore. It’s basically an alternative to git reset which we just covered.
From Git version 2.23.0 onwards, Git will use git restore instead of git reset 

let’s say you’ve changed two files and want to commit them as two separate changes, but you accidentally type git add * and 
stage them both. How can you unstage one of the two? The git status command reminds you:

$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   CONTRIBUTING.md
	renamed:    README.md -> README

Right below the “Changes to be committed” text, it says use git restore --staged <file> to unstage. So, let’s use that
advice to unstage the CONTRIBUTING.md file:

$ git restore --staged CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   CONTRIBUTING.md

The CONTRIBUTING.md file is modified but once again unstaged.
~~~

### Unmodifying a Modified File
----------------------------------
What if you realize that you don’t want to keep your changes to the CONTRIBUTING.md file? How can you easily unmodify it
 — revert it back to what it looked like when you last committed (or initially cloned, or however you got it into your working directory)
~~~
$ git checkout -- CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README

### Unmodifying a Modified File with git restore

We can also use git restore to achieve what we just did previously. 

In the last example output, the unstaged area looks like this:

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   CONTRIBUTING.md

It tells you pretty explicitly how to discard the changes you’ve made. Let’s do what it says:

$ git restore CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
~~~

### Working with Remotes
--------------------
*For showing remotes of our repository*
Command: git remote 
~~~
$ git clone https://github.com/schacon/ticgit
cd ticgit 

$ git remote 
origin

-v, which shows you the URLs that Git has stored for the shortname to be used when reading and writing to that remote:
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)

If you have more than one remote, the command lists them all. For example, a repository with multiple remotes for 
working with several collaborators might look something like this.

$ cd grit
$ git remote -v
bakkdoor  https://github.com/bakkdoor/grit (fetch)
bakkdoor  https://github.com/bakkdoor/grit (push)
cho45     https://github.com/cho45/grit (fetch)
cho45     https://github.com/cho45/grit (push)
defunkt   https://github.com/defunkt/grit (fetch)
defunkt   https://github.com/defunkt/grit (push)
koke      git://github.com/koke/grit.git (fetch)
koke      git://github.com/koke/grit.git (push)
origin    git@github.com:mojombo/grit.git (fetch)
origin    git@github.com:mojombo/grit.git (push)

This means we can pull contributions from any of these users pretty easily. We may additionally have permission to push 
to one or more of these, though we can’t tell that here.
~~~

*Adding Remote Repositories*
Command: git remote add <shortname> <url>:
~~~
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
pb	https://github.com/paulboone/ticgit (fetch)
pb	https://github.com/paulboone/ticgit (push)

Now you can use the string pb on the command line instead of the whole URL. For example, if you want to fetch all the information 
that Paul has but that you don’t yet have in your repository, you can run git fetch pb:

$ git fetch pb
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5)
Unpacking objects: 100% (43/43), done.
From https://github.com/paulboone/ticgit
 * [new branch]      master     -> pb/master
 * [new branch]      ticgit     -> pb/ticgit
~~~

*Fetching and Pulling from Your Remotes*
As you just saw, to get data from your remote projects, you can run:

$ git fetch <remote>

The command goes out to that remote project and pulls down all the data from that remote project that you don’t have yet. 
After you do this, you should have references to all the branches from that remote, which you can merge in or inspect at any time.

If you clone a repository, the command automatically adds that remote repository under the name “origin”. 
So, git fetch origin fetches any new work that has been pushed to that server since you cloned (or last fetched from) it. 
It’s important to note that the git fetch command only downloads the data to your local repository — it doesn’t automatically
merge it with any of your work or modify what you’re currently working on. You have to merge it manually into your work when 
you’re ready.

*Pushing to our remotes*
To push your master branch to your origin server
~~~
git push origin master
~~~

*Information about remote*
Command: git remote show <remote>
~~~
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/schacon/ticgit
  Push  URL: https://github.com/schacon/ticgit
  HEAD branch: master
  Remote branches:
    master                               tracked
    dev-branch                           tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
~~~

### Fetch vs Pull
---------------
The key difference between git fetch and pull is that git pull copies changes from a remote repository directly
into your working directory, while git fetch does not. 
In the simplest terms, git pull does a git fetch followed by a git merge.


### Temporarily store modified files
----
Command: git stash save
This command temporarily stores all the modified tracked files.

Command: git stash pop  
This command restores the most recently stashed files.

Command: git stash list  
This command lists all stashed changesets.

Command: git stash drop  
This command discards the most recently stashed changeset.

### Understanding git reset command
-------------------------------------
The `git reset` command is a versatile tool in Git that allows you to undo changes in your repository. It can be used in different ways depending on the options you provide. Here’s a breakdown of its main functionalities:

### Basic Usage
The command can be used in three primary modes, each affecting the working directory, staging area, and commit history differently:

1. **`git reset --soft <commit>`**:
   - Moves the HEAD pointer to the specified commit.
   - Keeps changes in the staging area (index).
   - Useful for undoing commits while retaining the changes for further editing.

2. **`git reset --mixed <commit>`** (default behavior if no option is specified):
   - Moves the HEAD pointer to the specified commit.
   - Keeps changes in the working directory but unstages them (removes them from the staging area).
   - Useful for undoing commits and preparing to make new changes without losing any work.

3. **`git reset --hard <commit>`**:
   - Moves the HEAD pointer to the specified commit.
   - Discards all changes in both the staging area and the working directory.
   - This option is destructive and should be used with caution, as it permanently removes any changes made after the specified commit.

### Example
Assuming you have the following commit history:

```
A -- B -- C -- D (HEAD)
```

- **`git reset --soft B`**:
  - Result: 
    ```
    A -- B (HEAD)
    ```
  - Changes from commits C and D are still staged.

- **`git reset --mixed B`**:
  - Result:
    ```
    A -- B (HEAD)
    ```
  - Changes from commits C and D are in the working directory but not staged.

- **`git reset --hard B`**:
  - Result:
    ```
    A -- B (HEAD)
    ```
  - Changes from commits C and D are permanently lost.

### Use Cases
- **Undoing Commits**: You can use `git reset` to undo one or more commits while deciding what to do with the changes.
- **Reorganizing Commits**: It allows you to rework your commit history before pushing changes to a remote repository.
- **Cleaning Up**: The `--hard` option is useful for cleaning up your working directory if you want to discard changes completely.

### Caution
Always be careful when using `git reset`, especially with the `--hard` option, as it can lead to loss of work that cannot be recovered.
