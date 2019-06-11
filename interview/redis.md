[TOC]

# 数据类型

字符串String

哈希表Hash

列表List

集合Set

有序集合 SortSet



# **redis过期策略**

生存时间可以通过使用 DEL 命令来删除整个 key 来移除，或者被 SET 和 GETSET 命令覆写(overwrite)

对key对应的value值修改，或者对key值修改不影响过期时间

**过期删除**

被动删除：

​	当读/写一个已经过期的key时，会触发惰性删除策略，直接删除掉这个过期key
主动删除：

​	由于惰性删除策略无法保证冷数据被及时删掉，所以Redis会定期主动淘汰一批已过期的key
​	当前已用内存超过maxmemory限定时，触发主动清理策略

	allkeys-lru ： 删除lru算法的key
	volatile-random：随机删除即将过期key
	allkeys-random：随机删除
	volatile-ttl ： 删除即将过期的
	noeviction ： 永不过期，返回错误当mem_used内存已经超过maxmemory的设定，对于所有的读写请求，都会触发redis.c/freeMemoryIfNeeded(void)函数以清理超出的内存。注意这个清理过程是阻塞的，直到清理出足够的内存空间。所以如果在达到maxmemory并且调用方还在不断写入的情况下，可能会反复触发主动清理策略，导致请求会有一定的延迟。
# **事务控制**



MULTI:标记事务块开始

EXEC:执行所有事务块里面的命令

DISCARD:取消事务，放弃执行事务块内的所有命令

WATCH:监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。

UNWATCH:取消 WATCH 命令对所有 key 的监视。如果在执行 WATCH 命令之后， EXEC 命令或 DISCARD 命令先被执行了的话，那么就不需要再执行 UNWATCH 了。因为 EXEC 命令会执行事务，因此 WATCH 命令的效果已经产生了；而 DISCARD 命令在取消事务的同时也会取消所有对 key 的监视，因此这两个命令执行之后，就没有必要执行 UNWATCH 了



# 引用

<http://redisdoc.com/>