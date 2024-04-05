---
title: Mac命令
date: 2017-06-06
tags:
- Mac
- Linux
---
<details>
<summary>点击展开目录</summary>

- [Mac命令](#mac命令)
  - [.DS\_store](#ds_store)
  - [dock](#dock)
  - [Mission Control](#mission-control)
  - [Launchpad](#launchpad)
  - [tree](#tree)
- [配置](#配置)
  - [Mission Control](#mission-control-1)

</details>

# Mac命令

隐藏文件
```bash
# 显示
defaults write com.apple.finder AppleShowAllFiles -bool true
# 不显示
defaults write com.apple.finder AppleShowAllFiles -bool false
```

## .DS_store

禁止.DS_store生成:

```bash
find . -name '.DS_Store' -type f -delete
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
# 恢复.DS_store生成
defaults delete com.apple.desktopservices DSDontWriteNetworkStores
```
[来源](https://support.apple.com/zh-cn/102064)

## dock

Dock 隐藏显示
```shell
# 时间设为最短,秒
defaults write com.apple.dock autohide-delay -int 0
# 恢复
defaults delete com.apple.Dock autohide-delay
```

Dock透明度
```shell
# 透明
defaults write com.apple.dock hide-mirror -bool true
# 非透明
defaults write com.apple.dock hide-mirror -bool false
```

## Mission Control


## Launchpad

```bash
defaults write com.apple.dock springboard-rows -int 6 # 每列数量
defaults write com.apple.dock springboard-columns -int 8 # 每行数量
killall Dock
# 恢复
defaults write com.apple.dock springboard-rows Default
defaults write com.apple.dock springboard-columns Default
killall Dock
```

```bash
defaults write com.apple.dock ResetLaunchPad -bool TRUE;
killall Dock
```

```bash
# 关闭Launchpad动画效果
defaults write com.apple.dock springboard-show-duration -int 0
defaults write com.apple.dock springboard-hide-duration -int 0
killall Dock
# 恢复
defaults delete com.apple.dock springboard-show-duration
defaults delete com.apple.dock springboard-hide-duration
killall Dock
```

**隐藏文件或文件夹**

```bash
chflags hidden ~/Desktop/macx
hidden-nohidden
```

**创建有密码保护的压缩文件**


```bash
zip -e protected.zip ~/Desktop/macx.txt
```

**在Finder标题栏显示完整路径**

```bash
defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES
killall Finder
```

预览时可选择文本

defaults write com.apple.finder QLEnableTextSelection -bool TRUE; killall Finder

## tree
中文解释: tree
功能说明: 以树状图列出目录的内容.
语　　法: tree [-aACdDfFgilnNpqstux][-I <范本样式>][-P <范本样式>][目录...]
补充说明: 执行tree指令, 它会列出指定目录下的所有文件, 包括子目录里的文件.
参　　数:
  -a   显示所有文件和目录.
  -A   使用ASNI绘图字符显示树状图而非以ASCII字符组合.
  -C   在文件和目录清单加上色彩, 便于区分各种类型.
  -d   显示目录名称而非内容.
  -D   列出文件或目录的更改时间.
  -f   在每个文件或目录之前, 显示完整的相对路径名称.
  -F   在执行文件, 目录, Socket, 符号连接, 管道名称名称, 各自加上"*","/","=","@","|"号.
  -g   列出文件或目录的所属群组名称, 没有对应的名称时, 则显示群组识别码.
  -i   不以阶梯状列出文件或目录名称.
  -I<范本样式>   不显示符合范本样式的文件或目录名称.
  -l   如遇到性质为符号连接的目录, 直接列出该连接所指向的原始目录.
  -n   不在文件和目录清单加上色彩.
  -N   直接列出文件和目录名称, 包括控制字符.
  -p   列出权限标示.
  -P<范本样式>   只显示符合范本样式的文件或目录名称.
  -q   用"?"号取代控制字符, 列出文件和目录名称.
  -s   列出文件或目录大小.
  -t   用文件和目录的更改时间排序.
  -u   列出文件或目录的拥有者名称, 没有对应的名称时, 则显示用户识别码.
  -x   将范围局限在现行的文件系统中, 若指定目录下的某些子目录, 其存放于另一个文件系统上, 则将该子目录予以排除在寻找范围外.


tree -L 1 -d:显示当前目录下一级目录



# 配置

java安装路径:

`/Library/Java/JavaVirtualMachines/jdk1.8.0_151.jdk/Contents/Home`

## Mission Control

```shell
# 系统默认的为0.2到0.25. 数字越大, 越慢. 如果想要取消动画效果, 改成0dock的动画效果设置:
defaults write com.apple.dock expose-animation-duration -int 0
# defaults write com.apple.dock expose-animation-duration -float 0.1
killall Dock
defaults delete com.apple.dock expose-animation-duration
killall Dock
```

defaults write com.apple.dock autohide-time-modifier -float 0.12
killall Dock

同样的道理.
如果想要改回系统默认的设置:

defaults delete com.apple.dock expose-animation-duration
killall Dock



delete
rm ~/Library/Application\ Support/Beyond\ Compare/registry.dat



允许任何来源的应用:
```shell
sudo spctl --master-disable
```

打开应用
open -a Safari

退出应用
osascript -e 'quit app"Safari"'