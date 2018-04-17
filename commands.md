# Git Commands Overview

[⟵ Take me back to the homepage](/README.md)

## Global setup

```
git config --global user.name "Federico Knüssel"
git config --global user.email federico.knussel@orbitz.com
```

## Enable terminal color

```
git config --global color.ui true
```

## Setting up custom editor for squashing / merging

```
git config --global core.editor <editor>
```

Options are: `gedit`, `emacs`, `vi` or `nano`. The default editor is `vi`.

If you just need to set this temporarily, omit the `global` keyword.

## Clone a repository

```
git clone https://fknussel@stash.orbitz.net/scm/android/android.git
```

## Initialize Git repo on an existing working directory

```
git init
```

## View the status of a repository

```
git status
```

## Stage files that have been modified and deleted

```
git add --all
```

## Stage files that have been deleted

```
git rm <filename>
```

## Interactive staging

```
git add -i
```

## Commit your staged changes

```
git commit -m "Add README file"
```

## View the commits for a particular branch

```
git log
git log --oneline
```

## View just the commits that modified a particular file

```
git log -- <filename>
```

## Go back to a particular commit

```
git checkout <sha>
```

**Heads up!** Stash or commit your changes before doing this.

## See what has changed in a local file against the same file in `origin/master`

```
git diff origin/master <filename>
git diff origin/master
```

If the file is not committed and just staged, you don't need to give the fully qualified path for the filename.

## Adding alternative remotes

```
git remote add personal https://fknussel@stash.orbitz.net/scm/~fknussel/android.git
```

## View info on remotes

```
git remote -v
```

## Delete / clean up remote aliases

```
git remote rm <remote>
```

## Create a new local branch based off an existing remote branch

```
git fetch origin
git checkout -b <your-new-branch> origin/<existing-remote-branch>
```

## Delete a branch in remote repo

```
git push <remote> :remoteBranchName
```

## Rebase off the latest code periodically

```
git fetch -f <remote>
git rebase <remote>/<branch>
```

## Send changes to remote repository

```
git push <remote> <branch>
```

## Push changes from local branch to a different remote branch

```
git push <remote> <local-branch-name>:<remote-branch-name>
```

## View all branches (local and remote) for a repo

```
git branch -a
```

## Delete a local branch

```
git branch -D DROID-123
```

## Create a new branch

```
git branch DROID-123
```

## Switch to another branch in repository

```
git checkout DROID-123
```

## Create a new branch and switch to it, all at once

```
git checkout -b DROID-123
```

## Forcefully push squashed / amended commits

```
git push -f personal DROID-123
```

## Cherry pick one or more commits and apply them to `HEAD`

```
git cherry-pick <commit>
```

## Show all tags in the repo

```
git tag
```

## Show the details of a particular tag

```
git show v<tag>
```

## Branches

`git branch` list all local branches
`git branch -r` list all remote branches
`git branch feature/blah`
`git checkout feature/blah`
`git checkout -b feature/blah` creates the branch + checks it out
`git merge feature/blah` from the target branch

## Unstage file

`git reset HEAD README.md` upstages the README file, HEADS refers to the last commit.

## Discard changes on last commit

`git checkout -- README.md` blows away all changes since last commit

## Rollback last commit

`git reset --soft HEAD^`

Undoes last commit and puts changes back into staging (KEEPS changes)

`git reset --hard HEAD^`

Undoes last commit and also DISCARDS all changed files.

`git reset --hard HEAD^2`

Blows away the last 2 commits (`HEAD^n` blows away the last `n` commits).

# Simple Workflow

`git help config` pass in any git command for help
`git init` initialises an empty Git repository, that is, creates a hidden `.git` folder
`git status` to see what has changed since the last commit
`git add --all`
`git commit -m "Message"`
`git commit -a -m "Message` = add all + commit
`git log` timeline history
`git diff` shows unstained differences since last commit
`git diff --staged` shows which lines have changed in staged files
`git commit --amend -m "Change commit message"` whatever has been staged is added to the last commit.
`git commit --amend --no-edit` change commit content w/o changing its message
`git remote show origin`

## Tags

`git tag` lists all tags
`git checkout v1.0.0` check outs tag
`git tag -a v1.0.1 -m "Fix some bugs"` adds new tag
`git push && git push --tags` make sure to push tags otherwise tags will remain local
