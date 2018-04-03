## Redis 简介 ##

Redis 开源免费的，遵守BSD[1]协议，在内存中进行数据存储的key-value数据库。
Redis 有以下特点：
    数据持久化：    Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
    丰富的数据结构： Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
    数据备份：      Redis支持数据的备份，即master-slave模式的数据备份。
    高性能：        Redis能读的速度是110000次/s,写的速度是81000次/s 。
    原子性[2]：     要么一起成功，要么一起失败。


[1] BSD是一种很开放的软件开源协议，允许你修改第三方代码，并进行商业行为，只是需要你再自己的代码中也加入BSD开源协议。
[2] 原子性说的是不可再分割的实物，举个例子：A想要从自己的帐户中转1000块钱到B的帐户里。那个从A开始转帐，到转帐结束的这一个过程，称之为一个事务。在这个事务里，要做如下操作：

     1. 从A的帐户中减去1000块钱。如果A的帐户原来有3000块钱，现在就变成2000块钱了。
     2. 在B的帐户里加1000块钱。如果B的帐户如果原来有2000块钱，现在则变成3000块钱了。

如果在A的帐户已经减去了1000块钱的时候，忽然发生了意外，比如停电什么的，导致转帐事务意外终止了，而此时B的帐户里还没有增加1000块钱。那么，我们称这个操作失败了，要进行回滚。回滚就是回到事务开始之前的状态，也就是回到A的帐户还没减1000块的状态，B的帐户的原来的状态。此时A的帐户仍然有3000块，B的帐户仍然有2000块。
我们把这种要么一起成功（A帐户成功减少1000，同时B帐户成功增加1000），要么一起失败（A帐户回到原来状态，B帐户也回到原来状态）的操作叫原子性操作。如果把一个事务可看作是一个程序,它要么完整的被执行,要么完全不执行。这种特性就叫原子性。


## Redis 安装 ##

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
这个就是表示 `Redis`服务已经启动成功，并且可以通过 6379 这个端口号进行链接。
这时候另启一个cmd窗口（依然在redis的安装目录），原来的不要关闭，不然 `Redis` 服务就被关闭了。

在新的cmd窗口中执行下面命令，来通过 `redis-cli` 来链接刚才启动的 `Redis` 服务。
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


## Redis 配置 ##

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

    daemonize   ： 是否为守护进程启动，默认值“no”，取值：[no | yes]
    pidfile     ： 当Redis以守护进程方式运行时，Redis默认会把pid写入`/var/run/redis.pid`文件，可以通过pidfile指定
    port        ： 指定Redis服务监听的端口号
    bind        ： 绑定主机的IP地址，默认：127.0.0.1
    timeout     ： 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能，默认取值：300
    loglevel    ： 设置日志的记录等级，默认值：verbose，取值：[debug | verbose | notice | warning]
    databases   ： 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    save <seconds> <changes> ：  指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合，Redis默认配置文件中提供了三个条件：save 900 1 save 300 10 save 60 10000 分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
    rdbcompression ： 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    dbfilename  ：指定本地数据库文件名，默认值为dump.rdb
    dir         ：  指定本地数据库存放目录
    requirepass ： 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭，设置密码后要重新启动redis服务。
    maxclients  ： 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxmemory   ： 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly  ： 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendfilename ： 指定更新日志文件名，默认为appendonly.aof
    appendfsync ： 指定更新日志条件，共有3个可选值：no：表示等操作系统进行数据缓存同步到磁盘（快）always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）  everysec：表示每秒同步一次（折衷，默认值）
    vm-enabled  ： 指定是否启用虚拟内存机制，默认值为no
    vm-swap-file ：虚拟内存文件路径，默认值为/tmp/redis.swap
    glueoutputbuf ： 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为yes
    include      :  定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件


##Redis 数据类型 ##

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。
首先我认为应该要明确的是，redis本质仍然是一个基于key/value存储的数据库，所以我们这里要说的数据类型更多指的描述 `value` 的数据类型。



### String ###
`string` 是 Redis 最基本的数据类型，最大可以存储 512MB
`string` 是基于二进制的，所以 string可以包含任何的数据，例如图片或者序列化的对象以及包括JSON数据。

