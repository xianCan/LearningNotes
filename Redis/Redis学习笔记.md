# Redis学习笔记

## 第一章  Redis简介

### 1.1  什么是Redis

* Redis是完全开源免费的，遵循BSD协议，是一个高性能（NOSQL）的key-vakue数据库，Redis是一个开源的  

  使用ANSI C语言编写、支持网络，科技与内存亦可持久化的日志型、key-value数据库，并提供多种语言API

### 1.2  NoSql的类别

**键值（Key-Value）存储数据库**

> ​		这一类数据库主要会使用一个哈希表，这个表中有一个特定的键和一个指针指向特定的数据。key-value模型对于IT系统来说的优势在于简单、易部署。但是如果DBA只对部分值进行查询或更新的时候，key-value就显得效率低下
>
> 相关产品：Redis
> 典型应用：内容缓存，主要用于处理大量数据的高访问负载
> 数据模型：一系列键值对
> 优势：快速查询
> 劣势：存储的数据缺少结构化

**列存储数据库**

> ​		这部分数据库通常用来应对分布式存储的海量数据。键仍然存在，但是它们的特点是指向了多列。这些列是由列家族来安排的
>
> 相关产品：HBase、Riak
> 典型应用：分布式的文件系统
> 数据模型：以列簇式存储，将同一列数据存在一起
> 优势：查找速度快、可扩展性强，更容易进行分布式扩展
> 劣势：功能相对局限

**文档型数据库**

>​		文档型数据库的灵感来源于Lotus Notes办公软件的，而且它同第一种键值存储相类似。该类型的数据模板是版本化的文档，半结构化的文档以特定的格式存储，比如JSON。文档型数据库可看作是键值数据库的升级版，允许之间嵌套键值。而且文档型数据库比键值数据库的查询效率更高
>
>相关产品：MongoDB
>典型应用：Web应用（与key-value类型，value是结构化的）
>数据模型：一系列键值对
>优势：数据结构要求不严格
>劣势：查询性能不高，而且缺乏统一的查询语法

**图形数据库**

### 1.3  Redis总结

* **Redis常用的场景如下：**

  > 1、缓存
  > 		缓存现在几乎是所有中大型网站都在用的技术，合理的利用缓存不仅能够提升网站访问速度，还能大大降低数据库的压力。Redis提供了键过期功能，也提供了灵活的键淘汰策略。所以，现在Redis用在缓存的场合非常多。

  > 2、排行榜
  > 		很多网站都有排行榜应用，如京东的月销量榜单、商品按时间的上新排行榜等。Redis提供的有序集合数据结构能实现各种复杂的排行榜应用。

  > 3、计数器
  > 		什么是计数器，如电商网站商品的浏览量、视频网站的播放数等。为了保证数据实时效，每次浏览都得给+1，并发量高时如果每次都请求数据库操作无疑是各种挑战和压力。Redis提供的incr命令来实现计数器功能，内存操作，性能非常好，非常适用于这些计数场景。

  > 4、分布式会话
  > 		集群模式下，在应用不多的情况下一般使用容器自带的session复制功能就能满足，当应用增多相对复杂的系统中，一般都会搭建以Redis等内存数据库为中心的session服务，session不再由容器管理，而是由session服务以及内存数据库管理。

  > 5、分布式锁
  > 		在很多互联网公司都使用了分布式技术，分布式技术带来的技术挑战是对同一个资源的并发访问，如全局ID、减库存、秒杀等场景，并发量不大的场景可以使用数据库的悲观锁、乐观锁来实现，但在并发量高的场合中，利用数据库锁来控制资源的并发访问是不太理想的，大大影响了数据库的性能。可以利用Redis的setnx功能来编写分布式锁，如果设置返回1说明获取锁成功，否则获取锁失败，实际应用中要考虑的细节还要更多。

  > 6、社交网络
  > 		点赞、踩、关注/被关注、共同好友等是社交网络的基本功能，社交网络的访问量通常来说是比较大的，而且传统的关系数据库类型并不适合存储这种类型的数据库，Redis提供的哈希、集合等数据结构能很方便的实现这些功能。

  > 7、最新列表
  > 		Redis列表结构，LPUSH可以在列表头部插入一个内容ID作为关键字，LTRIM可用来限制列表的数量，这样列表永远为N个ID，无需查询最新的列表，直接根据ID去到对应的内容页即可。

  > 8、消息系统
  > 		消息队列是大型网站必用中间件，如ActiveMQ、RabbitMQ、Kafka等流行的消息中间件，主要用于业务解耦、削峰和异步处理实时性低的业务。Redis提供了发布/订阅及阻塞队列功能，能实现一个简单的消息队列系统。另外，这个不能和专业的消息中间件相比。

