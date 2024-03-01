<details>
<summary>点击展开目录</summary>
<!-- TOC -->
<!-- /TOC -->
</details>


prettyping
diff-so-fancy

```bash
# install Command Line Tools
xcode-select --install

# install software manager homebrew(maybe very slowly - you can use cellular)
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# change mirror to tuna
cd "$(brew --repo)"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

# install oh-my-zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-completions $ZSH_CUSTOM/plugins/zsh-completions
git clone https://github.com/zsh-users/zsh-history-substring-search $ZSH_CUSTOM/plugins/zsh-history-substring-search
# configure system set in ~/.zshrc
vim ~/.zshrc

# plugins=(
#     git
#     docker
#     zsh-syntax-highlighting
#     zsh-autosuggestions
#     zsh-completions
#     history-substring-search
# )

# no update when use brew
export HOMEBREW_NO_AUTO_UPDATE=true
```

https://wyydsb.xin/other/terminal.html
https://escapelife.github.io/posts/1c151aac.html


## brew


```bash
# git://mirrors.ustc.edu.cn/brew.git
# git://mirrors.ustc.edu.cn/homebrew-core.git
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git
```

```bash
brew tap buo/cask-upgrade
brew tap homebrew/cask-fonts

```


```bash
brew install tree wget tmux cloc axel lftp \
abhimanyu003/sttr/sttr \ # 文本转换
httpie \ # curl替换
fd \ # find 替换
lrzsz \ # rz sz 命令
yajl \ #
jq \ # json
# md5sha1sum \ #
lnav \
ccat bat \# cat命令增强
exa lsd rm-improved dust \
grpcui \
s/microsoft-edge-dev \
    android-file-transfer android-platform-tools \
    font-hack-nerd-font \
    intellij-idea datagrip goland visual-studio-code utools docker \
    offset-explorer another-red


brew install qlcolorcode quicklook-json qlimagesize quicklookase qlvideo webpquicklook betterzip
# 预览位置类型文本, 预览 markdown
brew install --cask iterm2 snipaste \
    wechat homebrew/cask-versionis-desktop-manager \
    wpsoffice-cn iina \
    qlmarkdown qlimagesize qlcolorcode \
    qlstephen qlvideo quicklook-json quicklookase webpquicklook \
    grpcurl
# suspicious-package apparency
```

> [quick-look-plugins](https://github.com/sindresorhus/quick-look-plugins)


```bash
cd /Applications/Beyond\ Compare.app/Contents/MacOS
mv BCompare BCompare.real
cat > BCompare << EOF
#!/bin/bash
rm "/Users/$(whoami)/Library/Application Support/Beyond Compare/registry.dat"
# "`dirname "$0"`"/BCompare.real $@
# $(pwd)/BCompare.real
/Applications/Beyond\ Compare.app/Contents/MacOS/BCompare.real
EOF
chmod 755 BCompare
```


[mac上用的cli/app/config](https://github.com/nikitavoloboev/config)
