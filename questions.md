# Git Interview Questions

[⟵ Take me back to the homepage](/README.md)

## Reset Soft vs Reset Hard

What `git reset origin/master` does is basically to undo the commit and leave all of the changes in an un-staged state.

On the other hand, `git reset --hard origin/master` will reset to a previous commit but will also get rid of all of your local changes.

## What's the `HEAD` of a branch?

`HEAD` is a pointer to the latest commit in your current branch. You can use the keyword `HEAD` as a shortcut to refer to the latest commit instead of using its SHA code.

### Squashing multiple commits into a single one (interactive rebasing)

Squashing multiple commits into a single commit will overwrite history, and should be done with caution. However, this is useful when working in feature branches. To squash the last N commits of the current branch, run the following command (with `{N}` replaced with the number of commits that you want to squash):

```
git rebase -i HEAD~{N}
```

Upon running this command, an editor will open with a list of these N commit messages, one per line. Each of these lines will begin with the word `pick`. Replacing `pick` with `squash` or `s` will tell Git to combine the commit with the commit before it. To combine all N commits into one, set every commit in the list to be squash except the first one. Upon exiting the editor, and if no conflict arises, git rebase will allow you to create a new commit message for the new combined commit.

### What are the different ways you can refer to a commit?

In Git each commit is given a unique hash. These hashes can be used to identify the corresponding commits in various scenarios (such as while trying to checkout a particular state of the code using the `git checkout {hash}` command).

Additionally, Git also maintains a number of aliases to certain commits, known as refs. Also, every tag that you create in the repository effectively becomes a ref (and that is exactly why you can use tags instead of commit hashes in various git commands). Git also maintains a number of special aliases that change based on the state of the repository, such as `HEAD`, `FETCH_HEAD`, `MERGE_HEAD`, etc.

Git also allows commits to be referred as relative to one another. For example, `HEAD~1` refers to the commit parent to `HEAD`, `HEAD~2` refers to the grandparent of HEAD, and so on. In case of merge commits, where the commit has two parents, `^` can be used to select one of the two parents, e.g. `HEAD^2` can be used to follow the second parent.

And finally, refspecs. These are used to map local and remote branches together. However, these can be used to refer to commits that reside on remote branches allowing one to control and manipulate them from a local Git environment.

## Talking to the remote

There are just two ways to communicate to the remote: `git fetch` and `git push`. We are not including `git pull` here b/c `git pull = git fetch + git merge`.

What `git fetch` does is go to the remote (GitHub, BitBucket, whatever) and brings down any changes BUT does not merge them.

`git rebase` does three things:

1. All of the commits in `master` which are not in `origin/master` are moved to a temporary area.
2. Applies all of the commits in `origin/master` to `master`
3. Applies all of the commits in the temporary area back to `master`

## Merge vs Rebase

Merge creates a merge commit.