```
set name "gtshen"
get name
```

### Hash ###

`hash` 是一个键值对(key/value)的数据集合。可以理解为 `string` 类型的field和value的映射表，因此 `hash` 特别适合用于存储对象。

```
# eg:hmset hashname keyvalues

hmset myinfo name "gtshen" age "26"

hget myInfo name
hget myInfo age

```

### List ###

`list` 就是字符串列表，按照前端理解，我们可以理解成 `HTML` 中的 `li` 标记，所有的 `li` 构成了一个list，每个 `li` 都是一个数据项，每个数据项存储的内容都是字符串。
`list` 中的列表顺序按照push插入的顺序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。


```
# eg: lpush listname value

lpush wordList "a"
lpush wordList "b"
...
lpush wordList "z"

lrange wordList 0 1

```

### Set ###

`set` 是 `string` 类型的无序集合，集合是通过哈希表实现的。你可以理解为一个数组中存储了很多字符串，但是这些数组元素的书序并非固定的。

`sadd ` 添加一个string元素到,key对应的set集合中，成功返回1,如果元素已经在集合中返回0,key 对应的 set不存在返回错误。

```
# eg: sadd setName value

sadd wordSet "a"
sadd wordSet "b"
sadd wordSet "d"

smembers setData

```

### zSet ###

`zset` 和 `set` 一样也是string类型元素的集合,且不允许重复的成员。
不同的是`zset`会为每个元素分配一个排序数值。然后通过这些排序数值来对成员进行从小到大的排序。需要注意的是这些排序数值是可以重复的，因此相同的排序数值，则按照字母进行排序。

与 `set` 一样 `zset` 是使用 `zadd` 来将值添加到集合中。

```
# eg: zadd  zsetName score value

zadd zs1 0 "b"
zadd zs1 0 "a"
zadd zs1 0 "c"

zadd zs2 2 "a"
zadd zs2 1 "b"
zadd zs2 0 "c"

zrangebyscore zs1 0 100
zrangebyscore zs2 0 100

```
note : 其中 `score` 就是我们说的排序数值。对于排序数值你不能单单的认为它就是一个辅助的值，实际上它也可以作为一种数据，例如工资收入，分数多少...


## Redis 命令 ##

Redis 命令是指允许在 Redis 服务上运行的命令，一般供客户机链接 Redis 服务使用。

`redis-cli` 便是 Redis 客户机命令行工具，在其中我们便可以执行 Redis命令。

首先，我们通过 `redis-cli` 来连接redis服务。

```
redis-cli -p 6379  [-a password] [-h hostAddress]

```
链接成功后，我们可以执行下面一些常用到的命令来进行测试：

```
ping            #pong 表示Redis服务启动中。

echo "content"  #在命令行

quit            #关闭当前链接

select          #切换到指定的数据库

```

更多的 `redis 命令` 可以在cli中执行：

```
command
```

### Redis 字符串(String) ###

Redis 字符串数据类型的相关命令用于管理 redis 字符串值，基本语法如下

```

set key value               # 设置指定key的值。
get key                     # 获取指定key的值。
getrange key start end      #返回key中字符串的子字符，类似于substring功能。
getset key value            #设置指定key的值，并返回之前的旧值。
mget   key1,key2...         #返回指定的多个key的值，如果其中有个key不存在则返回nil
setex key seconds value     #设置指定key的值并设置过期时间(单位为秒)。
setnx key value             #只有在key不存在的时候设置值，如果存在返回0
setrange key offset value   #从指定key字符串值的偏移位置开始用指定value覆盖。
strlen key                  #返回指定key值的长度
mset key1 value  key2 value key3 value ...  #同时设置多个key的值。
msetnx key1 value key2 value key3 value ... #同时设置多个key的值，但只针对key不存在的时候。
psetex key milliseconds value #设置指定key的值并设置过期时间(单位为毫秒)。
incr   key                    #将 key 中储存的数字值增一。
incrby key buffer             #将 key 存储的值加上指定的增量
incryfloat key buffer         #将 key 所储存的值加上给定的浮点增量值（increment）
decr   key                    #将 key 中储存的数字值减一。
decrby key buffer             #将 key 所储存的值减去给定的减量值（decrement） 。
append key value              #如果指定的 key 存在并且值为字符型，那么便在之前的key的值后面追加指定的值。
```

