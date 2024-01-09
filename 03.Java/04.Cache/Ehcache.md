@Cacheable: 加入缓存
@CachePut: 更新缓存
@CacheEvict: 删除缓存

```xml
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://www.ehcache.org/ehcache.xsd"
         updateCheck="false">
    <!-- diskStore: ehcache其实是支持内存+磁盘+堆外内存, 几个层级的缓存 -->
    <diskStore path="java.io.tmpdir/Tmp_EhCache"/>

    <!--
    eternal: 如果设置为true的话, 那么timeout就没有效果, 缓存就会一直存在, 一般默认就是false
    maxElementsInMemory: 内存中可以缓存多少个缓存条目
    overflowToDisk: 如果内存不够的时候, 是否溢出到磁盘
    diskPersistent: 是否启用磁盘持久化的机制, 在jvm崩溃的时候和重启之间, 不用
    timeToIdleSeconds: 对象最大的闲置的时间, 如果超出闲置的时间, 可能就会过期
    timeToLiveSeconds: 对象最多存活的时间, 我们这里也不用, 超过这个时间, 缓存就过期
    memoryStoreEvictionPolicy: 当缓存数量达到了最大的指定条目数的时候, 需要采用一定的算法, 从缓存中清除一批数据, LRU, 最近最少使用算法, 最近一段时间内, 最少使用的那些数据, 就被干掉了
    -->
    <defaultCache
            eternal="false"
            maxElementsInMemory="1000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="300"
            timeToLiveSeconds="0"
            memoryStoreEvictionPolicy="LRU"/>

    <cache
            name="local"
            eternal="false"
            maxElementsInMemory="1000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="300"
            timeToLiveSeconds="0"
            memoryStoreEvictionPolicy="LRU">
    </cache>
</ehcache>
```