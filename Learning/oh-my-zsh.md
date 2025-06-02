
## Install

* Via wget
  
```bash
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## Selecting Theme

* Rename the default `robbyrussell` to desired theme as required
```bash
nano ~/.zshrc
ZSH_THEME="agnoster"
```

## Plugins

* Install the required autosuggestions and syntax-highlighting plugins as below

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

* Enable the plugins by navigating to `~/.zshrc` and renaming the `plugins=(git)` by `plugins=(git zsh-autosuggestions zsh-syntax-highlighting)`