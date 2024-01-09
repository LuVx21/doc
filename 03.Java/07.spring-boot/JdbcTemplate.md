<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [jdbc映射为对象](#jdbc映射为对象)

<!-- /TOC -->
</details>



## jdbc映射为对象

```Java
RowMapper<User> rowMapper = new BeanPropertyRowMapper<>(User.class);
String sql = "select * from user where id = ?";
List<User> ps = jdbcTemplate.query(sql, new Object[]{id}, rowMapper);
```

