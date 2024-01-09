<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [VPS防御](#vps防御)

<!-- /TOC -->
</details>

## VPS防御

```conf
; ssh日志文件
SECURE_LOG = /var/log/secure

将阻止IP写入到hosts.deny
HOSTS_DENY = /etc/hosts.deny

过多久后清除已经禁止的,其中w代表周,d代表天,h代表小时,s代表秒,m代表分钟
PURGE_DENY = 5m

阻止服务名
BLOCK_SERVICE = sshd

允许无效用户(在/etc/passwd未列出)登录失败次数,允许无效用户登录失败的次数.
DENY_THRESHOLD_INVALID = 5

允许普通用户登录失败的次数
DENY_THRESHOLD_VALID = 5

允许root登录失败的次数
DENY_THRESHOLD_ROOT = 5
DENY_THRESHOLD_RESTRICTED = 1
设定 deny host 写入到该资料夹

; 将deny的host或ip纪录到Work_dir中
WORK_DIR = /usr/share/denyhosts/data

; 将DenyHOts启动的pid纪录到LOCK_FILE中,已确保服务正确启动,防止同时启动多个服务.
LOCK_FILE = /var/lock/subsys/denyhosts

; 有效用户登录失败计数归零的时间
AGE_RESET_VALID=1d

; root用户登录失败计数归零的时间
AGE_RESET_ROOT=1d

; 用户的失败登录计数重置为0的时间(/usr/share/denyhosts/data/restricted-usernames)
AGE_RESET_RESTRICTED=5d

; 无效用户登录失败计数归零的时间
AGE_RESET_INVALID=10d

; 自己的日志文件
DAEMON_LOG = /var/log/denyhosts
```