# Redis

### redis与其他数据库对比

| 名称      | 类型                                      | 数据存储选项                                                 | 查询类型                                                     | 附加功能                                                     |
| --------- | ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Redis     | 使用内存存储（inmemeory）的非关系数据库   | 字符串、列表、集合、散列表、有序集合                         | 每种数据类型都有自己的专属命令，另外还有批量操作（bulk operation）和不完全（partial）的事务支持 | 发布与订阅，主从复制（master/slave replication），持久化，脚本（存储过程，stored procedure） |
| memcached | 使用内存存储的键值缓存                    | 键值之间的映射                                               | 创建命令、读取命令、更新命令、删除命令以及其他几个命令       | 为提升性能儿设的多线程服务器                                 |
| MySQL     | 关系数据库                                | 每个数据库可以包含多个表，每个表可以包含多个行；可以处理表的视图（View）；支持空间（spatial）和第三方扩展 | select、insert、update、delete、内置函数、自定义的存储过程   | 支持ACID性质，主从复制，由第三方支持的多主复制（multi-master replication） |
| MongoDB   | 使用硬盘存储（on-disk）的费关系型文档存储 | 每个数据库可以包含多个表，每个表可以包含多个无schema（schema-less）的BSON文档 | 创建命令、读取命令、更新命令、删除命令、条件查询命令等       | 支持map-reduce操作，主从复制，分片，控件索引（spatial index） |

### Redis持久化方法

~~~
	时间点转存（Point-in-time dump），转存操作即可在“指定时间段内有指定数量的写操作执行”这一条件被满足时执行，又可以通过调用两条转存到硬盘（dump-to-disk）命令中的任何一条来执行。
	将所有修改了数据库的命令都写入一个只追加（append-only file，AOF）文件里面，用户可以根据数据的重要程度，将只追加写入设置为从不同步（sync）、每秒同步一次或者每写入一个命令就同步一次。
~~~

### Redis数据结构

| 结构类型         | 结构存储的值                                                 | 结构的读写能力                                               |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| STRING           | 可以是字符串、整数或者浮点数                                 | 对整个字符串或者字符串的其中一部分执行操作；对整数和浮点数执行自增（increment）或者自减（decrement）操作 |
| LIST             | 一个链表，连表上的每个节点都包含一个字符串                   | 从链表两端推入或者弹出元素；根据偏移量对链表进行修建（trim）；读取单个或者多个元素；根据值查找或者移除元素 |
| SET              | 包含字符串的无序收集器（unordered collection），并且被包含的每个字符串都是独一无二、各不相同的 | 添加、获取、移除单个元素；检查一个元素是否存在与集合中；计算交集、并集、差集；从集合里面随机获取元素 |
| HASH             | 包含键值对的无序散列表                                       | 添加、获取、移除单个键值对；获取所有键值对                   |
| ZSET（有序集合） | 字符串成员（member）与浮点数分值（score）之间的有序映射，元素的排列顺序由分值的大小决定 | 添加、获取、删除单个元素；根据分值范围（range）或者成员来获取元素 |

#### 字符串命令（String）

| 命令 | 行为                                                   |
| ---- | ------------------------------------------------------ |
| GET  | 获取存储在给定键中的值                                 |
| SET  | 设置存储在给定键中的值                                 |
| DEL  | 删除存储在给定键中的值（这个命令可以用于所有数据类型） |

#### 列表命令（List）

| 命令   | 行为                                     |
| ------ | ---------------------------------------- |
| RPUSH  | 将给定值推入列表的右端                   |
| LRANGE | 获取列表在给定范围上的所有值             |
| LINDEX | 获取列表在给定位置上的单个元素           |
| LPOP   | 从列表的左端弹出一个值，并返回被弹出的值 |

#### 集合命令（Set）

| 命令      | 行为                                         |
| --------- | -------------------------------------------- |
| SADD      | 将元素添加到集合                             |
| SMEMBERS  | 返回集合包含的所有元素                       |
| SISMEMBER | 检查给定元素是否存在于集合中                 |
| SREM      | 如果给定的元素存在于集合中，那么移除这个元素 |
| SINTER    | 交集计算                                     |
| SUNION    | 并集计算                                     |
| SDIFF     | 差集计算                                     |