### Redis 哈希(Hash) ###

`hash` 类型是可以看作为 `string` 类型 `key/value` 的 map集合。
下面是有关 `hash` 数据类型的常用　`redis` 命令。

```
hset  hashName key value               #创建一个hash表，并添加一个key/value键值对。
hmset hashName keyvalues               #创建一个hash表，并添加多个key/value键值对。
hget  hashName key                     #获取指定hash表中指定的key的值。
hmget hashName keys                    #获取指定hash表中多个key的值。
hdel  hashName keys                    #从hash表中删除多个key以及对应的值。
hexists hashName key                   #从hash表中查找指定的key是否存在，存在返回1，不存在返回0
hgetall hashName                       #获取指定hash表中的所有key与value。
hincrby hashName key buffer            #为指定的key的值设置增量
hincrbyfloat hashName key buffer       #指定的key的值设置浮点增量
hkeys   hashName                       #获取指定hash表中的所有key。
hvals   hashName                       #获取指定hash表中的所有value。
hsetnx  hashName key value             #只有在 key 不存在时，设置哈希表字段的值。             

```

### Redis 列表(List) ###

`list` 就是 `string`的字符串列表。下面是有关 `list` 数据类型的常用　`redis` 命令。

```
lpush listName value1 value2 ...                #创建一个list，并向列表左侧添加一个或多个数据项。
lpushx listName key value                       #将一个值插入到已存在的列表的左侧。
rpush listName value1 value2 ...                #创建一个list，并向列表右侧添加一个或多个数据项。
rpushx listName key value                       #将一个值插入到已存在的列表的右侧。
lset   listName index value                     #通过索引设置列表元素的值
lrange listName start end                       #获取指定列表中指定范围的数据。

lrem listName count value
                                                根据参数 COUNT 的值，移除列表中与参数 VALUE 相等的元素。
                                                count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT 。
                                                count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值。
                                                count = 0 : 移除表中所有与 VALUE 相等的值。
ltrim listName start stop                       #按照指定的区域来裁剪列表。

lindex listName index                           #根据索引值(index)获取指定列表中的值，如果列表中索引对应的值不存在则返回 nli。
llen  listName                                  #获取列表长度
lpop  listName                                  #移出并获取列表的第一个元素
rpop  listName                                  #移除并获取列表最后一个元素

blpop listName1 listName2 ... timeout           #移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
brpop listName1 listName2 ... timeout           #移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
brpoplpush listName1 listName2 timeout          #从列表1中弹出一个值，插入到列表2中并返回它，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。


```


### Redis 集合(Set) ###
Redis 的 `set` 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。
下面是有关 `set` 数据类型的常用　redis命令。

```
sadd setName member1 member2 ...      #创建一个集合，并添加一个或多个成员
scard setName                         #获取集合的成员数量。
sdiff setName1 setName2 ...           #根据setName1来求多个集合的差集。
sdiffstore desi setName1 setName2...  #根据setName1来求多个集合的差集并将结果新建并保存到一个新的集合中.
sinter setName1 setName2 ...          #返回多个集合的交集。
sinterstore desi setName1 setName2 ...#返回多个集合的交集并将结果新建并保存到一个新的集合中。
sunion setName1 setName2...           #返回多个集合的并集。
sunionstore desi setName1 setName2... #返回多个集合的并集并将结果新建并保存到一个新的集合中。
sismember setName member              #判断 member 元素是否是集合 setName 的成员
smembers  setName                     #返回集合中的所有成员
smove setName1 setName2 member        #将 member 元素从 setName1 集合移动到 setName2 集合
spop  setName                         #移除并返回集合中的一个随机成员
srandmember setName                   #从指定的结合中随机返回一个成员
srem setName member1 member2...       #删除指定集合中的一个或多个成员
sscan setName match pattern           #迭代并匹配指定的集合成员。
```

### Redis 有序集合(Zset) ###

下面是有关 `zset` 数据类型的常用　redis命令。


