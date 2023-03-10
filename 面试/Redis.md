# Redis

## Redis到底是单线程还是多线程

常说的Redis 是单线程，主要是指 Redis 在网络 IO和键值对读写是采用一个线程来完成的，这也是 Redis 对外提供键值存储服务的核心流程。但对于 Redis 的其他功能来说，比如持久化、异步删除、集群数据同步等，其实都是由额外的线程执行的。

## Redis单线程的优劣势

1. 单线程的优势：

   - 使用单线程可以避免频繁的上下文切换
   - Redis 中有各种类型的数据操作，甚至包括一些事务处理，如果采用多线程，还可能因为加锁导致软件复杂度提升，更有可能会因为加解锁，甚至出现死锁，造成的性能损耗，所以使用单线程反而性能会更好
2. 单线程的劣势：
   - 无法发挥多核CPU的优势
   - 当删除大建，会导致服务阻塞
   - QPS达到瓶颈

## Redis单线程为什么还能这么快

1. 命令执行基于内存操作，一条命令在内存里操作的时间是几十纳秒。 

2. 命令执行是单线程操作，没有线程切换开销。 

3. 基于IO多路复用机制提升Redis的I/O利用率。 

4. 高效的数据存储结构：全局hash表以及多种高效数据结构，比如：跳表，压缩列表，链表等等

   ![](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302261924400.png)

## Redis底层数据是如何用跳表来存储的

跳表：将**有序链表**改造为支持近似“折半查找”算法，可以进行快速的插入、删除、查找操 作。

![image-20230226192601058](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302261929541.png)

## Redis Key过期了为什么内存没释放

1. SET 除了可以设置 key-value 之外，还可以设置 key 的过期时间，就像下面这样：

   ```java
   127.0.0.1:6379> SET tuling zhuge EX 120
   OK
   127.0.0.1:6379> TTL tuling
   (integer) 117
   ```

   此时如果你想修改 key 的值，但只是单纯地使用 SET 命令，而没有加上过期时间的参数， 那这个 key 的过期时间将会被擦除。

   ```java
   127.0.0.1:6379> SET tuling zhuge666
   OK
   127.0.0.1:6379> TTL tuling // key永远不过期了！
   (integer) ‐1
   ```

   导致这个问题的原因在于：**SET 命令如果不设置过期时间，那么 Redis 会自动擦除这个 key 的过期时间。**

   

   如果你发现 Redis 的内存持续增长，而且很多 key 原来设置了过期时间，后来发现过期时间丢失了，很有可能是因为这个原因导致的。 这时你的 Redis 中就会存在大量不过期的 key，消耗过多的内存资源。 所以，你在使用 SET 命令时，如果刚开始就设置了过期时间，那么之后修改这个 key，也 务必要加上过期时间的参数，避免过期时间丢失问题。

## Redis**过期键的删除策略？**

1. **定时删除**

   - 定时删除时给key都设置一个过期的时间，当达到删除时间节点时，立即执行对key的删除。
   - 优点：节约内存，到时间执行删除，释放内存空间；
   - 缺点：CPU资源占用率过高，当其他任务在执行时，会导致两者同时进行，会影响两者的效率，redis服务器的响应时间和指令吞吐量收到影响；
   - 总结：用处理器的性能换取存储空间（时间换空间），适用范围：小内存，强CPU。

2. **惰性删除**：数据到达过期时间，不做处理，只有访问这个键时才会检查它是否过期，如果过期则清除，返回不存在；如果未过期，返回数据

   - 优点：最大化地节约CPU资源，发现必须删除的时候才删除；
   - 缺点：如果大量过期键没有被访问，会一直占用大量内存，内存压力很大；
   - 总结：用存储空间换取处理器性能（拿空间换时间）；

3. **定期删除**：每隔一段时间就对一些键进行检查，删除其中过期的键(周期性轮询redis库中的时效性数据，采用随机抽取的策略，利用过期数据占比的方式控制删除频度)。该策略是惰性删除和定时删除的一个折中，既避免了占用大量CPU资源又避免了出现大量过期键不被清除占用内存的情况。

   - 优点1：CPU性能占用设置有峰值，检测频度可自定义设置；
   - 优点2：内存压力不是很大，长期占用内存的冷数据会被持续清理；
   - 缺点：难以确定删除操作执行的时长和频率。
   - 总结：周期性抽查存储空间 （随机抽查，重点抽查）

