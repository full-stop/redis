##Redis 简介##

Redis 开源免费的，遵守BSD[1]协议，在内存中进行数据存储的key-value数据库。
Redis 有以下特点：
    数据持久化：   Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
    丰富的数据结构：Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
    数据备份：     Redis支持数据的备份，即master-slave模式的数据备份。
    高息性能：     Redis能读的速度是110000次/s,写的速度是81000次/s 。
    原子性：       要么一起成功，要么一起失败。


[1] BSD是一种很开放的软件开源协议，允许你修改第三方代码，并进行商业行为，只是需要你再自己的代码中也加入BSD开源协议。


##Redis 安装 ##

下载：https://github.com/MSOpenTech/redis/releases

安装后，进入到`Redis`的安装目录，window按照以下命令执行：

```
redis-server.exe redis.windows.conf
```

如果提示 `bind:No Error`,则按照下面的命令顺序执行：

```
Redis-cli.exe
shutdown
exit
redis-server.exe redis.windows.conf
```

执行顺利的话，会有以下提示：

```

                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 3.2.100 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 5660
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

[5660] 30 Mar 15:35:08.242 # Server started, Redis version 3.2.100
[5660] 30 Mar 15:35:08.242 * DB loaded from disk: 0.000 seconds
[5660] 30 Mar 15:35:08.242 * The server is now ready to accept connections on port 6379

```
这个就是表示 `Redis`服务已经启动成功，并且可以通过 6379这个端口号进行链接。
这时候另启一个cmd窗口（依然在redis的安装目录），原来的不要关闭，不然 `Redis` 服务就被关闭了。

在新的cmd窗口中执行下面命令，来通过 `redis-cli` 来链接 `Redis` 服务。
```
redis-cli.exe -p 6379
```
如果设置了密码，则需要通过密码验证链接：
```
redis-cli.exe -p 6379 -a YourPass
```

继续执行下面命令，来很简单的设置一个值，获许一个值。

```
set name gtshen
get name
```


##Redis 配置 ##

Redis的配置命令格式如下：

```
# 获得参数对应的值
config get key

# 修改参数对应的值
config set key value

# 获取redis所有的配置项
config get *
```

下面是常见的`Redis`配置项：

    daemonize ： 是否为守护进程启动，默认值“no”，取值：[no | yes]
    pidfile   ： 当Redis以守护进程方式运行时，Redis默认会把pid写入`/var/run/redis.pid`文件，可以通过pidfile指定
    port      ： 指定Redis服务监听的端口号
    bind      ： 绑定主机的IP地址，默认：127.0.0.1
    timeout   ： 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能，默认取值：300
    loglevel  ： 设置日志的记录等级，默认值：verbose，取值：[debug | verbose | notice | warning]
    databases ： 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    save <seconds> <changes> ：  指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合，Redis默认配置文件中提供了三个条件：save 900 1 save 300 10 save 60 10000 分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
    rdbcompression ： 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    dbfilename    ：指定本地数据库文件名，默认值为dump.rdb
    dir        ：  指定本地数据库存放目录
    requirepass ： 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭，设置密码后要重新启动redis服务。
    maxclients  ： 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxmemory   ： 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly  ： 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendfilename ： 指定更新日志文件名，默认为appendonly.aof
    appendfsync ： 指定更新日志条件，共有3个可选值：no：表示等操作系统进行数据缓存同步到磁盘（快）always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）  everysec：表示每秒同步一次（折衷，默认值）
    vm-enabled ： 指定是否启用虚拟内存机制，默认值为no
    vm-swap-file ：虚拟内存文件路径，默认值为/tmp/redis.swap
    glueoutputbuf ： 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为yes
    include      :  定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件


##Redis 数据类型 ##

Redis主要有四种数据类型：string、hash、list、set

string是 Redis最基本的数据，最大可以存储512MB

```
set name "gtshen"
get name
```

Redis Hash 是一个键值对集合
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

```
# hmset <HashName> fileID1 value  fileID2 value ...

hmset myInfo name "gtshen" age "26"
hget myInfo name
hget myInfo age

```

Redis list 是简单的字符串列表，其顺序是按照push插入的顺序。

```
# lpush listName values

lpush stringData "abc"
lpush stringData "efg"

lrange stringData 0 1

```

Redis set 是string类型的无序集合，集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。

向集合中添加数据需要用到 `sadd` 命令。
`sadd ` 添加一个string元素到,key对应的set集合中，成功返回1,如果元素已经在集合中返回0,key对应的set不存在返回错误。

```
sadd setData "abc"
sadd setData "efg"

smembers setData
```

Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。相同的排序数值，则按照字母进行排序
zset的成员是唯一的,但分数(score)却可以重复。

与 set 一样 zeset 是 zadd 来将值添加到集合中。

```
# zadd  zsetData values

zadd zs1 0 "b"
zadd zs1 0 "a"
zadd zs1 0 "c"

zadd zs2 2 "a"
zadd zs2 1 "b"
zadd zs2 0 "c"

zrangebyscore zs1 0 100
zrangebyscore zs2 0 100

```


##Redis 命令##

Redis 命令是指允许再 Redis 服务上的命令，一般用户客户机链接Redis服务使用。

redis-cli 便是 Redis 客户机命令行工具，在其中我们便可以执行 Redis命令。

```
#链接Redis服务

redis-cli -p 6379 -a password -h hostAddress

ping -> pong 表示Redis服务启动中。
```

### Redis 字符串(String) ###

Redis 字符串数据类型的相关命令用于管理 redis 字符串值，基本语法如下

```

set key value # 设置指定key的值。
get key       # 获取指定key的值。
getrange key start end #返回key中字符串的子字符，类似于substring功能。
getset key value  #设置指定key的值，并返回之前的旧值。
mget   key1,key2... #返回指定的多个key的值，如果其中有个key不存在则返回nil
setex key seconds value #设置指定key的值并设置过期时间。
setnx key value         #只有在key不存在的时候设置值，如果存在返回0
setrange key offset value #从指定key字符串值的偏移位置开始用指定value覆盖。
strlen key    #返回指定key值的长度
mset key1 value  key2 value key3 value ... #同时设置多个key的值。
msetnx key1 value key2 value key3 value ... #同时设置多个key的值，但只针对key不存在的时候。


```



>http://www.runoob.com/redis/redis-strings.html
