- [Branching/Tagging](#branchingtagging)
  - [List branches](#list-branches)
- [Account](#account)
  - [Set up commit email](#set-up-commit-email)

# Local Changes

### Changed in working

```
git status
```

# Branching/Tagging

### List branches

```
git branch
```

### Switch to branch

```
git switch <branch_name>
```

# Merge/Rebase

### Merge branch into current

```
git merge <branch_name>
```

# Stash

### Stash single file

```
git stash push -- <file full path>
```

# Account

## Set up commit email

### Set up commit email globally

```
git config --global user.email <your_email> // Set up email
git config --global user.email // Confirm email
```

### Set up commit email for current repository

```
git config user.email <your_email> // Set up email
git config user.email // Confirm email
```
