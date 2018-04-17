# A Git Flow for Agile Teams

[⟵ Take me back to the homepage](/README.md)

There are many usable Git workflows. Indeed, Git is really a tool for designing VCS workflows.

Just a heads up: this is by no means a normative document or an attempt to define the one true workflow. I've found this workflow to be productive and relatively painless, especially for teams that are still learning and transitioning towards a more Agile process.

## Feature Development

One possible Git feature development workflow consists of these steps:

1. Pull to update your local `develop` branch
2. Check out a feature branch
3. Do work in your feature branch, committing early and often
4. Rebase frequently to incorporate upstream changes
5. Interactive rebase (squash) your commits
6. Merge your changes with `develop`
7. Push your changes to the upstream

First, and while in your `develop` branch (`git checkout develop`), pull in the most recent changes:

```
git pull origin develop
```

This should never create a merge commit because we are never working directly in develop (or at least we shouldn't as all commits getting into develop should result from an approved and merged pull request).

Whenever you perform a pull, merge or rebase, make sure that you run tests directly afterwards. Git may not show any conflicts but that doesn’t mean that two changes are compatible. Also run tests before you commit (of course).

We begin with the topmost available story in our backlog. Let's say that it's "WEB-132: User Can Add A Comment To a Post". First, check out a feature branch named with the story id and a short, descriptive title:

```
git checkout -b feature/WEB-123-add-comments-to-posts
```

The id allows us to easily track this branch back to the story that spawned it. The title is there to give us humans a little hint as to what's in it.

Do some work on this branch, committing early and often (ie: whenever your tests pass).

Rebase against the upstream frequently to prevent your branch from diverging significantly:

```
git fetch origin develop
git rebase origin/develop
```

Once work on the feature is complete, you will have a branch with a lot of small commits like "Add comment form", "Hook up comment form to API", "Add some tests" and so on. This is useful while developing but larger, incremental commits are more easier to maintain. We will use an interactive rebase to squash them together.

We want the rebase to affect only the commits we've made to this branch, not the commits that exist on the upstream. To ensure that we only deal with the "local" commits, use:

```
git rebase -i origin/develop
```

Git will display an editor window with a list of the commits to be modified, something like:

```
pick 3dcd585 Add comment form
pick 9f5c362 Hook up comment form with API
pick dcd4813 Add some tests
pick 9ea48e3 Display comments on the post page
```

Now we tell Git what we to do. Change these lines to:

```
pick 3dcd585 Add comment form
s 9f5c362 Hook up comment form with API
s dcd4813 Add some tests
s 9ea48e3 Display comments on the post page
```

Save and close the file. This will squash these commits together into one commit and present us with a new editor window where we can give the new commit a message. We’ll use the story id and title for the subject and list the original commit messages in the body:

```
@TODO: Get example
```

Now, save and close your editor. This commit is now ready to be merged back into `develop`. First rebase against any recent changes in the upstream. Then merge your changes back into `develop`:

```
@TODO: Talk about PRs
```

To merge your PR you typically need to have:
* two approvals
* one green build

Finally, clean up obsolete branches (ie: remove merged in branches).

## Bug Fixes

Bug fixes will use the same workflow as feature work. Name your bugfix branch after the bug id and give it a descriptive name. Prefix the branch name with `bugfix/` to help you keep track of them, for instance: `bugfix/WEB-321-empty-comments-allowed`.

Do work in your bugfix branch, committing early and often. Rebase frequently against the upstream and again when you are finished. Then, use an interactive rebase to squash all the commits together. Use the bug id and title for the subject of the new commit. Include "BUG" or "BUGFIX" to make these commits easier to identify. For instance: `[BUGFIX] WEB-321: Empty Comments Should Not Be Allowed`.

## Git Log Style

Your Git commit log should be in the following format:

```
MBL-123 Story Title/Summary
<space>
Short description
```

## Commons Mistakes

* Unless you are absolutely sure of what you are doing, do not use `git pull` on branches other than `develop` and `rc`.
* Never force push to `develop`. You should rebase your changes, otherwise you'll wipe someone else's changes.
* -Unless you are squashing (or know exactly what you are doing), always use `git pull --rebase` to rebase your changes, not `git rebase`.-

## How to Resolve Conflicts During Rebase

When doing a `git pull --rebase`, if there's any conflict, it'll ask you to resolve it and continue.

1. Run `git status` to see the conflicting files
2. Resolve the conflict(s)
3. `git add` the resolved files
4. Once everything is resolved, do `git rebase --continue`

## Release Versions

Releasing versions means merging your `develop` branch into your `master` branch. This should only happen once the develop branch has been tested and is rock solid to go to production.

```
git fetch
git checkout develop && git reset --hard origin/develop
npm version [<newversion> | major | minor | patch]
git checkout master && git reset --hard origin/master
git merge develop
git push --tags && git push && git checkout develop && git push
```

## Semantic Versioning

Given a version number `MAJOR.MINOR.PATCH`, increment the:

1. `MAJOR` version when you make incompatible API changes,
2. `MINOR` version when you add functionality in a backwards-compatible manner, and
3. `PATCH` version when you make backwards-compatible bug fixes.

Additional labels for pre-release and build metadata [are available](https://docs.npmjs.com/cli/version) as extensions to the `MAJOR.MINOR.PATCH` format.

See the [Semantic Versioning](http://semver.org/) specification for more information.

---

## Typical workflow for working on a new feature

Developer already has a local repository and a personal Git repository setup and developer needs to work on a feature during a sprint. This use case can be reused for every new feature.

Fetch the newest code from upstream:

```
git fetch origin
```

Create a new branch in your local repository with the ticket name:

```
git checkout -b jira-123 origin/master
```

Ensure there aren't any files that differ from the HEAD of the upstream master, (if needed) reset working tree to origin/master:

```
git status
git reset --hard origin/master
```

Do some work and remember to commit early and often!

```
git status
// For new files only
git add <filename>
// For committing individual files
git commit <filename> -m "jira-id: a brief message about what you've done"
// To commit all modified files
git commit -am "jira-id: a brief message about what you've done"
```

Periodically merge with the upstream repository and rebase your local repository changes after any upstream changes:

```
git fetch -f origin
git rebase origin/master
```

In simple words, `git rebase` allows one to move the first commit of a branch to a new starting location. For example, if a feature branch was created from master, and since then the master branch has received new commits, git rebase can be used to move the feature branch to the tip of master. The command effectively will replay the changes made in the feature branch at the tip of master, allowing conflicts to be resolved in the process. When done with care, this will allow the feature branch to be merged into master with relative ease and sometimes as a simple fast-forward operation.

If there is a conflict you will see a message as follows, you have to manually do the merge and commit it:

```
CONFLICT (content): Merge conflict in SOME_FILE_OF_YOURS
Failed to merge in the changes.
Patch failed at 0001 forget upstream rebase needed
```

When you have manually resolved this problem run:

```
git rebase --continue
```

If you would prefer to skip this patch, instead run:

```
git rebase --skip
```

To check out the original branch and stop rebasing run:

```
git rebase --abort
```

Periodically push changes to your personal fork:

```
git push personal jira-123
```

Be careful when issuing a git pull command. In cases where your feature branch is based off an older commit in the parent branch, this can cause the creation of a merge commit. A merge commit occurs when when two previous commits are merged together to form a new commit. This new commit has two parent commits instead of one. This makes pulling changes out of a branch (when necessary) difficult. Following the instructions above, especially rebase, should avoid these issues.

---

# An enterprise Git flow

## Classification

* Main branches: `master`, `develop`
* Supporting branches: `feature/x`, `bugfix/x`, `hot fix/x`, `release/x`

## Lifetime
`develop` and `master` are the only two branches with an infinite lifetime. Feature and Bugfix branches are temporary and live only until they get merged in to `develop`.

## Contents
`origin/master`: source code of `HEAD` always reflects a production-ready state of the application.

`origin/develop`: source code of `HEAD` always reflects a state with the latest changes for the next release.

## Intent
`origin/develop` is an **integration branch**. We always merge feature and bugfix branches to `develop`.

`origin/master` is a release branch. Each time changes are merged into `master`, this is a production release by definition. This allows for CD.

## CI/CD

We can configure a Git hook script to automatically build and rollout our application to our production servers every time we get a commit on `master`.

We can also deploy to our staging environment every time a new commit lands on `origin/develop`.

## Feature and Bugfix Branches

* May branch off: `develop`
* Must merge back into: `develop`
* Naming convention: `feature/x`, `bugfix/x`

This includes bug fixes which are not hot fixes to prod.

Feature branch
  -> pull request
  -> merge back into develop: `git merge --no-ff`
  -> remove feature branch from remote

## Release Branches

* May branch off: `develop`
* Must merge back into: `develop` and `master`
* Naming convention: `release/x` or `release-x`

When to branch off `develop`? When it (almost) reflects the desired state of the new release.

After branching off `develop`, we assign a version number.

Release branches allow for meta-data preparation for the release (i.e. bump version number) but **we don't tag here**.

```
git checkout -b release-1.2 develop
./bump-version.sh 1.2
git commit -m "Bump version 1.2"
```

This release branch may exist for a while as bug fixes are applied here (**and not directly on `develop`**). Do not introduce new features on a release branch.

Finishing a release branch:

1) Merge release branch into `master`.
2) The merge commit in `master` must be tagged. **This first tag happens in `master`.**
3) Merge release branch into `develop`.
4) Remove release branch.

## Hotfix Branches

* May branch off: `master` (it actually branches off a tag in `master` that marks the prod version we need to patch)
* Must merge back into: `develop` and `master`
* Naming convention: `hotfix/x` or `hotfix-x`

These sort of branches arise from the need to act immediately upon a **critical bug on production**.

Their aim is to prepare for an unplanned production release. In the meantime, people can keep working on the `develop` branch.

```
git checkout -b hotfix-1.2.1 master
./bump-version.sh 1.2.1
git commit -a -m "Bump v1.2.1"
```

Note how we bump a new version immediately before branching off (we don't create a new tag just yet, though).

Finishing a hot fix branch:

1) Merge back into `master`.
2) Tag into master.
3) Merge into `develop` to safeguard that the bugfix is included in the next release as well.

```
git checkout master
git merge --no-ff hot fix-1.2.1
git tag -a 1.2.1

git checkout develop
git merge --no-ff hotfix-1.2.1
```

4) Remove the temporary branch from the remote.

