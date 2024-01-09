

```Java
@JSONField(format = "yyyyMMdd HH:mm:ss")
private Date                 startEffectDate;
```

为什么大数据量时, 主键推荐使用uuid而不是自增主键?
32位小写16进制数字组成.
mysql自带UUID()函数
```sql
select REPLACE(uuid(), '-', '');
```

