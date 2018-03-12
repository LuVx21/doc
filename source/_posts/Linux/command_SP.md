

# Mac OS

## tmux

tmux通过开出窗口, 分拆面板, 接管和分离会话
能够让使用者在一个窗口内同时进行多项操作

```
# 创建新的session会话
tmux new -s sessionName
# 重新连接上某一个session
tmux attach -t sessionName
# 杀死某个session
tmux kill-session -t sessionName
# 显示所有会话
tmux ls
```

> `ctrl+b d`从session脱离, 可使用`attach`恢复session
> `ctrl+b c`:创建新的虚拟窗口
> `ctrl+b %`:创建新的虚拟窗口, 纵向
> `ctrl+b w`:查看所有窗口
> `ctrl+b <num>`:切换窗口
> `ctrl+b f`:查找窗口


## paste

可用于将多个文件的内容合并

|命令项|说明|
|:-|:-|
|-s|串行处理而非并行|
|-d|设定间隔符号|

```
paste -d ':' file1 file2
paste -s -d ':' file1 file2
```

## cut

在文件中截取数据, 以每一行为处理对象

|命令项|说明|
|:-|:-|
|-b|按字节分割|
|-c|按字符分割|
|-f|按域分割|
|-d|指定域分隔符|


```shell
# 截取第2-6字符
cut -c2-6 <filename>
# 截取文件2, 4域
cut -f2, 4 <filename>
# 去文件除第3域的所有列:
cut -f3 --complement <filename>
# -d 指定定界符
cat -f2 -d";" <filename>
```
