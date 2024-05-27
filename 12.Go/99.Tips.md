<details>
<summary>点击展开目录</summary>

- [私有仓库](#私有仓库)
- [defer](#defer)

</details>

* [jupyter go 内核](https://github.com/gopherdata/gophernotes)
* [代理](https://github.com/goproxy/goproxy.cn/)


## 私有仓库

```bash
git config --global url."ssh://git@github.com/".insteadof "https://github.com/"
go env -w GOPRIVATE=github.com
go env -w GONOSUMDB=github.com/LuVx21/*
```

## defer


```go
import "testing"

func around(t *testing.T, msg string) func(t *testing.T) {
    t.Log(msg + ": 测试前")
    return func(t *testing.T) {
        t.Log(msg + ": 测试后")
    }
}

func Test_00(t *testing.T) {
    defer around(t, "Test_00")(t)

    t.Log("执行...")
}
```