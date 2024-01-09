
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [配置使用](#配置使用)
- [设置请求头](#设置请求头)

<!-- /TOC -->
</details>

## 配置使用

```Java
@Configuration
public class RestTemplateConfig {
    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder) {
        return builder
                .setReadTimeout(30 * 1000)
                .build();
    }
}
```

```Java
@Configuration
public class RestTemplateConfig {
    @Bean
    public RestTemplate restTemplate() {
        HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
        factory.setReadTimeout(30 * 1000);

        return new RestTemplate(factory);
    }
}
```

## 设置请求头

```Java
HttpHeaders headers = new HttpHeaders();
List<String> cookies = new ArrayList<>();
cookies.add("JSESSIONID=" + Strings.nullToEmpty(jsessionId));
cookies.add("token=" + Strings.nullToEmpty(token));
headers.put(HttpHeaders.COOKIE,cookies);
HttpEntity request = new HttpEntity(null, headers);
ResponseEntity<String> response = restTemplate.postForEntity(url, request, String.class);
```


post表单
```Java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
MultiValueMap<String, String> map = new LinkedMultiValueMap<String, String>();
map.add("title", title);
map.add("desc", desc);
map.add("userid", toUserId);
HttpEntity<MultiValueMap<String, String>> request = new HttpEntity<MultiValueMap<String, String>>(map, headers);
ResponseEntity<String> response = restTemplate.postForEntity(url, request, String.class);
```


post json
```Java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);
headers.setAccept(Lists.newArrayList(MediaType.APPLICATION_JSON));
HttpEntity<String> entity = new HttpEntity<String>(requestJson, headers);
ResponseEntity<String> resp = restTemplate.postForEntity(url,entity,String.class);
```

5.0 以上, 官方标注了更推荐使用非阻塞的响应式 HTTP 请求处理类 `org.springframework.web.reactive.client.WebClient` 来替代 RestTemplate

可阅读[文章](https://juejin.im/post/5cd680eff265da037b612e28)