```
zadd zsetName score1 member1 score2 member2 ...     #使用上与 sadd相同，只是需要注意每个成员前面都会有一个排序数值
zcard zsetName                                      #略
zcount zsetName min max                             #计算有序集合中指定分数区间的成员数量。其中 min 与 max 可以是 - + 也可以用 [member1 [member2 的范围。
zincrby zsetName buffer member                      #有序集合中对指定成员的分数加上给定增量
zinterstore desi zsetNum zsetName1 zsetName2..      #计算给定的一个或多个有序集的交集，其中给定 有序集合的的数量必须以 zsetNum 参数指定，并将该交集(结果集)储存到 desi 。
zlexcount zsetName min max                          #计算有序集合中指定字典区间内成员数量。其中 min 与 max 可以是 - + 也可以用 [member1 [member2 的范围。
zrange zsetName start end withscore                 #按照给定的区间返回有序集合中的成员列表，其中如果start为0 end为-1表示返回整个集合中的成员列表。
zrangebylex zsetName min max                        #通过字典区间返回有序集合中的成员列表。其中 min 与 max 可以是 - + 也可以用 [member1 [member2 的范围。
zrangebyscore zsetName min max withscore            #通过分数区间来返回有序结合中的成员列表。其中 min 与 max可以是 -inf +inf 也可以是具体的score

                                                     zadd myzset 5000 jack 6000 tom 7000 bob 12000 bob
                                                     zrangebyscore myzset -inf +inf             #显示整个有序集
                                                     zrangebyscore myzset -inf +inf withscore  #显示整个有序集并显示排序值。
                                                     zrangebyscore myzset -inf 12000 withscore #<12000      
                                                     zrangebyscore myzset (5000 8000      #5000 - 80000

zrank zsetName member                               #返回集合中指定成员的索引
zrem  zsetName member1 member2...                   #移除有序集合中的一个或多个成员
zremrangebylex zsetName min max                     #移除有序集合中给定的字典区间的所有成员  
zrevrange zsetName start stop [WITHSCORES]          #返回有序集中指定区间内的成员，通过索引，分数从高到底
zrevrank zsetName memeber                           #返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
zscore   zesetName memeber                          #返回有序集中，成员的分数值
zunionstore desi zsetNmu zsetName1 zsetName2...     #略
```

### Redis 键(Key) ###

`redis` 本质就是通过 key/value 进行数据存储的数据库，而下面就是关于操作 `key` 的常用命令：

```
del key                     #删除Key
dump key                    #序列化key的值
exists key                  #检查给定 key 是否存在。
expire key seconds          #设置 key 的过期时间以秒计。
pexpire key milliseconds    #设置 key 的过期时间以毫秒计。
keys pattern                #匹配符合模式的key，keys *可以列出当前 redis中的所有key
move key db                 #将当前数据库的 key 移动到给定的数据库 db 当中。
persist key                 #移除 key 的过期时间，key 将持久保持。
pttl key                    #以毫秒为单位返回 key 的剩余的过期时间。
ttl  key                    #以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
rankdomkey                  #从当前数据库中随机返回一个 key 。
rename key newkey           #修改 key 的名称
renamenx key newkey         #仅当 newkey 不存在时，将 key 改名为 newkey 。
type key                    #返回 key 所储存的值的类型。[string,list,hash,set,zset]
```


## Redis 发布订阅 ##

对于 Redis的发布(pub)订阅(sub)，实际上就是消息的发送与接收，你可以理解为发送者就是发布者，而接收者就是订阅者。
这个跟前端的 `websocket` 与 `postmessage` 很类似。

下面是关于 Redis的发布订阅的相关使用：

```
# eg (在指定的频道上接收消息）: subscribe channelName
# eg（在指定的频道上发送消息）: publish channelName "message"
```

具体实例：

```
subscribe eat #接收指定频道的消息
publish eat 'What to eat ?'
```

在知道发布订阅的概念，并且会简单的使用后，下面提供发布订阅相关的常用命令：

```
pubsub channels                             #获取当前的频道列表
subscribe channelName1 channelName2 ...     #订阅一个或多个频道
psubscribe pattern1 pattern2                #订阅一个或多个匹给定模式的频道
unsubscribe channelName1 channelName2...    #退订一个或多个频道
punsubscribe pattern1 pattern2 ...          #退订一个或多个匹配给定模式的频道。
```


