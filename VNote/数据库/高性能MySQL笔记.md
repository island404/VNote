# 高性能MySQL笔记

## 第1章　MySQL架构与历史
### MySQL逻辑架构
对于SELECT语句，在解析查询之前，服务器会先检查查询缓存（Query Cache），如
果能够在其中找到对应的查询，服务器就不必再执行查询解析、优化和执行的整个过程，
而是直接返回查询缓存中的结果集。第7章详细讨论了相关内容

### 并发控制
共享锁（shared lock）和排他锁（exclusive lock），也叫读锁（read lock）和写锁（write lock）。

锁粒度
表锁（table lock）与行锁（row lock）
