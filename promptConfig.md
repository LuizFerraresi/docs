# Prompt Config

Instructions to config and setup a terminal.


## Dependencies

Prompt: [zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)

Extensions: [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh), [antigen](https://github.com/zsh-users/antigen), [spaceship](https://github.com/spaceship-prompt/spaceship-prompt)

Package Manager: [Brew](https://brew.sh/)

Resources: [pyenv](https://github.com/pyenv/pyenv)


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

### pyenv
```bash
brew install pyenv
```


## Config Files

### .zshrc

```bash
cat > ~/.zshrc <<EOF

# Path's
export PATH=$HOME/bin:/usr/local/bin:$PATH

# Plugins
plugins=(docker)

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
antigen bundle git
antigen bundle github
antigen bundle ptavares/zsh-direnv
antigen bundle docker-compose
antigen bundle zsh-users/zsh-completions
antigen bundle zsh-users/zsh-autosuggestions
antigen bundle zsh-users/zsh-syntax-highlighting

# Python Pluggins
antigen bundle pip
antigen bundle python
antigen bundle virtualenv
antigen bundle darvid/zsh-poetry

# Spaceship Config
antigen theme spaceship-prompt/spaceship-prompt
antigen bundle "spaceship-prompt/spaceship-ember"
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

# Add custom Ember section
# See: https://github.com/spaceship-prompt/spaceship-ember
spaceship add ember

# Add a custom vi-mode section to the prompt
# See: https://github.com/spaceship-prompt/spaceship-vi-mode
spaceship add --before char vi_mode

# Extras
spaceship add kubectl
EOF
```

You can chenge your spaceship config file path with ``export SPACESHIP_CONFIG``
