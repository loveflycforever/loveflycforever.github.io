1. Redis用过哪些数据数据，以及Redis底层怎么实现
Redis是一种key/value型数据库，其中，每个key和value都是使用对象表示的。    
/*  Redis 对象  */  
typedef struct redisObject {  
    // 类型  
    unsigned type:4;          
    // 不使用(对齐位)  
    unsigned notused:2;  
    // 编码方式  
    unsigned encoding:4;  
    // LRU 时间（相对于 server.lruclock）  
    unsigned lru:22;  
    // 引用计数  
    int refcount;  
    // 指向对象的值  
    void *ptr;  
} robj;  
type表示了该对象的对象类型，即上面五个中的一个。但为了提高存储效率与程序执行效率，每种对象的底层数据结构实现都可能不止一种。encoding就表示了对象底层所使用的编码。
Redis对象底层数据结构共有八种
REDIS_ENCODING_INT 	long 类型的整数
REDIS_ENCODING_EMBSTR 	embstr 编码的简单动态字符串
REDIS_ENCODING_RAW 	简单动态字符串
REDIS_ENCODING_HT 	字典
REDIS_ENCODING_LINKEDLIST 	双端链表
REDIS_ENCODING_ZIPLIST 	压缩列表
REDIS_ENCODING_INTSET 	整数集合
REDIS_ENCODING_SKIPLIST 	跳跃表和字典
字符串对象
字符串对象的编码可以是int、raw或者embstr。
如果一个字符串的内容可以转换为long，那么该字符串就会被转换成为long类型，对象的ptr就会指向该long，并且对象类型也用int类型表示。
普通的字符串有两种，embstr和raw。
embstr应该是Redis 3.0新增的数据结构,在2.8中是没有的。
如果字符串对象的长度小于39字节，就用embstr对象。否则用传统的raw对象。
需要注意的是，redis并未提供任何修改embstr的方式，即embstr是只读的形式。对embstr的修改实际上是先转换为raw再进行修改。
列表对象
列表对象的编码可以是ziplist或者linkedlist。
ziplist是一种压缩链表，它的好处是更能节省内存空间，因为它所存储的内容都是在连续的内存区域当中的。当列表对象元素不大，每个元素也不大的时候，就采用ziplist存储。整个ziplist只需要malloc一次，它们在内存中是一块连续的区域。
linkedlist是一种双向链表。它的结构比较简单，节点中存放pre和next两个指针，还有节点相关的信息。当每增加一个node的时候，就需要重新malloc一块内存。
哈希对象
哈希对象的底层实现可以是ziplist或者hashtable。
ziplist中的哈希对象是按照key1,value1,key2,value2这样的顺序存放来存储的。当对象数目不多且内容不大时，这种方式效率是很高的。
hashtable的是由dict这个结构来实现的，dict是一个字典，其中的指针dicht ht[2] 指向了两个哈希表，dicht[0] 是用于真正存放数据，dicht[1]一般在哈希表元素过多进行rehash的时候用于中转数据。 
集合对象
集合对象的编码可以是intset或者hashtable。
intset是一个整数集合，里面存的为某种同一类型的整数，支持三种长度的整数，INTSET_ENC_INT16、INTSET_ENC_INT32、INTSET_ENC_INT64
intset是一个有序集合，查找元素的复杂度为O(logN)，但插入时不一定为O(logN)，因为有可能涉及到升级操作。比如当集合里全是int16_t型的整数，这时要插入一个int32_t，那么为了维持集合中数据类型的一致，那么所有的数据都会被转换成int32_t类型，涉及到内存的重新分配，这时插入的复杂度就为O(N)了。是intset不支持降级操作。
有序集合对象
有序集合的编码可能两种，一种是ziplist，另一种是skiplist与dict的结合。
ziplist作为集合和作为哈希对象是一样的，member和score顺序存放。按照score从小到大顺序排列。
skiplist是一种跳跃表，它实现了有序集合中的快速查找，在大多数情况下它的速度都可以和平衡树差不多。但它的实现比较简单，可以作为平衡树的替代品。

