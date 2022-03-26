# redis 数据库

事务：一组sql操作，要么都成功，要么都失败

## 1. 安装redis

### (1)  网上下载tar的包解压

用mv 命令将安装包移到/usr/local/redis

### (2) 然后进入安装包  用c编译 

sudo make 

### (3) 编译结束 要进行环境测试

sudo make test

### (4) 将redis命令安装到 /usr/local/bin 下面

sudo make install



redis -server 是redis服务端

redis-cli redis 命令行客户端

redis-benchmark 	redis性能测试工具

redis-check-aof 	AOF文件测试工具

redis-check-rdb	RDB文件检索工具

### (5) 将主配文件 conf 拷贝到 /etc/redis下

cp redis.conf /etc/redis/redis.conf



## 2.主配文件配置 详解

### 主要主配文件

```shell
1: Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    daemonize no

2: 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid

3: 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，
因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
  port 6379

4: 绑定的主机地址
  bind 127.0.0.1

5: 当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
  timeout 300

6: 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，
默认为verbose
  loglevel verbose

7: 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志
记录方式为标准输出，则日志将会发送给/dev/null
  logfile stdout

8: 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
  databases 16

9: 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
  save <seconds> <changes>
  Redis默认配置文件中提供了三个条件：
  save 900 1
  save 300 10
  save 60 10000
  分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。

10: 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，
可以关闭该选项，但会导致数据库文件变的巨大
  rdbcompression yes

11: 指定本地数据库文件名，默认值为dump.rdb
  dbfilename dump.rdb

12: 指定本地数据库存放目录
  dir ./

13: 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
  slaveof <masterip> <masterport>

14: 当master服务设置了密码保护时，slav服务连接master的密码
  masterauth <master-password>

15: 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
  requirepass foobared

16: 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，
如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端
返回max number of clients reached错误信息
  maxclients 128

17: 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，
当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，
Value会存放在swap区
  maxmemory <bytes>

18: 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。
因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
  appendonly no

19: 指定更新日志文件名，默认为appendonly.aof
   appendfilename appendonly.aof

20: 指定更新日志条件，共有3个可选值： 
  no：表示等操作系统进行数据缓存同步到磁盘（快） 
  always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
  everysec：表示每秒同步一次（折衷，默认值）
  appendfsync everysec

21: 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，
访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
   vm-enabled no

22: 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
   vm-swap-file /tmp/redis.swap

23: 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),
也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
   vm-max-memory 0

24: Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，
vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；
如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
   vm-page-size 32

25: 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
   vm-pages 134217728

26: 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，
可能会造成比较长时间的延迟。默认值为4
   vm-max-threads 4

27: 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
  glueoutputbuf yes

28: 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
  hash-max-zipmap-entries 64
  hash-max-zipmap-value 512

29: 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
  activerehashing yes

30: 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
  include /path/to/local.conf
```



### 完整主配文件

