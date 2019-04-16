* W3CSchool 的 Redis 教程

# 安装运行

## Windows

* 服务器
    在 cmd 中切换到安装目录，运行 `redis-server.exe redis.windows.conf`(32 位: `redis-server.exe redis.conf`)
* 客户端
    在 cmd 中切换到安装目录，运行 `redis-cli.exe -h 127.0.0.1 -p 6379`

## Linux

* 下载安装
    ```
    $ wget http://download.redis.io/releases/redis-2.8.17.tar.gz
    $ tar xzf redis-2.8.17.tar.gz
    $ cd redis-2.8.17
    $ make

    //Ubuntu 版
    $sudo apt-get update
    $sudo apt-get install redis-server
    ```
* 启动
    ```
    //服务器 redis.conf 可选，指定配置文件，否则为默认配置
    $ ./redis-server redis.conf
    //客户端
    $ ./redis-cli

    //Ubuntu 版
    $redis-server
    $redis-cli
    ```
# 配置

获取设置配置项
```
CONFIG GET Key
CONFIG SET Key Value 
CONFIG GET *    //获取所有配置项
```

## 参数说明

1. Redis 默认不是以守护进程的方式运行，使用 yes 启用守护进程
    `daemonize no`
2. 当 Redis 以守护进程方式运行时，pid 写入的文件路径，默认写入 `/var/run/redis.pid` 文件
    `pidfile /var/run/redis.pid`
3. Redis 监听端口，默认为 6379
    `port 6379`
4. 绑定的主机地址
    `bind 127.0.0.1`
5. 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    `timeout 300`
6. 日志记录级别，支持四个级别：debug, notice, warning, verbose(默认)
    `loglevel verbose`
7. 日志记录方式，默认为标准输出；如果配置 Redis 为守护进程方式运行，而日志记录方式为标准输出，则日志将会发送给 `/dev/null`
    `logfile stdout`
8. 数据库的数量，默认数据库为0，可以使用 `SELECT <dbid>` 命令在连接上指定数据库id
    `databases 16`
9. 多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    `save <seconds> <changes>`
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
10. 存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大
    `rdbcompression yes`
11. 本地数据库文件名，默认值为 dump.rdb
    `dbfilename dump.rdb`
12. 本地数据库存放目录
    `dir ./`
13. 当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步
    `slaveof <masterip> <masterport>`
14. 当 master 服务设置了密码保护时，slave 服务连接 master 的密码
    `masterauth <master-password>`
15. 连接密码，如果配置了密码，客户端在连接时需要通过 `AUTH <password>` 命令提供密码，默认关闭
    `requirepass foobared`
16. 同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置为 0，表示不作限制；当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 `max number of clients reached` 错误信息
    `maxclients 128`
17. 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，若处理后，仍然到达最大内存设置，将无法再写入，但仍可读取。Redis 新的 VM 机制，会把 Key 存放内存，Value 会存放在 swap 区
    `maxmemory <bytes>`
18. 是否在每次更新操作后**只**进行日志记录，Redis 在默认情况下是异步地把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 Redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no
    `appendonly no`
19. 更新日志文件名，默认为appendonly.aof
    `appendfilename appendonly.aof`
20. 更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折衷，默认值）
    `appendfsync everysec`
21. 是否启用虚拟内存机制，默认值为 no；VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据交换到磁盘上，访问多的页面由磁盘自动换出到内存中
    `vm-enabled no`
22. 虚拟内存文件路径，默认值为 `/tmp/redis.swap`，不可多个 Redis 实例共享
    `vm-swap-file /tmp/redis.swap`
23. 将所有大于该值的数据存入虚拟内存，无论设置多小，所有索引数据(Key)都是内存存储的，即当设置为0的时候，所有 Value 都存在于磁盘。默认值为0
    `vm-max-memory 0`
24. Redis Swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，`vm-page-size` 是要根据存储的数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64 bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值
    `vm-page-size 32`
25. Swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，在磁盘上每 8 个 pages 将消耗 1 byte 的内存。
    `vm-pages 134217728`
26. 访问 Swap 文件的线程数，最好不要超过机器的核数，如果设置为 0，那么所有对 Swap 文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
    `vm-max-threads 4`
27. 向客户端应答时，是否把较小的包合并为一个包发送，默认为 yes
    `glueoutputbuf yes`
28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    `hash-max-zipmap-entries 64`
    `hash-max-zipmap-value 512`
29. 指定是否激活重置哈希，默认为 yes
    `activerehashing yes`
30. 指定包含其它的配置文件，可以在同一主机上多个 Redis 实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    `include /path/to/local.conf`

# 数据类型

五种：string, hash, list, set, zset

## String

该类型是二进制安全的（不含任何格式定义，比如像 C 语言字符串以 '\0' 来判断结尾），可以包含任何数据，比如图片或序列化的对象；  
一个键最大存储 512 MB
```
SET Key Val
GET Key
DEL Key
```