## Redis 事务 ##

Redis 事务可以一次执行多个命令。
Redis 事务从开始到结束会经历以下三个阶段：

* 开始事务。
* 命令入队。
* 执行事务。

在 Redis 事务中的命令相比与单个命令，它们的区别主要是：

* 事物中的命令将不具有单个redis命令的原子性，事务中任意命令执行失败，其余的命令依然被执行。
* 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

下面是redis事物的具体使用：

```
multi 开始一个事物

set name gtshen #将命令加入事物中

set age 26      #将命令加入事物中

set sex man     #将命令加入事物中

exec            #开始执行事物

```

对于事物的更形象理解，你可以将事物看作成很多redis命令集合在一起批量执行的脚本，只是脚本中的命令不具有原子性。

下面是关于 Redis事物的更多常用命令：

```
discard             #取消事务，放弃执行事务块内的所有命令，相对于 exec
watch key1 key2...  #监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断
unwatch             #取消所有key的监视。
```

## Redis 客户端连接 ##

Redis 通过监听一个 TCP 端口或者 Unix socket 的方式来接收来自客户端的连接。
redis提供了一下命令来管理客户端链接：

```
config get maxclients   #设置客户端的链接数。
clinet kill [ip:port] [ID clientid] #关闭客户端链接
client list             #客户端列表
client getname          #获取链接的名称
client pause            #在指定时间内终止运行来自客户端的命令
client setname names    #设置当前链接的名称
```

## Redis 服务常用命令 ##

在前面我们已经介绍了一些简单的 Redis服务相关的命令，这里将列出更多的命令。

```
auth pass   #验证密码
info        #查看Redis服务以及设备的信息
bgsave      #在后台异步保存当前数据库的数据到磁盘
time            #返回当前服务器的时间
config get name #获取指定配置的值
config set name value #修改配置参数
config rewrite  #会将当前使用config set 临时修改的配置参数写入到配置文件中。
dbsize          #获取当前数据库key的数量
debug object key #获取指定key的调试信息。
debug segfault   #让redis服务崩溃
flushall         #删除所有数据库的key
flushdb          #删除当前数据库的所有key
lastsave         # Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示
monitor          #实时打印出 Redis 服务器接收到的命令，调试用
save             #同步保存数据到硬盘
shutdown         #关闭服务器
shutdown save    #关闭服务器并保存数据
```
最后，同样的使用 `command` 可以获取redis上的所有命令，`redis count` 可以统计命令的个数。

## Redis 数据备份与恢复 ##

在 Redis 上将内存中的数据保存到磁盘上，主要有两种方法：

* save
* bgsave

使用这两个命令，都会在 redis的安装目录中创建要给 `dump.rdb`的数据库备份文件。你可以选择将这个备份文件移走保存到其它地方，当需要恢复的时候，只需要再拷贝到 redis的安装目录，启动服务即可。

对于 `redis` 的安装目录，你可以使用下面这个命令：

```
config get dir
```

## NodeJs 使用 redis ##

使用NodeJS来链接 redis服务 首先我们需要安装给予nodeJS的redis驱动。

```
npm install redis
```

安装好后，配置链接redis的相关参数，可以单独保存在一个配置文件中。

```
## redis-config.js
{
    redis: {
        host: "127.0.0.1",
        port: "6379",
        ttl: 1000 * 60 * 2
    }
}
```
然后引入 `redis`驱动去链接redis服务并创建要给redis服务的实例

```
var redis = require('redis');
var redisConfi = require('redis-config.js');
var client = redis.createClient(redisConfi);

client.on("error", function(err) {
    console.log("Error " + err);
});


client.set("name", "aaaa", redis.print);
client.get("name", function(err, value) { console.log(err, value) })
client.expire('name', 8); //设置过期时间。

```

更多关于 `redis` 驱动的使用，可以到官方文档上查看：https://www.npmjs.com/package/redis



## 疑问 ##

redis 的服务与客户端的界限是什么？



>http://www.runoob.com/redis/redis-strings.html