4. 三种策略对比

   | 删除策略 | 特点             | 对CPU资源                     | 总结               |
   | :------- | :--------------- | :---------------------------- | :----------------- |
   | 定时删除 | 节约内存，无占用 | 不分时段占用CPU资源，频度高   | 拿时间换空间       |
   | 惰性删除 | 内存占用严重     | 延时执行，CPU利用率高         | 拿空间换时间       |
   | 定期删除 | 内存定期随机清理 | 每秒花费固定的CPU资源维护内存 | 随机抽查，重点抽查 |

## Redis Key没设置过期时间为什么被Redis主动删除了

1. ### Redis内存淘汰策略

   - 在配置文件redis.conf 中，可以通过参数 maxmemory <bytes> 来设定最大内存，当数据内存达到 maxmemory 时，便会触发redis的内存淘汰策略（我们一般会将该参数设置为物理内存的四分之三）。
   - 当 Redis 的内存超过最大允许的内存之后，Redis 会触发内存淘汰策略。（过期策略是指正常情况下清除过期键，内存淘汰是指内存超过最大值时的保护策略）。
   - 内存淘汰策略可以通过maxmemory-policy进行配置，目前Redis提供了以下几种
     - 不进行数据淘汰策略
       - **noeviction：** 它是Redis3.0 之后，默认的内存淘汰策略，它表示当运行内存超过最大设置内存时，不淘汰任何数据，这时如果有新的数据写入，则会触发 OOM，但是如果没用数据写入的话，只是单纯的查询或者删除操作的话，还是可以正常工作；
     - 进行数据淘汰策略
       - 在设置了过期时间的数据中进行淘汰
         - **volatile-random**： 随机淘汰设置了过期时间的任意键值；
           **volatile-ttl**： 优先淘汰更早过期的键值；
           **volatile-lru**： 它是Redis3.0 之前，默认的内存淘汰策略，淘汰所有设置了过期时间的键值中，最久未使用的键值；
           **volatile-lfu**： Redis 4.0 后新增的内存淘汰策略，淘汰所有设置了过期时间的键值中，最少使用的键值；
       - 在所有数据范围内进行淘汰
         - **allkeys-random：** 随机淘汰任意键值；
         - **allkeys-lru：** 淘汰整个键值中最久未使用的键值；
         - **allkeys-lfu** Redis 4.0 后新增的内存淘汰策略，淘汰整个键值中最少使用的键值；

## LRU和LFU的区别

- LRU 算法(Least Recently Used，**最近最少使用**)：淘汰很久没被访问过的数据，以最近一 次访问**时间**作为参考。 

- LFU 算法(Least Frequently Used，**最不经常使用**)：淘汰最近一段时间被访问**次数**最少的数据，以次数作为参考。 

- 绝大多数情况我们都可以用LRU策略，当存在大量的热点缓存数据时，LFU可能更好点。

  某一个热点数据访问了很多次，但是近期一段时间没有被访问，采用LRU的话就会被删除掉。

## 删除Key的命令会阻塞Redis吗

- 有可能的，我们看下**DEL Key**命令的时间复杂度：
  -  删除单个字符串类型的 key ，时间复杂度为 O(1)。 
  - 删除单个列表、集合、有序集合或哈希表类型的 key ，时间复杂度为 O(M)， M 为以上数据结构内的元素数量。
- 如果删除的是列表、集合、有序集合或哈希表类型的 key，如果集合元素过多，是会阻塞 Redis的。对于这种情况我们可以借助scan这样的命令循环删除元素。 如果删除的是字符串类型的 key，但是key对应value比较大，比如有几百M，那么也是会 阻塞Redis的。这种bigkey是我们要尽量减少出现的情况。

## Redis主从、哨兵、集群架构优缺点比

- 主从模式

![image-20230226202541767](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262025820.png)

- 哨兵模式

  ![image-20230226202657688](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262026727.png)

  在主从的基础上增加哨兵节点去监控主节点状态，如果主节点异常会进行主从切换，全程自动化。

  - 缺点：在主从切换的时候有**访问瞬断**问题
  - 节点本身也不能存储过大，会影响同步效率和数据恢复
  - 并且单节点并发量也受限

- ​	集群模式

  ![image-20230226202715906](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262027941.png)

  可以水平扩容，到不超过1000个节点。
  将数据存到不同的节点中，并不会每个节点都重复存储。
  即使出现**访问瞬断**也会减少影响。

