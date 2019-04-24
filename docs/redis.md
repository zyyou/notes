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

## 客户端常用指令
```shell
# -a 指定密码
redis-cli -h 127.0.0.1 -p 6379 -a 123456
# 列出所有key
keys *
# 列出zyy.test.为前缀的key
keys "zyy.test.*"
# 检查是否存在key
exists key
# 设置key值
set key value
# 读key值
get key
# 删除key
del key
# 删除当前库
flushdb
# 删除所有库
flushall
# 当前库中key总数
dbsize
# 选择0-15的库(redis-cli会在指令前显示当前库，如[1]，默认0不显示)
select index
# 计数，每次key值+1
incr key

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

# 默认情况下，集群全部的slot有节点负责，集群状态才为ok，才能提供服务。
# 设置为no，可以在slot没有全部分配的时候提供服务。不建议打开该配置，这样会造成分区的时候，
# 小分区的master一直在接受写请求，而造成很长时间数据不一致。
# cluster-require-full-coverage yes

################################## SLOW LOG ###################################

# 执行命令时间大于该值则记录在slow log中，单位微秒，1000000=1秒，slog log保存在内存中，所以没有IO操作。
# 执行时间比slowlog-log-slower-than大的请求记录到slowlog里面，单位微秒，所以1000000就是1秒。
# 负数禁用，0记录所有
slowlog-log-slower-than 10000

# 慢查询日志长度。当一个新的命令被写进日志的时候，最老的那个记录会被删掉
# 只要有足够的内存这个长度没有限制，可以通过 SLOWLOG RESET 释放内存
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# 延迟监控功能是用来监控redis中执行比较缓慢的一些操作，
# 用LATENCY打印redis实例在跑命令时的耗时图表。只记录大于等于下边设置的值的操作。
# 0的话，就是关闭监视。默认延迟监控功能是关闭的，
# 如果你需要打开，也可以通过CONFIG SET命令动态设置。
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################

# 键空间通知使得客户端可以通过订阅频道或模式，来接收那些以某种方式改动了 Redis 数据集的事件。
# 因为开启键空间通知功能需要消耗一些 CPU ，所以在默认配置下，该功能处于关闭状态。
# 参数可以是以下字符的任意组合，它指定了服务器该发送哪些类型的通知：
# K 键空间通知，所有通知以 __keyspace@__ 为前缀
# E 键事件通知，所有通知以 __keyevent@__ 为前缀
# g DEL 、 EXPIRE 、 RENAME 等类型无关的通用命令的通知
# $ 字符串命令的通知
# l 列表命令的通知
# s 集合命令的通知
# h 哈希命令的通知
# z 有序集合命令的通知
# x 过期事件：每当有过期键被删除时发送
# e 驱逐(evict)事件：每当有键因为 maxmemory 政策而被删除时发送
# A 参数 g$lshzxe 的别名
# 输入的参数中至少要有一个 K 或者 E，否则的话，不管其余的参数是什么，都不会有任何 通知被分发。
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# hash类型的数据结构在编码上可以使用ziplist和hashtable。
# ziplist的特点就是文件存储(以及内存存储)所需的空间较小,在内容较小时,性能和hashtable几乎一样。
# 因此redis对hash类型默认采取ziplist。如果hash中条目的条目个数或者value长度达到阀值,将会被重构为hashtable。
# 这个参数指的是ziplist中允许存储的最大条目个数，默认为512
hash-max-ziplist-entries 512

# ziplist中允许条目value值最大字节数，默认为64
hash-max-ziplist-value 64

# 正值表示按照数据项个数来限定每个quicklist节点上的ziplist长度。
# 比如，当这个参数配置成5的时候，表示每个quicklist节点的ziplist最多包含5个数据项。
# 负值，表示按照占用字节数来限定每个quicklist节点上的ziplist长度。这时，它只能取-1到-5这五个值，每个值含义如下：
#     -5: 每个quicklist节点上的ziplist大小不能超过64 Kb。（注：1kb => 1024 bytes）
#     -4: 每个quicklist节点上的ziplist大小不能超过32 Kb。
#     -3: 每个quicklist节点上的ziplist大小不能超过16 Kb。
#     -2: 每个quicklist节点上的ziplist大小不能超过8 Kb。（-2是Redis给出的默认值）
#     -1: 每个quicklist节点上的ziplist大小不能超过4 Kb。
list-max-ziplist-size -2

# 这个参数表示一个quicklist两端不被压缩的节点个数。
# 注：这里的节点个数是指quicklist双向链表的节点个数，而不是指ziplist里面的数据项个数。
# 实际上，一个quicklist节点上的ziplist，如果被压缩，就是整体被压缩的。
# 参数list-compress-depth的取值含义如下：
#     0: 是个特殊值，表示都不压缩。这是Redis的默认值。
#     1: 表示quicklist两端各有1个节点不压缩，中间的节点压缩。
#     2: 表示quicklist两端各有2个节点不压缩，中间的节点压缩。
#     3: 表示quicklist两端各有3个节点不压缩，中间的节点压缩。
#     依此类推…
# 由于0是个特殊值，很容易看出quicklist的头节点和尾节点总是不被压缩的，以便于在表的两端进行快速存取。
list-compress-depth 0

# 数据量小于等于配置值用intset，大于配置值用set
set-max-intset-entries 512

# sorted set中的元素个数，即(数据, score)对的数目超过128的时候，也就是ziplist数据项超过256的时候，ziplist会转成zset
zset-max-ziplist-entries 128

# 数据的长度超过了配置字节，ziplist会转成zset
zset-max-ziplist-value 64

# value大小小于等于配置值使用稀疏数据结构（sparse）
# 大于配置值使用稠密的数据结构（dense），一个比16000大的value是几乎没用的，
# 建议的value大概为3000。如果对CPU要求不高，对空间要求较高的
hll-sparse-max-bytes 3000

# 100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用。
# 当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。
# 如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存
activerehashing yes

# 对客户端输出缓冲进行限制可以强迫那些不从服务器读取数据的客户端断开连接，用来强制关闭传输缓慢的客户端。
# 对于normal client，第一个0表示取消hard limit，第二个0和第三个0表示取消soft limit，
# normal client默认取消限制，因为如果没有寻问，他们是不会接收数据的。
client-output-buffer-limit normal 0 0 0

# 对于slave client和MONITER client，如果client-output-buffer一旦超过256mb，
# 又或者超过64mb持续60秒，那么服务器就会立即断开客户端连接。
client-output-buffer-limit slave 256mb 64mb 60

# 对于pubsub client，如果client-output-buffer一旦超过32mb，
# 又或者超过8mb持续60秒，那么服务器就会立即断开客户端连接。
client-output-buffer-limit pubsub 32mb 8mb 60

# redis执行任务的频率为1s除以hz
hz 10

# 在aof重写的时候，如果yes，系统会每32MB执行一次fsync，可以避免过大的延迟峰值
aof-rewrite-incremental-fsync yes

```