```shell
#daemonize no  默认情况下， redis 不是在后台运行的，如果需要在后台运行，把该项的值更改为 yes

daemonize yes

#  当 redis 在后台运行的时候， Redis 默认会把 pid 文件放在 /var/run/redis.pid ，你可以配置到其他地址。

#  当运行多个 redis 服务时，需要指定不同的 pid 文件和端口

pidfile /var/run/redis_6379.pid

#  指定 redis 运行的端口，默认是 6379

port 6379

#  在高并发的环境中，为避免慢客户端的连接问题，需要设置一个高速后台日志

tcp-backlog 511

#  指定 redis 只接收来自于该 IP 地址的请求，如果不进行设置，那么将处理所有请求

# bind 192.168.1.100 10.0.0.1

# bind 127.0.0.1

#  设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接

# 0 是关闭此设置

timeout 0

# TCP keepalive

#  在 Linux 上，指定值（秒）用于发送 ACKs 的时间。注意关闭连接需要双倍的时间。默认为 0 。

tcp-keepalive 0

#  指定日志记录级别，生产环境推荐 notice

# Redis 总共支持四个级别： debug 、 verbose 、 notice 、 warning ，默认为 verbose

# debug     记录很多信息，用于开发和测试

# varbose   有用的信息，不像 debug 会记录那么多

# notice    普通的 verbose ，常用于生产环境

# warning   只有非常重要或者严重的信息会记录到日志

loglevel notice

#  配置 log 文件地址

#  默认值为 stdout ，标准输出，若后台模式会输出到 /dev/null 。

logfile /var/log/redis/redis.log

#  可用数据库数

#  默认值为 16 ，默认数据库为 0 ，数据库范围在 0- （ database-1 ）之间

databases 16

################################ 快照#################################

#  保存数据到磁盘，格式如下 :

#   save  

#    指出在多长时间内，有多少次更新操作，就将数据同步到数据文件 rdb 。

#    相当于条件触发抓取快照，这个可以多个条件配合

#    比如默认配置文件中的设置，就设置了三个条件

#   save 900 1  900 秒内至少有 1 个 key 被改变

#   save 300 10  300 秒内至少有 300 个 key 被改变

#   save 60 10000  60 秒内至少有 10000 个 key 被改变

# save 900 1

# save 300 10

# save 60 10000

#  后台存储错误停止写。

stop-writes-on-bgsave-error yes

#  存储至本地数据库时（持久化到 rdb 文件）是否压缩数据，默认为 yes

rdbcompression yes

# 对rdb数据进行校验,耗费CPU资源,默认为yes

rdbchecksum yes

#  本地持久化数据库文件名，默认值为 dump.rdb

dbfilename dump.rdb

#  工作目录

#  数据库镜像备份的文件放置的路径。

#  这里的路径跟文件名要分开配置是因为 redis 在进行备份时，先会将当前数据库的状态写入到一个临时文件中，等备份完成，

#  再把该该临时文件替换为上面所指定的文件，而这里的临时文件和上面所配置的备份文件都会放在这个指定的路径当中。

# AOF 文件也会存放在这个目录下面

#  注意这里必须制定一个目录而不是文件

dir /var/lib/redis-server/

################################# 复制 #################################

#  主从复制 . 设置该数据库为其他数据库的从数据库 .

#  设置当本机为 slav 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步

# slaveof 

#  当 master 服务设置了密码保护时 ( 用 requirepass 制定的密码 )

# slave 服务连接 master 的密码

# masterauth 

#  当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：

# 1)  如果 slave-serve-stale-data 设置为 yes( 默认设置 ) ，从库会继续响应客户端的请求

# 2)  如果 slave-serve-stale-data 是指为 no ，出去 INFO 和 SLAVOF 命令之外的任何请求都会返回一个

#     错误 "SYNC with master in progress"

slave-serve-stale-data yes

#  配置 slave 实例是否接受写。写 slave 对存储短暂数据（在同 master 数据同步后可以很容易地被删除）是有用的，但未配置的情况下，客户端写可能会发送问题。

#  从 Redis2.6 后，默认 slave 为 read-only

slaveread-only yes

#  从库会按照一个时间间隔向主库发送 PINGs. 可以通过 repl-ping-slave-period 设置这个时间间隔，默认是 10 秒

# repl-ping-slave-period 10

# repl-timeout  设置主库批量数据传输时间或者 ping 回复时间间隔，默认值是 60 秒

#  一定要确保 repl-timeout 大于 repl-ping-slave-period

# repl-timeout 60

#  在 slave socket 的 SYNC 后禁用 TCP_NODELAY

#  如果选择“ yes ” ,Redis 将使用一个较小的数字 TCP 数据包和更少的带宽将数据发送到 slave ， 但是这可能导致数据发送到 slave 端会有延迟 , 如果是 Linux kernel 的默认配置，会达到 40 毫秒 .

#  如果选择 "no" ，则发送数据到 slave 端的延迟会降低，但将使用更多的带宽用于复制 .

repl-disable-tcp-nodelay no

#  设置复制的后台日志大小。

#  复制的后台日志越大， slave 断开连接及后来可能执行部分复制花的时间就越长。

#  后台日志在至少有一个 slave 连接时，仅仅分配一次。

# repl-backlog-size 1mb

#  在 master 不再连接 slave 后，后台日志将被释放。下面的配置定义从最后一个 slave 断开连接后需要释放的时间（秒）。

# 0 意味着从不释放后台日志

# repl-backlog-ttl 3600

#  如果 master 不能再正常工作，那么会在多个 slave 中，选择优先值最小的一个 slave 提升为 master ，优先值为 0 表示不能提升为 master 。

slave-priority 100

#  如果少于 N 个 slave 连接，且延迟时间 <=M 秒，则 master 可配置停止接受写操作。

#  例如需要至少 3 个 slave 连接，且延迟 <=10 秒的配置：

# min-slaves-to-write 3

# min-slaves-max-lag 10

#  设置 0 为禁用

#   默认 min-slaves-to-write 为 0 （禁用）， min-slaves-max-lag 为 10

################################## 安全 ###################################

#  设置客户端连接后进行任何其他指定前需要使用的密码。

#  警告：因为 redis 速度相当快，所以在一台比较好的服务器下，一个外部的用户可以在一秒钟进行 150K 次的密码尝试，这意味着你需要指定非常非常强大的密码来防止暴力破解

# requirepass foobared

#  命令重命名 .

#  在一个共享环境下可以重命名相对危险的命令。比如把 CONFIG 重名为一个不容易猜测的字符。

#  举例 :

# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52

#  如果想删除一个命令，直接把它重命名为一个空字符 "" 即可，如下：

# rename-command CONFIG ""

################################### 约束###################################

#设置同一时间最大客户端连接数，默认无限制， 

#Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，

#如果设置  maxclients 0 ，表示不作限制。

#当客户端连接数到达限制时， Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息

# maxclients 10000

#  指定 Redis 最大内存限制， Redis 在启动时会把数据加载到内存中，达到最大内存后， Redis 会按照清除策略尝试清除已到期的 Key

#  如果 Redis 依照策略清除后无法提供足够空间，或者策略设置为 ”noeviction” ，则使用更多空间的命令将会报错，例如 SET, LPUSH 等。但仍然可以进行读取操作

#  注意： Redis 新的 vm 机制，会把 Key 存放内存， Value 会存放在 swap 区

#  该选项对 LRU 策略很有用。

# maxmemory 的设置比较适合于把 redis 当作于类似 memcached 的缓存来使用，而不适合当做一个真实的 DB 。

#  当把 Redis 当做一个真实的数据库使用的时候，内存使用将是一个很大的开销

# maxmemory 

#  当内存达到最大值的时候 Redis 会选择删除哪些数据？有五种方式可供选择

# volatile-lru ->  利用 LRU 算法移除设置过过期时间的 key (LRU: 最近使用  Least RecentlyUsed )

# allkeys-lru ->  利用 LRU 算法移除任何 key

# volatile-random ->  移除设置过过期时间的随机 key

# allkeys->random -> remove a randomkey, any key

# volatile-ttl ->  移除即将过期的 key(minor TTL)

# noeviction ->  不移除任何可以，只是返回一个写错误

#  注意：对于上面的策略，如果没有合适的 key 可以移除，当写的时候 Redis 会返回一个错误

#  默认是 :  volatile-lru

# maxmemory-policy volatile-lru  

# LRU  和  minimal TTL 算法都不是精准的算法，但是相对精确的算法 ( 为了节省内存 ) ，随意你可以选择样本大小进行检测。

# Redis 默认的灰选择 3 个样本进行检测，你可以通过 maxmemory-samples 进行设置

# maxmemory-samples 3

############################## AOF###############################

#  默认情况下， redis 会在后台异步的把数据库镜像备份到磁盘，但是该备份是非常耗时的，而且备份也不能很频繁，如果发生诸如拉闸限电、拔插头等状况，那么将造成比较大范围的数据丢失。

#  所以 redis 提供了另外一种更加高效的数据库备份及灾难恢复方式。

#  开启 append only 模式之后， redis 会把所接收到的每一次写操作请求都追加到 appendonly.aof 文件中，当 redis 重新启动时，会从该文件恢复出之前的状态。

#  但是这样会造成 appendonly.aof 文件过大，所以 redis 还支持了 BGREWRITEAOF 指令，对 appendonly.aof 进行重新整理。

#  你可以同时开启 asynchronous dumps 和  AOF

appendonly no

# AOF 文件名称  ( 默认 : "appendonly.aof")

# appendfilename appendonly.aof

# Redis 支持三种同步 AOF 文件的策略 :

# no:  不进行同步，系统去操作  . Faster.

# always: always 表示每次有写操作都进行同步 . Slow, Safest.

# everysec:  表示对写操作进行累积，每秒同步一次 . Compromise.

#  默认是 "everysec" ，按照速度和安全折中这是最好的。

#  如果想让 Redis 能更高效的运行，你也可以设置为 "no" ，让操作系统决定什么时候去执行

#  或者相反想让数据更安全你也可以设置为 "always"

#  如果不确定就用  "everysec".

# appendfsync always

appendfsync everysec

# appendfsync no

# AOF 策略设置为 always 或者 everysec 时，后台处理进程 ( 后台保存或者 AOF 日志重写 ) 会执行大量的 I/O 操作

#  在某些 Linux 配置中会阻止过长的 fsync() 请求。注意现在没有任何修复，即使 fsync 在另外一个线程进行处理

#  为了减缓这个问题，可以设置下面这个参数 no-appendfsync-on-rewrite

no-appendfsync-on-rewrite no

# AOF  自动重写

#  当 AOF 文件增长到一定大小的时候 Redis 能够调用  BGREWRITEAOF  对日志文件进行重写

#  它是这样工作的： Redis 会记住上次进行些日志后文件的大小 ( 如果从开机以来还没进行过重写，那日子大小在开机的时候确定 )

#  基础大小会同现在的大小进行比较。如果现在的大小比基础大小大制定的百分比，重写功能将启动

#  同时需要指定一个最小大小用于 AOF 重写，这个用于阻止即使文件很小但是增长幅度很大也去重写 AOF 文件的情况

#  设置  percentage 为 0 就关闭这个特性

auto-aof-rewrite-percentage 100

auto-aof-rewrite-min-size 64mb

################################ LUASCRIPTING #############################

# 一个 Lua 脚本最长的执行时间为 5000 毫秒（ 5 秒），如果为 0 或负数表示无限执行时间。

lua-time-limit 5000

################################LOW LOG################################

# Redis Slow Log  记录超过特定执行时间的命令。执行时间不包括 I/O 计算比如连接客户端，返回结果等，只是命令执行时间

#  可以通过两个参数设置 slow log ：一个是告诉 Redis 执行超过多少时间被记录的参数 slowlog-log-slower-than( 微妙 ) ，

#  另一个是 slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除

#  下面的时间以微妙为单位，因此 1000000 代表一秒。

#  注意指定一个负数将关闭慢日志，而设置为 0 将强制每个命令都会记录

slowlog-log-slower-than 10000

#  对日志长度没有限制，只是要注意它会消耗内存

#  可以通过  SLOWLOG RESET 回收被慢日志消耗的内存

#  推荐使用默认值 128 ，当慢日志超过 128 时，最先进入队列的记录会被踢出

slowlog-max-len 128

################################  事件通知  #############################

#  当事件发生时， Redis 可以通知 Pub/Sub 客户端。

#  可以在下表中选择 Redis 要通知的事件类型。事件类型由单个字符来标识：

# K     Keyspace 事件，以 _keyspace@_ 的前缀方式发布

# E     Keyevent 事件，以 _keysevent@_ 的前缀方式发布

# g     通用事件（不指定类型），像 DEL, EXPIRE, RENAME, …

# $     String 命令

# s     Set 命令

# h     Hash 命令

# z     有序集合命令

# x     过期事件（每次 key 过期时生成）

# e     清除事件（当 key 在内存被清除时生成）

# A     g$lshzxe 的别称，因此 ”AKE” 意味着所有的事件

# notify-keyspace-events 带一个由 0 到多个字符组成的字符串参数。空字符串意思是通知被禁用。

#  例子：启用 list 和通用事件：

# notify-keyspace-events Elg

#  默认所用的通知被禁用，因为用户通常不需要改特性，并且该特性会有性能损耗。

#  注意如果你不指定至少 K 或 E 之一，不会发送任何事件。

notify-keyspace-events “”

##############################  高级配置  ###############################

#  当 hash 中包含超过指定元素个数并且最大的元素没有超过临界时，

# hash 将以一种特殊的编码方式（大大减少内存使用）来存储，这里可以设置这两个临界值

# Redis Hash 对应 Value 内部实际就是一个 HashMap ，实际这里会有 2 种不同实现，

#  这个 Hash 的成员比较少时 Redis 为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的 HashMap 结构，对应的 valueredisObject 的 encoding 为 zipmap,

#  当成员数量增大时会自动转成真正的 HashMap, 此时 encoding 为 ht 。

hash-max-zipmap-entries 512

hash-max-zipmap-value 64  

#  和 Hash 一样，多个小的 list 以特定的方式编码来节省空间。

# list 数据类型节点值大小小于多少字节会采用紧凑存储格式。

list-max-ziplist-entries 512

list-max-ziplist-value 64

# set 数据类型内部数据如果全部是数值型，且包含多少节点以下会采用紧凑格式存储。

set-max-intset-entries 512

#  和 hashe 和 list 一样 , 排序的 set 在指定的长度内以指定编码方式存储以节省空间

# zsort 数据类型节点值大小小于多少字节会采用紧凑存储格式。

zset-max-ziplist-entries 128

zset-max-ziplist-value 64

# Redis 将在每 100 毫秒时使用 1 毫秒的 CPU 时间来对 redis 的 hash 表进行重新 hash ，可以降低内存的使用

#  当你的使用场景中，有非常严格的实时性需要，不能够接受 Redis 时不时的对请求有 2 毫秒的延迟的话，把这项配置为 no 。

#  如果没有这么严格的实时性要求，可以设置为 yes ，以便能够尽可能快的释放内存

activerehashing yes

# 客户端的输出缓冲区的限制，因为某种原因客户端从服务器读取数据的速度不够快，

# 可用于强制断开连接（一个常见的原因是一个发布 / 订阅客户端消费消息的速度无法赶上生产它们的速度）。

#  可以三种不同客户端的方式进行设置：

# normal ->  正常客户端

# slave  -> slave 和 MONITOR 客户端

# pubsub ->  至少订阅了一个 pubsub channel 或 pattern 的客户端

#  每个 client-output-buffer-limit 语法 :

# client-output-buffer-limit   

#  一旦达到硬限制客户端会立即断开，或者达到软限制并保持达成的指定秒数（连续）。

#  例如，如果硬限制为 32 兆字节和软限制为 16 兆字节 /10 秒，客户端将会立即断开

#  如果输出缓冲区的大小达到 32 兆字节，客户端达到 16 兆字节和连续超过了限制 10 秒，也将断开连接。

#  默认 normal 客户端不做限制，因为他们在一个请求后未要求时（以推的方式）不接收数据，

#  只有异步客户端可能会出现请求数据的速度比它可以读取的速度快的场景。

#  把硬限制和软限制都设置为 0 来禁用该特性

client-output-buffer-limit normal 0 0 0

client-output-buffer-limit slave 256mb 64mb60

client-output-buffer-limit pubsub 32mb 8mb60

# Redis 调用内部函数来执行许多后台任务，如关闭客户端超时的连接，清除过期的 Key ，等等。

#  不是所有的任务都以相同的频率执行，但 Redis 依照指定的“ Hz ”值来执行检查任务。

#  默认情况下，“ Hz ”的被设定为 10 。

#  提高该值将在 Redis 空闲时使用更多的 CPU 时，但同时当有多个 key 同时到期会使 Redis 的反应更灵敏，以及超时可以更精确地处理。

#  范围是 1 到 500 之间，但是值超过 100 通常不是一个好主意。

#  大多数用户应该使用 10 这个预设值，只有在非常低的延迟的情况下有必要提高最大到 100 。

hz 10  

#  当一个子节点重写 AOF 文件时，如果启用下面的选项，则文件每生成 32M 数据进行同步。

aof-rewrite-incremental-fsync yes
```