## Redis集群数据hash分片算法

- Redis Cluster 将所有数据划分为 16384 个 slots(槽位)，每个节点负责其中一部分槽位。 槽位的信息存储于每个节点中。

- 当 Redis Cluster 的客户端来连接集群时，它也会得到一份集群的槽位配置信息并将其缓存 在客户端本地。这样当客户端要查找某个 key 时，可以根据槽位定位算法定位到目标节 点。

- **槽位定位算法** 

  - Cluster 默认会对 key 值使用 crc16 算法进行 hash 得到一个整数值，然后用这个整数值对 16384 进行取模来得到具体槽位。

    ==**HASH_SLOT = CRC16(key) mod 16384**==

    再根据槽位值和Redis节点的对应关系就可以定位到key具体是落在哪个Redis节点上的

## Redis执行命令竟然有死循环阻塞Bug

- 如果你想随机查看 Redis 中的一个 key，Redis里有一个 **RANDOMKEY** 命令可以从 Redis 中随机取出一个 key，这个命令可能导致Redis死循环阻塞
- 前面的面试题讲过Redis对于过期Key的清理策略是定时删除与惰性删除两种方式结合来做 的，而 RANDOMKEY 在随机拿出一个 key 后，**首先会先检查这个 key 是否已过期**，如果 该 key 已经过期，那么 Redis 会删除它，这个过程就是惰性删除。但清理完了还不能结 束，Redis 还要找出一个没过期的 key，返回给客户端。 此时，Redis 则会继续随机拿出一个 key，然后再判断它是否过期，直到找出一个没过期的 key 返回给客户端。
- 这里就有一个问题了，如果此时 Redis 中，有大量 key 已经过期，但还未来得及被清理 掉，那这个循环就会持续很久才能结束，而且，这个耗时都花费在了清理过期 key 以及寻 找不过期 key 上，导致的结果就是，RANDOMKEY 执行耗时变长，影响 Redis 性能。 以上流程，其实是在 master 上执行的。
- 如果在 **slave 上执行 RANDOMEKY**，那么问题会更严重。 
- **slave 自己是不会清理过期 key，当一个 key 要过期时，master 会先清理删除它，之后 master 向 slave 发送一个 DEL 命令，告知 slave 也删除这个 key，以此达到主从库的数据一致性。**
- 假设Redis 中存在大量已过期还未被清理的 key，那在 slave 上执行 RANDOMKEY 时，就 会发生以下问题： 
  1. slave 随机取出一个 key，判断是否已过期。 
  2. key 已过期，但 slave 不会删除它，而是继续随机寻找不过期的 key。 
  3. 由于大量 key 都已过期，那 slave 就会寻找不到符合条件的 key，此时就会陷入死循 环。
- 也就是说，在 slave 上执行 RANDOMKEY，有可能会造成整个 Redis 实例卡死。 这其实是 Redis 的一个 Bug，这个 Bug 一直持续到 5.0 才被修复，修复的解决方案就是==**在 slave中最多找一定的次数，无论是否能找到，都会退出循环。**==

## Redis主从切换导致缓存雪崩

- 我们假设，slave 的机器时钟比 master 走得快很多。 
- 此时，Redis master里设置了过期时间的key，从 slave 角度来看，可能会有很多在 master 里没过期的数据其实已经过期了。 
- 如果此时操作主从切换，把 slave 提升为新的 master。 
- 它成为 master 后，就会开始大量清理过期 key，此时就会导致以下结果： 
  1.  master 大量清理过期 key，主线程可能会发生阻塞，无法及时处理客户端请求。 
  2. Redis 中数据大量过期，引发缓存雪崩。 
- 当 master与slave 机器时钟严重不一致时，对业务的影响非常大。 所以，我们一定要保证主从库的机器时钟一致性，避免发生这些问题。

## 缓存雪崩、缓存击穿、缓存穿透

