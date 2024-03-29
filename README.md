# Git Cheat Sheet

This file has information on how to work with git and GitHub. The commands have been hand selected and sorted to be as easy to parse as possible.

For more information, please refer to the [Git](https://git-scm.com/doc) and [GitHub CLI](https://cli.github.com/manual/index) documentation. For information on the branching model and how to work in a team on the project, see [git-flow](https://nvie.com/posts/a-successful-git-branching-model/).

- [Common Commands](#common-commands)
  - [Overview](#overview)
  - [Stage and Snapshots](#stage-and-snapshots)
  - [Branch](#branch)
  - [Diff](#diff)
  - [Merge](#merge)
  - [Working with remote repositories](#working-with-remote-repositories)
  - [Config](#config)
  - [Repository Setup](#repository-setup)
- [The Git workflow](#the-git-workflow)
  - [Branches](#branches)
  - [Creating a feature](#creating-a-feature)
  - [Review a GitHub Pull Request](#review-a-github-pull-request)
- [Common Problems](#common-problems)
  - [Console shows a lengthy list](#console-shows-a-lengthy-list)
  - [Cannot commit a message](#cannot-commit-a-message)
  - [fatal: not a git repository (or any of the parent directories): .git](#fatal-not-a-git-repository-or-any-of-the-parent-directories-git)

## Common Commands

### Overview

#### Show branches

```PowerShell
git branch
```

- Use `git checkout <branch name>` to change the current branch.

#### Show commit history (latest commits)

```PowerShell
git log
git log --oneline
```

- Press `enter` to see more entries.
- Type `:q` to quit viewing the list.

#### Show staged and unstaged files

```PowerShell
git status
```

### Stage and Snapshots

#### Adding/removing file to/from next commit

```PowerShell
git add <file name>
git reset <file name>
```

#### Committing

`-a` adds all unstaged files before commiting, so that you do not have to run `git add --all` before.

```PowerShell
git commit -m <descriptive message>
git commit -a -m <descriptive message>
```

- If the `-m` parameter is omitted, use vim (or your installed terminal text editor) to write the commit message. Also see [Cannot commit a message](#cannot-commit-a-message).

#### Undo a commit

For example, because there is a typo in the commit message or files are missing or wrongly added.

```PowerShell
git reset --soft HEAD^
```

#### Discard staged and unstaged files

For example because software such as Unity has automatically generated files after a commit.

!!ATTENTION!! *If you have not commited the files in your staging area and working tree, they will be lost indefinitely.*

```PowerShell
git reset --hard HEAD
```

- The `HEAD` attibute is optional, as `--hard` defaults to it.

#### Remove commits indefinitely

If something went wrong terribly and they shall not appear again.

!!ATTENTION!! *Only to this if you have NOT pushed yet! Else see [rebase](https://git-scm.com/docs/git-rebase).*

!!ATTENTION!! *If you have not commited the files in your staging area and working tree, they will be lost indefinitely.*

```PowerShell
git reset --hard HEAD~<X>
```

Where X is the amount of commits to delete (do not include the angle brackets).

### Branch

#### Show all branches

```PowerShell
git branch
```

#### Work on a branch (checkout)

```PowerShell
git checkout <branch name>
```

#### Create new branch based on the default branch

The default branch is usually the one that is worked on, for example 'development'.

```PowerShell
git fetch # To get the latest changes
git branch <new branch name>
```

- Remember that `fetch` only fetches the current branch (check which one you are on with `git branch`). If you are not on the default branch, check it out first using `git checkout <default branch name>`).
- It is possible to create branches that are not based on other branches, so called orphans, but it is almost never desirable to have those in an everyday repository, as they cannot be merged into other branches that are not based on them.

#### Create new branch based on any branch

For example when implementing a fix for a non-standard branch.

```PowerShell
git checkout -b <new branch name> <base branch name>
```

#### Delete local branches

For example because they are outdated and have been deleted remotely after a successful pull request.

!!Attention!! *This will delete changes that have not been pushed to a remote repository!*

```PowerShell
git branch -d <branch name>
```

### Diff

Compare files to older versions of themselves.

- Use `difftool` instead of `diff`, to use VSCode for diffing. This must be [set up](#use-vscode-for-diffing-and-resolving-merge-conflicts) once.

#### Diff the current UNstaged files to the latest commit

```PowerShell
git diff
```

#### Diff the currently staged files to the latest commit

```PowerShell
git diff --cached
```

#### Diff two selected commits

```PowerShell
git diff <commit ID 1> <commit ID 2>
```

- A commit ID is a SHA1 hash value uniquely identifying a commit. Sometimes only the first 6 digits are used. It can be obtained by taking a look at the [log](#show-commit-history-latest-commits).
- Commits can be diffed across branches and forks.

#### Diff a file to a version of itself in the parent commit

```PowerShell
git diff HEAD^ <file name>
```

### Merge

!!Attention!! Make sure to merge in the correct direction, for example using the GitHub.com UI. A merge always merges another branch that must be specified into the currently loaded one.

- In the following, a feature branch will be merged into the development branch, such that the development branch will contain the changes from the feature branch after the merge.
- The feature branch can usually get deleted after the merge has been exetuted successfully.
- According to the [git-flow](https://nvie.com/posts/a-successful-git-branching-model/) branching model, one should never merge their own features but ask a team member to do so. Also see [creating a feature](#creating-a-feature).
- To avoid mistakes, consider using a graphical interface such as on GitHub.com.

#### Merge another branch into the current one

*Consider using the GitHub.com GUI to avoid mistakes.*

```PowerShell
git checkout <name of feature branch>
git merge <name of development branch>
```

### Working with remote repositories

#### Fetch

Download all remote branches without changing the working environment. The working directory (the files you are working on) and the staging area (already added files) are untouched. This command is considered safe and can be run any time.

```PowerShell
git fetch
```

- Remotely deleted branches i.e., after an accepted merge request will not be deleted locally. To do that use

```PowerShell
git branch -d <branch name>
```

-It might be necessary to use further attributes depending on your exact scenario, you will be prompted a suggestion in this case.

#### Pull

Download all remote branches and merge the remote copy of the currently selected one into the latter. The working directory and staging area will be overwritten.

- `pull` is a combination of `fetch` and `merge`, where the remote branch is merged into the local one.
- This command does not allow for verifying the changes and can result in merge conflicts. To avoid this, use `fetch` and `merge` (or `mergetool`) separately.

```PowerShell
git pull
# or
git fetch
git merge # or git mergetool
```

#### Push

Upload commits to a remote repository.

```PowerShell
git checkout <branch name to push>
git push # You might get prompted with further arguments to push to the remote repository.
```

### Config

#### Set up working environment

```PowerShell
# Read settings
git config --list
git config --global user.email
git config --global user.name

# Write settings
git config --global user.email '55597910+LennartCode@users.noreply.github.com'
git config --global user.name 'Lennart S'
```

- The noreply GitHub email address can be found in the [GitHub settings](https://github.com/settings/emails) under 'Keep my email address private'.

#### Use VSCode for diffing and resolving merge conflicts

```PowerShell
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait --merge $REMOTE $LOCAL $BASE $MERGED'
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'
```

### Repository Setup

Be sure to `cd` into the correct directory!

Cloning an existing repository:

- `cd` into the folder into which the repository folder should be cloned.

```PowerShell
cd path/to/repository
git clone <Repository Clone URL>
```

Initialising a local repository:

- cd into the root folder of the project.

```PowerShell
cd path/to/repository
git init --branch main
git add .
git commit --all --message <commit message>
gh repo create <repository name> {--private | --public} --source .
git push --set-upstream origin main
```

## The Git workflow

Use these techniques to work on a project with multiple people.

### Branches

- main\
Contains production ready code
- development\
Contains code for the next release. Gets merged into main on release day.
- feature branches\
Contain code for a feature that is currently developed. Will be merged into development up on completion.

Each merge requires a pull request, which must be validated and then merged by a different team member. Those pull requestes are sometimes also called merge requests.

### Creating a feature

For creating a feature, work on a feature branch and let a team member merge it into the development branch. For more information, see the [git-flow](https://nvie.com/posts/a-successful-git-branching-model/) branching model.

```PowerShell
# Get the latest updates from the team
git fetch
# Create new feature branch
git checkout <name of new feature branch>

# Work on the feature branch
git add --all
git commit -m <commit message>

# Push to the feature branch
git push
# You might be prompted with a suggestion for further arguments to push to the remote repository.

# Create a pull request
# We advise to use the GitHub UI to create the pull request, as the command can get very long and is vulnearable to mistakes. For the documentation see https://cli.github.com/manual/gh_pr_create

# A team member reviews your code, merges the feature branch into the development branch and deletes the feature branch

# Remove local copy of the feature branch
git branch -d <feature branch name>
```

### Review a GitHub Pull Request

- Note, in some solutions such as GitLab, pull requests that merge branches are called merge requests.

```PowerShell
# Download latest changes
git fetch

# List all pull requests on the repository
gh pr list

# Switch to branch that should be reviewed
gh pr checkout {<number> | <url> | <branch>}

# Review code using tools like Unity or do a diff
git difftool head^ # or git difftool <file name to diff>

# Go to GitHub and accept/reject the merge request or demand changes, or
gh pr close {<number> | <url> | <branch>} --delete-branch --comment <explain why the pull request got rejected>
gh pr review [<number> | <url> | <branch>] {--approve | --request-changes} --body <comment>
gh pr merge {<number> | <url> | <branch>} --delete-branch --body <optional merge message>
```

## Common Problems

### Console shows a lengthy list

Usually after `git log`

You are currently in a vim console. Type `:q` to quit.

### Cannot commit a message

Usually after `git commit` without `-m 'Commit Message'`

You are currently in vim. After typing your commit message, type `esc` (multiple times is fine) to enter the command entering mode at the bottom of the console.

- Going into edit (insert) mode again to edit the commit message: `i`

- Aborting the commit: `:q!`

- Confirming the commit: `:wq`

### fatal: not a git repository (or any of the parent directories): .git

You did not `cd` into any folders that are Git repositories (contain a .git folder), so the command you are trying to run does not know on which repo to do so.
