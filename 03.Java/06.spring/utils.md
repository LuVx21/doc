<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [集合](#集合)

<!-- /TOC -->
</details>


## 集合

```Java
MultiValueMap<String, String> map = new LinkedMultiValueMap<>();
map.add("title", "foobar");
map.add("desc", "foobar1");
map.add("userid", "foobar2");
map.add("title", "foobar0");
System.out.println(map);
```