* **优势：**

  * **性能极高：**读的速度是110000次/s，写的速度是81000次/s

  * **丰富的数据类型：**String、Hash、List、Set及Zset等

  * **原子性：**Redis的所有操作都是原子性的，意思是要么成功执行要么失败完全不执行。单个操作是原子  

    性的。多个操作也支持事务，通过MULTI和EXEC指令包起来

  * **丰富的特性：**Redis支持public/subscribe，通知，key过期等特性

  * **高速读写：**redis使用自己实现的分离器，代码量很短，没有使用lock，因此效率非常高

* **劣势：**

  * **持久化：**Redis直接将数据存储到内存中，要将数据保存在磁盘上，Redis可以使用两种方式实现持久化过  

    程。定制快照（RDB）：每隔一段时间将整个数据库写到磁盘上，每次均是写全部数据，**代价非常高**。第  

    中方式基于语句追加（AOF）：只追踪变化的数据，但是追加的log可能过大，同时所有的操作均重新执  

    行一遍，**恢复速度慢**  

  * **耗内存：**占用内存过高

### 1.4  Redis的安装

* **Redis安装**

  Redis是C语言开发，安装Redis需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需  

  要先安装gcc

* **编译**

  ```shell
  tar zxvf redis-5.0.8.tar.gz -C /opt  #解压到opt目录#
  cd redis-5.0.8.tar.gz
  make
  ```

* **安装**

  ```shell
  make PREFIX=/usr/local/redis install  #安装到指定目录
  ```

* **运行**

  ```shell
  cd /usr/local/redis/bin
  ./redis-server		#启动服务器
  ./redis-cli -h IP地址 -p 端口 -a 密码 -u 用户名		#默认本机IP地址，端口6379
  ```

## 第二章  Redis配置详解

### 2.1  配置Redis

* 命令：解压目录下的redis.conf配置文件复制到安装文件的目录下

  ```shell
  cp /opt/redis-5.0.8/redis.con /usr/local/redis
  ```

### 2.2  Redis.conf

```bat
1.Redis默认不是守护进程的方式运行，可以通过该哦诶之项修改，使用yes启用守护进程
daemonize no

2.当Redis以守护进程方式运行时，redis默认会吧pid写入/var/run/redis.pid文件，可以通过pidfile指定
pidfile /var/run/redis.pid

3.指定redis监听端口，默认端口为6379
port 6379

4.绑定的主机地址
bind 127.0.0.1

5.当客户端闲置多长时间后关闭连接，如果只定位0，表示关闭该功能
timeout 300

6.指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
loglevel verbose

7.日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将发送给/dev/null
logfile stdout

8.设置数据库的数量 默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
database 16

9.指定多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配置
save <seconds> <changes>
redis 默认文件中提供了三个条件
save 900 1
save 300 10
save 60 10000
分别表示900s内有一个更改，300s内有10个更改以及60s内有10000个更改

10.指定存储至本地数据库时是否压缩数量，默认为yes，Redis采用LZF（压缩算法）压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变得巨大
rdbcompression yes
```

```bat
11.指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

12.指定本地数据库存放目录
dir ./

13.设置当本机为slave服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
slaveof <masterip> <masterport>

14.当master服务设置了密码保护时，slave服务连接master的密码
masterauth <master-password>

15.设置redis连接密码，如果配置了连接密码，客户端在连接Redis时需要AUTH <password>命令提供密码，默认关闭
requirepass xianCan

16.设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxClient 0，表示不作限制，当客户端连接数到达最大限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
maxClient 128

17.指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的mv机制，会把key存放内存，Value会存放在swap区
maxmemory <bytes>

18.指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失，因为Redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
appendonly no

19.指定更新日志文件名，默认为appendonly.aof
appendfilename appendonly.aof

20.指定更新日志条件，共有3个可选值
no：表示等操作系统进行数据缓存同步到磁盘（快）
always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
everysec：表示每秒同步一次（折中，默认值）
```

