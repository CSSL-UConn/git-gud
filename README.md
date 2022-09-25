# Git Gud: A guide to efficient collaboration with git

This document aims to be an introduction to how to efficiently use `git` by people who never used git or have a basic working knowledge of how to use it. It also contains some references on how to branch out and learn more advanced `git` techniques. 

The remote server used to build this document is github. However, it can be adapted to gitlab's or bitbucket's (or any other git remote for that matter) workflow.


## Administrivia

This section touches upon the authorship, contributions, license under which this document is released and such.

### Authorship

This document was written by Mohamed E. Najd to be used by the CSSL Lab at the University of Connecticut. It is partially based off a git tutorial designed by Simon Graham and other online resources. Some of the examples and illustrations come directly from the [git book](https://git-scm.com/book/en/v2).

### License

This document is released under Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International ([CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/))

Thus anybody is free to:

* **Share** — copy and redistribute the material in any medium or format
* **Adapt** — remix, transform, and build upon the material

Under the following terms:

* **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made. You may do so in any reasonable manner, but not in any way that suggests the licensor endorses you or your use.

* **NonCommercial** — You may not use the material for commercial purposes.

* **ShareAlike** — If you remix, transform, or build upon the material, you must distribute your contributions under the same license as the original.

* **No additional restrictions** — You may not apply legal terms or technological measures that legally restrict others from doing anything the license permits

### Changelog:

| Version  |  Changes                         |
|----------|:---------------------------------|
| 1.0      |  Initial Release                 |

### Contributions

This document is in no way perfect nor complete. Contributions that improve or add to the document are welcomed and encouraged. 

If one wants to contribute, they are free to fork the [git repository]() and modify the tutorial as they see fit, then open a pull request against the main repository. People with successful PRs will be listed as contributors underneath. 

The following people contributed to this document:

* Simon Graham

## Getting Started With `git`

`git` is a free and opensource distributed version control system that aims to make coordination and collaboration between people working on the same source code. Its goals include speed, data integrity and support for distributed non-linear workflows.

### Installing Git

To Install git, please head to [https://git-scm.com/downloads](https://git-scm.com/downloads) and follow the instructions for your current operating systems.

### Git Basics

This chapter covers the basic commands you need to use in most of your interactions with git. It will teach how to configure a repo, clone a remote repo, add or remove files from tracking and how to stage and commit changes. For a detailed review of Git Basics, please check Chapters 2 and 3 of the [Git Book](https://git-scm.com/book/en/v2/)

#### Initializing a Local Git Repo

To initialize a locat git repo, the following is to be done:

~~~bash
$ cd <path/to/your/project>
$ git init
~~~

This creates a `.git` directory within your project. It containsall the necessary files needed for the normal operation of `git`. For more info on this folder, see The [Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain#ch10-git-internals) Chapter in the Git Book.


#### Cloning a Remote Git Repo

You can clone a local git repo using the following command

~~~~bash
$ git clone [-b <branch>] <remote-repo-url> [directory-name]
~~~~

* Specifying `-b <branch>` clones a specific branch of the remote repo (if unspecified it clones master)

* Specifying `[directory-name]` clones the repo under a directory named `directory-name` (otherwise, it clones under the same name as a repo).


#### Branching Strategies

Get introduces the concept of branches in order to manage different people working on different features. This section explains how git branching works. For a more in detail discussion on branching, check Chapter 3 of the [Git Book](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository)

We strongly advise that development is done in branches that are separate from the `main` branch of the repo (to avoid 

##### Creating a branch

To create a branch, get your local repository to the required remote branch/commit (using `git checkout`) and then run

~~~ bash
$ git branch <branchname>
~~~ 

this can be illustrated as follows:

Let's suppose we have a branch `branch-1` where the `HEAD` pointer points to the commit `D`

~~~
                          HEAD
                            |
                        branch-1
                            |
          A --- B --- C --- D

~~~

* Note: The `HEAD` pointer indicates the branch/commit your local repo is at for the moment.

When you execute `git branch branch-2` here is what happens:


~~~
                          HEAD
                            |
                        branch-1
                            |
          A --- B --- C --- D
                            |
                        branch-2

~~~

The `HEAD` pointer did not move as part of this operation


##### Moving to a branch

To move to a branch, use the following command

~~~~ bash
$ git checkout <branchname>
~~~~
or (as of Git 2.23)

~~~~ bash
$ git switch <branchname>
~~~~

This can be illusrated as follows, let's take off where you stopped in the previous example

~~~
                          HEAD
                            |
                        branch-1
                            |
          A --- B --- C --- D
                            |
                        branch-2

~~~

When you execute `git checkout branch-2` or `git switch branch-2` the head pointer moves to point to branch-2


~~~
                        branch-1
                            |
          A --- B --- C --- D
                            |
                        branch-2
                            |
                           HEAD

~~~

* Remember that the `HEAD` pointer points at the state (branch/commit) commit your local repo  is at.

##### Shortcuts

You can combine the two previous operations into one operations using the following

~~~~ bash
git checkout -b <branch-name>
~~~~

or

~~~~ bash
git switch -c <branch-name>
~~~~



#### Staging Files 

Files under git are classified two categories:

* Untracked: which are files that are not part of the repository.
* tracked: files that exist in the repo, and they exist in three states:
	*  Unmodified: Files that are not modified since the last commit.
	*  Modified: files that you modified since the last commit but not ready to be synchornized in next commit.
	*  Staged: modified files staged to be included in the next commit.

The following chart demonstrates how to move from a file state to another:

![Moving from a file state to another in git](https://git-scm.com/book/en/v2/images/lifecycle.png)

##### Getting the current status of the files in your local repo.

To get the current status of the files in your local branch

~~~~bash
 $ git status
~~~~

the result can look like:

~~~~bash
On branch <branch>
Your branch is up-to-date with 'origin/<branch>'.
nothing to commit, working tree clean
~~~~ 

* Using Short Status:

~~~~ bash
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt 
~~~~
 * `M` : Modified, `MM`: Modified, Staged then modified again, `A`: Added, `??`: Unstaged

When adding a file to the repo

~~~ bash
$ echo 'My Project' > README
$ git status
On branch <branch>
Your branch is up-to-date with 'origin/<branch>'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README

nothing added to commit but untracked files present (use "git add" to track)
~~~

##### Tracking a file

In order to add  a file to be tracked the following command is used:

~~~~ bash
$ git add <file-to-be-added>
~~~~

example:

~~~bash
$ git add README
$ git status
On branch <branch>
Your branch is up-to-date with 'origin/<branch>'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README
~~~


Some shortcuts to be used

* Stage all the modified files

~~~~bash
$ git add -u
~~~~

* Stage all modified and untracked files

~~~~bash
$ git add -A
~~~~



##### Staging modified files

In order to stage the files to be synched up with the remote repository, use the following command:

~~~~bash
$ git add <file-to-be-staged>
~~~~

example:

~~~ bash
$ git status

On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
~~~

In this example, the file `CONTRIBUTING.md` was modified but not staged. To stage it:

~~~~ bash
$ git add CONTRIBUTING.md
~~~~

##### Removing a file from git

To remove a file from being tracked in git, use the following command:

~~~~ bash 
$ git rm <file-to-be-removed>
~~~~

##### Removing changes from a file:

To remove changes from a file please use the following:

~~~~ bash
$ git checkout <file> HEAD~
git add <file>
~~~~



##### Viewing the changes done to a branch

To view the changes done on your branch you can use the following command

~~~~ bash
$ git diff 
~~~~

example:

~~~ git
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
~~~

Where the lines indicated by `---` are removed and `+++` are added.


#### Committing Changes

If you are satisfied with your changes and you want to add them to the branch, use the following command

~~~~ bash
$ git commit
~~~~

This command will open a text editor so you can add a commit message describingthe work done. See the [Managing commits](#Managing Commits) section of this document for more info.

You can also use 

~~~~ bash
$ git commit -m <your-commit-message>
~~~~


##### Synchronising Changes with the remote repo

In order to synchronised the changes with the remote repo, you can use the following command

~~~~ bash
$ git push origin/<branch>
~~~~ 

## Managing Commits

This section provides insight on how to be effective with the commit messages in order to facilitate work and collaboration between different people. The rules in this section are not meant to be proscriptive in any shape or form (we are not saying that you must follow these rules or you are a git heretic), however they make work and collaboration between people much easier

### Commit Message Style

Commits are the basic building blocks of git. Thus, it is very important that the commit contain clear descriptions of the change they encompass. We follow a modified version of the commit message style defined in [https://gist.github.com/turbo/efb8d57c145e00dc38907f9526b60f17](https://gist.github.com/turbo/efb8d57c145e00dc38907f9526b60f17). In a nutshell, we deviate in the following ways.

1. We put the issue(s) to be solved by the commit in the description line of the commit
2. If the commit is a cherry-pick into a `release` branch, indicate that it is a cherry-pick, and the commit id in the last line of your commit message.

The commit message style we encourage is as follows:

* First line: issue #<issue-number>: meaningful summary of the changes in around 50 chars or less, starting with an action verb.
* Second Line (Optional, only required when there is a longer explanation following): Blank
* Third to N-Th Line: A more detailed explanation of the changes, wrapped around 72 characters
* (Optional N+1 line): CherryPick and/or Co-Author Indicators

An example of the following message can look as follows:

~~~ git
issue #404: Illustrate an effective commit message

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here


Optional Lines Will follow here

cherry-picked from: 39a6ffa25dbec554a61eda9a5f7beeb196d9f2722a13cdd0095b3e7f12bdd731
Co-Authored-By: John Doe <john.doe@example.com>

~~~


### What Should a commit encompass?

In a very high level way, the commit should encompass changes that logically fit in together. In other words, changes that are pertaining to the same ticket number, and that modify the same subcomponent in the repo can go in a single commit. Thus, you can obviously have pull request containing a single commit, as you may have a PR with multiple commits.

Generally, you should follow this guideline for commits [in the dev branch, no history rewriting in the main branch]:

* A Commit is usually a logical set of changes to one subcomponent under one issue. 
* Do not split up a commit for no reason.
* Multiple attempts to fix the same issue should be in the same commit (within the same PR) the commits in the style [oops, this fix should have been in commit xyz] are frowned upon.
* Fixes for different issues should be in separate commits (maybe even separate branches)


### Restructuring Commits

Sometimes, you have changes pertaining to the same logical set in different places. And as per the guideline [above](#What should a commit encompass?), we advise that those changes go into the same commit. Thus, sometimes you need to restructure your commits accordingly.

| ❗  on rewriting commits                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------|
| When you are rewriting commits, Please do not rewrite commits that are already in the mainline branch. Doing so will make the mainline branch (usually the source of truth for a repo) very messy. The mainline branch should be append only.|

We distinguish between two different startegies:

* The changes will go into the commit referenced by the `HEAD` pointer: in that case you can do: `git commit --amend` and it will amend the top commit to add the new changes. 

* The changes will go into a previous commit (that is not the current `HEAD` pointer): you can create a temporary commit on your branch then go through an interactive rebase where you do a `fixup` into the desired commit. (see: [Rebasing Guideline](#Rebasing Guideline)) 

In case your changes haven't been pushed to remote, you can use `git push` . Otherwise, use `git push --force`


### Foxtrot Commits

(TBD - Advanced git topic, tl;dr: Avoid foxtrot commits as much as possible)


## Rebasing Guideline

Rebasing is the process of moving or combining a sequence of commits to a new base commit. It is very helpful to catch up with a specific branch, or to reorder and merge the commits into logical units. In this section we explore those two scenarios and demonstrate how to do a rebase effectively. 

### Rebasing to Catch up With a Branch

You created your branch a while ago, and now you need it to "catch up" with changes in a branch (we call `branch-1`).  Use rebase instead of merge from `branch-1`. Rebase with `branch-1` will make it the new "base" (hence "rebase") for your commits, replaying the changes and creating new commits. 

example:

Before rebase:

~~~
branch-1   : A --- B --- C --- D
                   \
your-branch:        E --- F
~~~

After rebase:

~~~
branch-1   : A --- B --- C --- D
                               \
your-branch:                    E --- F
~~~

In order to do so, you have to do the following:

~~~~ bash
$ git fetch
$ git rebase origin/<branch>
~~~~

If there are no conflicts, you will see something like

~~~~ 
Your branch and 'origin/your-branch' have diverged,
and have 111 and 3 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
~~~~

| ❗  on using `git pull`                                                                                               |
|-----------------------------------------------------------------------------------------------------------------------|
| **DO NOT USE `git pull`** `git pull` does a `git fetch` followed by `git merge` (from remote) and will mess up your branch!|

If you have no conflicts, just force push into the remote (this overwrites the version in the remote)

~~~ bash
$ git push ---force-with-lease
~~~

### Rebasing to Reorganize Commits and Rewrite History

Rebasing gives you a chance to rewrite history and combine, remove, reorder or update commits. You just give it an indication on the number of commits you want to play with (indicated with an X below), 

~~~ bash 
$ git rebase -i HEAD~X
~~~

or the ID of the newest commit that you do not want to be affected by the rebase

~~~ bash
$ git rebase -i <id-of-newest-commit-not-to-be-affected>
~~~

This opens a text editor containing a file, where the multiple rebase instructions appear as comments. That file is the set of instructions to be executed once the editor is closed, the most common choices as are:

* pick  Leave this commit as-is

* fixup  Combine the changes into the commit above and discard the comment.  Great for "oops, fixed it".

* reword Keep the commit, but give me a chance to change the commit message later.  Do not bother changing the message in this file.  It will open the editor again for each "reword"

Example:

~~~ git
pick d46455bb LOP-4492: 1. Added METAL renderer. 2. Modify test server and client to use METAL.
pick d46455bb LOP-4492: 1. Added METAL renderer. 2. Modify test server and client to use METAL.
pick 9849e525 Add microphone and camera entitlement in info.plist.
pick debabf52 LOP-4492: Change xcode to 11.5.
pick f653180c Remove all the debugging code.
pick 2069e73b LOP-4492:  Fixed a minor cleanup logic and moved a compiling option to apple only.
 
# Rebase 509e0f7e..2069e73b onto 509e0f7e (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
~~~

Note that the comments provide a documentation on what you can do in a rebase.

When you are done, you close the editor opened. git runs a script and applies those instructions. if you have picked `reword`, it will open a text editor to reword the commit message.

When you are done, you can do a `git push --force-with-lease` as above in order to sync up with the remote branch.


### Rebasing your commits on-top of another branch

Sometimes you have a branch and you want to change its parent branch. Let's follow the illustration

~~~
branch-1   : A --- B --- C --- D
                          \ 
your-branch:               E --- F

branch-2   : X --- Y --- Z
~~~

and you want to get into the following state

~~~
branch-1   : A --- B --- C --- D
                           
your-branch:               E --- F
                          /
branch-2   : X --- Y --- Z
~~~

in this case, use the following:

~~~ bash
$ git rebase --onto=<branch-2> HEAD~X
~~~

where X is the number of commits you want to merge into `branch2`

Then you do your usual 

~~~ bash
$ git push --force
~~~

### Resolving Conflicts After a Rebase

Rebasing is basically moving commits around, which may cause merge conflicts. That means that two commits modified the same lines in the same file and git doesn't know what changes to apply. When a merge conflict happens, `git` will tell you with a message that looks like the following:

~~~
Applying: Improve naming around create-import-process end-point
(....)
CONFLICT (content):conflicting-file.txt
Recorded preimage for 'conflicting-file.txt)'
error: Failed to merge in the changes.
Patch failed at 0003 Group tests into classes for Account data-import serializer
hint: Use 'git am --show-current-patch' to see the failed patch
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
~~~

In this case, use your favourite text editor to open the file causing the conflict. You can locate the conflict by looking for the conflict marker `<<<<<<<`

* `<<<<<<< HEAD` : indicates the changes in the base branch or the `HEAD` pointer
* `=======`      : separator between changes in the base branch and the one in the other branch
* `>>>>>>> branch-name or commit id` : the changes in the branch or commit id you are trying to apply

In order to fix the conflict, modify the conflicting code to contain the code that you want to appear in your branch post-rebase then do the following.

~~~ bash
git add <conflicting_files>
git rebease --continue
~~~



### More Git Rebase Resources

* General rebasing tutorial: [https://help.github.com/en/github/using-git/using-git-rebase-on-the-command-line] (https://help.github.com/en/github/using-git/using-git-rebase-on-the-command-line)

* Rebasing in a nutshell: [https://dev.to/erikaheidi/pick-squash-drop-rebase-comic-607](https://dev.to/erikaheidi/pick-squash-drop-rebase-comic-607)


## An introduction to The Git Reference Log (Advanced)

(TBD)



## Helpful Git Commands

There are a number of useful git commands that can help with analyzing and modifying commits including:

* `git blame` - shows the commit id where each change in a file was made.

* `git log -L<funcname>:<file>` (show changes over time to area of code) - the following shows all commits that affected the AttachVolume method:


* `git log --grep` to find commits referencing a pattern.


* `git log --stat` to show files involved in commit:


* `git show `to show the changes in a commit:


* ` git branch --all --contains <commit>`  to see what branches contain a given commit



* `git remote prune origin` - remove stale remote branches from your local repository (this is also done by the git fetch --prune commands used during rebasing)

* `git gc` - run the garbage collector - this will do the prune operation and also delete any commits that are no longer referenced. The git manual recommends running this on a regular basis.



