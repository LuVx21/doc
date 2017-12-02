---
title: Mac命令
date: 2017-06-06
tags:
- Mac
- Linux
---
<!-- TOC -->

- [Mac命令](#mac命令)
    - [Brew](#brew)
    - [tree](#tree)

<!-- /TOC -->

# Mac命令

-- 显示隐藏文件
defaults write com.apple.finder AppleShowAllFiles -bool true
-- 不显示隐藏文件
defaults write com.apple.finder AppleShowAllFiles -bool false

使用chflags命令隐藏文件或文件夹

1. chflags hidden ~/Desktop/macx


hidden-nohidden


创建有密码保护的压缩文件


1. zip -e protected.zip ~/Desktop/macx.txt

4.在Finder标题栏显示完整路径
在“终端”中输入下面的命令：
defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES
killall Finder


## Brew

brew install tree


## tree
中文解释：tree
功能说明：以树状图列出目录的内容。
语　　法：tree [-aACdDfFgilnNpqstux][-I <范本样式>][-P <范本样式>][目录...]
补充说明：执行tree指令，它会列出指定目录下的所有文件，包括子目录里的文件。
参　　数：
  -a   显示所有文件和目录。
  -A   使用ASNI绘图字符显示树状图而非以ASCII字符组合。
  -C   在文件和目录清单加上色彩，便于区分各种类型。
  -d   显示目录名称而非内容。
  -D   列出文件或目录的更改时间。
  -f   在每个文件或目录之前，显示完整的相对路径名称。
  -F   在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*","/","=","@","|"号。
  -g   列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码。
  -i   不以阶梯状列出文件或目录名称。
  -I<范本样式>   不显示符合范本样式的文件或目录名称。
  -l   如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录。
  -n   不在文件和目录清单加上色彩。
  -N   直接列出文件和目录名称，包括控制字符。
  -p   列出权限标示。
  -P<范本样式>   只显示符合范本样式的文件或目录名称。
  -q   用"?"号取代控制字符，列出文件和目录名称。
  -s   列出文件或目录大小。
  -t   用文件和目录的更改时间排序。
  -u   列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码。
  -x   将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该子目录予以排除在寻找范围外。


tree -L 1 -d:显示当前目录下一级目录

