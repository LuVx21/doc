<details>
<summary>点击展开目录</summary>
<!-- TOC -->
<!-- /TOC -->
</details>


**删除对windows环境变量的引用**

```bash
export PATH=`echo $PATH | sed 's/:\/mnt\/[a-z]\/[^:]*//g'`
```

**windows中访问wsl磁盘**

```bash
C:\Users\Administrator\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs
```

## 阅读

* [wsl文档](https://docs.microsoft.com/zh-cn/windows/wsl/)
* [WSL快速设置](https://mechanical-consciousness.com/2018/10/19/wsl-quick-setup-zh.html)
