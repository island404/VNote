# MySQL存储引擎：MyISAM和InnoDB

MyISAM是MySQL的默认数据库引擎（5.5版之前），MyISAM不支持事务和行级锁，而且最大的缺陷就是崩溃后无法安全恢复。不过，5.5版本之后，MySQL引入了InnoDB。

大多数时候我们使用的都是InnoDB存储引擎，但是在某些情况下使用MyISAM更好，比如：MyISAM更适合读密集的表，而InnoDB更适合写密集的的表。 在数据库做主从分离的情况下，经常选择MyISAM作为主库的存储引擎。

## MyISAM特点：

* 不支持行锁(MyISAM只有表锁)，读取时对需要读到的所有表加锁，写入时则对表加排他锁；
* 不支持事务；
* 不支持外键；
* 不支持崩溃后的安全恢复；
* 在表有读取查询的同时，支持往表中插入新纪录；
* 支持BLOB和TEXT的前500个字符索引，支持全文索引；
* 支持延迟更新索引，极大地提升了写入性能；
* 对于不会进行修改的表，支持 压缩表 ，极大地减少了磁盘空间的占用；

## InnoDB特点：
* 支持行锁，采用MVCC来支持高并发，有可能死锁；
* 支持事务；
* 支持外键；
* 支持崩溃后的安全恢复；
* 不支持全文索引；

## 二者的对比与总结

二者的常见对比

1. count运算上的区别： 因为MyISAM缓存有表meta-data（行数等），因此在做 COUNT(*) 时对于一个结构很好的查询是不需要消耗多少资源的。而对于InnoDB来说，则没有这种缓存。
2. 是否支持事务和崩溃后的安全恢复： MyISAM 强调的是性能，每次查询具有原子性,其执行数度比InnoDB类型更快，但是不提供事务支持。但是InnoDB 提供事务支持事务，外部键等高级数据库功能。 具有事务(commit)、回滚(rollback)和崩溃修复能力(crash recovery capabilities)的事务安全(transaction-safe (ACID compliant))型表。
3. 是否支持外键： MyISAM不支持，而InnoDB支持。

总结：

* MyISAM更适合读密集的表，而InnoDB更适合写密集的的表。 在数据库做主从分离的情况下，经常选择MyISAM作为主库的存储引擎。
* 一般来说，如果需要事务支持，并且有较高的并发读取频率(MyISAM的表锁的粒度太大，所以当该表写并发量较高时，要等待的查询就会很多了)，InnoDB是不错的选择。
* 如果数据量很大（MyISAM支持压缩特性可以减少磁盘的空间占用），而且不需要支持事务时，MyISAM是最好的选择。


链接：https://juejin.im/post/5b1685bef265da6e5c3c1c34

