- [Branching/Tagging](#branchingtagging)
	- [List branches](#list-branches)
- [Account](#account)
	- [Set up commit email](#set-up-commit-email)

# Branching/Tagging

## List branches

```console
git branch
```

Switch to branch

```console
git switch <branch>
```

# Account

## Set up commit email

Set up commit email globally

```console
git config --global user.email <your_email> // Set up email
git config --global user.email // Confirm email
```

Set up commit email for current repository

```console
git config user.email <your_email> // Set up email
git config user.email // Confirm email
```