1. ### 缓存雪崩

   - 缓存雪崩是指大量的应用请求无法在 Redis 缓存中进行处理，紧接着，应用将大量请求发送到数据库层，导致数据库层的压力激增。

     ![img](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262104321.png)

   - 原因

     - 缓存中有大量数据同时过期，导致大量请求无法得到处理。
     - Redis 缓存实例发生故障宕机了

   - 解决办法

     1. 避免给大量的数据设置相同的过期时间，设置随机的过期时间

     2. 通过**服务降级**（针对不同的数据采取不同的处理方式）

        ​    当业务应用访问的是非核心数据（例如电商商品属性）时，暂时停止从缓存中查询这些数据，而是直接返回预定义信息、空值或是错误信息；

        ​	当业务应用访问的是核心数据（例如电商商品库存）时，仍然允许查询缓存，如果缓存缺失，也可以继续通过数据库读取。

     3. 对于“Redis挂掉了，请求全部走数据库”这种情况，我们可以有以下的思路：

        - 事发前：实现Redis的高可用(主从架构+Sentinel（哨兵） 或者Redis Cluster（集群）)，尽量避免Redis挂掉这种情况发生。

        - 事发中：万一Redis真的挂了，我们可以设置本地缓存(ehcache)+限流(hystrix)，尽量避免我们的 数据库被干掉(起码能保证我们的服务还是能正常工作的)

          > 当业务应用访问的是非核心数据（例如电商商品属性）时，暂时停止从缓存中查询这些数据，而是直接返回预定义信息、空值或是错误信息；
          >
          > 当业务应用访问的是核心数据（例如电商商品库存）时，仍然允许查询缓存，如果缓存缺失，也可以继续通过数据库读取。

        - 事发后：redis持久化，重启后自动从磁盘上加载数据，快速恢复缓存数据。

2. ### 缓存穿透

   - 原因

     - **大量请求访问数据库不存在的数据**

       例如一个卖书的商城一直被请求查询茶叶产品，由于Redis缓存主要是用来缓存热点数据，对于数据库不存在的数据，是没法缓存的，这种异常流量就会直接到达数据库并且返回NULL

   - 解决方法
     1. 接口层增加校验。
        - 例如数据库的数据 id 一般都是递增的，如果请求 id = -10 这种参数，势必会绕过 Redis，要避免这种情况，可以进行参数的真实性检验等操作
     2. 当我们从数据库找不到的时候，我们也将这个空对象设置到缓存里边去。下次再请求的时候，就可以从缓存里边获取了。这种情况我们一般会将空对象设置一个较短的过期时间，如30秒。这样可以防止攻击用户反复用一个id进行暴力攻击。
     3. 采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被这个bitmap拦截掉，从而避免了对底层存储系统的查询压力

3. ### 缓存击穿

   - 原因
     - 缓存击穿是指缓**存中没有但数据库中有的数据**（一般是缓存时间到期），这时由于并发用户特别多，同时读缓存没读到数据，又同时去数据库去取数据，引起数据库压力瞬间增大，造成过大压力。和缓存雪崩不同的是，**缓存击穿指并发查同一条数据**，缓存雪崩是不同数据都过期了，很多数据都查不到从而查数据库。
   - 解决方法
     - 设置热点数据永不过期
     - 加互斥锁
       - 当发现缓存失效的时候，不是立即从数据库加载数据，而是先尝试获取分布式锁，只有获取锁成功才执行数据库查询和写数据到缓存的操作，获取锁失败则说明当前有线程在执行数据库查询操作，让当前线程睡眠一段时间后在重试，这样只让一个请求去数据库读取数据

## Redis的持久化机制

- RDB

  RDB全称Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。简单来说就是把内存中的所有数据都记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。快照文件称为RDB文件，默认是保存在当前运行目录。

- AOF

  AOF全称为Append Only File（追加文件）。Redis处理的每一个写命令都会记录在AOF文件，可以看做是命令日志文件。

- 混合持久化

  - 命令：aof-use-rdb-preamble yes

  - 在开启混合持久化的情况下，AOF 重写时会把 Redis 的持久化数据，以 RDB 的格式写入到 AOF 文件的开头，之后的数据再以 AOF 的格式化追加的文件的末尾。

1. ### RDB持久化触发情况

   - 执行save命令

     - save命令会导致**主进程**执行RDB，这个过程中其它所有命令都会被**阻塞**。只有在数据迁移时可能用到。

   - 执行bgsave命令
     bgsave开始时会**fork主进程得到子进程**，子进程共享主进程的内存数据。完成fork后读取内存数据并写入 RDB 文件。

     - 这个命令执行后会开启独立进程完成RDB，**主进程**可以持续处理用户请求，不受影响。
     - fork子进程的时候会有短暂的阻塞，子进程创建完成之后就不会再阻塞了
     - fork采用的是**copy-on-write**技术：

       - 当主进程执行读操作时，访问共享内存；
       - 当主进程执行写操作时，则会拷贝一份数据，执行写操作。

     ![image-20210725151319695](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302271545413.png)

   - Redis停机时

     - Redis停机时会执行一次save命令，实现RDB持久化。

   - 触发RDB条件时--执行bgsave

     ```properties
     # 900秒内，如果至少有1个key被修改，则执行bgsave ， 如果是save "" 则表示禁用RDB
     save 900 1  
     save 300 10  
     save 60 10000 
     ```