#### 散列命令（Hash）

| 命令    | 行为                                     |
| ------- | ---------------------------------------- |
| HSET    | 在散列里面关联起给定的键值对             |
| HGET    | 获取指定散列键的值                       |
| HGETALL | 获取散列包含的所有键值对                 |
| HDEL    | 如果给定键存在于散列里面，那么移除这个键 |

#### 有序集合命令

| 命令          | 行为                                                       |
| ------------- | ---------------------------------------------------------- |
| ZADD          | 将一个带有给定分值的成员添加到有序集合里面                 |
| ZRANGE        | 根据元素在有序排列中所处的位置，从有序集合里面获取多个元素 |
| ZRANGEBYSCORE | 获取有序集合在给定分值范围类的所有元素                     |
| ZREM          | 如果给定成员存在于有序集合中，那么移除这个成员             |

#### 签名cookie和令牌cookie的优点与缺点

| Cookie类型 | 有点                                                         | 缺点                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 签名cookie | 验证cookie所需的一切信息都存储在cookie里面。cookie可以包含额外的信息（additional Information），并且对这些信息进行签名也很容易 | 正确的处理签名很难。很容易忘记对数据进行签名，或者忘记验证数据的签名，从而造成安全漏洞 |
| 令牌cookie | 添加信息非常容易。cookie的体积非常小，因此移动终端和速度较慢的客户端可以更快的发送请求 | 需要在服务器中存储更多信息。如果使用的是关系型数据库，那么载入和存储cookie的代价可能会很高 |

#### Redis中的自增命令和自建命令

| 命令        | 用例和描述                                                  |
| ----------- | ----------------------------------------------------------- |
| INCR        | incre key-name（将键存储的值加上1）                         |
| DCRE        | dcre key-name（将键存储的值减去1）                          |
| INCRBY      | incrby key-name amount（将键存储的值加上整数amount）        |
| DECRBY      | decrby key-name amount（将键存储的值减去整数amount）        |
| INCRBYFLOAT | incrbyfloat key-name amount（将键存储的值加上浮点数amount） |

#### Redis处理子串和二进制为的命令

| 命令     | 用例和描述                                                   |
| -------- | ------------------------------------------------------------ |
| APPEND   | append key-name value（将值value追加到给定键key-name当前存储的值得末尾） |
| GETRANGE | getrange key-name start end（获取一个由偏移量Start至偏移量end范围内的所有字符组成的子串，包括start和end在内） |
| SETRANGE | setrange key-name offset value（将从start偏移量开始的子串设置为给定值） |
| GETBIT   | getbit key-name offset（将字节串看作是二进制串(bit string)，并返回位串中偏移量为offset的二进制位的值） |
| SETBIT   | setbit key-name offset value（将字节串看作是二进制串，并将位串中偏移量为offset的二进制位设置为value） |
| BITCOUNT | bitcount key-name [start end]（统计二进制位串里面值为1的二进制位的数量，如果给顶了可选的start偏移量和end偏移量，那么支队偏移量指定范围内的二进制位进行统计） |
| BITOP    | bitop operation dest-key key-name [key-name ...]（对一个或多个二进制位串执行包括并(AND)、或(OR)、异或(XOR)、非(NOT)在内的任意一种按位运算操作(bitwise opreation)，并将计算得出的结果保存在dest-key键里面） |

#### Redis列表常用命令