```bat
21.指定是否启用虚拟内存机制，默认值为no，简单介绍：vm机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中
vm-enabled no

22.虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
vm-swap-file /tmp/redis.swap

23.将所有大于vm-max-memory的数据存入虚拟内存，无论vm-max-memory设置多小，所有索引数据都是内存存储的（Redis的索引数据就是keys），也就是说，当vm-max-memory设置为0的时候，其实是所有value都存于磁盘。默认值为0
vm-max-memory 0

24.Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-max-memory是要根据存储的数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes，如果存储大对象，则可以使用更大的page，如果不确定，就是用默认值
vm-page-size 32

25.设置swap文件的page数量，由于页表（一种表示页面空闲使用的bitmap）是存放在内存中的，在磁盘上每8个pages会消耗1byte的内存
vm-pages 134217728

26.设置访问swap文件的线程数，最好不要超过机器的核数，如果设置为0，那么所有对swap文件的操作都是串行的，可能会造成较长时间的延迟，默认值为4
vm-max-threads 4

27.设置项客户端应答时，是否把较小的包合并为一个包发送，默认为开启
glueoutputbuf yes

28.指定在超过一定的数量或者最大的元素超过某一临届值时，采用一种特殊的哈希算法
hash-max-zipmap-entries 64
hash-max-zipmap-value 512

29.指定是否激活重置哈希，默认值为开启
activerehashing yes

30.指定包含其他的配置文件，可以再同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
include /path/to/local.conf
```

### 2.3  Redis中的内存维护策略

* **设置过期时间**

  ```shell
  expire key time #以秒为单位，最常用
  setex(String key, int seconds, String value) #字符串独有的方式
  ```

  * 除了字符串自己独有设置过期时间的方法外，其它方法都需要依赖expire方法来设置时间
  * 如果有设置时间，那缓存就是永不过期
  * 如果设置了过期时间，之后又想让缓存永不过期，使用 persist key

***

* **采用LRU算法动态将不用的数据删除**

  > ​		LRU为内存管理的一种页面置换算法，对于在内存中但又不用的数据块叫做LRU，操作系统会根据哪些属于LRU而将其移出内存从而腾出空间来加载其它数据

  * 1、**volatile-lru**：设置超时时间的数据中心，删除最不常用的数据
  * 2、**allkeys-lru**：查询所有的key中最近最不常使用的数据进行删除，这是应用最广泛的策略
  * 3、volatile-random：在已经设定了超时的数据中心随即删除
  * 4、allkeys-random：查询所有的key之后随即删除
  * 5、volatile-ttl：查询全部设定超时的数据，之后排序，将马上将要过期的数据进行删除操作
  * 6、noeviction：如果设置为该属性，则不会进行删除操作，如果内存溢出则报错返回

### 2.4  简单配置

**进入对应的安装目录 /usr/local/redis**

修改redis.conf配置文件  vim redis.conf

```shell
daemonize no	#修改为 daemonize yes 守护进程启动
bind 127.0.0.1	#注释掉 允许除本机外的机器访问redis服务
requirepass 密码	#设置密码 有些时候不设置密码是无法进行远程登录的
```

> Redis采用的是IO多路复用的模式，当Redis.conf中选项daemonize设置为yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid写入redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。但当daemonize选项设置为no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具都会导致redis进程退出
>
> 服务端开发的大部分应用都是采用后台运行的模式

> requirepass设置密码，因为redis速度相当快，所以一台比较好的服务器下，一个外部用户在一秒内可以进行15w次密码尝试，这意味着你需要设定非常强大的密码来防止暴力破解

### 2.5  Redis关闭

* 1、非正常关闭：会造成未持久化的数据丢失

  ```shell
  ps -ef | grep redis  #查询redis的进程号
  kill -9 PID
  ```

* 2、正常关闭

  通过客户端可以进行关闭redis服务器

  ```shell
  #登录redis客户端
  exit	#退出客户端
  shutdown	#关闭redis服务器
  ```

## 第三章  Redis数据类型

### 3.1  Redis常用命令

* **常用命令key管理**

  ```shell
  keys *	#返回满足的所有键，可以模糊匹配，比如 keys abc*
  exists key	#是否存在指定的key，存在返回1，不存在返回0
  expire key second	#设置某个key的过期时间，时间为秒
  del key	#删除某个key
  ttl key	#查看剩余时间，当key不存在时，返回-2，当返回-1时代表无限时间，其它代表剩余的秒数
  persist key	#将key改为无限时间
  pexpire key millisecons	#修改key的过期时间为毫秒
  select num	#选择数据库，数据库从0到15。设置成多个数据库实际上是为了数据库安全和备份
  move key dbindex	#将当前数据中的key转移到其他库
  randomkey	#随机返回一个key
  rename key key2	#重命名key
  echo	#打印
  dbsize	#查看数据库的key数量
  info	#查看数据库信息
  config get *	#实时传储收到的请求，返回相关的配置
  flushdb	#清空当前数据库
  flushall	#清空所有数据库
  type key	#返回key的类型
  ```