## 3. 服务端和客户端启动

### 1.启动服务

sudo systemctl start/stop/restart redis

### 2.如果是解压的安装要 要启动 

sudo redis-server /etc/redis/redis.conf 加载主配文件

杀死 用kill命令杀  sudo kill -9 ‘pid



### 3. 客户端启动

’redis-cli （-h -p 默认是用户名+端口号 主机地址127.0.0.1:6379）

#### 1.redis-cli 测试

​	用ping 测试

​	回一个pong 代表测试成功

![image-20210726110419385](E:\学习笔记\Redis\redis 数据库.assets\image-20210726110419385.png)

 



#### 2. 切换数据库

连接数据库默认连接第一个数据库。 数据库没有名称 通过0-15来表示 

select n（n=（0-15））





## 4. 数据操作

![p1_67](E:\学习笔记\Redis\redis 数据库.assets\p1_67.png)

#### 1. 值的类型分为五种：

- 字符串string
- 哈希hash
- 列表list
- 集合set
- 有序集合zset

#### 2.数据操作行为

- 保存
- 修改
- 获取
- 删除

点击中⽂官⽹查看命令⽂档http://redis.cn/commands.html



### 1. string类型

- 字符串类型是Redis中最为基础的数据存储类型，它在Redis中是二进制安全的，这便意味着该类型可以接受任何格式的数据，如JPEG图像数据或Json对象描述信息等。在Redis中字符串类型的Value最多可以容纳的数据长度是512M。