2. ### RDB优缺点

   - 优点：
     1、整个Redis数据库将只包含一个文件dump.rdb,方便持久化。
     2、容灾性好，方便备份。
     3、性能最大化，fork子进程来完成写操作，让主进程继续处理命令，所以是IO最大化。使用单独子进程来进行持久化，主进程不会进行任何IO操作，保证了redis的高性能
     4.相对于数据集大时，比AOF的启动效率更高。
   - 缺点：
     1. 数据安全性低。
        - RDB是间隔一段时间进行持久化，如果持久化，之间redis发生故障，会发生数据丢失。所以这种方式更适合数据要求不严谨的时候
     2. 由于RDB是通过fok子进程来协助完成数据持久化工作的，因此，如果当数据集较大时，可能会导致整个服务器停止服务几百毫秒，甚至是1秒钟。会占用cpu

3. ### AOF机制

   1. 如果生产中不小心执行了flushall或者flushDB命令，导致RDB文件被删除，那么可以通过AOF文件来恢复数据，因为flushall / flushDB命令也会被记载在AOF上，前提是开启了AOF。

      ```properties
      # 是否开启AOF功能，默认是no
      appendonly yes
      # AOF文件的名称
      appendfilename "appendonly.aof"
      ```

      

4. ### AOF同步策略

   - 每秒同步：异步完成，效率非常高，一旦系统出现宕机现象，那么这一秒钟之内修改的数据将会丢失
   - 每修改同步：同步持久化，每次发生的数据变化都会被立即记录到磁盘中，最多丢一条
   - 不同步：由操作系统控制，可能丢失较多数据

   ```properties
   # 表示每执行一次写命令，立即记录到AOF文件
   appendfsync always 
   # 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
   appendfsync everysec 
   # 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
   appendfsync no
   ```

   - ​	三种策略对比

   ![image-20210725151654046](G:/2022%E6%9C%80%E6%96%B0%E7%89%88Java%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF%E5%9B%BE/%E7%AC%AC4%E9%98%B6%E6%AE%B5%E2%80%94%E4%B8%AD%E9%97%B4%E9%94%AE&%E6%9C%8D%E5%8A%A1%E6%A1%86%E6%9E%B6/1%E3%80%81%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%BC%80%E5%8F%91%E6%A1%86%E6%9E%B6SpringCloud+RabbitMQ+Docker+Redis+%E6%90%9C%E7%B4%A2+%E5%88%86%E5%B8%83%E5%BC%8F%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%85%A8%E6%8A%80%E6%9C%AF%E6%A0%88%E8%AF%BE%E7%A8%8B/%E9%AB%98%E7%BA%A7%E7%AF%87/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/day03-%E5%88%86%E5%B8%83%E5%BC%8F%E7%BC%93%E5%AD%98/%E8%AE%B2%E4%B9%89/assets/image-20210725151654046.png?lastModify=1677419744)

   

   Redis也会在触发阈值时自动去重写AOF文件。阈值也可以在redis.conf中配置：

   ```properties
   # AOF文件比上次文件 增长超过多少百分比则触发重写
   auto-aof-rewrite-percentage 100
   # AOF文件体积最小多大以上才触发重写 
   auto-aof-rewrite-min-size 64mb 
   ```

5. ### AOF重写-rewrite

   因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过执行bgrewriteaof命令，可以让AOF文件执行重写功能，用最少的命令达到相同效果。

   ![image-20210725151729118](G:/2022%E6%9C%80%E6%96%B0%E7%89%88Java%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF%E5%9B%BE/%E7%AC%AC4%E9%98%B6%E6%AE%B5%E2%80%94%E4%B8%AD%E9%97%B4%E9%94%AE&%E6%9C%8D%E5%8A%A1%E6%A1%86%E6%9E%B6/1%E3%80%81%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%BC%80%E5%8F%91%E6%A1%86%E6%9E%B6SpringCloud+RabbitMQ+Docker+Redis+%E6%90%9C%E7%B4%A2+%E5%88%86%E5%B8%83%E5%BC%8F%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%85%A8%E6%8A%80%E6%9C%AF%E6%A0%88%E8%AF%BE%E7%A8%8B/%E9%AB%98%E7%BA%A7%E7%AF%87/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/day03-%E5%88%86%E5%B8%83%E5%BC%8F%E7%BC%93%E5%AD%98/%E8%AE%B2%E4%B9%89/assets/image-20210725151729118.png?lastModify=1677419875)

   如图，AOF原本有三个命令，但是`set num 123 和 set num 666`都是对num的操作，第二次会覆盖第一次的值，因此第一个命令记录下来没有意义。