2. Redis缓存穿透，缓存雪崩
一般的缓存系统，都是按照key去缓存查询，如果不存在对应的value，就应该去后端系统查找（比如DB）。如果key对应的value是一定不存在的，并且对该key并发请求量很大，就会对后端系统造成很大的压力。这就叫做缓存穿透。
当缓存服务器重启或者大量缓存集中在某一个时间段失效，这样在失效的时候，也会给后端系统(比如DB)带来很大压力。这就叫做缓存雪崩。
分布式缓存系统存在缓存一致性问题和缓存穿透和缓存雪崩
解决方案的话：
使用互斥锁排队
从一定程度上减轻数据库压力，但是锁机制使得逻辑的复杂度增加，吞吐量也降低了，有点治标不治本。
布隆过滤器
guava BloomFilter<Integer> bloomFilter = BloomFilter.create(Funnels.integerFunnel(), capacity);
 
3. 如何使用Redis来实现分布式锁
要确保锁的实现同时满足以下四个条件：
    互斥性。在任意时刻，只有一个客户端能持有锁。
    不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
    具有容错性。只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。
    加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。
加锁代码
String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
return LOCK_SUCCESS.equals(result)；
解锁代码
String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));
return RELEASE_SUCCESS.equals(result)；
这段Lua代码获取锁对应的value值，检查是否与requestId相等，如果相等则删除锁（解锁）。
在eval命令执行Lua代码的时候，Lua代码将被当成一个命令去执行，并且直到eval命令执行完成，Redis才会执行其他命令。

4. Redis的并发竞争问题如何解决
Redis的并发竞争问题，主要是发生在并发写竞争。
考虑到redis没有像db中的sql语句，update val = val + 10 where ...，无法使用这种方式进行对数据的更新。
方案1 利用redis自带的incr命令
方案2 可以使用独占锁的方式，类似操作系统的mutex机制。
方案3 使用乐观锁的方式进行解决（成本较低，非阻塞，性能较高）
方案4 这个是针对客户端来的，在代码里要对redis操作的时候，针对同一key的资源，就先进行加锁（java里的synchronized或lock）。
方案5 利用redis的setnx实现内置的锁。
    这个方法有一个特性，就是当键不存在的时候，会将这条数据插入，并且返回1，如果这个键已经存在了，那么就不会插入这条数据，并且返回0。
    在事务开启的时候，我们就去redis中setnx一条数据，这条数据的键要和你当前操作的数据有关，这样就只会锁定一条数据，而不影响其他数据的业务，例如：做订单审核的时候，将订单号+业务简写作为键。
    判断上面插入操作的返回值，如果返回1，就继续执行，如果返回0，直接return.
    在事务结束之后，将redis中的这条数据删除。直接使用del（String key）就可以了。

5. Redis持久化的几种方式，优缺点是什么，怎么实现的
RDB持久化可以在指定的时间间隔内生成数据集的时间点快照
AOF持久化记录服务器执行的所有写操作命令,并在服务器启动时,通过重新执行这些命令来还原数据集,AOF文件中全部以redis协议的格式来保存,新命令会被追加到文件的末尾,redis还可以在后台对AOF文件进行重写,文件的体积不会超出保存数据集状态所需要的实际大小,
redis还可以同时使用AOF持久化和RDB持久化,在这种情况下,当redis重启时,它会有限使用AOF文件来还原数据集,因为AOF文件保存的数据集通常比RDB文件所保存的数据集更加完
ＲＤＢ的优点
    RDB 是一个非常紧凑（compact）的文件，它保存了 Redis 在某个时间点上的数据集。 这种文件非常适合用于进行备份： 比如说，你可以在最近的 24 小时内，每小时备份一次 RDB 文件，并且在每个月的每一天，也备份一个 RDB 文件。 这样的话，即使遇上问题，也可以随时将数据集还原到不同的版本。
    RDB 非常适用于灾难恢复（disaster recovery）：它只有一个文件，并且内容都非常紧凑，可以（在加密后）将它传送到别的数据中心，或者亚马逊 S3 中。
    RDB 可以最大化 Redis 的性能：父进程在保存 RDB 文件时唯一要做的就是 fork 出一个子进程，然后这个子进程就会处理接下来的所有保存工作，父进程无须执行任何磁盘 I/O 操作。
    RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快