#### 增加 

- 设置键值

    ```tex
    set key value
    ```

    例如 设置键为name | 值为itcast 的数据

    ```tex
    set name itcast
    ```

    

    ![image-20210726112557193](E:\学习笔记\Redis\redis 数据库.assets\image-20210726112557193.png)



#### 修改

get 是拿到这个key 获取value的值

同样的 直接set name zhcp 就可以改掉这个值

- 设置键值过期时间

    ```tex
    setex key second value
    ```

    例如 设置键为aa    30 秒后过期   | 值为 zhcp

    ```tex
    setex aa 30 zhcp
    ```

![image-20210726113247202](E:\学习笔记\Redis\redis 数据库.assets\image-20210726113247202.png)





- 同时设置多个键可以对应多个值 

    ```tex
    mset key1 value1 key2 value2 key3 value3
    ```

    例如

    ```tex
    mset a python b java c c
    ```

    ![image-20210726113537187](E:\学习笔记\Redis\redis 数据库.assets\image-20210726113537187.png)





- 追加值 可以对一个值追加数据

    ```tex
    append key value
    ```

    例如 向 a键追加值为hello

    ```tex
    append a 'hello'
    ```

    

![image-20210726124147418](E:\学习笔记\Redis\redis 数据库.assets\image-20210726124147418.png)





#### 获取

- 获取 根据键获取值 不存在则返回nill

    ```tex
    get key
    ```

    例如 获取name的值

    ```tex
    get name
    ```

- 获取多个键的值

    ```tex
    mget key1 key2 key3
    ```

    例如 获取abc的值

    ```tex
    mget a b c 
    ```

    

![image-20210726131214414](E:\学习笔记\Redis\redis 数据库.assets\image-20210726131214414.png)











键命令

- 查找键 参数支持正则表达式

    ```tex
    keys pattern
    ```

    例如 查看所有键

    ```
    keys *
    ```

    例如查看键名中包含a 的键

    ```tex
    keys a*
    ```

- 判断键 值是否存在 存在则返回1  不存在则返回0

    ```tex
    exists key
    ```

    例如![image-20210726131815704](E:\学习笔记\Redis\redis 数据库.assets\image-20210726131815704.png)





- 使用type 的key对应什么类型

    ```tex
    type key
    ```

    

#### 删除

- 删除键

    ```tex
    del key
    ```

    

- 设置过期时间

    ```tex
    expire key second
    ```

    例如设置a 键过期时间为3秒

    ```tex
    expire 'a' 3 
    ```

    

- 查看多少秒过期

    ```tex
    setex key second value
    ```

    例如 bb10 秒过期 用ttl查看

    ![image-20210726132427682](E:\学习笔记\Redis\redis 数据库.assets\image-20210726132427682.png)





### 2. hash类型

- hash⽤于存储对象，对象的结构为属性、值
- 值的类型为string

#### 增加修改

- 设置单个属性

    ```tex
    hset key field value
    ```

    例如 设置user的属性name的值为zhcp

    ```tex
    hset user name zhcp
    ```

    

- 设置多个属性

    ```tex
    hmset key filed value1 field2 value2
    ```

    例如 设置键u2 的属性为zhcp 属性age为11

    ```tex
    hmset u2 zhcp age 11
    ```

#### 获取

指定键的所有值

- 获取指定键所有的属性

    > hkeys key

- 例3：获取键a的所有属性

    > hkeys a

    ![image-20210726134931116](E:\学习笔记\Redis\redis 数据库.assets\image-20210726134931116.png)

- 获取⼀个属性的值

    > hget key field

- 例4：获取键a属性'name'的值

    > hget a 'name'

    ![image-20210726135026408](E:\学习笔记\Redis\redis 数据库.assets\image-20210726135026408.png)

- 获取多个属性的值

    > hmget key field1 field2 ...

- 例5：获取键a属性'name'、'age的值

    > hmget a name age

    ![image-20210726135112875](E:\学习笔记\Redis\redis 数据库.assets\image-20210726135112875.png)

- 获取所有属性的值

    > hvals key

- 例6：获取键'a'所有属性的值

    > hvals a

    ![image-20210726135157132](E:\学习笔记\Redis\redis 数据库.assets\image-20210726135157132.png)

#### 删除

- 删除整个hash键及值，使⽤del命令

- 删除属性，属性对应的值会被⼀起删除

    > hdel key field1 field2 ...

- 例7：删除键'a'的属性'age'

    > hdel a age

    ![image-20210726135240686](E:\学习笔记\Redis\redis 数据库.assets\image-20210726135240686.png)





### 3. list类型

- 列表的元素类型为string
- 按照插⼊顺序排序

#### 增加

- 在左侧插⼊数据

    > lpush key value1 value2 ...

- 例1：从键为'a1'的列表左侧加⼊数据a 、 b 、c

    > lpush a1 a b c

    ![image-20210726140004106](E:\学习笔记\Redis\redis 数据库.assets\image-20210726140004106.png)

    ![image-20210726140019399](E:\学习笔记\Redis\redis 数据库.assets\image-20210726140019399.png)

