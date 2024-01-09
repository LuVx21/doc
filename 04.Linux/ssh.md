<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [免密登录](#免密登录)
- [远程执行命令](#远程执行命令)

<!-- /TOC -->
</details>

## 免密登录

`ssh-keygen -t rsa`

RSA也是默认的加密类型. 所以你也可以只输入`ssh-keygen`.

默认的RSA长度是2048位.

如果你非常注重安全, 那么可以指定4096位的长度:

`ssh-keygen -b 4096 -t rsa`

可以使用一个密码加密秘钥

将公钥上传到你的服务器

`ssh-copy-id username@remote-server`

ssh config

`~/.ssh/config`文件:
```
Host luvx
    HostName 192.168.1.1
    User luvx
    IdentityFile ~/.ssh/id_rsa
    Port 22
```

## 远程执行命令

简单命令

```bash
ssh root@luvx "cd /home/admin/app-run/;pwd;bash ./jar-exec.sh restart app-executable.jar"
```

脚本中

```bash
#!/bin/bash
ssh root@luvx > /dev/null 2>&1 << eof
cd /root/code
touch foo.txt
exit
eof
```