ＲＤＢ的缺点
    如果你需要尽量避免在服务器故障时丢失数据，那么 RDB 不适合你。 虽然 Redis 允许你设置不同的保存点（save point）来控制保存 RDB 文件的频率， 但是， 因为RDB 文件需要保存整个数据集的状态， 所以它并不是一个轻松的操作。 因此你可能会至少 5 分钟才保存一次 RDB 文件。 在这种情况下， 一旦发生故障停机， 你就可能会丢失好几分钟的数据。
    每次保存 RDB 的时候，Redis 都要 fork() 出一个子进程，并由子进程来进行实际的持久化工作。 在数据集比较庞大时， fork()可能会非常耗时，造成服务器在某某毫秒内停止处理客户端； 如果数据集非常巨大，并且 CPU 时间非常紧张的话，那么这种停止时间甚至可能会长达整整一秒。 虽然 AOF 重写也需要进行 fork() ，但无论 AOF 重写的执行间隔有多长，数据的耐久性都不会有任何损失。
AOF 的优点
    使用 AOF 持久化会让 Redis 变得非常耐久（much more durable）：你可以设置不同的 fsync 策略，比如无 fsync ，每秒钟一次 fsync ，或者每次执行写入命令时 fsync 。 AOF 的默认策略为每秒钟 fsync 一次，在这种配置下，Redis 仍然可以保持良好的性能，并且就算发生故障停机，也最多只会丢失一秒钟的数据（ fsync 会在后台线程执行，所以主线程可以继续努力地处理命令请求）。
    AOF 文件是一个只进行追加操作的日志文件（append only log）， 因此对 AOF 文件的写入不需要进行 seek ， 即使日志因为某些原因而包含了未写入完整的命令（比如写入时磁盘已满，写入中途停机，等等）， redis-check-aof 工具也可以轻易地修复这种问题。
    Redis 可以在 AOF 文件体积变得过大时，自动地在后台对 AOF 进行重写： 重写后的新 AOF 文件包含了恢复当前数据集所需的最小命令集合。 整个重写操作是绝对安全的，因为 Redis 在创建新 AOF 文件的过程中，会继续将命令追加到现有的 AOF 文件里面，即使重写过程中发生停机，现有的 AOF 文件也不会丢失。 而一旦新 AOF 文件创建完毕，Redis 就会从旧 AOF 文件切换到新 AOF 文件，并开始对新 AOF 文件进行追加操作。
    AOF 文件有序地保存了对数据库执行的所有写入操作， 这些写入操作以 Redis 协议的格式保存， 因此 AOF 文件的内容非常容易被人读懂， 对文件进行分析（parse）也很轻松。 导出（export） AOF 文件也非常简单： 举个例子， 如果你不小心执行了 FLUSHALL 命令， 但只要 AOF 文件未被重写， 那么只要停止服务器， 移除 AOF 文件末尾的 FLUSHALL 命令， 并重启 Redis ， 就可以将数据集恢复到 FLUSHALL 执行之前的状态。
AOF 的缺点
    对于相同的数据集来说，AOF 文件的体积通常要大于 RDB 文件的体积。
    根据所使用的 fsync 策略，AOF 的速度可能会慢于 RDB 。 在一般情况下， 每秒 fsync 的性能依然非常高， 而关闭 fsync 可以让 AOF 的速度和 RDB 一样快， 即使在高负荷之下也是如此。 不过在处理巨大的写入载入时，RDB 可以提供更有保证的最大延迟时间（latency）。
    AOF 在过去曾经发生过这样的 bug ： 因为个别命令的原因，导致 AOF 文件在重新载入时，无法将数据集恢复成保存时的原样。 