6. ### AOF优缺点

   - 优点：
     1. 数据安全
     2. 通过append模式写文件，即使中途服务器宕机也不会破坏已经存在的内容，可以通过redis-check-aof工具解决数据一致性问题。
     3. AOF机制的rewrite模式。定期对AOF文件进行重写，以达到压缩的目的
   - 缺点：
     1. AOF文件比RDB文件大，且恢复速度慢。
     2. 数据集大的时候，比rdb启动效率低。
     3. 运行效率没有RDB高

7. **混合持久化**

   - 必须先开启AOF
   - 如果开启了混合持久化，AOF在重写时，不再是单纯将内存数据转换为RESP命令写入AOF文件，而是将重写这一刻之前的内存做RDB快照处理，并且将RDB快照内容和增量的AOF修改内存数据的命令存在一起，都写入新的AOF文件，新的文件一开始不叫
     appendonly.aof, 等到重写完新的AOF文件才会进行改名，覆盖原有的AOF文件，完成新旧两个AOF文件的替换。
     于是在Rdis重启的时候，可以先加载RDB的内容，然后再重放增量AOF日志就可以完全替代之前的AOF全量文件重放，因此重启效率大幅得到提升。

8. ### 线上持久化策略一般如何设置

   如果对性能要求较高，在Master最好不要做持久化，可以在某个Slave开启AOF备份数据， 策略设置为**每秒同步一次**即可。

## Redis如何线上备份

1、写crontab定时调度脚本，每小时都copy一份rdb或aof文件到另外一台机器中去，保留 最近48小时的备份 

2、每天都保留一份当日的数据备份到一个目录中去，可以保留最近1个月的备份 

3、每次copy备份的时候，都把太旧的备份给删

## Redis主从复制风暴是怎么回事

如果Redis主节点有很多从节点，在某一时刻如果所有从节点都同时连接主节点，那么主节 点会同时把内存快照RDB发给多个从节点，这样会导致Redis主节点压力非常大，这就是所 谓的Redis主从复制风暴问题。

这种问题我们对Redis主从架构做一些优化得以避免，比如可以做下面这种树形复制架构。

![image-20230226225558551](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262255592.png)

## Redis集群网络抖动导致频繁主从切换怎么处理

真实世界的机房网络往往并不是风平浪静的，它们经常会发生各种各样的小问题。比如网络 抖动就是非常常见的一种现象，突然之间部分连接变得不可访问，然后很快又恢复正常。 为解决这种问题，Redis Cluster 提供了一种选项cluster­node­timeout，表示当某个 节点持续 timeout 的时间失联时，才可以认定该节点出现故障，需要进行主从切换。如果没有这个选项，网络抖动会导致主从频繁切换 (数据的重新复制)。

## Redis集群为什么至少需要三个master节点

因为新master的选举需要大于半数的集群master节点同意才能选举成功，如果只有两个 master节点，当其中一个挂了，是达不到选举新master的条件的。

## Redis集群为什么推荐奇数个节点

因为新master的选举需要大于半数的集群master节点同意才能选举成功，奇数个master节 点可以在满足选举该条件的基础上节省一个节点，比如三个master节点和四个master节点 的集群相比，大家如果都挂了一个master节点都能选举新master节点，如果都挂了两个 master节点都没法选举新master节点了，所以奇数的master节点更多的是从节省机器资源 角度出发说的。

## Redis主从复制的核心原理

## 如何保证数据库与缓存的一致性

