# InnoDB存储引擎

## 概述

- 支持事务
- 行锁
- 支持MVCC
- 支持外键
- 提供一致性非锁定读



## InnoDB体系架构

**内存池**

- 维护进程，线程运行时的数据结构

- 缓存数据库文件的数据
- 重做日志（redo log）的缓存

**后台线程**

- 刷新内存池中的数据
- 将被修改的缓存同步到磁盘

![InnoDB存储引擎体系架构](images\InnoDB存储引擎体系架构.png)

### 内存

![InnoDB存储引擎内存结构](images\InnoDB存储引擎内存结构.jpg)

#### 缓冲池

- innodb_buffer_pool_size 设置大小
- 从InnoDB 1.0.X开始，允许有多个缓冲池实例，每个页通过hash值分配到不同的缓冲池实例中，减少竞争，增加并发
- **通过LRU算法对缓存（已读取的磁盘页）进行管理**





#### 重做日志缓冲



刷新到日志文件的时机：

- Master Thread每隔1秒刷新
- 事务提交
- 重做日志缓冲池剩余空间小于1/2







### 后台线程

- Master Thread
- IO Thread：InnoDB大量使用异步IO，该线程负责这些IO请求的回调
- Purge Thread：InnoDB1.1开始有，用来回收undo页
- Page Cleaner Thread : InnoDB1.2X 版本引入，将脏页数据刷新到磁盘这一个操作单独放在一个线程

## Checkpoint技术

决定什么时候将脏页数据写到磁盘

- Sharp Checkpoint 在数据库关闭的时候，将所有脏页写回磁盘
- Fuzzy Checkpoint 只刷新一部分脏页
  - Master Thread Checkpoint : Master线程隔一段时间从缓冲池的脏页列表中刷新一定比例的脏页
  - FLUSH_LRU_LIST Checkpoint：InnoDB要保证LRU列表中有100左右空闲页可使用
  - Async/Sync Flush Checkpoint：当重做日志写不下时，需要刷新一部分数据，然后让重做日志的一部分内容失效，达到重用的效果
  - Dirty Page too much Checkpoint：缓冲池中太多脏页了，当脏页数量占了75%，刷新一部分脏页到磁盘







