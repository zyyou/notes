## 常用命令
```shell
# 启动redis
service redis start
# 停止redis
service redis stop
# 查看redis运行状态
service redis status
# 查看版本
redis-server -v
# 设置开机启动
chkconfig redis on
```

## 客户端
```shell
redis-cli
# 列出所有key
keys *

```

## 配置文件
/etc/redis.conf，内容参考
```shell

# 自定义配置文件
# ./redis-server /path/to/redis.conf

# 指定内存不区分大小写，通常指定 1k 5GB 4M 等
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes


################################## INCLUDES ###################################

# 有标准配置模板，每台redis服务器有个性配置时可以include配置文件
# include /path/to/local.conf
# include /path/to/other.conf

# 绑定的IP，多个以空格分割
bind 127.0.0.1

# 是否启用保护模式 yes no
# 如果没有指定bind和密码，开启该参数后，redis只允许本地访问
protected-mode yes

# 监听端口，默认 6379
port 6379

# 在一个并发量高的环境中，你需要指定一个比较大的backlog值来避免慢连接的情况
# 注意，linux内核会默认 使用/proc/sys/net/core/somaxconn 的值来削减 backlog的实际值，
# 因此你需要确保提升 somaxconn 和 tcp_max_syn_backlog 这两个值来确保此处的backlog生效
tcp-backlog 511

# Unix socket.
#
# 指定unix sock的路径来进行连接监听，默认是不指定，因此redis不会在unix socket上进行监听
#
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# 空闲N秒的连接自动关闭（0不处理）
timeout 0

# 如果该值不为0，将使用 SO_KEEPALIVE 这一默认的做法来向客户端连接发送TCP ACKs 
#
# 这样的好处有以下两个原因
# 1）检测已经死亡的对端（译者注：TCP的关闭会存在无法完成4次握手的情况，如断电，断网，数据丢失等等）
# 2）保持连接在网络环境中的存活
tcp-keepalive 300

################################# GENERAL #####################################

# 守护进程来运行
# pid文件 /var/run/redis.pid
daemonize yes

# 以守护进程运行时，pid文件
pidfile /var/run/redis.pid

# 可以通过upstart和systemd管理Redis守护进程
# 选项：
#    supervised no - 没有监督互动
#    supervised upstart - 通过将Redis置于SIGSTOP模式来启动信号
#    supervised systemd - signal systemd将READY = 1写入$ NOTIFY_SOCKET
#    supervised auto - 检测upstart或systemd方法基于 UPSTART_JOB或NOTIFY_SOCKET环境变量
supervised no

# 日志级别
# debug 最多
# verbose 比debug少
# notice 一般用于生产
# warning 重要信息记录
loglevel notice

# 日志文件，为空时将输出到标准输出设备
# demo模式下使用标准输出，将会输出到 /dev/null
logfile /var/log/redis/redis.log

# yes时记录日志到系统日志
# 可以使用更多日志参数
# syslog-enabled no

# 在系统日志中的身份
# syslog-ident redis

# 系统日志功能，USER 或 LOCAL0 到 LOCAL7 之间
# syslog-facility local0

# 设置数据库个数，使用SELECT切换，默认使用0号库 默认16个库
databases 16

################################ SNAPSHOTTING  ################################
# 硬盘持久化，两个值要同时满足
# save <seconds> <changes>
# 下面表示1个key变更900秒后保存到硬盘
# save 900 1
# 下面表示不保存到硬盘
# save ""

save 900 1
save 300 10
save 60 10000

# 持久化失败时是否继续工作，但终止客户端write请求。
# 设置"yes"，一旦保存失败，此server将提供只读服务
# 设置"no"，持久化失败后不受影响，但如果数据库故障,数据只能恢复到"最近一个成功点"
stop-writes-on-bgsave-error yes

# 镜像备份时是否启用rdb文件压缩，默认为yes
rdbcompression yes

# 是否校验rdb文件，yes有利于文件的容错性，但是在保存文件时会有大概10%的性能损耗，如果追求高性能可关闭
rdbchecksum yes

# 镜像备份文件名
dbfilename dump.rdb

# 工作目录，rdb、aof文件等
dir /var/lib/redis

################################# REPLICATION #################################

# 主从复制，复制另一个数据库数据，当前为从库 slave
# slaveof <masterip> <masterport>

# 主库开启了密码保护则需要配置访问密码
# masterauth <master-password>

#从库正在同步数据时：
# yes(默认设置)，从库会继续响应客户端的请求，有可能数据已过期
# no，除去INFO和SLAVOF命令之外的任何请求都会返回一个错误”SYNC with master in progress”。
slave-serve-stale-data yes

# 作为从服务器，默认只读（yes），可以修改成NO开启写（不建议）
slave-read-only yes

# 是否使用socket方式复制数据。目前redis复制提供两种方式，disk和socket。
# 如果新的slave连上来或者重连的slave无法部分同步，就会执行全量同步，master会生成rdb文件。
# 有2种方式：
# disk方式是master创建一个新的进程把rdb文件保存到磁盘，再把磁盘上的rdb文件传递给slave。
# socket是master创建一个新的进程，直接把rdb文件以socket的方式发给slave。
# disk方式的时候，当一个rdb保存的过程中，多个slave都能共享这个rdb文件。
# socket的方式就的一个个slave顺序复制。
# 在磁盘速度缓慢，网速快的情况下推荐用socket方式。
repl-diskless-sync no

# socket方式复制数据延迟时间（单位秒，默认5），以期更多的从站到达，谨慎设置0
# 一旦复制开始，节点不会再接收新slave的复制请求，从站则要排队等待下一次RDB传送
repl-diskless-sync-delay 5


# 从库向主库发PING包间隔（秒），默认10。
# repl-ping-slave-period 10

# 复制连接超时时间。master和slave都有超时时间的设置。
# master检测到slave上次发送的时间超过repl-timeout，即认为slave离线，清除该slave信息。
# slave检测到上次和master交互的时间超过repl-timeout，则认为master离线。
# 参照 repl-ping-slave-period 设置，否则经常检测到超时。
# repl-timeout 60

# 同步之后是否禁用从站上的TCP_NODELAY
# yes，redis会使用较少量的TCP包和带宽向从站发送数据。但这会导致在从站增加一点数据的延时，Linux内核默认配置情况下最多40毫秒的延时。
# no，从站的数据延时不会那么多，但备份需要的带宽相对较多。
# 在数据传输量大高负载情况下或者在主从站都跳的情况下，推荐yes
repl-disable-tcp-nodelay no

# 复制缓冲区大小
# slave离线的时候，不需要完全复制master的数据，只需要把缓冲区的部分数据复制给slave
# 缓冲区的大小越大，slave离线的时间可以更长
# 一个从站连接，就会立刻分配一个工作储备。
# 没有slave的一段时间，内存会被释放出来，默认1m。
# repl-backlog-size 1mb

# master没有slave一段时间会释放复制缓冲区的内存，单位秒
# 从断开的一刻开始计算，值为0表示不释放。
# repl-backlog-ttl 3600

# 当master不可用，Sentinel会根据slave的优先级选一个master，0不会被选
# 除0外最低优先级的slave当选master
slave-priority 100

# 健康的slave的个数小于多少，mater就禁止写入，0关闭该功能
# min-slaves-to-write 3

# 延迟小于多少秒的slave才认为是健康的slave
# 从最后一次收到从站的ping开始计算
# min-slaves-max-lag 10

# 常用于端口转发或NAT场景下，对Master暴露真实IP和端口信息
# slave-announce-ip 5.5.5.5
# slave-announce-port 1234

################################## SECURITY ###################################


# 设置redis连接密码
# requirepass foobared

# 将命令重命名，为了安全考虑，可以将某些重要的、危险的命令重命名
# 当你把某个命令重命名成空字符串的时候就等于取消了这个命令。
# rename-command CONFIG ""

################################### LIMITS ####################################

# 设置客户端最大并发连接数，默认0无限制，不区分连接是客户端连接还是内部打开文件或者和slave连接等，建议大于32
# maxclients 10000

# redis配置的最大内存容量。当内存满了，需要配合maxmemory-policy策略进行处理。
# 注意slave的输出缓冲区是不计算在maxmemory内的。所以为了防止主机内存使用完，建议设置的maxmemory需要更小一些。
# maxmemory <bytes>

# 内存容量超过maxmemory后的处理策略。
#  volatile-lru：利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used ) 
#  volatile-random：随机移除设置过过期时间的key
#  volatile-ttl：移除即将过期的key，根据最近过期时间来删除（辅以TTL）
#  allkeys-lru：利用LRU算法移除任何key
#  allkeys-random：随机移除任何key
#  noeviction：不移除任何key，只是返回一个写错误，默认
# maxmemory-policy noeviction

# lru检测的样本数。使用lru或者ttl淘汰算法，从需要淘汰的列表中随机选择sample个key，选出闲置时间最长的key移除
# maxmemory-samples 5

############################## APPEND ONLY MODE ###############################

# 默认redis使用的是rdb方式持久化，如果中途宕机，会导致可能有几分钟的数据丢失
# Append Only File是另一种持久化方式，Redis会把每次写入的数据在接收后都写入 appendonly.aof 文件，
# 每次启动时Redis都会先把这个文件的数据读入内存里，先忽略RDB文件。
appendonly no

# aof文件名
appendfilename "appendonly.aof"

# aof持久化策略的配置
# no 表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快。
# always 表示每次写入都执行fsync，以保证数据同步到磁盘。
# everysec 表示每秒执行一次fsync，可能会导致丢失这1s数据。
appendfsync everysec

# 在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，
# 执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no。
# 如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，
# 这样对持久化特性来说这是更安全的选择。设置为yes表示rewrite期间对新写操作不fsync,
# 暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。
# Linux的默认fsync策略是30秒。可能丢失30秒数据。
no-appendfsync-on-rewrite no

# aof自动重写配置。当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，
# 即当aof文件增长到一定大小的时候Redis能够调用bgrewriteaof对日志文件进行重写。
# 当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。
auto-aof-rewrite-percentage 100

# 设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写
auto-aof-rewrite-min-size 64mb

# aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。
# 重启可能发生在redis所在的主机操作系统宕机后，
# 尤其在ext4文件系统没有加上data=ordered选项（redis宕机或者异常终止不会造成尾部不完整现象。）
# 出现这种现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，
# 当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。
# 如果是no，用户必须手动redis-check-aof修复AOF文件才可以。
aof-load-truncated yes

################################ LUA SCRIPTING  ###############################
# Lua脚本的最大超时时间.
# 如果达到最大时间限制（毫秒），redis会记个log，然后返回error。
# 当一个脚本超过了最大时限。只有SCRIPT KILL和SHUTDOWN NOSAVE可以用。
# 第一个可以杀没有调write命令的东西。要是已经调用了write，只能用第二个命令杀。
lua-time-limit 5000

################################ REDIS CLUSTER  ###############################
# 集群开关，默认是不开启集群模式
# cluster-enabled yes

# 集群配置文件的名称，每个节点都有一个集群相关的配置文件，持久化保存集群的信息。
# 这个文件并不需要手动配置，这个配置文件有Redis生成并更新，
# 每个Redis集群节点需要一个单独的配置文件，请确保与实例运行的系统中配置文件名称不冲突
# cluster-config-file nodes-6379.conf

# 节点互连超时的阀值。集群节点超时毫秒数
# cluster-node-timeout 15000

# 在进行故障转移的时候，全部slave都会请求申请为master，
# 但是有些slave可能与master断开连接一段时间了，导致数据过于陈旧，
# 这样的slave不应该被提升为master。该参数就是用来判断slave节点与master断线的时间是否过长。
# 判断方法是：
# 比较slave断开连接的时间和(node-timeout * slave-validity-factor) + repl-ping-slave-period
# 如果节点超时时间为三十秒, 并且slave-validity-factor为10,
# 假设默认的repl-ping-slave-period是10秒，即如果超过310秒slave将不会尝试进行故障转移
# cluster-slave-validity-factor 10

# master的slave数量大于该值，slave才能迁移到其他孤立master上，
# 如这个参数若被设为2，那么只有当一个主节点拥有2 个可工作的从节点时，它的一个从节点会尝试迁移。
# cluster-migration-barrier 1

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least an hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
#
# cluster-require-full-coverage yes

# In order to setup your cluster make sure to read the documentation
# available at http://redis.io web site.

################################## SLOW LOG ###################################

# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################

# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at http://redis.io/topics/notifications
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  A     Alias for g$lshzxe, so that the "AKE" string means all the events.
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# Hashes are encoded using a memory efficient data structure when they have a
# small number of entries, and the biggest entry does not exceed a given
# threshold. These thresholds can be configured using the following directives.
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# -5: max size: 64 Kb  <-- not recommended for normal workloads
# -4: max size: 32 Kb  <-- not recommended
# -3: max size: 16 Kb  <-- probably not recommended
# -2: max size: 8 Kb   <-- good
# -1: max size: 4 Kb   <-- good
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
list-max-ziplist-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding in just one case: when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
set-max-intset-entries 512

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
zset-max-ziplist-entries 128
zset-max-ziplist-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When an HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
hll-sparse-max-bytes 3000

# Active rehashing uses 1 millisecond every 100 milliseconds of CPU time in
# order to help rehashing the main Redis hash table (the one mapping top-level
# keys to values). The hash table implementation Redis uses (see dict.c)
# performs a lazy rehashing: the more operation you run into a hash table
# that is rehashing, the more rehashing "steps" are performed, so if the
# server is idle the rehashing is never complete and some more memory is used
# by the hash table.
#
# The default is to use this millisecond 10 times every second in order to
# actively rehash the main dictionaries, freeing memory when possible.
#
# If unsure:
# use "activerehashing no" if you have hard latency requirements and it is
# not a good thing in your environment that Redis can reply from time to time
# to queries with 2 milliseconds delay.
#
# use "activerehashing yes" if you don't have such hard requirements but
# want to free memory asap when possible.
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
#
# The limit can be set differently for the three different classes of clients:
#
# normal -> normal clients including MONITOR clients
# slave  -> slave clients
# pubsub -> clients subscribed to at least one pubsub channel or pattern
#
# The syntax of every client-output-buffer-limit directive is the following:
#
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and slave clients, since
# subscribers and slaves receive data in a push fashion.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeout, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are performed with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
hz 10

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
aof-rewrite-incremental-fsync yes

```