- 由于缓存和数据库是分开的，无法做到原子性的同时进行数据修改，可能出现缓存更新失败，或者数据库更新失败的情况，这时候会出现数据不一致，影响前端业务

  - 先更新数据库，再更新缓存。缓存可能更新失败，读到老数据

  - 先删缓存，再更新数据库。并发时，读操作可能还是会将旧数据读回缓存

  - 先更新数据库，再删缓存。也存在缓存删除失败的可能

    > **经典模式**
    >
    > 读的时候，先读缓存，缓存没有的话，就读数据库，然后取出数据后放入缓存，同时返回响应。
    > 更新的时候，先更新数据库，然后再删除缓存。

- 为什么是删除而不是更新？

  - 删除更加轻量，延迟加载是一种体现，更新可能涉及多个表，比较耗时

- 延时双删

  先删除缓存，再更新数据库，**休眠1s**、再次删除缓存。写数据的休眠时间则在读数据业务逻辑的耗时基础上，加几百ms即可。这么做的目的，就是确保读请求结束，写请求可以删除读请求造成的缓存脏数据，并发还是可能读到旧值覆盖缓存

  - 再删除一次的是避免更新数据库期间的读操作更新了缓存

- 终极方案

  - 将访问操作串行化
    1.先删缓存，将更新数据库的操作放进有序队列中（队列先进先出）
    2.从缓存查不到的查询操作，都进入有序队列
  - 会面临的问题：
    1.读请求积压，大量超时，导致数据库的压力：限流、熔断
    2.如何避免大量请求积压：将队列水平拆分，提高并行度。
    3.保证相同请求路由正确

## Redis如何配置key的过期时间？他的实现原理是什么？

redis设置过期时间：
expire key time(以秒为单位)–这是最常用的方式
setex(String key, int seconds, String value)–字符串独有的方式

```java
// 两条命令分别设置值和过期时间
	// 时间单位：秒
	redis.expire("key",6);
	// 时间单位：毫秒
	redis.pexpire("key",6000L);

	expire (key seconds)        // 为给定 key 设置过期时间，以秒计。
    pexpire (key milliseconds)  // 设置 key 的过期时间以毫秒计。

```



实现原理->过期key处理策略

## Redis有哪些数据结构？分别有哪些典型的应用场景？

- Redis的数据结构有：
  1. **字符串**：可以用来做最简单的数据缓存，可以缓存某个简单的字符串，也可以缓存某个json格式的字符串，Redis:分布式锁的实现就利用了这种数据结构，还包括可以实现计数器、Session共享、分布式ID
  2. **哈希表**：可以用来存储一些key-value对，更适合用来存储对象
  3. **列表**：Redis的列表通过命令的组合，既可以当做栈，也可以当做队列来使用，可以用来缓存类似微信公众号、微博等消息流数据
  4. **集合**：和列表类似，也可以存储多个元素，但是不能重复，集合可以进行交集、并集、差集操作，从而可以实现类似，我和某人共同关注的人、朋友圈点赞等功能
  5. **有序集合**：集合是无序的，有序集合可以设置顺序，可以用来实现排行榜功能

## 简述redis九大数据结构

