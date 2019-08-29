# Redis

## 1.1 为什么要用Redis / 缓存

主要从“高性能”和“高并发”这两点来看待这个问题。缓存IO性能高，而且读写缓存能提供的并发量远高于MySQL。

## 1.2 redis memcached 区别

1. **redis支持更丰富的数据类型（支持更复杂的应用场景）**：Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。memcache支持简单的数据类型，String。
2. **Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用,而Memecache把数据全部存在内存之中。**
3. **集群模式**：memcached没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；但是redis目前是原生支持cluster模式的，redis官方就是支持redis cluster集群模式的，比memcached来说要更好。
4. **Memcached是多线程，非阻塞IO复用的网络模型；Redis使用单线程的多路 IO 复用模型。**

<img src="./assets/redis 和 memcached 的区别.webp" width=60% align=center /><br/>

## 1.3 redis 常见数据结构以及使用场景分析

**String：**常用命令: set,get,decr,incr,mget ；常规key-value缓存应用； 常规计数：微博数，粉丝数等。

**Hash：**常用命令： hget,hset,hgetall；hash 特别适合用于存储对象。

**List：**常用命令: lpush,rpush,lpop,rpop,lrange；微博的关注列表，粉丝列表，消息列表等功能，lrange 命令实现简单的高性能分页。

**Set：**常用命令： zadd,zrange,zrem,zcard；set是可以自动排重，实现交集、并集、差集的操作，如微博共同关注。

## 1.4 redis过期时间

set key的时候，设置一个expire time。

定期删除和惰性删除

## 1.5 redis 内存淘汰机制

已设置过期时间：最少使用淘汰、将要过期淘汰、随机淘汰

任意key：最少使用key淘汰、任意key淘汰

禁止新写入

## 1.6 redis 持久化机制

快照：快照持久化是Redis默认采用的持久化方式，默认配置在redis.conf配置文件中。

追加：AOF持久化 的实时性更好，因此已成为主流的持久化方案。默认情况下Redis没有开启AOF（append only file）方式的持久化，可以通过appendonly参数开启：appendonly yes

在Redis的配置文件中存在三种不同的 AOF 持久化方式，它们分别是：

* appendfsync always     #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度
* appendfsync everysec  #每秒钟同步一次，显示地将多个写命令同步到硬盘
* appendfsync no      #让操作系统决定何时进行同步


为了兼顾数据和写入性能，可以考虑 appendfsync everysec选项.

## 1.7 缓存雪崩

缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。

解决办法（中华石杉老师Java面试视频）：
	事前：尽量保证整个 redis 集群的高可用性，发现机器宕机尽快补上。选择合适的内存淘汰策略。
	事中：本地ehcache缓存 + hystrix限流&降级，避免MySQL崩掉
	事后：利用 redis 持久化机制保存的数据尽快恢复缓存

## 1.8 缓存穿透

一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。

解决办法：
	最常见的则是采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。
	另外也有一个更为简单粗暴的方法，如果一个查询返回的数据为空，仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。

## 1.9 解决 Redis 的并发竞争 Key 问题

基于zookeeper临时有序节点可以实现的分布式锁。

## 1.10 缓存与数据库双写时的数据一致性

一般来说，就是如果你的系统不是严格要求缓存+数据库必须一致性的话，缓存可以稍微的跟数据库偶尔有不一致的情况，最好不要做这个方案，读请求和写请求串行化，串到一个内存队列里去，这样就可以保证一定不会出现不一致的情况。

