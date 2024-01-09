<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [HTTP Client](#http-client)
- [String](#string)
- [移除Java EE 部分模块](#移除java-ee-部分模块)
- [ZGC](#zgc)
- [废弃Nashorn JavaScript Engine](#废弃nashorn-javascript-engine)

<!-- /TOC -->
</details>

## HTTP Client

在Java9, Java10 开始孵化, 11中成为正式模块, 命名为`java.net.http`模块

```Java
public static void sync() throws URISyntaxException, IOException, InterruptedException {
    HttpClient client = HttpClient.newHttpClient();
    HttpRequest request = HttpRequest.newBuilder()
            .uri(new URI("http://openjdk.java.net/"))
            .GET()
            .build();

    HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
    System.out.println(response.headers());
    System.out.println(response.statusCode());
    System.out.println(response.body());
}

public static void async() throws URISyntaxException {
    HttpClient client = HttpClient.newHttpClient();
    HttpRequest request = HttpRequest.newBuilder()
            .uri(new URI("http://openjdk.java.net/"))
            .GET()
            .build();

    CompletableFuture<HttpResponse<String>> response = client.sendAsync(request, HttpResponse.BodyHandlers.ofString());
    response
            .whenComplete((resp, t) -> {
                if (t != null) {
                    t.printStackTrace();
                } else {
                    System.out.println(resp.body());
                    System.out.println(resp.statusCode());
                }
            })
            .join();
}
```

## String

```Java
// 是否为空白字符串
public boolean isBlank()
//去除首尾空格
public String strip()
//去除首部空格
public String stripLeading()
//去除尾部空格
public String stripTrailing()
// 重复字符串
public String repeat(int count)
// 返回由换行符分隔的字符串集合
public Stream<String> lines()
```

## 移除Java EE 部分模块

## ZGC

[一文读懂Java 11的ZGC为何如此高效](https://mp.weixin.qq.com/s/nAjPKSj6rqB_eaqWtoJsgw)

## 废弃Nashorn JavaScript Engine

废除Nashorn JavaScript 引擎, 在后续版本准备移除掉, 有需要的可以考虑使用GraalVM
