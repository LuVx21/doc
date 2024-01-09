<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [ZoneId](#zoneid)
- [Instant](#instant)
- [LocalDate](#localdate)
- [LocalTime](#localtime)
- [LocalDateTime](#localdatetime)
- [Clock](#clock)
- [Period](#period)
- [Duration](#duration)

<!-- /TOC -->
</details>

## ZoneId

时区ID, 用来确定Instant和LocalDateTime互相转换的规则


## Instant

用来表示时间线上的一个点


## LocalDate

表示没有时区的日期, LocalDate是不可变并且线程安全的
yyyy-MM-dd

## LocalTime

表示没有时区的时间, LocalTime是不可变并且线程安全的


## LocalDateTime

表示没有时区的日期时间, LocalDateTime是不可变并且线程安全的

```Java
// 零时的毫秒值
LocalDate.now().atStartOfDay(ZoneId.systemDefault()).toInstant().toEpochMilli();
LocalDateTime.now().with(LocalTime.MIN).toInstant(ZoneOffset.of("+8")).toEpochMilli();
```

## Clock

用于访问当前时刻, 日期, 时间, 用到时区

## Period
日期间隔

Period.of
`PnYnMnD`

## Duration
秒或纳秒时间间隔, 适合处理较短的时间
`PnDTnHnMn.nS`
Duration.parse("P1DT1H10M10.5S");

用秒和纳秒表示时间的数量
java.time.Duration

ChronoUnit

