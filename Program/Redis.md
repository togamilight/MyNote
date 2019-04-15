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