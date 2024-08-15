# Prompt Config

Instruction to config and setup a terminal.

This file will show how to setup a zsh prompt, with oh-my-zsh and antigen 
as package managers with spaceship theme.

This project config files organization was design thinking to keep the general
config on ``.zshrc`` and the specific configs on each manager file.


## Dependencies

Prompt:
- [zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)

Prompt Configuration Managers: 
- [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh)
- [antigen](https://github.com/zsh-users/antigen)

Prompt Theme:
- [spaceship](https://github.com/spaceship-prompt/spaceship-prompt)

Package Manager:
- [Brew](https://brew.sh/)

Resources: 
- [pyenv](https://github.com/pyenv/pyenv)
- [direnv]()
- [awscli]()
- [kubectl](https://kubernetes.io/pt-br/docs/tasks/tools/install-kubectl-linux/)
- [tfenv]()
- [tgswitch]()


## Install

### zsh

```bash
sudo apt install zsh -y
sudo chsh -s $(which zsh)  # make it default prompt
```

### oh-my-zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### antigen

```bash
curl -L git.io/antigen > antigen.zsh
```

* spaceship will be installed using antigen

### brew

```bash
sudo apt-get install build-essential procps curl file git
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> ```bash
> # install package
> brew install < PACKAGE >
>
> # update package
> brew upgrade < PACKAGE >
>
> # uninstall package
> brew uninstall < PACKAGE >
>
> # update package manager
> brew update
> ```

### direnv

```bash
curl -sfL https://direnv.net/install.sh | bash
```

Fetch the binary, ``chmod +x direnv`` and add it to your path

To export the environemnt variables, you can declare them on ``.envrc`` file with ``export`` as you 
do in your prompt or you can add a reference to an env file with ``dotenv_if_exists .env``

To enable direnv or export the variables, run ``direnv allow .``

### pyenv

```bash
brew install pyenv
```

### kubectl

```bash
brew install kubectl
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
eval "$($(brew --prefix)/bin/brew shellenv)"

# Pyenv 
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Direnv
eval "$(direnv hook zsh)"

# Aliases

# Extras
EOF
```

The values added with ``cat > ~/.zshrc <EOF`` will be added after ``# Extras``

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

### .spaceshiprc

```bash
cat > ~/.spaceshiprc.zsh <<EOF

# Display time
SPACESHIP_TIME_SHOW=true

# Display username always
SPACESHIP_USER_SHOW=always

# Do not truncate path in repos
SPACESHIP_DIR_TRUNC_REPO=false

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

SPACESHIP_PROMPT_ORDER=(
  exit_code      # Exit code section
  exec_time      # Execution time
)

EOF
```

You can chenge your spaceship config file path with ``export SPACESHIP_CONFIG``

If your prompt line is with bad format, check your ``SPACESHIP_PROMPT_ORDER`` variable to see if
there is no miss configuration