| 命令       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| RPUSH      | rpush key-name value [value...]（将一个或多个值推入列表的右端） |
| LPUSH      | lpush key-name value [value...]（将一个或多个值推入列表的左端） |
| RPOP       | rpop key-name（移除并返回表最右端的元素）                    |
| LPOP       | lpop key-name（移除并返回表最左端的元素）                    |
| LINDEX     | lindex key-name offset（返回列表中偏移量为offset的元素）     |
| LRANGE     | lrange key-name start end（返回列表从start偏移量到end偏移量范围内的元素，其中偏移量为start和偏移量为end的元素也会被保留） |
| LTRIM      | ltrim key-name start end（对列表进行修剪，只保留从start偏移量到end偏移量的所有元素，其中偏移量为start和偏移量为end的元素也会被保留） |
|            | **阻塞式的列表弹出命令以及在列表之间移动元素的命令**         |
| BLPOP      | blpop key-name [key-name ...] timeout（从第一个非空列表中弹出位于最左端的元素，或者在timeout秒之内阻塞并等待可弹出的元素出现） |
| BRPOP      | brpop key-name [key-name ...] timeout（从第一个非空列表中弹出位于最右端的元素，或者在timeout秒之内阻塞并等待可弹出的元素出现） |
| RPOPLPUSH  | rpoplpush source-key dest-key（从source-key列表中弹出最右端的元素，然后将这个元素推入dest-key列表的最左端，并向用户返回这个元素） |
| BRPOPLPUSH | brpoplpush source-key dest-key（从source-key 列表中弹出位于最右端的元素，然后将这个元素推入dest-key列表的最左端，并向用户返回这个元素；如果source-key为空，那么在timeout秒之内阻塞并等待可弹出的元素出现） |

#### Redis常用的集合命令

| 命令         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| SADD         | sadd key-name item [item ...]（将一个或多个元素添加到集合里里面，并返回被添加元素当中原本不存在于集合里面的元素数量） |
| SREM         | srem key-name item [item ...]（将从几何里面移除一个或多个元素，并返回被移出元素的数量） |
| SISMEMBER    | sismember key-name item（检查元素item是否存在于集合key-name里） |
| SCARD        | scard key-name（返回集合包含的元素的数量）                   |
| SMEMBERS     | smembers key-name（返回集合包含的所有元素）                  |
| SRANGEMEMBER | srangemember key-name [count]（从几何里面随机地返回一个或多个元素。当count为正数时，命令返回的随机元素不会重复；当count为负数时，命令返回的随机元素可能会出现重复） |
| SPOP         | spop key-name（随机地移除集合中的一个元素，并返回被移出的元素） |
| SMOVE        | smove source-key dest-key item（如果集合source-key包含元素item，那么从几何source-key里面移除元素item，并将元素item添加到集合dest-key中；如果item被成功移除，那么命令返回1，否则返回0） |
|              | **用于组合和处理多个集合的Redis命令**                        |
| SDIFF        | sdiff key-name [key-name ...]（返回存在于第一个集合、但不存在于其他集合中的元素---数学上的差集运算） |
| SDIFFSTORE   | sdiffstore dest-key key-name [key-name ...]（将存在于第一个集合但并不存在于其他集合中的元素(数学上的差集运算)存储到dest-key键里面） |
| SINTER       | sinter key-name [key-name ...] （返回那些同时存在于所有集合中的元素(数学上的交集运算)） |
| SINTERSTORE  | snterstore dest-key key-name [key-name ...]（将那些同时存在于所有集合中的元素(数学上的交集运算)存储到dest-key键里面） |
| SUNION       | sunion key-name [key-name ...]（返回那些至少存在于一个集合中的元素(数学上的并集运算)） |
| SUNIONSTORE  | sunionstore dest-key key-name [key-name ...]（将那些至少存在于一个集合中的元素(数学上的并集运算)存储到dest-key键里面） |

#### Redis散列操作

|              | 用例和描述                                                   |
| ------------ | ------------------------------------------------------------ |
|              | **用于添加和删除键值对的散列操作**                           |
| HMGET        | hmget key-name key [key ...]（从散列里面获取一个或多个键的值） |
| HMSET        | hmset key-name key value [key value ...]（为散列里面的一个或多个键设置值） |
| HDEL         | hdel key-name key [key ..]（删除散列里面的一个或多个键值对，返回成功找到并删除的键值对数量） |
| HLEN         | hlen key-name（返回散列包含的键值对数量）                    |
|              | **散列的高级特性**                                           |
| HEXISTS      | hexists key-name key（检查给定键是否存在散列中）             |
| HKEYS        | hkeys key-name（获取散列包含的所有键）                       |
| HVALS        | hvals key-name（获取散列包含的所有值）                       |
| HGETALL      | hgetall key-name（获取散列包含的所有键值对）                 |
| HINCRBY      | hincrby key-name key increment（将键key存储的值加上整数increment） |
| HINCRBYFLOAT | hincrbyfloat key-name key increment（将键key存储的值加上浮点数increment） |

