# Baseline picking logic

When conducting a visual review, it's useful to understand what you are comparing and why. When new changes are added to your codebase we have to determine what we'll compare these new changes to.

To better understand this, let's first define some basic concepts:

* **Baseline build** - a previous Percy build used as the basis for comparison.
* **Head build** - the latest Percy build which is compared to the baseline build.

## Workflows

While the head build is easy understand, sometimes how the baseline is picked is not as intuitive. Let's go over three different scenarios and reveal how Percy selects an appropriate baseline.

### Commits to master

The simplest example is no branches and commits made directly to master. In this case, new commits are compared against the last completed Percy build, which is often the previous commit.

![](/images/docs/baseline-picking-logic/master-commits-scenario.svg)

### Branches

If you are working on a branch but have yet to create a pull request, the baseline is the latest commit on the master branch.

![](/images/docs/baseline-picking-logic/branch-scenario.svg)

By default, Percy automatically picks the latest `master` build as the baseline for comparison and generating visual diffs. This means that as your product evolves and adds new features, Percy will automatically keep up to date and use the most relevant screenshots for comparison.

### Pull requests

When the [GitHub integration](/docs/integrations/github) is enabled and the build is from a pull request, you want to know what visual changes will be made after this request is merged. Percy uses the pull request base branch as the baseline. Specifically the baseline is the commit where the base branch stemmed from.

![](/images/docs/baseline-picking-logic/pull-created-scenario.svg)

This strategy significantly reduces visual diff noise in codebases where `master` changes frequently. Unrelated visual changes from master are not included in the comparison and the visual diffs will more accurately show only the visual changes from the pull request. If you use `git rebase` to incorporate master changes, Percy will start using the new branch point.

## Configure baseline branch

Percy automatically uses `master` as the default baseline branch. You can change the baseline branch that Percy will use by setting the `PERCY_TARGET_BRANCH` environment variable. See [Environment variables](/docs/learn/env-vars) for more info.

For example, if you use a `develop` branch instead of `master`, just set this environment variable in CI:

```bash
PERCY_TARGET_BRANCH=develop
```

## Automatic vs. manual diff base

Percy automatically picks the latest `master` build as the baseline for comparison and generating visual diffs. You can override this and require approval of master builds before they will be used for comparison. To do this, switch to "manual diff base" mode in your project settings.

Switching to manual diff base is *not* recommended as it will require much more manual intervention as your application is updated, but is available if your workflow demands it.