* **应用场景**

  **expire key seconds**

  1、限时的优惠活动信息，配合**exists key**

  2、网站数据缓存（对于一些需要定时更新的数据，例如：积分排行榜）  

  3、手机验证码  

  4、限制网站访客访问频率（例如：1分钟最多访问10次）

  * 通过获取请求的ip，以ip作为key存活时间为1s，请求前先去查询redis，存在即不可发起请求

### 3.2  key的命名建议

* 1、key不要太长，尽量不要超过1024字节，不仅消耗内存，还会降低查找的效率
* 2、key也不要太短，太短的话，key的可读性会降低
* 3、在一个项目中，key最好使用统一的命名模式，例如：user:123:password
* 4、key名称区分大小写
* 5、redis单个key最大限制为512M
* 6、**建议用冒号分隔key的字段**

### 3.3  String类型

> 简介：
>
> String类型是Redis最基本的数据类型，一个键最大能存储512M
>
> String数据结构是简单的key-value类型，value不仅可以使String，也可以是数字，是包含很多种类型的特殊类型，相当于一个Map<String, Object>
>
> String类型是二进制安全的，意思是redis的String可以包含任何数据。比如说序列化的对象进行存储，一张图片进行二进制存储，一个简单的字符串、数值等

**String命令**

``` shell
#设值
set key value	#赋值语句，多次set同一个key则会覆盖
setnx key value	# nx：not exist。如果key不存在，则设置并返回1。如果key存在，则不设置并返回0（解决 分布式锁 的方案之一，只有在key不存在时设置key的值。setnx(set if not exists)命令在指定的key不存在是，为key设置指定的值）
setnx key 10 value	#设置key的值为value，过期时间为10秒，是String类型特有的设置过期时间命令
setrange string range value	#替换字符串

#取值
get key	#取值，如果key不存在，返回nil
getrange key start end	#用于获取存储在指定key中字符串的子字符串，字符串的截取范围为start和end两个偏移量决定（包括start和end在内），相当于java的subString
gitbit key offset	#对key所存储的字符串值，获取指定偏移量上的位（bit）
getset key value	#设置key的值，并返回key的旧值，当key不存在时，返回nil
strlen key	#返回key所存储的字符串值长度

#删值
del key	#删除指定的key，如果存在，返回数字类型

#批量写
MSET k1 v1 k2 v2

#批量读
MGET k1 k2

#自增/自减
incr key	#命令将key中存储的值+1，如果key不存在，那么key的值会被初始化为0，然后自增，返回自增后的值
decr key	#自减1
incrby key val	#对key进行自增，增量为val
decrby key val	#对key进行自减，减少量为val

#追加
append key value #在key对应的值后面追加value
```

* **应用场景**

  * 1、String通常用于保存单个字符串或JSON字符串数据
  * 2、因String是二进制安全的，所以你完全可以把一个图片文件的内容作为字符串来存储
  * 3、计数器（常规key-value缓存应用。常规计数：微博数、粉丝数）

  > incr等指令本身就具有原子操作的特性，所以我们完全可以利用redis的incr、incrby、decr、decrby等指令来实现原子计数的效果。假如，在某种场景下有3个客户端同时读取了mynum的值（值为2），然后对其同时进行加1的操作，那么最后mynum的值一定是5。不少网站都是利用redis的特性来实现业务上的统计计数需求。

### 3.4  Hash类型

> 简介：
>
> Hash类型是String类型的field和value的映射表，或者说是一个String的集合。hash特别适合用于存储对象，相比较而言，将一个对象类型存储在Hash类型要存储在String类型占用更少的内存空间，并对整个对象的获取可以看成是具有key和value的Map容器
>
> 如 uname，upass，age等，该类型的数据仅占用很少的磁盘空间（相比于json）。redis中每个hash可以存储2的32次方 - 1键值对（40多亿）

**Hash命令**