#### Redis有序集合操作

| 命令             | 用例和描述                                                   |
| ---------------- | ------------------------------------------------------------ |
|                  | **常用的有序集合命令**                                       |
| ZADD             | zadd key-name score member [score member ...]（将带有给定分值的成员添加到有序集合里面） |
| ZREM             | zrem key-name member [member ...]（从有序集合里面移除给定的成员，并返回被移除成员的数量） |
| ZCARD            | zcard key-name（返回有序集合包含的成员数量）                 |
| ZINCRBY          | zincrby key-name increment member（将member成员的分值加上increment） |
| ZCOUNT           | zcount key-name min max（返回分值介于min和max之间的成员数量） |
| ZRANK            | zrank key-name member（返回成员member在有序集合中的排名）    |
| ZSCORE           | zscore key-name member（返回成员member的分值）               |
| ZRANGE           | zrange key-name start stop [withscores]（返回有序集合中排名介于start和stop之间的成员，如果给定了可选项withscores选项，那么命令会将成员的分值一并返回） |
|                  | **高级特性**                                                 |
| ZREVRANK         | zrevrank key-name member（返回有序集合里成员member的排名，成员按照分值从大到小排列） |
| ZREVRANGE        | zrevrange key-name start stop [withscores]（返回有序集合给定排名范围内的成员，成员按照分值从大到小排列） |
| ZRANGEBYSCORE    | zrangebyscore key-name min max [withscores] [limit offset count]（返回有序集合中，分值介于min和max之间的所有成员） |
| ZREVRANGEBYSCORE | zrevrangebyscore key-name max min [withscores] [limit offset count]（返回有序集合中分值介于min和max之间的所有成员，并按照分值从大到小的顺序来返回他们） |
| ZREMRANGEBYRANK  | zremrangebyrank key-name start stop（移除有序集合中排名介于start和stop之间的所有成员） |
| ZREMRANGEBYSCORE | zremrangebyscore key-name min max（移除有序集合中分值介于min和max之间的所有成员） |
| ZINTERSTORE      | zinterstore dest-key key-count key [key ...] [weights weight [weight ...]] [aggregate sum\|min\|max]（对给定的有序集合执行类似于集合的交集运算） |
| ZUNIONSTORE      | zunionstore dest-key key-count key [key ...] [weights weight [weight ...]] [aggregate sum\|min\|max]（对给定的有序集合执行类似于集合的并集运算） |

#### Redis发布订阅命令

| 命令         | 用例和描述                                                   |
| ------------ | ------------------------------------------------------------ |
| SUBSCRIBE    | subscribe channel [channel ...]（订阅给定的一个或多个频道）  |
| UNSUBCRIBE   | unsubscribe [channel [channel ...]]（退订给定的一个或多个频道，如果执行时没有给定任何频道，那么退订所有频道） |
| PUBLISH      | publish channel message（向给定的频道发送消息）              |
| PSUBSCRIBE   | psubscribe pattern [pattern ...]（订阅与给定模式相匹配的所有频道） |
| PUNSUBSCRIBE | punsubscribe [pattern [pattern ...]]（退订给定的模式，如果执行时没有给定任何模式，那么退订所有模式） |

#### SORT命令的定义

| 命令 | 用例和描述                                                   |
| ---- | ------------------------------------------------------------ |
| SORT | sort source-key [by pattern] [limit offset count] [get pattern [get pattern ...]] [asc\|desc] [alpha] [source dest-key]（根据给定的选项，对输入的列表、集合、或者有序集合进行排序，然后返回或者存储排序的结果） |

#### Redis基本事务

~~~~
Redis的基本事务（Basic Transaction）需要用到multi命令和exec命令，这种事务可以让一个客户端不被其他客户端打断的情况下执行多个命令。和关系型数据库那种可以在执行的过程中进行回滚（rollback）的事务不同，在Redis里面，被multi命令和exec命令包围的所有命令会一个接一个的执行，知道所有命令都执行完毕为止。当一个事务执行完毕之后，Redis才会处理其他客户端的命令。
~~~~

