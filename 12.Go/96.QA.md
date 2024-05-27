<details>
<summary>点击展开目录</summary>

- [交叉编译](#交叉编译)

</details>


## 交叉编译

在mac上编译linux平台, 命令`CGO_ENABLED=1 GOOS=linux GOARCH=amd64 go build -o xxx`

`CGO_ENABLED=1` 出现的问题:

```log
runtime/cgo
# runtime/cgo
linux_syscall.c:67:13: error: call to undeclared function 'setresgid'; ISO C99 and later do not support implicit function declarations [-Wimplicit-function-declaration]
linux_syscall.c:67:13: note: did you mean 'setregid'?
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/unistd.h:593:6: note: 'setregid' declared here
linux_syscall.c:73:13: error: call to undeclared function 'setresuid'; ISO C99 and later do not support implicit function declarations [-Wimplicit-function-declaration]
linux_syscall.c:73:13: note: did you mean 'setreuid'?
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/unistd.h:595:6: note: 'setreuid' declared here
```

1. mac上需要安装
   * linux: `brew install FiloSottile/musl-cross/musl-cross`
   * win下exe: `brew install mingw-w64`
2. 编译命令调整为`CGO_ENABLED=1 GOOS=linux  GOARCH=amd64  CC=x86_64-linux-musl-gcc  CXX=x86_64-linux-musl-g++ go build -o xxx`
3. 运行的机器上安装`apt-get install -y musl` 或 `yum install -y musl-libc-static`