## Hash

键值对集合，适合用于存储对象，可存储 2^32^ - 1 个键值对
```
//添加一个
HSET Set Key Val
//添加多个
HMSET Set Key1 Val1 Key2 Val2 ... 
//获取所有键值对
HGETALL Set
//获取多个 Key 对应的 Val
HMGET Set Key1 Key2
//获取键值对个数
HLEN Set
//获取所有 Key
HKEYS Set
//获取所有 Val
HVALS Set
//删除
HDEL Set
//判断 Key 是否存在，存在返回 1，否则返回 0 
HEXISTS Set Key
...
```

## List

简单的字符串序列
```
//插入多个
LPUSH List Val1 Val2 ...    //左插入(最前面)
RPUSH List Val1 Val2 ...    //右插入(最后面)
//插入一个，且只能插入到已存在的 List 中
LPUSHX List Val    //左插入(最前面)
RPUSHX List Val    //右插入(最后面)
//插入到 Pivot 前面或后面，Pivot 不存在则不插入，有多个则找第一个
LINSERT List BEFORE/AFTER Pivot Val
//弹出（删除），返回删除的值
LPOP List   //左(前)
RPOP List   //右(后)
//删除该区域外的元素
LTRIM List LIndex RIndex
//删除等于该值的一定数量的元素，Count = 0 则删除所有，为负数则从后面开始删
LREM List Count Val
//修改指定索引的值，索引不存在则报错
LSET List Index Val
//返回该索引上的值，Index 为负数时从后面开始数
LINDEX List Index
//返回该区域内的所有值，0 表头，-1 表尾
LRange List LIndex RIndex
//列表长度
LLEN List
...
```

## Set

字符串的无序集合，元素唯一，通过哈希表实现，操作复杂度为 O(1)
```
SADD Set Val1 Val2 ...
//集合长度
SCARD Set
//差集
SDIFF Set1 Set2 ...
SDIFFSTORE NewSet Set1 Set2 ...    //存起来
//交集
SINTER Set1 Set2 ...
SINTERSTORE NewSet Set1 Set2 ...    //存起来
//并集
SUNION Set1 Set2 ...
SUNIONSTORE NewSet Set1 Set2 ...    //存起来
//是否存在于集合中
SISMEMBER Set Val
//所有元素
SMEMBER Set
//将元素移动到 Set2
SMOVE Set1 Set2 Val
//弹出随机一个
SPOP Set
//返回任意个随机元素
SRANDMEMBER Set Count
//删除
SREM Set Val1 Val2 ...
...
```

## ZSet

有序集合，类似 Set，但是每个元素都会关联一个 double 类型的分数，通过分数进行**从小到大**的排序，元素唯一，但分数可以重复；个数小于 64 时，采用跳表实现，大于 64 时同时使用哈希和跳表两种方式实现
```
ZADD ZSet Score1 Val1 Score2 Val2 ...
//成员数
ZCARD ZSet
//指定分数区间成员数
ZCOUNT ZSet MinScor MaxScore
//将指定成员的分数加上某个数值
ZINCRBY ZSet Num Val
//指定排名区间的成员
ZRANGE ZSet LIndex RIndex
ZREVRANGE ZSet LIndex RIndex                //分数从高到低排
//指定分数区间内的成员
ZRANGEBYSCORE ZSet MinScore MaxScore
ZREVRANGEBYSCORE ZSet MinScore MaxScore     //分数从高到低排
//指定成员的排名 
ZRANK ZSet Val
ZREVRANK ZSet Val   //分数从高到低排
//删除
ZREM ZSet Val1 Val2 ...
ZREMRANGEBYRANK ZSet LIndex RIndex
ZREMRANGEBYSCORE ZSet MinScore MaxScore
//指定成员分数
ZSCORE ZSet Val
...
//还有其它操作与 Set 类似
```

## HyperLogLog

用于计算输入元素的基数（即输入的元素中不重复元素的个数），采用 HyperLogLog 算法，每个键只需要 12KB 的空间，能估算 2^64^ 个元素的基数，误差是 0.81%；  
可用于统计网站不同 IP 访问量等
```
PFADD Key Val1 Val2 ...
//估算基数
PFCOUNT Key1 Key2 ...
//多个合并成一个
PFMERGE DestKey Key1 Key2 ...
```

* HyperLogLog DEMO: http://content.research.neustar.biz/blog/hll.html

# 数据备份与恢复

`SAVE` 命令将数据保存到安装目录下的 dump.rdb 文件，`BGSAVE` 可在后台执行保存操作；只要 dump.rdb 文件存在于安装目录下，启动 Redis 服务器时将自动加载到内存中

# 安全

设置密码后，客户端连接时需要密码验证
```
//设置密码
CONFIG SET requirepass Password
//客户端连接验证
AUTH Password
```

# 性能测试

执行此命令进行测试：`REDIS-BENCHMARK [Option] [OptionValue]`