[Redis数据结构_makersy的博客-CSDN博客](https://blog.csdn.net/makersy/article/details/123826886)

![在这里插入图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262337244.png)

## 常见的缓存淘汰算法？

- FIFO(First In First Out,先进先出)，根据缓存**被存储**的时间，离当前最远的数据优先被淘汰：
- LRU(Least Recently Used,最近最少使用)，根据最近**被使用**的时间，离当前最远的数据优先被淘汰；
- LFU(Least Frequently Used,最不经常使用)，在一段时间内，缓存数据被使用次数最少的会被淘汰。

## 分布式系统用中常用的缓存方案有哪些？

- 客户端缓存：⻚⾯和浏览器缓存，APP缓存，H5缓存，localStorage 和 sessionStorage CDN缓存：内容存储：数据的缓存，内容分发：负载均衡
- nginx缓存：静态资源
- 服务端缓存 ： 本地缓存，外部缓存
- 数据库缓存 ： 持久层缓存（mybatis，hibernate多级缓存） ，mysql查询缓存 操作系统缓存：PageCache、BufferCache

![在这里插入图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302262344572.png)

## Redis高可用方案

- 主从->哨兵模式
- Redis Cluster
- Redis Sharding

## 布隆过滤器原理以及优缺点

[布隆过滤器的原理，优缺点_C位出道_2022的博客-CSDN博客_布隆过滤器优缺点](https://blog.csdn.net/m0_53611007/article/details/120953964)

[布隆过滤器的原理+优缺点+使用场景+项目具体使用_零点起航的博客-CSDN博客_布隆过滤器原理](https://blog.csdn.net/csyuyaoxiadn/article/details/108442983)

- 位图：int[10],每个int类型的整数是4*8=32个bit,则int[10]一共有320bit,每个bit非0即1，初始化时都是0
- 添加数据时，将数据进行hash得到hash值，对应到bit位，将该bit改为1，hash函数可以定义多个，则一个数据添加会将多个(hash函数个数)bit改为1，多个hash函数的目的是减少hash碰撞的概率
- 查询数据：hash函数计算得到hash值，对应到bit中，如果有一个为0，则说明数据不在bit中，如果都为1，则该数据可能在bit中

- 优点：
  - 占用内存小
  - 增加和查询元素的时间复杂度为：O(K,(K为哈希函数的个数，一般比较小)，与数据量大小无关
  - 哈希函数相互之间没有关系，方便硬件并行运算
  - 布隆过滤器不需要存储元素本身，在某些对保密要求比较严格的场合有很大优势
  - 数据量很大时，布隆过滤器可以表示全集
  - 使用同一组散列函数的布隆过滤器可以进行交、并、差运算

## 一个字符串类型的值能存储最大容量是多少？

512M

## 为什么 Redis 需要把所有数据放到内存中？

Redis 为了达到最快的读写速度将数据都读到内存中，并通过**异步**的方式将数 据写入磁盘。所以 redis 具有快速和数据持久化的特征。如果不将数据放在内存中， 磁盘 I/O 速度为严重影响 redis 的性能。在内存越来越便宜的今天，redis 将会越 来越受欢迎。如果设置了最大使用的内存，则数据已有记录数达到内存限值后不 能继续插入新值。

## Redis 集群如何选择数据库

Redis 集群目前无法做数据库选择，默认在 0 数据库。

## 怎么测试 Redis 的连通性？

使用 ping 命令。

## Redis 最适合的场景？

1、会话缓存（Session Cache）

最常用的一种使用 Redis 的情景是会话缓存（session cache）。用 Redis 缓存会 话比其他存储（如 Memcached）的优势在于：Redis 提供持久化。当维护一个不 是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不 高兴的，现在，他们还会这样吗？ 幸运的是，随着 Redis 这些年的改进，很容 易找到怎么恰当的使用 Redis 来缓存会话的文档。甚至广为人知的商业平台 Magento 也提供 Redis 的插件。 

2、全页缓存（FPC）

 除基本的会话 token 之外，Redis 还提供很简便的 FPC 平台。回到一致性问题， 即使重启了 Redis 实例，因为有磁盘的持久化，用户也不会看到页面加载速度的 下降，这是一个极大改进，类似 PHP 本地 FPC。 再次以 Magento 为例，Magento 提供一个插件来使用 Redis 作为全页缓存后端。 此外，对 WordPress 的用户来 说，Pantheon 有一个非常好的插件 wp-redis，这个插件能帮助你以最快速度加 载你曾浏览过的页面。

3、队列

Reids 在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得 Redis 能作为一个很好的消息队列平台来使用。Redis 作为队列使用的操作，就类似于本 地程序语言（如 Python）对 list 的 push/pop 操作。 如果你快速的在 Google 中搜索“Redis queues”，你马上就能找到大量的开源项目，这些项目的目的就 是利用 Redis 创建非常好的后端工具，以满足各种队列需求。例如，Celery 有一 个后台就是使用 Redis 作为 broker，你可以从这里去查看。

4，排行榜/计数器 

Redis 在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序 集合（Sorted Set）也使得我们在执行这些操作的时候变的非常简单，Redis 只是 正好提供了这两种数据结构。所以，我们要从排序集合中获取到排名最靠前的 10 个用户–我们称之为“user_scores”，我们只需要像下面一样执行即可： 当然， 这是假定你是根据你用户的分数做递增的排序。如果你想返回用户及用户的分数， 你需要这样执行： ZRANGE user_scores 0 10 WITHSCORES Agora Games 就 是一个很好的例子，用 Ruby 实现的，它的排行榜就是使用 Redis 来存储数据的， 你可以在这里看到。 

5、发布/订阅 

最后（但肯定不是最不重要的）是 Redis 的发布/订阅功能。发布/订阅的使用场景 确实非常多。我已看见人们在社交网络连接中使用，还可作为基于发布/订阅的脚 本触发器，甚至用 Redis 的发布/订阅功能来建立聊天系统！