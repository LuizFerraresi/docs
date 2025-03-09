# Prompt Config

Instruction to config and setup a terminal.

This file will show how to setup a zsh prompt, with oh-my-zsh and antigen 
as package managers with spaceship theme.

This project config files organization was design thinking to keep the general
config on ``.zshrc`` and the specific configs on each manager file.

## Dependencies

Prompt:
[zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)

Configuration Managers: 
[oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh),
[antigen](https://github.com/zsh-users/antigen)

Theme:
[spaceship](https://github.com/spaceship-prompt/spaceship-prompt)

Package Manager:
[Brew](https://brew.sh/)

Resources:
[pyenv](https://github.com/pyenv/pyenv),
[direnv](https://direnv.net/),
[awscli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html),
[ghcli](https://cli.github.com/),
[kubectl](https://kubernetes.io/pt-br/docs/tasks/tools/install-kubectl-linux/),
[kubent](https://github.com/doitintl/kube-no-trouble),
[tfenv](https://github.com/tfutils/tfenv),
[tgswitch](https://github.com/warrensbox/tgswitch)

CLI Tools (Install using `apt`):
[jq](https://jqlang.github.io/jq/),
[yq](https://github.com/mikefarah/yq),
[htop](https://htop.dev/)

Tools:
[solaar](https://pwr-solaar.github.io/Solaar/index)
[flameshot](https://flameshot.org/)

## Install

> [!IMPORTANT]
> Always try to install the packages using `apt`

### zsh

```bash
sudo apt install zsh -y
```

Make it default prompt:

```bash
sudo chsh -s $(which zsh)
```

### oh-my-zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Antigen

```bash
curl -L git.io/antigen > ~/antigen.zsh
```

* spaceship will be installed using antigen

### Homebrew

```bash
sudo apt-get install build-essential procps curl file git
```

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Add Brew to `$PATH`:

```bash
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
```

Validate Install

```bash
brew --version
```

> ```bash
> # install package
> brew install [PACKAGE]
>
> # update package
> brew upgrade [PACKAGE]
>
> # uninstall package
> brew uninstall [PACKAGE]
>
> # update package manager
> brew update
> ```

### direnv

```bash
sudo apt-get install direnv
```

Validate Install

```bash
direnv --version
```

To export the environemnt variables, you can declare them on ``.envrc`` file with 
``export`` as you do in your prompt or you can add a reference to an env file with
``dotenv_if_exists .env``

To enable direnv or export the variables, run ``direnv allow .``

### pyenv

```bash
curl -fsSL https://pyenv.run | bash
```

```bash
pyenv --version
```

```bash
# download python version
pyenv install [PYTHON VERSION]

# set global python version
pyenv global [PYTHON VERSION]

# set local python version
pyenv local [PYTHON VERSION]
```

* This will create a ``.python-version`` file in our directory, so every time we enter
this project, the terminal will select the python version automatically.

* virtualenv's managed by pyenv will be stored as virtualenvwrapper, so all the venv's
will be togheter in a folder managed by this extension.

```bash
# create virtualenv
virtualenv .venv

# activate virtualenv
source .venv/bin/activate 
```

### AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Validate Install

```bash
aws --version
```

### GitHub CLI

```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
	&& sudo mkdir -p -m 755 /etc/apt/keyrings \
	&& wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& sudo apt update \
	&& sudo apt install gh -y
```

Validate Install

```bash
gh --version
```

### kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Validate Install

```bash
kubectl --version
```

### kubent

```bash
sh -c "$(curl -sSL https://git.io/install-kubent)"
```

Validate Install

```bash
kubent --version
```

### tfenv

```bash
git clone --depth=1 https://github.com/tfutils/tfenv.git ~/.tfenv
```

Add ~/.tfenv/bin to your $PATH any way you like

echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bash_profile

Validate Install

```bash
tfenv --version
```

```bash
# download terraform version
tfenv install [TERRAFORM VERSION | latest]

# set terraform version
tfenv use [TERRAFORM VERSION]
```

### tgswitch

```bash
curl -L https://raw.githubusercontent.com/warrensbox/tgswitch/release/install.sh | sudo bash
```

Validate Install

```bash
tgswitch --version
```

## Config Files

### .zshrc

```bash
cat > ~/.zshrc <<EOF

# Path's
export PATH=$HOME/bin:/usr/local/bin:$PATH

# Antigen Plugin Manager
source ~/antigen.zsh
antigen init ~/.antigenrc

# Brew
# eval "$($(brew --prefix)/bin/brew shellenv)"

# Pyenv 
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Direnv
eval "$(direnv hook zsh)"

# Set default user
DEFAULT_USER=$(whoami)

# Aliases
alias ohmyzsh="nano ~/zshrc"
alias k='kubectl'

# Function Calls

aws-login () {
  # Set AWS_PROFILE varibale and make sso login on desired account

  aws sso login --profile "$1"
  export AWS_PROFILE="$1"
  echo "Aws Account SSO $1"
}

#### Other's - Not Mapped ####

echo "zshrc config completed"

EOF
```

More config values can be appended with ``cat > ~/.zshrc <EOF``

### .antigenrc

```bash
cat > ~/.antigenrc <<EOF

# Load oh-my-zsh library 
antigen use oh-my-zsh

# Antigen Bundles
antigen bundle zsh-users/zsh-completions
antigen bundle zsh-users/zsh-autosuggestions
antigen bundle zsh-users/zsh-syntax-highlighting

# Spaceship Config
antigen theme spaceship-prompt/spaceship-prompt
antigen bundle spaceship-prompt/spaceship-vi-mode@main

antigen apply

EOF
```

> [!TIP]
> 'Antigen: Another process in running.' on shell startup
>
> ```bash
> rm ~/.antigen/.lock
> ```

### .spaceshiprc

```bash
cat > ~/.spaceshiprc.zsh <<EOF

# Display time
SPACESHIP_TIME_SHOW=true

# Display username always
SPACESHIP_USER_SHOW=always

# Do not truncate path in repos
SPACESHIP_DIR_TRUNC_REPO=true

SPACESHIP_HOST_SHOW="always"

# Display kubectl info
SPACESHIP_KUBECTL_SHOW=true
SPACESHIP_KUBECTL_VERSION_SHOW=false
SPACESHIP_KUBECTL_CONTEXT_SHOW=true

SPACESHIP_EXIT_CODE_SHOW=true

SPACESHIP_PROMPT_ORDER=(
  async          # Async jobs indicator
  time           # Time stamps section
  user           # Username section
  sudo           # Sudo indicator
  venv           # virtualenv section
  dir            # Current directory section
  host           # Hostname section
  git            # Git section (git_branch + git_status)
  python         # Python section
  docker_compose # Docker section
  aws            # Amazon Web Services section
  kubectl        # Kubectl context section
  terraform      # Terraform workspace section
  gnu_screen     # GNU Screen section
  line_sep       # Line break
  char           # Prompt character
)

SPACESHIP_RPROMPT_ORDER=(
  exit_code      # Exit code section
  exec_time      # Execution time
)

EOF
```

You can chenge your spaceship config file path with ``export SPACESHIP_CONFIG``

If your prompt line is with bad format, check your ``SPACESHIP_PROMPT_ORDER`` variable to see if
there is no miss configuration

## Reload Prompt

```bash
source ~/.zshrc
```

## Install Linux Terminator

[Documentation](https://github.com/gnome-terminator/terminator/blob/master/INSTALL.md)

```bash
sudo add-apt-repository ppa:mattrose/terminator
sudo apt-get update
sudo apt install terminator
```

## Tools

### Solaar

```bash
sudo apt-get install solaar
```

Validate Install

```bash
solaar --version
```

### Flameshot

```bash
sudo apt-get install flameshot
```

Validate Install

```bash
flameshot --version
```

Take a snapshot from terminal

```bash
flameshot gui
```
