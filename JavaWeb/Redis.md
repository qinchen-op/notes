
## `Redis`简介
***

### `Redis`是什么？

`Redis`（`Remote Dlctionary Server`）通常被称为数据结构服务器，因为值 ( `value` ) 可以是字符串 ( `String` ) , 哈希 ( `Map` ) , 列表 ( `list` ) , 集合 ( `Sets` ) 或有序集合 ( `Sorted Sets` ) 等类型。

`Redis`是开源的，遵守`BSD`协议，使用C语言开发。

`Redis`（全称为`Remote Dictionary Server`）是一个开源的高性能键值对存储系统，具有快速、灵活和可扩展的特性。它是一个基于内存的数据结构存储系统，可以用作数据库、缓存和消息代理。

**主要特点和用途**
- 高性能：`Redis` 数据存储在内存中，因此能够提供极快的读写操作。它采用单线程模型和异步 `I/O`，避免了多线程的竞争和阻塞，从而达到了非常高的性能。
	
- 数据结构多样：`Redis` 支持多种数据结构，包括字符串（`String`）、哈希（`Hash`）、列表（`List`）、集合（`Set`）和有序集合（`Sorted Set`）。这些数据结构提供了丰富的操作命令，使得开发者可以方便地处理各种数据需求。
	
- 持久化支持：`Redis` 提供了两种持久化方式，即快照（`Snapshotting`）和日志追加（`Append-only file，AOF`）。快照方式将 `Redis` 内存数据以二进制格式写入磁盘，而 `AOF` 则通过追加记录 `Redis` 的操作命令来实现持久化。
	
- 发布/订阅：`Redis` 支持发布/订阅模式，可以用作消息代理。发布者将消息发送到指定的频道，订阅者则可以接收和处理这些消息。这种模式在构建实时通信、事件驱动系统和消息队列等场景中非常有用。
	
- 分布式缓存：`Redis`可以通过主从复制和分片来实现数据的分布式存储和高可用性。主从复制可以将数据复制到多个从节点，实现读写分离和数据备份。而分片则可以将数据分布在多个`Redis`节点上，实现横向扩展和负载均衡。
	
- 事务支持：`Redis` 支持事务，开发者可以将多个操作组合成一个原子性的操作序列，保证这些操作要么全部执行成功，要么全部不执行。

`Redis` 是一个功能丰富的存储系统，适用于多种场景，包括缓存、会话存储、排行榜、实时分析等。它有广泛的应用，并且拥有活跃的社区支持。


### `Redis` 与其他 `key-value` 存储有什么不同？

- `Redis` 比起其它键值类数据库，值可以包含更复杂的数据类型，并且在数据类型上定义原子操作。`Redis` 数据类型与基本数据结构密切相关，并直接向程序员公开，无需额外的抽象层。
- `Redis` 运行在内存中但是可以持久化到磁盘，因此它代表了一种不同的权衡，即在数据集不能大于内存的限制下实现非常高的写入和读取速度。内存数据库的另一个优点是，与磁盘上的相同数据结构相比，复杂数据结构的内存表示更易于操作，因此 `Redis` 可以做很多事情而内部复杂性很小。同时，因 `RDB` 和 `AOF` 两种磁盘持久化方式是不适合随机访问，因为它们是顺序写入的。

### `Redis` 架构

`Redis` 主要由有两个程序组成：

- `Redis` 客户端 `redis-cli`
- `Redis` 服务器 `redis-server`

客户端、服务器可以位于同一台计算机或两台不同的计算机中。
****



## 安装`Redis`
***

官网：<https://redis.io/downloads/>

### `Windows`下安装

下载地址：<https://github.com/redis-windows/redis-windows/releases>

运行服务端命令：
```shell
redis-server.exe redis.windows.conf
```
可以将`Redis`的路径加入到环境变量中，更方便。

运行客户端命令：
```shell
redis-cli.exe - 127.0.0.1 -p 6379
```



### `Linux`下安装

下载地址：<http://redis.io/download>

可执行以下命令：
```shell
wget http://download.redis.io/releases/redis-6.0.8.tar.gz
tar -xzvf redis-6.0.8.tar.gz
cd redis-6.0.8
make
```

执行完 `make` 命令后，`redis-6.0.8` 的 `src` 目录下会出现编译后的 `redis` 服务程序 `redis-server`，还有用于测试的客户端程序 `redis-cli`。

启动`Redis`服务
```shell
cd src
./redis-server ../redis.conf
```

**Ubuntu apt**命令安装
```shell
# sudo apt update
# sudo apt install redis-server
```

***



## `Redis`配置

`Redis` 的配置文件位于 `Redis` 安装目录下，文件名为 `redis.conf`(`Windows` 名为 `redis.windows.conf`)。

你可以通过 **CONFIG** 命令查看或设置配置项，也可以直接打开打开文件进行修改。

`redis.conf` 配置项说明如下：