| 选项  | 描述                                           |
| ----- | ---------------------------------------------- |
| -h    | 服务器主机名，默认：127.0.0.1                  |
| -p    | 服务器端口，默认：6379                         |
| -s    | 服务器 scoket                                  |
| -c    | 并发连接数，默认：50                           |
| -n    | 指定请求数，默认：10000                        |
| -d    | GET/SET 的 Val 的数据大小，单位为字节，默认：2 |
| -k    | 1=keep alive，0=reconnect，默认：1             |
| -r    | SET/GET/INCR 使用随机 Key，SADD 使用随机 Val   |
| -P    | 通过管道传输 <numreq> 请求，默认：1            |
| -q    | 强制退出 Redis，仅显示 query/sec 值            |
| --csv | 以 CSV 格式输出                                |
| -l    | 生成循环，永久执行测试                         |
| -t    | 仅运行以逗号分隔的测试命令列表                 |
| -I    | Idle 模式，仅打开 N 个 idle 连接并等待         |

# 客户端连接

服务器通过监听一个 TCP 端口或 Unix Socket 的方式接收客户端连接，连接建立后，Redis 内部进行以下操作：
1. 客户端 socket 被设置为非阻塞模式，因为 Redis 在网络事件处理上采用**非阻塞多路复用模型**
2. 为这个 scoket 也只 TCP_NODELAY 属性，禁用 Nagle 算法
3. 创建一个可读的文件事件用于监听这个客户端 socket 的数据发送

## 最大连接数

2.6 版本前，最大连接数是被直接硬编码在代码中的，2.6 版本开始则可配置：`maxclients`，默认是 10000

## 客户端命令

| 命令                  | 描述                           |
| --------------------- | ------------------------------ |
| CLIENT LIST           | 已连接的客户端列表             |
| CLENT SETNAME/GETNAME | 设置/获取当前连接名称          |
| CLIENT PAUSE          | 挂起客户端连接，单位为**毫秒** |
| CLIENT KILL           | 关闭客户端连接                 |

# 管道技术

Redis 是基于客户端-服务端模型以及请求/响应协议的 TCP 服务，一个请求一般会遵循以下步骤：
1. 客户端向服务器发送一个查询请求，并监听 Socket 返回，通常以阻塞模式，等待服务器响应
2. 服务器处理命令，将结果返回客户端

管道技术可以在服务器未响应时，客户端继续发送请求，并最终一次性读取服务器的所有响应；管道技术可以显著提高性能

# 分区

分割数据到多个 Redis 实例中

优势：
* 利用多台计算机的内存，构造更大的数据库
* 利用多核和多台计算机，扩展计算能力
* 利用多台计算机和网络适配器，扩展网络带宽

不足：
* 涉及多个 Key 的操作通常是不被支持的，比如当两个 Set 被映射到不同 Redis 实例上时，不能对它们执行交集操作
* 涉及多个 Key 的 Redis 事务不能使用
* 数据处理较为复杂，比如说需要处理多个 rdb/aof 文件，并且从多个实例和主机备份持久化文件
* 增加或删除容量比较复杂，Redis 集群大部署支持在运行时增加、删除节点的透明数据平衡的能力，但是类似于客户端分区、代理等其他系统则不支持这项特性

## 分区类型

* 范围分区
    映射一定范围的对象到特定 Redis 实例，需要有一个区间范围到 Redis 实例的映射表，这个表要被管理，还需要各种对象的映射表，不是很好
* 哈希分区
    用哈希函数将 Key 转换为一个数字，根据 Redis 实例数量对其取模，映射到对应实例中

# 订阅发布

发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息；  
客户端可订阅任意数量的频道，一个频道可以被任意数量的客户端订阅，当某个客户端从某个频道发送消息时（发送者不需要订阅该频道），该频道的所有订阅者将收到消息  
```
//订阅频道
SUBSCRIBE Channel1 Channel2 ...
PSUBSCRIBE Pattern1 Pattern2 ...    //符合模式的频道
//退订
UNSUBSCRIBE Channel1 Channel2 ...
PUNSUBSCRIBE Pattern1 Pattern2 ...  //符合模式的频道
//发送消息
PUBLISH Channel Msg
```

# 事务

事务可以一次执行多个命令，所有命令都会序列化、按顺序地执行，不会被其它命令请求打断，有原子性；  
事务经历 3 个阶段：开始事务，命令入队，执行事务；  
若某个命令出现语法错误，即命令入队时就能发现的错误，事务将不会执行；若某个命令执行时出现错误，事务不会中断也不会回滚！  
```
MULTI       //开始一个事务
...         //输入该事务中的多个命令
DISCARD     //取消事务
EXEC        //执行事务中的所有命令，取消对所有键的监视

//监视指定的 Key，若其被修改或删除，则之后的一个事务无法执行
WATCH Key1 Key2 ...
//取消对所有键的监视，EXEC 命令同样有此作用，不管事务能不能执行
UNWATCH
```