#### 用于处理过期时间的Redis命令

| 命令      | 示例和描述                                                   |
| --------- | ------------------------------------------------------------ |
| PERSIST   | persist key-name（移除键的过期时间）                         |
| TTL       | ttl key-name（查看给定键距离过期时间还剩多少秒）             |
| EXPIRE    | expire key-name seconds（让给定键在指定的描述后过期）        |
| EXPIREAT  | expireat key-name timestamp（将给定键的过期时间设置为给定的UNIX时间戳） |
| PTTL      | pttl key-name（查看给定键距离过期时间还有多少毫秒，这个命令在Redis2.6或以上版本可用） |
| PEXPIRE   | pexpire key-name milliseseconds（让给定键在指定的毫秒数之后过期，这个命令在Redis2.6或以上版本可用） |
| PEXPIREAT | pexpireat key-name timestamp-millseconds（将一个毫秒级经度的UNIX时间戳设置为给定键的过期时间，这个命令在Redis2.6或以上版本可用） |

#### appendfsync选项及同步频率

| 选项     | 同步频率                                                     |
| -------- | ------------------------------------------------------------ |
| always   | 每个Redis写命令都要同步写入硬盘。这样做会严重降低Redis的速度 |
| everysec | 每秒执行一次同步，显示地将多个写命令同步到硬盘               |
| no       | 让操作系统决定应该合适进行同步                               |

BGREWRITEAOF：

~~~
	命令会通过移除aof文件中冗余的命令来重写（rewrite）aof文件，使aof文件的体积变得尽可能地小。BGREWRITEAOF 的工作原理和BGSAVE 创建快照的工作原理非常相似：Redis会创建一个子进程，然后由子进程负责对AOF文件进行重写。因为AOF文件重写也需要用到子进程，所以快照持久化因为创建子进程而导致的性能问题和内存占用问题，在AOF持久化中也同样存在。
	auto-aof-rewrite-percentage 100
	auto-aof-rewrite-min-size 64M
	那么当AOF文件的体积大于64 MB，并且AOF文件的体积比上一次重写之后的体积大了至少一倍（100%）的时候，Redis将执行BGREWRITEAOF 命令.
~~~

#### 从服务器俩节主服务器时的步骤

| 步骤 | 主服务器操作                                                 | 从服务器操作                                                 |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1    | （等待命令进入）                                             | 连接（或者重新连接）主服务器，发送SYNC命令                   |
| 2    | 开始执行BGSAVE，并使用缓冲区记录BGSAVE之后所执行的所有写命令 | 根据配置选项来决定是继续使用现有的数据（如果有的话）来处理客户端的命令请求，还是向发送请求的客户端返回错误 |
| 3    | BGSAVE执行完毕，向从服务器发送快照文件，并在发送期间继续使用缓冲区记录被执行的写命令 | 丢弃所有旧数据（如果有的话），开始载入主服务器发送来的快照文件 |
| 4    | 快照文件发送完毕，开始向从服务器发送存储在缓冲区里面的写命令 | 完成对快照文件的解释操作，像往常一样接受命令请求             |
| 5    | 缓冲区存储的写命令发送完毕；从现在开始，每执行一个写命令，就想从服务器发送相同的写命令 | 执行主服务器发送来的所有存储在缓冲区里面的所有写命令；并从现在开始，接收并执行主服务器传来的每个写命令 |

#### 当一个从服务器连接一个已有快照的主服务器时，有可能重用已有的快照文件

| 当有新的从服务器连接主服务器时                               | 主服务器的操作                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 【从服务器俩节主服务器时的步骤】的步骤3尚未执行              | 所有从服务器都会接收到相同的快照文件和相同的缓冲区命令       |
| 【从服务器俩节主服务器时的步骤】的步骤3正在执行或者已经执行完毕 | 当主服务器与较早进行连接的从服务器执行完复制所需的5个步骤之后，主服务器会与新链接的从服务器执行一次新的步骤1至步骤5 |

