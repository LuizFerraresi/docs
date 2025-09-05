
# GitHub

## Github CLI Login

### Generate SSH Key Pair

```bash
GITHUB_EMAIL="your@email.com"
```

```bash
ssh-keygen -t ed25519 -C "$GITHUB_EMAIL" -f ~/.ssh/id_rsa_github
```

```bash
eval "$(ssh-agent -s)"
```

```bash
ssh-add ~/.ssh/id_rsa_github
```

```bash
echo "\n  ###  GITHUB SSH PUBLIC KEY  ###"
echo "  PASTE ON GITHUB DEPLOY KEY\n"
cat ~/.ssh/id_rsa_github.pub
```

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
