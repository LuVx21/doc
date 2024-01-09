
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [查看端口占用](#查看端口占用)

<!-- /TOC -->
</details>

cat /proc/meminfo


默认为0
echo 1 > /proc/sys/vm/drop_caches:表示清除pagecache.
echo 2 > /proc/sys/vm/drop_caches:表示清除回收slab分配器中的对象(包括目录项缓存和inode缓存).slab分配器是内核中管理内存的一种机制, 其中很多缓存数据实现都是用的pagecache.
echo 3 > /proc/sys/vm/drop_caches:表示清除pagecache和slab分配器中的缓存对象.


## 查看端口占用

netstat -aon | findstr "8080"
tasklist | findstr "9264"