6. Redis的缓存失效策略
生存时间可以通过使用 DEL 命令来删除整个 key 来移除，或者被 SET 和 GETSET 命令覆盖原来的数据
对一个 key 执行INCR命令，对一个列表进行LPUSH命令，或者对一个哈希表执行HSET命令，这类操作都不会修改 key 本身的生存时间。
另一方面，如果使用RENAME对一个 key 进行改名，那么改名后的 key 的生存时间和改名前一样。
3种过期策略
定时删除
在设置key的过期时间的同时，为该key创建一个定时器，让定时器在key的过期时间来临时，对key进行删除
惰性删除
key过期的时候不删除，每次从数据库获取key的时候去检查是否过期，若过期，则删除，返回null。
定期删除
每隔一段时间执行一次删除(在redis.conf配置文件设置hz，1s刷新的频率)过期key操作
定时删除和定期删除为主动删除：Redis会定期主动淘汰一批已过去的key
惰性删除为被动删除：用到的时候才会去检验key是不是已过期，过期就删除
惰性删除为redis服务器内置策略
定期删除可以通过：
    第一、配置redis.conf 的hz选项，默认为10 （即1秒执行10次，100ms一次，值越大说明刷新频率越快，最Redis性能损耗也越大） 
    第二、配置redis.conf的maxmemory最大值，当已用内存超过maxmemory限定时，就会触发主动清理策略
过期key对RDB没有任何影响
    从内存数据库持久化数据到RDB文件，持久化key之前，会检查是否过期，过期的key不进入RDB文件
    从RDB文件恢复数据到内存数据库，数据载入数据库之前，会对key先进行过期检查，如果过期，不导入数据库（主库情况）
另外：
Redis的内存淘汰策略是指在Redis的用于缓存的内存不足时，怎么处理需要新写入且需要申请额外空间的数据。
    noeviction：当内存不足以容纳新写入数据时，新写入操作会报错。
    allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key。
    allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key。
    volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key。
    volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key。
    volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除。
    
7. Redis集群，高可用，原理
通过利用多台计算机内存的和值，允许我们构造更大的数据库。
通过多核和多台计算机，允许我们扩展计算能力；通过多台计算机和网络适配器，允许我们扩展网络带宽
涉及多个key的操作通常是不被支持的：
    当两个set映射到不同的redis实例上时，你就不能对这两个set执行交集操作。
    涉及多个key的redis事务不能使用。
    当使用分区时，数据处理较为复杂，比如你需要处理多个rdb/aof文件，并且从多个实例和主机备份持久化文件。
    增加或删除容量也比较复杂。redis集群大多数支持在运行时增加、删除节点的透明数据平衡的能力，但是类似于客户端分区、代理等其他系统则不支持这项特性。
水平分区是根据一些规则把同一业务单元的Key拆分到不同的Redis实例上。
按Range水平分区
最简单的分区方式是按范围分区，就是映射一定范围的对象到特定的Redis实例，比如，ID从0到10000的用户会保存到实例R0，ID从10001到 20000的用户会保存到R1，以此类推。
在实际中使用，不足就是要有一个区间范围到实例的映射表。这个表要被管理，同时还需要各种对象的映射表，通常对Redis来说并非是好的方法。
优点：规则简单、数据均衡性较好、比较容易扩展；
缺点：请求的负载不一定均衡，一般来说，新注册的用户会比老用户更活跃，大range的服务请求压力会更大。
对Key哈希水平切分
按照Key进行Hash，支持任意类型的Key，然后对得到的Hash值进行取模运算，分配到不同Redis实例上。
优点：规则简单、数据均衡性较好、请求均匀性较好；
缺点是：不容易扩展，扩展一个数据服务，hash方法改变时候，可能需要进行数据迁移。
垂直分区就是把一个Redis实例上的不同业务单元的Key拆分到不同的Redis实例上。
Master-Slave复制与Fail-Over来实现真正的高可用
详细步骤：
    在Slave启动并连接到Master之后，它将主动发送一个SYNC命令。
    此后Master将启动后台存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕后，Master将传送整个数据库文件到Slave，以完成一次完全同步。
    而Slave服务器在接收到数据库文件数据之后将其存盘并加载到内存中。
    此后，Master继续将所有已经收集到的修改命令，和新的修改命令依次传送给Slaves，Slave将在依次执行这些数据修改命令，从而达到最终的数据同步。
    如果Master和Slave之间的链接出现断连现象，Slave可以自动重连Master，但是在连接成功之后，一次完全同步将被自动执行。