- 在右侧插⼊数据

    > rpush key value1 value2 ...

- 例2：从键为'a1'的列表右侧加⼊数据0 1

    > rpush a1 0 1

    ![image-20210726140128750](E:\学习笔记\Redis\redis 数据库.assets\image-20210726140128750.png)

    ![image-20210726140142897](E:\学习笔记\Redis\redis 数据库.assets\image-20210726140142897.png)

- 在指定元素的前或后插⼊新元素

    > linsert key before或after 现有元素 新元素

- 例3：在键为'a1'的列表中元素'b'前加⼊'3'

    > linsert a1 before b 3

![image-20210726141006702](E:\学习笔记\Redis\redis 数据库.assets\image-20210726141006702.png)

#### 获取

- 返回列表⾥指定范围内的元素

    - start、stop为元素的下标索引
    - 索引从左侧开始，第⼀个元素为0
    - 索引可以是负数，表示从尾部开始计数，如-1表示最后⼀个元素

    > lrange key start stop

- 例4：获取键为'a1'的列表所有元素

    > lrange a1 0 -1

    ![image-20210726141140468](E:\学习笔记\Redis\redis 数据库.assets\image-20210726141140468.png)

#### 设置指定索引位置的元素值

- 索引从左侧开始，第⼀个元素为0

- 索引可以是负数，表示尾部开始计数，如-1表示最后⼀个元素

    > lset key index value

- 例5：修改键为'a1'的列表中下标为4的元素值为'd'

    > lset a1 3 d

    ![image-20210726141332248](E:\学习笔记\Redis\redis 数据库.assets\image-20210726141332248.png)

#### 删除

- 删除指定元素

    - 将列表中前count次出现的值为value的元素移除
    - count > 0: 从头往尾移除
    - count < 0: 从尾往头移除
    - count = 0: 移除所有

    > lrem key count value

- 例6.1：向列表'a2'中加⼊元素'a'、'b'、'a'、'b'、'a'、'b'

    > lpush a2 a b a b a b

    ![image-20210726142555419](E:\学习笔记\Redis\redis 数据库.assets\image-20210726142555419.png)

- 例6.2：从'a2'列表右侧开始删除2个'b'

    > lrem a2 -2 b

    ![image-20210726142620289](E:\学习笔记\Redis\redis 数据库.assets\image-20210726142620289.png)

- 例6.3：查看列表'py12'的所有元素

    > lrange a2 0 -1

    ![image-20210726142640090](E:\学习笔记\Redis\redis 数据库.assets\image-20210726142640090.png)







### 4. set类型

- ⽆序集合
- 元素为string类型
- 元素具有唯⼀性，不重复
- 说明：对于集合没有修改操作

#### 增加

- 添加元素

    > sadd key member1 member2 ...

- 例1：向键'a3'的集合中添加元素'zhangsan'、'lisi'、'wangwu'

    > sadd a3 zhangsan sili wangwu

    ![image-20210726144213962](E:\学习笔记\Redis\redis 数据库.assets\image-20210726144213962.png)

#### 获取

- 返回所有的元素

    > smembers key

- 例2：获取键'a3'的集合中所有元素

    > smembers a3

    ![image-20210726144320462](E:\学习笔记\Redis\redis 数据库.assets\image-20210726144320462.png)

#### 删除

- 删除指定元素

    > srem key value

- 例3：删除键'a3'的集合中元素'wangwu'

    > srem a3 wangwu

    ![image-20210726144411117](E:\学习笔记\Redis\redis 数据库.assets\image-20210726144411117.png)





### 5. zset类型

- sorted set，有序集合
- 元素为string类型
- 元素具有唯⼀性，不重复
- 每个元素都会关联⼀个double类型的score，表示权重，通过权重将元素从⼩到⼤排序
- 说明：没有修改操作

#### 增加

- 添加

    > zadd key score1 member1 score2 member2 ...

- 例1：向键'a4'的集合中添加元素'lisi'、'wangwu'、'zhaoliu'、'zhangsan'，权重分别为4、5、6、3

    > zadd a4 4 lisi 5 wangwu 6 zhaoliu 3 zhangsan

    ![image-20210726145524468](E:\学习笔记\Redis\redis 数据库.assets\image-20210726145524468.png)

#### 获取

- 返回指定范围内的元素

- start、stop为元素的下标索引

- 索引从左侧开始，第⼀个元素为0

- 索引可以是负数，表示从尾部开始计数，如-1表示最后⼀个元素

    > zrange key start stop

- 例2：获取键'a4'的集合中所有元素  根据权值来排序的

    > zrange a4 0 -1

    ![image-20210726145615090](E:\学习笔记\Redis\redis 数据库.assets\image-20210726145615090.png)

- 返回score值在min和max之间的成员

    > zrangebyscore key min max

- 例3：获取键'a4'的集合中权限值在5和6之间的成员

    > zrangebyscore a4 5 6

    ![image-20210726145901867](E:\学习笔记\Redis\redis 数据库.assets\image-20210726145901867.png)

- 返回成员member的score值

    > zscore key member

- 例4：获取键'a4'的集合中元素'zhangsan'的权重

    > zscore a4 zhangsan

    ![image-20210726150044029](E:\学习笔记\Redis\redis 数据库.assets\image-20210726150044029.png)

#### 删除

- 删除指定元素

    > zrem key member1 member2 ...

- 例5：删除集合'a4'中元素'zhangsan'

    > zrem a4 zhangsan

    ![image-20210726150204624](E:\学习笔记\Redis\redis 数据库.assets\image-20210726150204624.png)

- 删除权重在指定范围的元素

    > zremrangebyscore key min max

- 例6：删除集合'a4'中权限在5、6之间的元素

    > zremrangebyscore a4 5 6

    ![image-20210726150337524](E:\学习笔记\Redis\redis 数据库.assets\image-20210726150337524.png)





## 5. python 与 redis 交互

### 1. 掌握虚拟环境redis安装

安装Redis的有3种方式https://github.com/andymccurdy/redis-py

- 第一种：进⼊虚拟环境py_django，联⽹安装包redis

    > pip install redis

- 第二种：进⼊虚拟环境py_django，联⽹安装包redis

    > easy_install redis

- 第三种：到中⽂官⽹-客户端下载redis包的源码，使⽤源码安装

    > 一步步执行 wget https://github.com/andymccurdy/redis-py/archive/master.zip
    > unzip master.zip
    > cd redis-py-master
    > sudo python setup.py install

#### 调⽤模块

- 引⼊模块

    > from redis import *

- 这个模块中提供了StrictRedis对象(Strict严格)，⽤于连接redis服务器，并按照不同类型提供 了不同⽅法，进⾏交互操作



### 2. StrictRedis对象⽅法

