# Git Cheat Sheet

This file has information on how to work with Git and GitHub. The commands have been hand selected and sorted to be as easy to parse as possible.

For more information, please refer to the [Git](https://git-scm.com/doc) and [GitHub](https://cli.github.com/manual/index) documentation.

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
  - [Commonly used commands](#commonly-used-commands)
  - [Review a GitHub Pull request](#review-a-github-pull-request)
- [Common Problems](#common-problems)
  - [Console shows a long list](#console-shows-a-lengthy-list)
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

`-a` adds all unstaged files.

```PowerShell
git commit -m <descriptive message>
git commit -a -m <descriptive message>
```

- If the `-m` parameter is omitted, use vim to write the commit message. Also see [Common Problems](#cannot-commit-a-message).

#### Undo a commit

For example, because there is a typo in the commit message or files are missing or wrongly added.

```PowerShell
git reset --soft HEAD^
```

#### Remove commits indefinitely

If something went wrong terribly and they shall not appear again.

Attention! Only to this if you have NOT pushed yet! Else see [rebase](https://git-scm.com/docs/git-rebase).

```PowerShell
git reset --hard HEAD~X
```

Where X is the amount of commits to delete.

### Branch

#### Show all branches

```PowerShell
git branch
```

#### Create new branch based on the default branch

The default branch is usually the one, that is worked on, for example 'development'.

```PowerShell
git fetch # To get the latest changes
git branch <new branch name>
```

#### Change branch

```PowerShell
git checkout <branch name>
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

Attention! Make sure to merge in the correct direction, for example using the GitHub.com UI. A merge always merges another branch that must be specified into the currently loaded one.

- In the following, branch F (like in feature) will be merged into branch D (like in development).

#### Merge another branch into the current one

Consider using GitHub.com to avoid mistakes.

D <- F

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

- Remotely deleted branches i.e., after an accepted merge request will not be deleted locally. To do that (prune them) use

```PowerShell
git fetch --prune
# or
git remote prune <branch name to delete> # Without fetching
```

#### Pull

Download all remote branches and merge the remote copy of the currently selected one into the latter. The working directory and staging area will be overwritten.

- `pull` is a combination of `fetch` and `merge`, where the remote branch is merged into the local one.
- This command does not allow for verifying the changes and can result in merge conflicts. To avoid this, use `fetch` and `merge` (or `mergetool`) separately.
- If a branch has been removed remotely (for example due to an accepted merge request), its local copy will not be deleted. To accomplish this, use `--prune`.

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
git push
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
git commit --all --message 'initial commit'
gh repo create <repository name> --include-all-branches {--private | --public} --source .
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

Each merge requires a pull request, which must be validated and then merged by a different team member.

### Commonly used commands

```PowerShell
# Get the latest updates from the team
git fetch
# Create new feature branch
git checkout <name of new feature branch>

# Work on the feature branch

# Push to the feature branch
git push

# Create a merge request
# A team member merges the feature branch into the development branch and deletes the feature branch

# Remove local copy of the feature branch
git fetch --prune
```

### Review a GitHub Pull request

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
