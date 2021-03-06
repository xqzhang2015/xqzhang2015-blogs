---
title: "iTerm2 + zsh"
description: ""
tags: [
    "tool",
]
date: "2018-09-11 11:42:31"
lastmod: "2018-09-12 11:42:57"
categories: [
	"tool",
    "linux",
    "C++",
]
---

## iTerm2 + zsh

### Install iTerm2

```shell
brew cask install iterm2
```
or [Download](http://www.iterm2.com/downloads.html) and install iTerm2

* Set solarized-dark color

```shell
Preferences > Profiles > Colors:
Color Presets > Solarized Dark
```

### Install powerline/fonts

powerline/fonts: Patched fonts for Powerline users. Here we install it to support agnoster.zsh-theme.

[powerline/fonts git repo](https://github.com/powerline/fonts)

* Install by git-clone and run install.sh

```shell
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit (optional)
cd ..
rm -rf fonts
```

### iTerm2 preference configuration

![This is an image in `static/image` folder.](/image/iterm2_preferences.png)

To test if your terminal and font support agnoster.zsh-theme, check that all the necessary characters are supported by copying the following command to your terminal: 
```shell
echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
```
### Install zsh

[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) is a framework for managing your zsh configuration.

Includes 200+ optional __plugins__, over 140 __themes__ to spice up your morning, and an auto-update tool.

#### via curl
```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### via wget
```shell
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

#### For non-sudoer, only change a specific user
```shell
export SHELL=/usr/bin/zsh
exec /usr/bin/zsh --login
```

#### zsh conf: .zshrc

* Update zsh theme in .zshrc

```shell
# ZSH_THEME="robbyrussell"
ZSH_THEME="agnoster"

# ...

plugins=(
  git
)

# ...

# optionally set DEFAULT_USER in ~/.zshrc to your regular username to hide the “user@hostname” info when you’re logged in as yourself on your local machine.
# DEFAULT_USER=$(whoami)
DEFAULT_USER=xqzhang
```

* zsh folder structure

```shell
ls ~/.oh-my-zsh/
CONTRIBUTING.md LICENSE.txt     README.md       cache           custom          lib             log             oh-my-zsh.sh    plugins         templates       themes          tools

ls ~/.oh-my-zsh/themes/
agnoster.zsh-theme robbyrussell.zsh-theme ...
```

### Zsh custom plugins

#### zsh-autosuggestions

[Fish](http://fishshell.com/)-like autosuggestions for zsh. It suggests commands as you type, based on command history.


1. Clone this repository into $ZSH_CUSTOM/plugins (by default .oh-my-zsh/custom/plugins)

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

2. Add the plugin to the list of plugins for Oh My Zsh to load (inside .zshrc):

```shell
plugins=(
  zsh-autosuggestions
)
```

3. Start a new terminal session.


#### Other plugins