- 通过init创建对象，指定参数host、port与指定的服务器和端⼝连接，host默认为localhost，port默认为6379，db默认为0

```
sr = StrictRedis(host='localhost', port=6379, db=0)

简写
sr=StrictRedis()
```

- 根据不同的类型，拥有不同的实例⽅法可以调⽤，与前⾯学的redis命令对应，⽅法需要的参数与命令的参数⼀致

#### string

- set
- setex
- mset
- append
- get
- mget
- key

#### keys

- exists
- type
- delete
- expire
- getrange
- ttl

#### hash

- hset
- hmset
- hkeys
- hget
- hmget
- hvals
- hdel

#### list

- ##### lpush

- rpush

- linsert

- lrange

- lset

- lrem

#### set

- sadd
- smembers
- srem

#### zset

- zadd
- zrange
- zrangebyscore
- zscore
- zrem
- zremrangebyscore

### 3. 交互python

- 在桌面上创建redis目录
- 使用pycharm打开 redis目录
- 创建redis_string.py文件

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()

    except Exception as e:
        print(e)
```

#### string-增加

- ⽅法set，添加键、值，如果添加成功则返回True，如果添加失败则返回False
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #添加键name，值为itheima
        result=sr.set('name','itheima')
        #输出响应结果，如果添加成功则返回True，否则返回False
        print(result)
    except Exception as e:
        print(e)
```

#### string-获取

- ⽅法get，添加键对应的值，如果键存在则返回对应的值，如果键不存在则返回None
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #获取键name的值
        result = sr.get('name')
        #输出键的值，如果键不存在则返回None
        print(result)
    except Exception as e:
        print(e)
```

#### string-修改

- ⽅法set，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #设置键name的值，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
        result = sr.set('name','itcast')
        #输出响应结果，如果操作成功则返回True，否则返回False
        print(result)
    except Exception as e:
        print(e)
```

#### string-删除

- ⽅法delete，删除键及对应的值，如果删除成功则返回受影响的键数，否则则返 回0
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #设置键name的值，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
        result = sr.delete('name')
        #输出响应结果，如果删除成功则返回受影响的键数，否则则返回0
        print(result)
    except Exception as e:
        print(e)
```

#### 获取键

- ⽅法keys，根据正则表达式获取键
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #获取所有的键
        result=sr.keys()
        #输出响应结果，所有的键构成⼀个列表，如果没有键则返回空列表
        print(result)
    except Exception as e:
        print(e)
```

### 4. django存储session

- 之前django的session默认是存在的数据库里面的，我们也可以把session存储在redis里面

#### 准备工作

- 创建test5项目和booktest应用
- 配置url

#### session的redis存储配置

- 安装包

    > pip install django-redis-sessions==0.5.6

    

- 修改settings文件，增加如下项

    > SESSION_ENGINE = 'redis_sessions.session'
    > SESSION_REDIS_HOST = 'localhost'
    > SESSION_REDIS_PORT = 6379
    > SESSION_REDIS_DB = 2
    > SESSION_REDIS_PASSWORD = ''
    > SESSION_REDIS_PREFIX = 'session'

    ```python
    SESSION_ENGINE = 'redis_sessions.session'
    SESSION_REDIS_HOST = 'localhost'
    SESSION_REDIS_PORT = 6379
    SESSION_REDIS_DB = 2
    SESSION_REDIS_PASSWORD = ''
    SESSION_REDIS_PREFIX = 'session'
    ```

    

#### 测试

- 打开booktest/views.py文件，创建session_set和session_get视图如下

```python
  def session_set(request):
      request.session['name']='itheima'
      return HttpResponse('ok')


  def session_get(request):
      name=request.session['name']
      return HttpResponse(name)
```

- 打开booktest/urls.py文件，配置url如下

```
  url(r'^session_set/$',views.session_set),
  url(r'^session_get/$', views.session_get),
```

- 通过redis-cli客户端查看

    

- Base64在线解码http://base64.xpcha.com/ 

    





## 6. redis主从





### 1.redis主从配置



#### 配置详解

- 在主机中将服务停掉  重新配置主配文件 将绑定ip 改为自己本地的ip

    

其他不需要更改 

然后重新启动服务

```shell
sudo redis-server /etc/redis/redis.conf
```



- 在redis文件夹中重新新建一个和主机配置一模一样的配置文件 修改里面的ip以及其他配置

    

![image-20210727104412919](E:\学习笔记\Redis\redis 数据库.assets\image-20210727104412919.png)

ip依然绑定自己本机地址 这里用的一台机器 所以绑定的是自己

![image-20210727104459569](E:\学习笔记\Redis\redis 数据库.assets\image-20210727104459569.png)

端口不能和主机一样 所以我们使用 6378

![image-20210727104718600](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727104718600.png)

在replicaof 中添加主机地址和端口



#### 主备机源码



##### 主机源码：

```shell
bind 192.168.119.131

protected-mode yes

port 6379

tcp-backlog 511

timeout 0

tcp-keepalive 300

daemonize yes

pidfile /var/run/redis_6379.pid

loglevel notice

logfile "/var/log/redis/redis-server.log"

databases 16

always-show-logo no

set-proc-title yes

proc-title-template "{title} {listen-addr} {server-mode}"

stop-writes-on-bgsave-error yes

rdbcompression yes

rdbchecksum yes

dbfilename dump.rdb

rdb-del-sync-files no

dir /var/lib/redis

replica-serve-stale-data yes

replica-read-only yes

repl-diskless-sync no

repl-diskless-sync-delay 5

repl-diskless-load disabled

repl-disable-tcp-nodelay no

replica-priority 100

acllog-max-len 128


lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no


lazyfree-lazy-user-del no


lazyfree-lazy-user-flush no

oom-score-adj no

oom-score-adj-values 0 200 800

disable-thp yes

appendonly no

appendfilename "appendonly.aof"

appendfsync everysec

no-appendfsync-on-rewrite no


auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

aof-load-truncated yes

aof-use-rdb-preamble yes

lua-time-limit 5000

slowlog-log-slower-than 10000

slowlog-max-len 128

notify-keyspace-events ""

hash-max-ziplist-entries 512
hash-max-ziplist-value 64

list-max-ziplist-size -2

list-compress-depth 0

set-max-intset-entries 512

zset-max-ziplist-entries 128
zset-max-ziplist-value 64

hll-sparse-max-bytes 3000

stream-node-max-bytes 4096
stream-node-max-entries 100

activerehashing yes

client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60


hz 10

dynamic-hz yes

aof-rewrite-incremental-fsync yes

rdb-save-incremental-fsync yes

jemalloc-bg-thread yes
```

##### 备机源码：

