# My MacBook Pro Setup for Web Development

How I configure my MacBookPro for web development at TB. This excludes the configuration steps outlined in codelabs setup.

# System Preferences

## General
- Appearance: Set to dark mode

## Dock & Menu Bar

- Enable: Automatically hide and show the dock
- Make docker smaller (~15%)
- Disable: Show recent applications in Dock
- Enable: Show indicators for open applications
- Battery Bar: Show percentage
- Clock: Use a 24-hour clock
- Spotlight: Disable Show in Menu Bar

## Display

- Nightshift: Sunset to Sunrise

## Trackpad

- Point & Click: Disable Look up and data detectors
- Point & Click: Enable Tap to click

## Keyboard

- Disable Capitalize words automatically
- Disable add period with double-space
- Disable Use smart quotes and dashes
- Change for Double Quotes to "
- Change for Single Quotes to '
- Shortcuts: Spotlight: Disable all (using Alfred, spotlight sucks)
- Shortcuts: Accessibility: Disable all

## Finder

- Sidebar
  - Enable show home user
  - Disable recent tags
- Advanced
  - Enable Show all filename extensions
  - Enable Remove items from the Trash after 30 days
- View
  - As list
  - Show status bar
  - Show path bar

# Homebrew and Apps

## Brew commands

Install Homebrew

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

Update everything

`brew update`

Install GUI apps

```bash
brew install --cask \ 
    1password \ 
    topnotch \
    google-chrome \ 
    firefox \ 
    iterm2 \ 
    visual-studio-code \ 
    docker \ 
    dotnet \ 
    notion \ 
    spotify \ 
    zoom \ 
    postman \ 
    rectangle \ 
    slack \ 
    maccy \ 
    alfred
```



Install terminal apps

```bash
brew install \ 
    wget \ 
    exa \ 
    git \ 
    nvm \ 
    gh
```

## Apps

- 1password - Password manager
- google-chrome - Google chrome web browsing and web dev
  - set default
  - never save passwords
  - enable dark mode
  - add 1password extension
  - add ublock origin
  - add react developer tools
- firefox - Web dev
- iterm2 - Improved terminal
- visual-studio-code - VS Code for dev
- docker - Docker desktop
  - enable docker compose
- dotnet - dotnet cli and sdk
- notion - Note taking app
- spotify - Play something to vibe to while coding
- zoom - For meetings!
- postman - web dev
- rectangle - Window management, lets you snap windows to size
- slack - For human contact
- maccy - Simple clipboard management
- alfred - replaces Spotlight search.
  - Change alfred hotkey to cmd+space
  - Set appearance to modern dark
  - Features → Web Search → Disable everything except google, images, maps, translate, gmail, wiki, youtube, help

## Terminal apps

- wget - alternative to curl
- exa - alternative to ls
- git - for version control
- nvm - node version manager
- gh - github cli

# iTerm2

- iTerm2 → Make iTerm2 Default Term
- Preferences
  - General → Window
    - Disable Native full screen windows
  - Appearance → Windows
    - Enable Hide scrollbars
  - Appearance → Tabs
    - Disable Stretch tabs to fill bar
  - Appearance → Dimming
    - Disable everything
  - Profiles → Window
    - Set Transparancy to 25
    - Set Style Normal - Opens new terminal in default window size and location
    - Set Screen to Screen with Cursor - Opens terminal where your cursor is, useful for multi monitor setup
  - Profiles → Keys → Key Mappings → Presets drop down → Natural Text Editing → Remove
    - Lets you move forwards and backwards using option + arrow or delete words using option + backspace
  - Profiles → General → Working Directory → Advanced Configuration Edit.. → Working Directory for New Split Panes
    - Select Reuse previous session’s directory. Makes it so that when you split a new panel (cmd + D or cmd + shift + D) it will keep the current path that the old pane was in.

## Oh My Zsh

This is an improved zsh shell that supports plugins and themes