```shell
#设值
hget key field value #为指定的key，设置field value
hmset key field1 value1 field2 value2	#设定多个值

#取值
hget key field	#获取某一个字段
hmget key field1 field2	#获取几个字段
hgetall key	#获取key下的所有field和value

hkeys key  #获取hash中所有的field
hlen key	#获取hash中字段的数量

#删除
hdel key field #删除对应的field

hsetnx key field value	#只有在字段field不存在时，设置hash字段的值

hincrby key field incrment	#为hash表key中field字段加上增量incrment

hincrbyfloat key field incrment	#为hash表key中的field字段加上浮点增量

hexists key field	#查看hash表中对应的field字段是否存在
```

* **应用场景**

  * 1、常用于存储一个对象

  * 2、为什么不用String存储一个对象

    > hash是最接近关系数据库结构的数据类型，可以讲数据库一条记录或程序中的一个对象转换成hashmap存放在redis中。
    >
    > 假设不用hash类型，用户id为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储，主要有以下两种存储方式：
    >
    > ​	1.将用户id作为查找的key，吧其他信息封装成一个对象以序列化的方式存储，这种方式的缺点是增加了序列化/反序列化的开销，并且在需要修改其中一项时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入CAS等复杂问题
    >
    > ​	2.这个用户信息对象有多少成员就存成多少个key/value对，用用户id+对应属性的名称作为唯一标识来取得对应属性的值，虽然省去了序列化/反序列化和并发问题，但是用户id为重复存储，如果存在大量这样的数据，内存浪费还是非常可观的

  * **总结**

    redis提供的hash很好的解决了这个问题，redis的hash实际是内部存储的value作为一个hashmap





## 第四章  Redis与Spring

### 4.1  常用的redis客户端

> 在SPringBoot2.0之后，对redis连接的支持，默认采用了lettuce，这就一定程度说明了lettuce和jedis的优劣

**概念**

>Jedis：是老牌的redis的java实现客户端，提供了比较全面的redis命令的支持
>
>redisson：实现了分布式和可扩展的java数据结构
>
>lettuce：高级redis客户端，用于线程安全同步，异步和响应使用，支持集群，sentinel，管理和编码器

**优点**

> jedis：比较全面的提供了redis的操作特性
>
> redisson：促使使用者对redis的关注分离，提供了很多分布式相关操作服务。例如：分布式锁，分布式事务，可通过redis支持延迟队列
>
> lettuce：基于netty框架的事件驱动的通信层，其方法的调用是异步的。lettuce的api是线程安全的，所以操作单个lettuce连接来完成各种操作

**可伸缩**

> jedis：使用阻塞的I/O，且其方法调用都是同步的，程序流需要等到sockets处理完I/O才能执行，不支持异步
>
> jedis客户端实例不是线程安全的，所以需要通过连接池来使用jedis
>
> redisson：基于netty框架的事件驱动的通信层，其方法调用都是异步的。redisson的api是线程安全的，所以操作单个redisson连接来完成各种操作
>
> lettuce：基于netty框架的事件驱动的通信层，其方法调用都是异步的。lettuce的api是线程安全的，所以操作单个lettuce连接来完成各种操作
>
> lettuce能够支持redis4，需要java8及以上
>
> lettuce是基于netty实现的与redis进行同步和异步的通信

**lettuce和jedis的比较**

> jedis直接连接redis server，如果在多线程环境下是非线程安全的，这个时候只有使用线程池，为每个jedis实例增加物理连接
>
> lettuce的连接基于netty，连接实例（statefulRedisConnection）可以再多个线程间并发访问，statefulRedisConnection是线程安全的，所以一个连接实例可以满足多线程环境下的并发访问，当然这也是可伸缩的设计，一个连接实例不够的情况下也可以按需增加连接实例
>
> redisson实现了分布式锁和可扩展的java数据结构，和jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等redis特性。

**总结**

> 优先使用lettuce，如果需要使用分布式锁，分布式集合等分布式高级特性，添加redisson结合使用，因为redisson本身对字符串的操作支持很差
>
> 在一些高并发的场景中，比如秒杀，抢票，抢购等场景，都存在对核心资源，商品库存的争夺，控制不好，库存数量可能会被减少到负数，出现超卖的情况，或者产生一个递增的ID，由于Web应用部署在多个机器上，简单的同步加锁是无法实现的，给数据库加锁，对于高并发，1000/s的并发，数据库可能由行锁变成表锁，性能下降会很厉害。那相对而言，redis的分布式锁，是一个很好的选择，redis官方推荐使用redisson来进行分布式锁和相关服务