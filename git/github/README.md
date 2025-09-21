
# GitHub

## Github CLI Login

## SSH Authentication

[Docs](https://en.wikipedia.org/wiki/Ssh-keygen)

### Generate SSH Key

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_[KEY NAME]
```

```bash
ssh-keygen -t ed25519 -b 4096 -f ~/.ssh/id_ed25519_[KEY NAME]
```

Adding github.com to knows hosts

```bash
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

### SSH Permissions

| Object        | Permision                       |
|---------------|---------------------------------|
| Private Key   | chmod 600 ~/.ssh/[KEY NAME]     |
| Public Key    | chmod 644 ~/.ssh/[KEY NAME].pub |
| `.ssh`        | chmod 700 ~/.ssh                |

### Authenticate

```bash
gh auth login
```

Select GitHub.com, SSH, path to generated key

Right click the link to open in brownser, type the CLI code to the web page, accept the key link

```bash
git config --global user.email "$GITUB_EMAIL"
git config --global user.name "Your Name"
```

## Signing Key

## GPG Key
gpg --full-generate-key

gpg --list-secret-keys --keyid-format=long

gpg --armor --export []

git config --global user.signingkey []
git config --global commit.gpgsign true

git config --global user.email "you@example.com"

## Pre Commit Hooks

> [!TIP]
> If you are using config file for the hooks, make sure to specify the config-files on command args to always run the desired/select config

### Commands

```bash
# install 
pip install pre-commit-hooks

# activate
pre-commit install

# update
pre-commit update

# clean
pre-commit clean
```

### Config File

pre-commit-config.yaml

```yaml
repos:
  - repo: [REPOSITORY ADDRESS | str]
    rev:  [REVISION | str]
    hooks:
      - id: [HOOK NAME | str]
      	args: [COMMAND ARGS | list[str]]
```

## GitHub Actions

Defined as `.yaml` file

> [!IMPORTANT]
> Must be in `.github/workflows/` folder to be recognized, its a gha limitation.

### Actions Repository

Calling some action in a remote repository with:

```yaml
# .github/workflows/main.yaml
name: GHA Workflow

on:
  workflow_dispatch:
    inputs:
      environment:
        type: choice
        options:
          - prd
        required: true
        default: prd

jobs:
  build:
    @[BRANCH NAME]
    

### Events

```yaml
on:
  workflow_call
  workflows_dispatch
  pull
  push
  pull_request
```