[https://ohmyz.sh](https://ohmyz.sh/)

Run `sh -c "$(curl -fsSL [https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh](https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh))"`

Then make sure it’s all up to date

`omz update`

Then force a reload of settings

`source ~/.zshrc`

Add nvm to zsh config

`echo "source $(brew --prefix nvm)/nvm.sh" >> ~/.zshrc`

### Themes and fonts

I’ve been using [Starship](https://starship.rs/) as my theme 🚀 It’s super fast and looks clear

`brew install starship`

Set as default theme for ohmyzsh

`echo 'eval "$(starship init zsh)"' >> ~/.zshrc`

For font I use [Fira Code](https://github.com/tonsky/FiraCode) - a monospaced font family designed by Mozilla for programmers

`brew tap homebrew/cask-fonts`

`brew install --cask font-fira-code-nerd-font`

Using the nerd-font version that contains a more icons and symbols that ohmyzsh and starship uses

Set as new font in iTerm2:

- Preferences → Profile → Text → Font
  - Set to FiraCode Nerd Font Mono
  - Enable Use ligatures

### Plugins

For oh my zsh plugins I use

- docker - autocompletion and aliases for docker cli
- docker-compose - autocompletion and aliases for docker-compose cli
- git - huge collection of aliases

Here is my full .zshrc configuration

```shellscript
export ZSH="$HOME/.oh-my-zsh"

# plugins
plugins=(
    docker
    docker-compose
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)

# nvm
export NVM_DIR="$HOME/.nvm"
source $(brew --prefix nvm)/nvm.sh

# load the plugins
source $ZSH/oh-my-zsh.sh

# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"
  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")
    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc

# easily reload zsh config
alias zshreload="source ~/.zshrc"

# aws-vault for touch bistro
alias sso="aws-vault exec touchbistro --"

# use thefuck but 'typo' instead of 'fuck'
eval $(thefuck --alias wtf)

# use 'fuck' with auto confirmation
alias yolo="wtf --yeah"

# wiring
alias wire="sso tb up -p vcs-bkda-core-deps"


# deletes all branches where remote is gone
clean-branches() {
  git fetch -p

  for branch in $(git branch -vv | grep ': gone]' | awk '{print $1}'); 
    do git branch -D $branch; 
  done
}

# Go back to latest version of master branch after finishing work
alias goup="git checkout master && git pull && clean-branches"

# Merge latest from master to current branch
alias gmm="git fetch origin && git merge origin/master"

# Force quit any apps listening on port
killport() { 
  lsof -i TCP:$1 | grep LISTEN | awk '{print $2}' | xargs kill -9 
}

export PATH="/usr/local/share/dotnet:$PATH"

# use startship theme - needs to be the last command
eval "$(starship init zsh)"

# bun completions
[ -s "/Users/ama/.bun/_bun" ] && source "/Users/ama/.bun/_bun"

# bun
export BUN_INSTALL="$HOME/.bun"
export PATH="$BUN_INSTALL/bin:$PATH"

```

## VS Code Configuration

### System Configs

- Launch VS Code, enter cmd+shift+P and type shell command
- Install ‘code’ command in PATH
- Follow [this guide](https://www.jimbobbennett.io/open-anything-in-vs-code-using-a-macos-quick-action/) to add VS code to quick actions. Then you can right click a folder in finder and open in VS Code via Quick Action

### Extensions and Settings

Open settings.json and add/replace the font settings

`"terminal.integrated.fontFamily": "FiraCode Nerd Font Mono"`

`"editor.fontFamily": "FiraCode Nerd Font Mono"`

`"editor.fontLigatures": true`

My extensions

- One Dark Pro (theme) - dark mode theme that is also available for Visual Studio 2019/2022 so that the color schemes for coding is consistent across different machines
- Babel JavaScript - syntax highlighting for JS
- Better Comments - colorize JS comments for visibility
- C# - OmniSharp C# intellisense
- Diff - Diffs two files
- Docker - for docker files
- DotENV - formatting and syntax for .env files
- EditorConfig for VS Code - VS code will respect .editorconfig files (formatting)
- ESLint - JS linting
- GitHub Pull Requests - Create and review PRs in IDE
- GitLens - See blame annotations in IDE
- Import Cost - Shows you size of a module imported in JS inline
- Prettier - Code formatter for JS, CSS, HTML etc..
- XML Tools - For formatting XML files
- PostgreSQL -Everything postgres related
- SQL Server - Everything sql server related
- Markdown All in One