同一个Master可以同步多个Slaves。
Slave同样可以接受其它Slaves的连接和同步请求，这样可以有效的分载Master的同步压力。因此我们可以将Redis的Replication架构视为图结构。
Master Server是以非阻塞的方式为Slaves提供服务。所以在Master-Slave同步期间，客户端仍然可以提交查询或修改请求。
Slave Server同样是以非阻塞的方式完成数据同步。在同步期间，如果有客户端提交查询请求，Redis则返回同步之前的数据。
为了分载Master的读操作压力，Slave服务器可以为客户端提供只读操作的服务，写服务仍然必须由Master来完成。即便如此，系统的伸缩性还是得到了很大的提高。
Master可以将数据保存操作交给Slaves完成，从而避免了在Master中要有独立的进程来完成此操作。
Redis-sentinel是2.6版开始加入的另一组独立运行的节点，提供自动Fail Over的支持
master地址在sentinel.conf里, sentinel会每10秒一次向master发送INFO，知道master的slave有哪些。
如果master已经变为slave，sentinel会分析INFO的应答指向新的master。
sentinel会在master上建一个pub/sub channel，名为”sentinel:hello”，通告各种信息，sentinel们也是通过接收pub/sub channel上的sentinel的信息发现彼此
基于Sentinel + master/slave + VIP漂移
如何实现VIP漂移？
    以使用redis sentinel的一个参数client-reconfig-script，这个参数配置执行脚本，sentinel在做failover的时候会执行这个脚本。
    漂移VIP也可以使用keepalived软件来实现。

8. Redis缓存分片
分片(partitioning)就是将你的数据拆分到多个 Redis 实例的过程，这样每个实例将只包含所有键的子集。
Redis 的分片承担着两个主要目标：
    允许使用很多电脑的内存总和来支持更大的数据库。没有分片，你就被局限于单机能支持的内存容量。
    允许伸缩计算能力到多核或多服务器，伸缩网络带宽到多服务器或多网络适配器。
客户端分片(Client side partitioning)意味着，客户端直接选择正确的节点来写入和读取指定键。许多 Redis 客户端实现了客户端分片。
代理协助分片(Proxy assisted partitioning)意味着，我们的客户端发送请求到一个可以理解 Redis 协议的代理上，而不是直接发送请求到 Redis 实例上。代理会根据配置好的分片模式，来保证转发我们的请求到正确的 Redis 实例，并返回响应给客户端。Redis 和 Memcached 的代理 Twemproxy 实现了代理协助的分片。
查询路由(Query routing)意味着，你可以发送你的查询到一个随机实例，这个实例会保证转发你的查询到正确的节点。Redis 集群在客户端的帮助下，实现了查询路由的一种混合形式 (请求不是直接从 Redis 实例转发到另一个，而是客户端收到重定向到正确的节点)。

9. Redis的数据淘汰策略
maxmemory在64位操作系统中默认是0，表示没有上限限制，在32位操作系统中，因为系统只能识别3GB内存，因此默认是3GB。
maxmemory可以在运行时动态设置，例如：CONFIG SET maxmemory "100mb"。
在已经设置了过期时间的数据中：
    淘汰最近未使用的一些数据，也就是volatile-lru策略
    随机淘汰一些数据，也就是volatile-random策略
    淘汰距离过期时间最近的一些数据，也就是volatile-ttl策略
在全部数据中
    淘汰最近未使用的一些数据，也就是allkeys-lru策略
    随机淘汰一些数据，也就是allkeys-random策略
不淘汰数据
    内存使用达到设定的内存上限时，用户试图存储新数据时会直接返回错误，也就是noeviction策略。
maxmemory-policy同样可以在运行时设置，用户可以根据内存的使用情况动态的修改淘汰策略。