| 序号  | 配置项                                                                                                                                                                                                     | 说明                                                                                                                                                                           |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | `daemonize no`                                                                                                                                                                                          | Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ）                                                                                                       |
| 2   | `pidfile /var/run/redis.pid`                                                                                                                                                                            | 当 Redis 以守护进程方式运行时，Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定                                                                                                   |
| 3   | `port 6379`                                                                                                                                                                                             | 指定 Redis 监听端口，默认端口为 6379，作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女 Alessia Merz 的名字                                                           |
| 4   | `bind 127.0.0.1`                                                                                                                                                                                        | 绑定的主机地址                                                                                                                                                                      |
| 5   | `timeout 300`                                                                                                                                                                                           | 当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能                                                                                                                                              |
| 6   | `loglevel notice`                                                                                                                                                                                       | 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice                                                                                                              |
| 7   | `logfile stdout`                                                                                                                                                                                        | 日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null                                                                                                    |
| 8   | `databases 16`                                                                                                                                                                                          | 设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id                                                                                                                                    |
| 9   | `save <seconds> <changes>`<br><br>Redis 默认配置文件中提供了三个条件：<br><br>**save 900 1**<br><br>**save 300 10**<br><br>**save 60 10000**<br><br>分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。 | 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合                                                                                                                                       |
| 10  | `rdbcompression yes`                                                                                                                                                                                    | 指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大                                                                                                |
| 11  | `dbfilename dump.rdb`                                                                                                                                                                                   | 指定本地数据库文件名，默认值为 dump.rdb                                                                                                                                                     |
| 12  | `dir ./`                                                                                                                                                                                                | 指定本地数据库存放目录                                                                                                                                                                  |
| 13  | `slaveof <masterip> <masterport>`                                                                                                                                                                       | 设置当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步                                                                                                      |
| 14  | `masterauth <master-password>`                                                                                                                                                                          | 当 master 服务设置了密码保护时，slave 服务连接 master 的密码                                                                                                                                    |
| 15  | `requirepass foobared`                                                                                                                                                                                  | 设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH <password> 命令提供密码，默认关闭                                                                                                       |
| 16  | `maxclients 128`                                                                                                                                                                                        | 设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息                |
| 17  | `maxmemory <bytes`>                                                                                                                                                                                     | 指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区               |
| 18  | `appendonly no`                                                                                                                                                                                         | 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no                                           |
| 19  | `appendfilename appendonly.aof`                                                                                                                                                                         | 指定更新日志文件名，默认为 appendonly.aof                                                                                                                                                 |
| 20  | `appendfsync everysec`                                                                                                                                                                                  | 指定更新日志条件，共有 3 个可选值：<br><br>- **no**：表示等操作系统进行数据缓存同步到磁盘（快）<br>- **always**：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）<br>- **everysec**：表示每秒同步一次（折中，默认值）                             |
| 21  | `vm-enabled no`                                                                                                                                                                                         | 指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制）                                                       |
| 22  | `vm-swap-file /tmp/redis.swap`                                                                                                                                                                          | 虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享                                                                                                                                |
| 23  | `vm-max-memory 0`                                                                                                                                                                                       | 将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0                              |
| 24  | `vm-page-size 32`                                                                                                                                                                                       | Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值 |
| 25  | `vm-pages 134217728`                                                                                                                                                                                    | 设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。                                                                                       |
| 26  | `vm-max-threads 4`                                                                                                                                                                                      | 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4                                                                                                    |
| 27  | `glueoutputbuf yes`                                                                                                                                                                                     | 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启                                                                                                                                             |
| 28  | `hash-max-zipmap-entries 64`<br>`hash-max-zipmap-value 512`                                                                                                                                             | 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法                                                                                                                                        |
| 29  | `activerehashing yes`                                                                                                                                                                                   | 指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍）                                                                                                                                     |
| 30  | `include /path/to/local.conf`                                                                                                                                                                           | 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件                                                                                                                 |

***



## `Redis`数据类型

`Redis` 主要支持以下几种数据类型：

- `string`（字符串）:基本的数据存储单元，可以存储字符串、整数或者浮点数。
- `hash`（哈希）:一个键值对集合，可以存储多个字段。
- `list`（列表）:一个简单的列表，可以存储一系列的字符串元素。
- `set`（集合）:一个无序集合，可以存储不重复的字符串元素。
- `zset(sorted set`：有序集合): 类似于集合，但是每个元素都有一个分数（`score`）与之关联。
- 位图（`Bitmaps`）：基于字符串类型，可以对每个位进行操作。
- 超日志（`HyperLogLogs`）：用于基数统计，可以估算集合中的唯一元素数量。
- 地理空间（`Geospatial`）：用于存储地理位置信息。
- 发布/订阅（`Pub/Sub`）：一种消息通信模式，允许客户端订阅消息通道，并接收发布到该通道的消息。
- 流（`Streams`）：用于消息队列和日志存储，支持消息的持久化和时间排序。
- 模块（`Modules`）：`Redis` 支持动态加载模块，可以扩展 `Redis` 的功能。