```shell
bind 192.168.119.131

protected-mode yes

port 6378

tcp-backlog 511

timeout 0

tcp-keepalive 300

daemonize yes

pidfile /var/run/redis_6379.pid

loglevel notice

logfile "/var/log/redis/redis-server.log"

databases 16

always-show-logo no

set-proc-title yes

proc-title-template "{title} {listen-addr} {server-mode}"

stop-writes-on-bgsave-error yes

rdbcompression yes

rdbchecksum yes

dbfilename dump.rdb

rdb-del-sync-files no

dir /var/lib/redis

replicaof 192.168.119.131 6379

replica-serve-stale-data yes

replica-read-only yes

repl-diskless-sync no

repl-diskless-sync-delay 5

repl-diskless-load disabled

repl-disable-tcp-nodelay no

replica-priority 100

acllog-max-len 128


lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no


lazyfree-lazy-user-del no


lazyfree-lazy-user-flush no

oom-score-adj no

oom-score-adj-values 0 200 800

disable-thp yes

appendonly no

appendfilename "appendonly.aof"

appendfsync everysec

no-appendfsync-on-rewrite no


auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

aof-load-truncated yes

aof-use-rdb-preamble yes

lua-time-limit 5000

slowlog-log-slower-than 10000

slowlog-max-len 128

notify-keyspace-events ""

hash-max-ziplist-entries 512
hash-max-ziplist-value 64

list-max-ziplist-size -2

list-compress-depth 0

set-max-intset-entries 512

zset-max-ziplist-entries 128
zset-max-ziplist-value 64

hll-sparse-max-bytes 3000

stream-node-max-bytes 4096
stream-node-max-entries 100

activerehashing yes

client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60


hz 10

dynamic-hz yes

aof-rewrite-incremental-fsync yes

rdb-save-incremental-fsync yes

jemalloc-bg-thread yes
```



#### 用命令查看是否搭建成功

主机

![image-20210727112046021](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727112046021.png)

备机

![image-20210727112143473](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727112143473.png)



同时打开两个终端

![image-20210727112515924](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727112515924.png)

主从备份



当你在主机上面写数据 备机会实时备份。相反 在备机不能写入

![image-20210727131940880](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727131940880.png)



为什么要主从？

1.读写分离

2.负载均衡 备份

3.防止数据丢失







## 7 搭建集群

集群：一组通过网络连接的计算机，共同对外提交服务，像是一个独立的服务器。

### 1. 为什么要有集群

- 之前我们已经讲了主从的概念，一主可以多从，如果同时的访问量过大(1000w),主服务肯定就会挂掉，数据服务就挂掉了或者发生自然灾难
- 大公司都会有很多的服务器(华东地区、华南地区、华中地区、华北地区、西北地区、西南地区、东北地区、台港澳地区机房)

#### 集群的概念

- 集群是一组相互独立的、通过高速网络互联的计算机，它们构成了一个组，并以单一系统的模式加以管理。一个客户与集群相互作用时，集群像是一个独立的服务器。集群配置是用于提高可用性和可缩放性。

![p1_58](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\p1_58.png)

当请求到来首先由负载均衡服务器处理，把请求转发到另外的一台服务器上。

#### redis集群

- 分类
    - 软件层面
    - 硬件层面
- 软件层面：只有一台电脑，在这一台电脑上启动了多个redis服务。

![p1_7](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\p1_7.png)

- 硬件层面：存在多台实体的电脑，每台电脑上都启动了一个redis或者多个redis服务。

     ![p1_6](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\p1_6.png)

#### 搭建集群

- 假如当前拥有两台主机172.16.179.130、172.16.179.131，这⾥的IP在使⽤时要改为实际值

#### 参考阅读

- redis集群搭建http://www.cnblogs.com/wuxl360/p/5920330.html
- [Python]搭建redis集群http://blog.5ibc.net/p/51020.html





### 2.机器的配置

#### 1、配置机器1

- 在演示中，192.168.119.131为当前ubuntu机器的ip

- 在192.168.119.131上进⼊Desktop⽬录，创建conf⽬录

- 在conf⽬录下创建⽂件7000.conf，编辑内容如下

    ```shell
    port 7000
    bind 192.168.119.131
    daemonize yes
    pidfile 7000.pid
    cluster-enabled yes
    cluster-config-file 7000_node.conf
    cluster-node-timeout 15000
    appendonly yes
    ```

- 在conf⽬录下创建⽂件7001.conf，编辑内容如下

    ```shell
    port 7001
    bind 192.168.119.131
    daemonize yes
    pidfile 7001.pid
    cluster-enabled yes
    cluster-config-file 7001_node.conf
    cluster-node-timeout 15000
    appendonly yes
    ```

- 在conf⽬录下创建⽂件7002.conf，编辑内容如下

    ```shell
    port 7002
    bind 192.168.119.131
    daemonize yes
    pidfile 7002.pid
    cluster-enabled yes
    cluster-config-file 7002_node.conf
    cluster-node-timeout 15000
    appendonly yes
    ```

- 总结：三个⽂件的配置区别在port、pidfile、cluster-config-file三项

- 使⽤配置⽂件启动redis服务

    ```shell
    redis-server 7000.conf
    redis-server 7001.conf
    redis-server 7002.conf
    ```

- 查看进程如下图 

- ![image-20210727161617467](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727161617467.png)

#### 2、配置机器2

- 在演示中，192.168.119.132为当前ubuntu机器的ip

- 在192.168.119.132上进⼊Desktop⽬录，创建conf⽬录

- 在conf⽬录下创建⽂件7003.conf，编辑内容如下

    ```
    port 7003
    bind 192.168.119.132
    daemonize yes
    pidfile 7003.pid
    cluster-enabled yes
    cluster-config-file 7003_node.conf
    cluster-node-timeout 15000
    appendonly yes
    ```

- 在conf⽬录下创建⽂件7004.conf，编辑内容如下

    ```
    port 7004
    bind 192.168.119.132
    daemonize yes
    pidfile 7004.pid
    cluster-enabled yes
    cluster-config-file 7004_node.conf
    cluster-node-timeout 15000
    appendonly yes
    ```

- 在conf⽬录下创建⽂件7005.conf，编辑内容如下

    ```
    port 7005
    bind 192.168.119.132
    daemonize yes
    pidfile 7005.pid
    cluster-enabled yes
    cluster-config-file 7005_node.conf
    cluster-node-timeout 15000
    appendonly yes
    ```

- 总结：三个⽂件的配置区别在port、pidfile、cluster-config-file三项

- 使⽤配置⽂件启动redis服务

    ```
    redis-server 7003.conf
    redis-server 7004.conf
    redis-server 7005.conf
    ```

- 查看进程如下图 

- ![image-20210727161548836](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727161548836.png)

### 3.创建集群

- redis的安装包中包含了redis-trib.rb，⽤于创建集群

