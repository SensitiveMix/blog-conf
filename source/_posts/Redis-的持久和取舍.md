---
title: Redis 的持久和取舍
date: 2019-01-01 21:32:37
tags: Redis
---

主要内容：

###   持久化的作用

*   什么是持久化
    *   所有的数据都保存在内存中，重启会造成数据丢失，所以需要对数据的更新将异步保存在磁盘中
    
*   主流数据库的持久化方式
    
    *   快照（mysql dump）
    *   写日志（Mysql Binlog、MongoDB oplog、HBase Hlog）
    
###   RDB
    
*   什么是 RDB
    
    *   Redis 存取快照，一般是 RDB 二进制文件，当重启的时候，Redis 重新载入这个快照，做到数据不丢失
    
    *   缓存名称， dbfilename dump.rdb，常用命名 dbfilename dump-${port}.rdb
    
    *   存储位置，dir ./，需要考虑到分盘
    
*   触发机制 - 主要三种方式
    
    *   save，同步命令，排队执行，容易造成阻塞；文件策略，新文件替换老的文件，复杂度 O(n)，不消耗额外内存；
    
    *   bgsave，异步命令，后台执行；fork（阻塞发生在 fork） 子进程来执行操作；文件策略，新文件替换老的文件，复杂度 O(n)，缺点是消耗额外内存用于 fork
    
    *   auto，自动执行（save 60 10000）60 秒钟改变 10000 数据，自动生成 RDB 文件；
    
*   触发机制 - 不可忽略的方式
    
    *   全量复制，适用于主从复制场景，主会自动生成 RDB 文件
    
    *   debug reload，不需要清空重启，触发 RDB 文件生成
    
    *   shutdown，shutdown save 生成 RDB 文件
    
###   AOF
    
*   RDB 现存问题
    
    *   耗时耗性能，O（n）数据，fork() 消耗内存；copy-on-write 策略；Disk I/O 性能损耗
    *   不可控，易丢失数据，宕机丢失数据
    
*   什么是AOF
    
    *   根据写入命令写入日志文件中，实时监控写入
    
*   三种策略
    
    *   always，写在缓冲区中，每条命令 fsync 到磁盘，缺点 IO 开销大，优点不丢失数据
    
    *   everysec，每秒写入磁盘中，容易丢失一秒的数据
    
    *   no，操作系统来决定什么时候该 fsync 到磁盘，缺点不可控，优点就是不用管
    
*   重写
    
    *   老的方式出现文件逐渐变大的问题，重写回 REDIS 会非常慢，针对这个问题提供了重写的功能，合并命令，几条命令合并成一条命令；过期的也不会写入新的文件；优点减少磁盘占用量，加速恢复速度
    
    *   bgwriteof，fork 出子进程来重写，在内存进行重写
    
*   配置
    
    *   auto-aof-rewrite-min-size（重写尺寸)
    
    *   auto-aof-rewrite-percentage （文件增长率）
    
    *   aof\_current\_size （当前尺寸）
    
    *   aof\_base\_size （上次启动和重写的尺寸）
    
###   RDB 和 AOF 抉择

*   比较
    

| 命令 | RDB | AOF |
| --- | --- | --- |
| 启动优先级 | 低 | 高 |
| 体积 | 小 | 大 |
| 恢复速度 | 快 | 慢 |
| 数据安全性 | 丢数据 | 根据策略决定 |
| 轻重 | 重 | 轻 |
    
    
*   RDB 最佳策略
    *   “关”，默认不开启（主从关闭掉），全量复制需要这个策略
    *   集中管理，对数据备份有一定作用
    *   主从，从开，保存历史文件（从节点不进行读写），不适合频繁读写，对磁盘存在影响
    
*   AOF 最佳策略
    *   “开” 缓存和存储，对数据源没有很大
    *   重写集中管理，单机多部署，60-70% 内存给 Redis
    *   everysec
    
*   最佳策略
    *   小分片，maxMemory
    *   缓存或者存储
    *   监控（磁盘、内存、负载、网络）
    *   足够内存（不需要全量给 Redis）
    

### 常见运维问题：

*   fork 操作（内存页的拷贝）
    
    *   同步操作
    
    *   与内存量息息相关，内存越大，耗时越长（与机器类型相关）
    
    *   info: latest\_fork\_usec 上个执行 fork 微秒数
    
*   改善 fork
    
    *   优先使用物理机或者高效支持 fork 操作的虚拟化技术
    
    *   控制 Redis 实例最大的可用内存
    
    *   合理配置 Linux 内存分配策略：vm.overcommit\_memory=1
    
    *   降低 fork 频率：放宽 AOF 重写触发时机，不必要的全量复制
    
*   进程外开销和优化
    
    *   CPU
    
        *   开销：RDB 和 AOF 文件生成，属于 CPU 密集型
    
        *   优化：不做 CPU 绑定，不和 CPU 密集型项目一起部署
    
    *   内存
    
        *   开销：fork 内存开销，copy-on-write
    
        *   优化：echo never > /sys/kernel/mm/transparent\_hugepage/enabled
    
    *   硬盘
    
        *   开销：AOF 和 RDB 文件写入
    
        *   优化：
    
            *   不要和高硬盘负载服务部署在一起：存储服务、消息队列等
    
            *   no-apendfsync-on-rewrite = yes
    
            *   根据写入量决定磁盘类型：例如 SSD
    
            *   单机多部署持久化目录可以考虑分盘
    
    *   AOF 追加阻塞
    
        *   定位
    
            *   Redis 日志 asynchronous AOF fsync is take too long
    
            *   Redis 命令，info Persistence（查看历史累计阻塞）；
    
            *   Linux 系统命令 Top 来查看资源占用
    
    *   单机多实例部署