- 接下来的操作在172.16.179.130机器上进⾏

- 将命令复制，这样可以在任何⽬录下调⽤此命令

    ```shell
    sudo cp /usr/share/doc/redis-tools/examples/redis-trib.rb /usr/local/bin/
    ```

- 安装ruby环境，因为redis-trib.rb是⽤ruby开发的

    > sudo apt-get install ruby

- 在提示信息处输⼊y，然后回⻋继续安装 

    ![image-20210727161727673](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727161727673.png)

- 运⾏如下命令创建集群

    ```shell
    redis-trib.rb create --replicas 1 172.16.179.130:7000 172.16.179.130:7001 172.16.179.130:7002 172.16.179.131:7003 172.16.179.131:7004 172.16.179.131:7005
    ```

- 新版为

    ```shell
    redis-cli --cluster create 192.168.119.131:7000 192.168.119.131:7001 192.168.119.131:7002 192.168.119.134:7003 192.168.119.134:7004 192.168.119.134:7005 --cluster-replicas 1
    ```

- 执⾏上⾯这个指令在某些机器上可能会报错,主要原因是由于安装的 ruby 不是最 新版本 ![p1_63](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\p1_63.png)

- 天朝的防⽕墙导致⽆法下载最新版本,所以需要设置 gem 的源

- 解决办法如下

    ```
    -- 先查看⾃⼰的 gem 源是什么地址
    gem source -l -- 如果是https://rubygems.org/ 就需要更换
    -- 更换指令为
    gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
    -- 通过 gem 安装 redis 的相关依赖
    sudo gem install redis
    -- 然后重新执⾏指令
    ```

    ![p1_64](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\p1_64.png)

    ```shell
    redis-cli --cluster create 192.168.119.131:7000 192.168.119.131:7001 192.168.119.131:7002 192.168.119.134:7003 192.168.119.134:7004 192.168.119.134:7005 --cluster-replicas 1
    ```

- 提示如下主从信息，输⼊yes后回⻋

    ![image-20210727162433768](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727162433768.png)

- 提示完成，集群搭建成功  图中有三个主机 分别是 131:7000 134:7003  131:7001








​				集群

三个主机为3个节点 三个节点互相通信  

    134：7005 属于 131：7000
    
    131：7002 属于 131：7003
    
    134：7004 属于 131：7001

​		把他当成一个大的计算机 

```
M: fbc4b83e13adf882d798558a7d2bd7e52b6f6ba5 192.168.119.131:7000
   slots:[0-5460] (5461 slots) master
M: 4bb418b92cd566baf5047690ecd92ac69b4e15ed 192.168.119.131:7001
   slots:[10923-16383] (5461 slots) master
S: 08dff936f810da83ed1521196709f9668608af91 192.168.119.131:7002
   replicates 5206462182c7c966a7203c6a543d59fb414b4a46
M: 5206462182c7c966a7203c6a543d59fb414b4a46 192.168.119.134:7003
   slots:[5461-10922] (5462 slots) master
S: fd63ca5bfb45f13cce8501f0c3252be0c1bdd3f9 192.168.119.134:7004
   replicates 4bb418b92cd566baf5047690ecd92ac69b4e15ed
S: 2684b7436c2fce38c47ebe951bf4dc8061927dc1 192.168.119.134:7005
   replicates fbc4b83e13adf882d798558a7d2bd7e52b6f6ba5
```

​					 分配槽


 集群中设置数据   set key name







### 4.数据验证

- 根据上图可以看出，当前搭建的主服务器为7000、7001、7003，对应的从服务器是7004、7005、7002

- 在172.16.179.131机器上连接7002，加参数-c表示连接到集群

    > redis-cli -h 172.16.179.131 -c -p 7002

    ![image-20210727163649370](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727163649370.png)

- 写⼊数据

    > set name itemia

- ⾃动跳到了7003服务器，并写⼊数据成功

![image-20210727164013730](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727164013730.png)

- 在7003可以获取数据，如果写入数据又重定向到7000(负载均衡)

![image-20210727164709593](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727164709593.png)



计算数据应该被写在哪个槽里就跳转到哪个槽里

![image-20210727164841297](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727164841297.png)

同理 查询也一样

![image-20210727164928400](E:\学习笔记\django\我的坚果云\Redis\redis 数据库.assets\image-20210727164928400.png)

##### 在哪个服务器上写数据：CRC16

- redis cluster在设计的时候，就考虑到了去中⼼化，去中间件，也就是说，集群中 的每个节点都是平等的关系，都是对等的，每个节点都保存各⾃的数据和整个集 群的状态。每个节点都和其他所有节点连接，⽽且这些连接保持活跃，这样就保 证了我们只需要连接集群中的任意⼀个节点，就可以获取到其他节点的数据
- Redis集群没有并使⽤传统的⼀致性哈希来分配数据，⽽是采⽤另外⼀种叫做哈希 槽 (hash slot)的⽅式来分配的。redis cluster 默认分配了 16384 个slot，当我们 set⼀个key 时，会⽤CRC16算法来取模得到所属的slot，然后将这个key 分到哈 希槽区间的节点上，具体算法就是：CRC16(key) % 16384。所以我们在测试的 时候看到set 和 get 的时候，直接跳转到了7000端⼝的节点
- Redis 集群会把数据存在⼀个 master 节点，然后在这个 master 和其对应的salve 之间进⾏数据同步。当读取数据时，也根据⼀致性哈希算法到对应的 master 节 点获取数据。只有当⼀个master 挂掉之后，才会启动⼀个对应的 salve 节点，充 当 master
- 需要注意的是：必须要3个或以上的主节点，否则在创建集群时会失败，并且当存 活的主节点数⼩于总节点数的⼀半时，整个集群就⽆法提供服务了







### 5.Python交互

- 安装包如下

    > pip install redis-py-cluster

- redis-py-cluster源码地址https://github.com/Grokzen/redis-py-cluster

- 创建⽂件redis_cluster.py，示例码如下

    ```python
    from rediscluster import *
    if __name__ == '__main__':
      try:
        	#131:7000 134:7003  131:7001
          # 构建所有的节点，Redis会使⽤CRC16算法，将键和值写到某个节点上
          startup_nodes = [
              {'host': '192.168.119.131', 'port': '7000'},
              {'host': '192.168.119.134', 'port': '7003'},
              {'host': '192.168.119.131', 'port': '7001'},
          ]
          # 构建StrictRedisCluster对象
          src=StrictRedisCluster(startup_nodes=startup_nodes,decode_responses=True)
          # 设置键为name、值为itheima的数据
          result=src.set('name','itheima')
          print(result)
          # 获取键为name
          name = src.get('name')
          print(name)
      except Exception as e:
          print(e)
    ```

