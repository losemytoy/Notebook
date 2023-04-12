# Redis

> Redis能干嘛?

1、内存存储、持久化，内存断电即失，持久化非常重要（RDB、AOF）

2、效率高，可以用来高速缓存

3、发布订阅系统

4、地图信息发布

·······

**建议基于Linux部署**

```shell
docker run -itd --name redis01 -p 6379:6379 redis
```

开启时

```shell
# docker run redis01
```

```shell
# docker exec -it redis01 /bin/bash
root@20aa8f1ad12d:/data# redis-cli
```

默认使用下标为0的第一个数据库

```shell
redis:0>select 3  #可以使用select命令切换数据库
"OK"
redis:3>dbsize    #查看DB大小
"0"

redis:3>keys *     #查看数据库所有的key
 1)  "name"
redis:3>flushdb   #清空当前数据库所有数据
"OK"
redis:3>keys *

```

`flushall`清空全部数据库数据

```shell
redis:0>flushall
"OK"
redis:0>keys *
```

> 6.0之前的Redis是单线程的，在6.0之后多线程

Redis基于内存操作，CPU并不是性能瓶颈，Redis的瓶颈是根据机器的内存和网络带宽。可以使用单线程，就使用单线程。在6.0以后的版本可以使用多线程，多线程用来处理网络数据的读写和协议解析，执行命令仍然是单线程。

**Redis为什么单线程还这么快？**

误区1、高性能服务器一定是多线程的？

误区2、多线程（CPU上下文切换！）一点比单线程效率高

CPU>内存>硬盘的速度要有所了解！

Redis（6.0以前）是将所有的数据放入内存，所以使用单线程的操作效率是最高的，多线程会上下文切换消耗大量时间，对于内存系统来说，单线程才能产生更高的效率。

## 五大基本数据类型

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。

### Redis-Key

#### [官方文档](http://www.redis.cn/commands.html#)

==记录的所有命令一定要记住，在后面使用的SpringBoot、Jedis中，所有方法都是这些命令==

`exists`查看当前数据库中是否存在该键

```shell
redis:0>keys *
 1)  "name"
redis:0>exists name
"1"
redis:0>exists name1
"0"
```

`move`将数据移动到其他数据库

```shell
redis:0>move name 1    #将name数据移动到下标为1的数据库
"1"
redis:0>keys *
"null"
```

`expire`为数据设置一个销毁时间

```shell
redis:1>expire name 10    #将name数据设置为10s
"1"
redis:1>ttl name
"6"
redis:1>ttl name
"4"
redis:1>ttl name
"3"
redis:1>ttl name
"2"
redis:1>ttl name
"1"
redis:1>ttl name
"0"
redis:1>ttl name
"-2"
redis:1>keys *
"null"
```

后面遇到不会的命令，可以在官网查看[帮助文档](http://www.redis.cn/commands.html)！

### String（字符串）

```shell
redis:0>set key1 v1                #设置值
"OK"
redis:0>get key1                #获得值
"v1"
redis:0>keys *                    #获得所有的key
 1)  "key1"
redis:0>exists key1                #判断key是否存在
"1"
redis:0>append key1 "Hello"        #追加字符串，如果当前的key不存在，就相当于setkey
"7"
redis:0>get key1
"v1Hello"
redis:0>strlen key1                #获取字符串长度
"7"
redis:0>append key1 ",yiming"    
"14"
```

#### 1、i++，值增长

```shell
redis:0>set views 0
"OK"
redis:0>get views
"0"
redis:0>incr views     #自增1
"1"
redis:0>incr views
"2"
redis:0>get views
"2"
redis:0>decr views        #自减1
"1"
redis:0>incrby views 10  #可以设置指定增量
"10"
```

#### 2、字符串范围 range

```shell
redis:0>set key1 "hello,yiming"            #设置key1的值
"OK"
redis:0>get key1
"hello,yiming"
redis:0>getrange key1 0 3                #截取字符串  【0，3】
"hell"
redis:0>getrange key1 0 -1                #获取全部字符串 等同于  getkey
"hello,yiming"
```

```shell
redis:0>set key2 abcdefg
"OK"
redis:0>get key2
"abcdefg"
redis:0>setrange key2 1 xx                #替换指定位置开始的字符串
"7"
redis:0>get key2
"axxdefg"
```

#### 3、setex     setnx  分布式锁

> setex(set with expire)设置过期时间             
> 
> setnx（set with exists）假如不存在（在分布式锁中会常常使用！）

```shell
redis:0>setex key3 30 "hello"            #设置key3的值为hello，过期时间30s
"OK"
redis:0>ttl key3
"17"
redis:0>setnx mykey "redis"                #如果mykey 不存在,创建mykey
"1"
redis:0>keys *
 1)  "key2"
 2)  "key1"
 3)  "mykey"
redis:0>ttl key3
"-2"
redis:0>setnx mykey "success"            #如果mykey存在，创建失败
"0"
redis:0>get key3
null
redis:0>get mykey
"redis"
```

#### 4、mset   mget  设置多值

```shell
redis:0>mset k1 v1k2 v2 k3 v3                    #同时设置多个值
"ERR wrong number of arguments for MSET"
redis:0>mset k1 v1 k2 v2 k3 v3
"OK"
redis:0>keys *
 1)  "k3"
 2)  "k2"
 3)  "k1"
redis:0>mget k1 k2 k3                            #同时获取多个值
 1)  "v1"
 2)  "v2"
 3)  "v3"
redis:0>msetnx k1 v1 k4 v4                        #msetnx  是一个原子性的操作，一起成功或一起失败
"0"
redis:0>get k4
null
```

#### 5、对象

```shell
set user:1{name:zangsan,age:3}   #设置一个user:1对象 值为json字符来保存一个对象！

redis:0>mset user:1:name zhangsan user:1:age 2        #user:{id}:{filed}    
"OK"
redis:0>mget user:1:name user:1:age
 1)  "zhangsan"
 2)  "2"
```

#### 6、getset

> getset（先get再set）

```shell
redis:0>getset db redis
null
redis:0>get db
"redis"
redis:0>getset db bb
"redis"
redis:0>get db
"bb"
redis:0>
```

#### 底层实现

Redis使用SDS（Simple Dynamic String，简单动态字符串）作为底层实现。

SDS共有5种实现方式SDS_TYPE_5（并未用到）、SDS_TYPE_8、SDS_TYPE_16、SDS_TYPE_32、SDS_TYPE_64。Redis根据初始化的长度决定使用那种类型，从而减少内存的使用。

都包含4个属性：

- `len`：字符串长度，即已使用的字节数。
- `alloc`：总共可用的字符空间大小，`alloc-len`就是SDS剩余的空间大小。
- `buf[]`：实际存储字符串的数组。
- `flags`：低三位保存类型标志

==**相对于C中字符串的提升**==

1. **避免缓冲区溢出：**C语言中的字符串被修改时（如拼接），一旦没有分配足够长度的内存空间，就会造成缓冲区溢出。SDS被修改时，会先根据`len`属性检查空间大小是否满足要求，如果不满足，则先扩展至所需大小在进行修改操作。
2. **获取字符串长度的复杂度较低：**C中的字符串长度通过遍历统计，时间复杂度为O(n)。SDS的长度读取`len`属性即可，时间复杂度为O(1)。
3. **减少内存分配次数：** 为了避免修改（增加/减少）字符串时，每次都需要重新分配内存（C 语言的字符串是这样的），SDS 实现了空间预分配和惰性空间释放两种优化策略。当 SDS 需要增加字符串时，Redis 会为 SDS 分配好内存，并且根据特定的算法分配多余的内存，这样可以减少连续执行字符串增长操作所需的内存重分配次数。当 SDS 需要减少字符串时，这部分内存不会立即被回收，会被记录下来，等待后续使用（支持手动释放，有对应的 API）。
4. **二进制安全**：C 语言中的字符串以空字符 `\0` 作为字符串结束的标识，这存在一些问题，像一些二进制文件（比如图片、视频、音频）就可能包括空字符，C 字符串无法正确保存。SDS 使用 len 属性判断字符串是否结束，不存在这个问题。
   
   

### List

在Redis中，List可以成为**栈**、**队列**、**阻塞队列**,类似于双端队列

#### 1、lpush    rpush 添加

> lpush相当于将元素从左加入列表，rpush则相反

```shell
redis:0>lpush list one            #将值从左边插入
"1"
redis:0>lpush list two
"2"
redis:0>lpush list three
"3"
redis:0>lrange list 0 -1        #显示全部元素
 1)  "three"
 2)  "two"
 3)  "one"
redis:0>lrange list 0 1            
 1)  "three"
 2)  "two"
redis:0>rpush list four            #将值从右边插入
"4"
redis:0>lrange list 0 -1
 1)  "three"
 2)  "two"
 3)  "one"
 4)  "four"
```

#### 2、lpop rpop  删除

```shell
redis:0>lrange list 0 -1
 1)  "three"
 2)  "two"
 3)  "one"
 4)  "four"
redis:0>lpop list                    #移除第一个元素
"three"
redis:0>rpop list                    #移除最后一个元素
"four"
redis:0>lrange list 0 -1            
 1)  "two"
 2)  "one"
```

#### 3、lindex 下标取值

```shell
redis:0>lrange list 0 -1
 1)  "two"
 2)  "one"
redis:0>lindex list 0
"two"
redis:0>lindex list 1
"one"
```

#### 4、llen 显示长度

```shell
redis:0>lpush list one
"1"
redis:0>lpush list two
"2"
redis:0>lpush list three
"3"
redis:0>llen list
"3"
```

5、lrem 移除

> lrem    key    count    value

```shell
redis:0>lrange list 0 -1
 1)  "three"
 2)  "three"
 3)  "two"
 4)  "one"
redis:0>lrem list 1 one
"1"
redis:0>lrange list 0 -1
 1)  "three"
 2)  "three"
 3)  "two"
redis:0>lrem list 2 hello
"0"
redis:0>lrem list 2 three
"2"
redis:0>lrange list 0 -1
 1)  "two"
redis:0>
```

#### 6、ltrim 截取

```shell
redis:0>lpush list one
"1"
redis:0>lpush list two
"2"
redis:0>lpush list three
"3"
redis:0>lpush list four
"4"
redis:0>ltrim list 1 2                    #通过下标截取元素
"OK"
redis:0>lrange list 0 -1
 1)  "three"
 2)  "two"
```

#### 7、rpoplpush 更换列表

> 删除列表中的最后一个元素，将其追加到另一个列表

```shell
redis:0>lpush list hello1
"1"
redis:0>lpush list hello2
"2"
redis:0>lpush list hello3
"3"
redis:0>rpoplpush list list1
"hello1"
redis:0>lrange list 0 -1
 1)  "hello3"
 2)  "hello2"
redis:0>lrange list1 0 -1
 1)  "hello1"
```

#### 8、lset 队列元素设值

> 设置 下标 位置的list元素的值为 value。

```shell
redis:0>rpush list one
"1"
redis:0>rpush list two
"2"
redis:0>rpush list three
"3"
redis:0>rpush list four
"4"
redis:0>lset list 0 oneone
"OK"
redis:0>lrange list 0 -1
 1)  "oneone"
 2)  "two"
 3)  "three"
 4)  "four"
```

#### 9、linsert    插入

> 把 value 插入存于 key 的列表中在基准值 pivot 的前面或后面。
> 
> 当 key 不存在时，这个list会被看作是空list，任何操作都不会发生。
> 
> 当 key 存在，但保存的不是一个list的时候，会返回error。

```shell
redis:0>rpush list hello
"1"
redis:0>rpush list world
"2"
redis:0>linsert list before world beautiful
"3"
redis:0>lrange list 0 -1
 1)  "hello"
 2)  "beautiful"
 3)  "world"
 redis:0>linsert list after world be
"4"
redis:0>lrange list 0 -1
 1)  "hello"
 2)  "beautiful"
 3)  "world"
 4)  "be"
```

#### 10、小结

本质是一个链表

如果key不存在，创建新的链表

如果移除所有值，空链表，也代表不存在

在两边插入或者改动值，效率最高！中间元素，相对效率会低一些

消息排队   消息队列（LPUSH    RPOP）,栈（LPUSH    LPOP）

### Set(无序不重复集合)

#### 1、sadd     添加

> 添加一个或多个指定的member元素到集合的 key中.指定的一个或者多个元素member 如果已经在集合key中存在则忽略.

```shell
redis:0>sadd myset hello
"1"
redis:0>sadd myset hello1
"1"
redis:0>sadd myset hello2
"1"
redis:0>smembers myset    #返回key集合所有的元素
 1)  "hello2"
 2)  "hello"
 3)  "hello1"
```

#### 2、sismember    判断存在

> 返回成员 member 是否是存储的集合 key的成员.

```shell
redis:0>smembers myset
 1)  "hello2"
 2)  "hello"
 3)  "hello1"
redis:0>sismember myset hello2
"1"
redis:0>sismember myset hello4
"0"
```

#### 3、scard    查看数量

> 返回集合存储的key的基数 (集合元素的数量).

```shell
redis:0>smembers myset
 1)  "hello2"
 2)  "hello"
 3)  "hello1"
redis:0>scard myset
"3"
```

#### 4、srem    删除

> 在key集合中移除指定的元素. 

```shell
redis:0>smembers myset
 1)  "hello2"
 2)  "hello"
 3)  "hello1"
redis:0>srem myset hello
"1"
redis:0>smembers myset
 1)  "hello2"
 2)  "hello1"
```

#### 5、srandmember    随机显示

> 仅提供key参数，那么随机返回key集合中的一个元素

```shell
redis:0>smembers myset
 1)  "hello2"
 2)  "hello1"
redis:0>srandmember myset
"hello2"
```

#### 6、spop    随机删除

> 从存储在`key`的集合中移除并返回一个或多个随机元素。

```shell
redis:0>smembers myset
 1)  "hello3"
 2)  "hello2"
 3)  "hello1"
redis:0>spop myset
"hello2"
redis:0>smembers myset
 1)  "hello3"
 2)  "hello1"
```

#### 7、smove    移到另一个集合

```shell
redis:0>sadd myset1 hello1
"1"
redis:0>sadd myset2 hello2
"1"
redis:0>smembers myset2
 1)  "hello2"
redis:0>smove myset1 myset2 hello1
"1"
redis:0>smembers myset2
 1)  "hello2"
 2)  "hello1"
```

#### 8、sunion    并集

```shell
redis:0>sadd myset1 a
"1"
redis:0>sadd myset1 b
"1"
redis:0>sadd myset1 c
"1"
redis:0>sadd myset2 c
"1"
redis:0>sadd myset2 d
"1"
redis:0>sunion myset1 myset2
 1)  "d"
 2)  "b"
 3)  "c"
 4)  "a"

```

#### 9、sdiff    差集

```shell
redis:0>SADD key1 "a"
"0"
redis:0>SADD key1 "c"
"1"
redis:0>SADD key2 "c"
"1"
redis:0>SADD key2 "d"
"1"
redis:0>SADD key2 "e"
"1"
redis:0>sdiff key1 key2
 1)  "a"
```

#### 10、sinter    交集

```shell
redis:0>SADD key1 "a"
"0"
redis:0>SADD key1 "c"
"1"
redis:0>SADD key2 "c"
"1"
redis:0>SADD key2 "d"
"1"
redis:0>SADD key2 "e"
"1"
redis:0>sinter key1 key2
 1)  "c"
```

### Hash(哈希)

Map集合，key-map

==key    filed    value==

#### 1、hset    hget

> 设置/返回 key 指定的哈希集中指定字段的值。

```
redis:0>hset myhash field1 ko
"1"
redis:0>hget myhash field1
"ko"
```

#### 2、hmset    hmget

```shell
redis:0>HMSET myhash field1 "Hello" field2 "World"
"OK"
redis:0>HGET myhash field1
"Hello"
redis:0>HGET myhash field2
"World"
```

#### 3、hgetall    得到所有值

```shell
redis:0>hgetAll myhash
 1)  "field1"
 2)  "Hello"
 3)  "field2"
 4)  "World"
```

#### 4、hdel    删除

```shell
redis> HSET myhash field1 "foo"
(integer) 1
redis> HDEL myhash field1
(integer) 1
redis> HDEL myhash field2
(integer) 0
```

#### 5、hlen    数量

```shell
redis> HSET myhash field1 "Hello"
(integer) 1
redis> HSET myhash field2 "World"
(integer) 1
redis> HLEN myhash
(integer) 2
```

#### 6、hexists    是否存在

```shell
redis> HSET myhash field1 "foo"
(integer) 1
redis> HEXISTS myhash field1
(integer) 1
redis> HEXISTS myhash field2
(integer) 0
```

#### 7、hkeys    获取字段

> 返回 key 指定的哈希集中所有字段（域）的名字。

```shell
redis> HSET myhash field1 "Hello"
(integer) 1
redis> HSET myhash field2 "World"
(integer) 1
redis> HKEYS myhash
1) "field1"
2) "field2"
```

#### 8、hvals    获取值

> 返回 key 指定的哈希集中所有字段的值。

```shell
redis> HSET myhash field1 "Hello"
(integer) 1
redis> HSET myhash field2 "World"
(integer) 1
redis> HVALS myhash
1) "Hello"
2) "World"
```

#### 9、hincrby 增加

```shell
redis> HSET myhash field 5
(integer) 1
redis> HINCRBY myhash field 1
(integer) 6
redis> HINCRBY myhash field -1
(integer) 5
redis> HINCRBY myhash field -10
(integer) -5
```

#### 10、hsetex    不存在设置

> 只在 `key` 指定的哈希集中不存在指定的字段时，设置字段的值。如果 `key` 指定的哈希集不存在，会创建一个新的哈希集并与 `key` 关联。如果字段已存在，该操作无效果。

```shell
redis> HSETNX myhash field "Hello"
(integer) 1
redis> HSETNX myhash field "World"
(integer) 0
redis> HGET myhash field
"Hello"
```

### Sorted Sets    有序集合

==key+score+member==

#### 1、zadd/zrange    添加/显示value

```shell
redis:0>zadd myset 1 hello
"1"
redis:0>zadd myset 1 one 
"1"
redis:0>zadd myset 2 two
"1"
redis:0>zrange myset 0 -1
 1)  "hello"
 2)  "one"
 3)  "two"
```

#### 2、zcount    在score范围内的值

```shell
redis> ZADD myzset 1 "one"
(integer) 1
redis> ZADD myzset 2 "two"
(integer) 1
redis> ZADD myzset 3 "three"
(integer) 1
redis> ZCOUNT myzset -inf +inf
(integer) 3
redis> ZCOUNT myzset (1 3
(integer) 2
```

#### 3、zincrby    增量score

```shell
redis> ZADD myzset 1 "one"
(integer) 1
redis> ZADD myzset 2 "two"
(integer) 1
redis> ZINCRBY myzset 2 "one"
"3"
redis> ZRANGE myzset 0 -1 WITHSCORES
1) "two"
2) "2"
3) "one"
4) "3"
```

#### 4、zrangebyscore    按score排序

```shell
redis> ZADD myzset 1 "one"
(integer) 1
redis> ZADD myzset 2 "two"
(integer) 1
redis> ZADD myzset 3 "three"
(integer) 1
redis> ZRANGEBYSCORE myzset -inf +inf
1) "one"
2) "two"
3) "three"
redis> ZRANGEBYSCORE myzset 1 2
1) "one"
2) "two"
redis> ZRANGEBYSCORE myzset (1 2
1) "two"
redis> ZRANGEBYSCORE myzset (1 (2
(empty list or set)
```

## 内存管理

```shell
expire key 30
setex key 30 value
persist #可以移除一个键的过期时间
```

**有助于缓解内存的消耗，同时满足一些业务需求**

如：短信验证码在1分钟内有效，用户的token可能在1天内有效

### Redis如何判断数据过期？

通过一个过期字典（可以看成hash表）来保存数据过期的时间

### 过期数据删除策略

1. **惰性删除：**只有在取出key的时候才对数据进行检查。这对CPU很友好，但是会存在大量的过期数据没被删除。
2. **定期删除：**每隔一段时间抽取一批key进行删除过期key操作。Redis底层通过限制删除操作执行的时长和频率来减少删除操作对CPU时间的影响。

定期删除对内存更加友好，惰性删除对 CPU 更加友好。两者各有千秋，所以 Redis 采用的是 **定期删除+惰性/懒汉式删除** 。

但是，仅仅通过给 key 设置过期时间还是有问题的。因为还是可能存在定期删除和惰性删除漏掉了很多过期 key 的情况。这样就导致大量过期 key 堆积在内存里，然后就 Out of memory 了。**Redis内存淘汰机制**解决这个问题。

### 内存淘汰机制

> 相关问题：MySQL 里有 2000w 数据，Redis 中只存 20w 的数据，如何保证 Redis 中的数据都是热点数据?

Redis 提供 6 种数据淘汰策略：

1. **volatile-lru（least recently used）**：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
2. **volatile-ttl**：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
3. **volatile-random**：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
4. **allkeys-lru（least recently used）**：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（这个是最常用的）
5. **allkeys-random**：从数据集（server.db[i].dict）中任意选择数据淘汰
6. **no-eviction**：禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使用吧！

4.0 版本后增加以下两种：

1. **volatile-lfu（least frequently used）**：从已设置过期时间的数据集（server.db[i].expires）中挑选最不经常使用的数据淘汰
2. **allkeys-lfu（least frequently used）**：当内存不足以容纳新写入数据时，在键空间中，移除最不经常使用的 key

## 事务

Redis事物本质：一组命令的集合；一个事务中的所有命令都会顺序化，在执行的过程中会按照顺序执行。

==Redis单条命令是保持原子性的，但是事务不保证原子性。==

Redis事务没有隔离级别的概念

redsi的事务：

1、开启事务（multi）

2、命令入队（）

3、执行事务（exec）

> 正常执行事务

```shell
redis:0>multi
"OK"
redis:0>set k1 v1
"QUEUED"
redis:0>set k2 v2
"QUEUED"
redis:0>get k2
"QUEUED"
redis:0>set k3 v3
"QUEUED"
redis:0>exec
 1)  "OK"
 2)  "OK"
 3)  "OK"
 4)  "OK"
 5)  "OK"
 6)  "v2"
 7)  "OK"
 8)  "OK"
 9)  "OK"
```

> 放弃事务

```shell
redis:0>multi
"OK"
redis:0>set k1 v1
"QUEUED"
redis:0>set k2 v2
"QUEUED"
redis:0>set k3 v3
"QUEUED"
redis:0>discard            #取消事务
"OK"
redis:0>get k4            #事务队列中的命令都不会执行
null
```

> 编译型异常（代码有问题，命令有错），事务中所有的命令都不会执行！

```shell
redis:0>multi
"OK"
redis:0>set k1 v1
"QUEUED"
redis:0>set k2 v2
"QUEUED"
redis:0>set k3 v3
"QUEUED"
redis:0>getset k3                                    #错误的命令
"ERR wrong number of arguments for 'getset' command"
redis:0>set k4 v4
"QUEUED"
redis:0>set k5 v5
"QUEUED"
redis:0>exec                                        #执行事务报错
"EXECABORT Transaction discarded because of previous errors."
redis:0>get k5                                        #所有的命令都不会执行
null
```

> 运行时异常，如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以执行的，错误命令抛出异常

```shell
redis:0>set k1 "v1"
"OK"
redis:0>multi
"OK"
redis:0>incr k1
"QUEUED"
redis:0>set k2 v2
"QUEUED"
redis:0>set k3 v3
"QUEUED"
redis:0>get k3
"QUEUED"
redis:0>exec
 1)  "OK"
 2)  "ERR value is not an integer or out of range"                #第一条命令错误，后面的命令依然执行
 3)  "OK"
 4)  "OK"
 5)  "OK"
 6)  "OK"
 7)  "OK"
 8)  "v3"
 9)  "OK"
redis:0>get k2
"v2"
redis:0>get k3
"v3"
```

> 监控        watch（面试常问）

**悲观锁：**

无论进行什么操作都会加锁

**乐观锁：**

认为任何时候都不会出问题，不会上锁。更新数据的时候去判断一下，在此期间有没有修改过这个数据

获取version

更新时比较version

> Redis监视测试

正常执行成功：

```shell
redis:0>set money 100
"OK"
redis:0>set out 0
"OK"
redis:0>watch money
"OK"
redis:0>multi
"OK"
redis:0>decrby money 20
"QUEUED"
redis:0>incrby out 20
"QUEUED"
redis:0>exec
 1)  "OK"
 2)  "80"
 3)  "OK"
 4)  "20"
 5)  "OK"
```

测试多线程修改值，使用watch可以当作redis的乐观锁操作**watch是乐观锁**

```shell
redis:0>watch money                #监视money
"OK"
redis:0>multi
"OK"
redis:0>decrby money 10
"QUEUED"
redis:0>incrby out 10
"QUEUED"
redis:0>exec                    #执行事务之前，另外一个线程修改了money，这个时候就会导致事务执行失败
"null"
```

## Jedis

> Redis官方推荐的java连接开发工具！使用java操作Redis的中间件

1、导入对应的依赖

```xml
<dependencies>
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.3.0</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <!--            项目尽量不要使用fastjson,有安全漏洞，使用jackson-->
        <artifactId>fastjson</artifactId>    
        <version>1.2.38</version>
    </dependency>
</dependencies>
```

2、编码测试

- 连接数据库
- 操作命令
- 断开连接

```java
package com.yiming;

import redis.clients.jedis.Jedis;

public class TestPing {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.31.190",6379);
        System.out.println(jedis.ping());
    }
}

```

output:

```java
PONG

Process finished with exit code 0
```

常用的API

String

List

## SpringBoot整合

在2.0之后的版本，jedis被替换为lettuce

## 缓存问题

#### 1、在实际项目中使用缓存有遇到什么问题或者会遇到什么问题？

**缓存和数据库数据一致性问题**：分布式环境下非常容易出现缓存和数据库间数据一致性问题，针对这一点，如果项目对缓存的要求是强一致性的，那么就不要使用缓存。我们只能采取合适的策略来降低缓存和数据库间数据不一致的概率，而无法保证两者间的强一致性。合适的策略包括合适的缓存更新策略，更新数据库后及时更新缓存、缓存失败时增加重试机制。

#### 2、**Redis 雪崩**

目前电商首页以及热点数据都会去做缓存，一般缓存都是定时任务去刷新，或者查不到之后去更新缓存的，定时任务刷新就有一个问题。举个栗子：如果首页所有 Key 的失效时间都是 12 小时，中午 12 点刷新的，我零点有个大促活动大量用户涌入，假设每秒 6000 个请求，本来缓存可以抗住每秒 5000 个请求，但是缓存中所有 Key 都失效了。此时 6000 个/秒的请求全部落在了数据库上，数据库必然扛不住，真实情况可能 DBA 都没反应过来直接挂了，此时，如果没什么特别的方案来处理，DBA 很着急，重启数据库，但是数据库立马又被新流量给打死了。这就是我理解的缓存雪崩。

我心想：同一时间大面积失效，瞬间 Redis 跟没有一样，那这个数量级别的请求直接打到数据库几乎是灾难性的，你想想如果挂的是一个用户服务的库，那其他依赖他的库所有接口几乎都会报错，如果没做熔断等策略基本上就是瞬间挂一片的节奏，你怎么重启用户都会把你打挂，等你重启好的时候，用户早睡觉去了，临睡之前，骂骂咧咧“什么垃圾产品”。

> **该怎么应对？**

在批量往 Redis 存数据的时候，把每个 Key 的失效时间都加个随机值就好了，这样可以保证数据不会再同一时间大面积失效。

```java
setRedis（key, value, time+Math.random()*10000）;
```

如果 Redis 是集群部署，将热点数据均匀分布在不同的 Redis 库中也能避免全部失效。或者设置热点数据永不过期，有更新操作就更新缓存就好了（比如运维更新了首页商品，那你刷下缓存就好了，不要设置过期时间），电商首页的数据也可以用这个操作，保险。

#### 3、缓存穿透

缓存穿透是指缓存和数据库中都没有的数据，而用户（黑客）不断发起请求，比如：我们数据库的 id 都是从 1 自增的，如果发起 id=-1 的数据或者 id 特别大不存在的数据，这样的不断攻击导致数据库压力很大，严重会击垮数据库。

> **该怎么解决？**

1、缓存穿透我会在接口层增加校验，比如用户鉴权，参数做校验，不合法的校验直接 return，比如 id 做基础校验，id<=0 直接拦截。

2、Redis 里还有一个高级用法**布隆过滤器（Bloom Filter）**这个也能很好的预防缓存穿透的发生，他的原理也很简单，就是利用高效的数据结构和算法快速判断出你这个 Key 是否在数据库中存在，不存在你 return 就好了，存在你就去查 DB 刷新 KV 再 return。

#### 4、缓存击穿

和缓存雪崩有点像，但是又有一点不一样，缓存雪崩是因为大面积的缓存失效，打崩了 DB，而缓存击穿不同的是缓存击穿是指一个 Key 非常热点，在不停地扛着大量的请求，大并发集中对这一个点进行访问，当这个 Key 在失效的瞬间，持续的大并发直接落到了数据库上，就在这个 Key 的点上击穿了缓存。

> 怎么解决？

缓存击穿的话，设置热点数据永不过期，或者加上互斥锁就搞定了。

```java
public static String getData(String key) throws InterruptedException {
        //从Redis查询数据
        String result = getDataByKV(key);
        //参数校验
        if (StringUtils.isBlank(result)) {
            try {
                //获得锁
                if (reenLock.tryLock()) {
                    //去数据库查询
                    result = getDataByDB(key);
                    //校验
                    if (StringUtils.isNotBlank(result)) {
                        //插进缓存
                        setDataToKV(key, result);
                    }
                } else {
                    //睡一会再拿
                    Thread.sleep(100L);
                    result = getData(key);
                }
            } finally {
                //释放锁
                reenLock.unlock();
            }
        }
        return result;
    }
```

## Redis持久化机制

### 如何保证Redis挂了之后重启数据可以恢复？

持久化

- 快照（RDB）
- 只追加文件（AOF）

### RDB持久化

Redis通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。Redis创建快照后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis主从结构）。

快照持久化是 Redis 默认采用的持久化方式，在 `redis.conf` 配置文件中默认有此下配置：



```shell
save 900 1           #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发bgsave命令创建快照。

save 300 10          #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发bgsave命令创建快照。

save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发bgsave命令创建快照。
```

### RDB创建快照时会阻塞主线程吗？

- `save`：同步保存，会阻塞主线程。
- `bgsave`：使用子线程进行保存，不会阻塞主线程。

### AOF持久化

与快照持久化相比，AOF持久化的实时性更好。

```shell
appendonly yes  #开启AOF持久化
```

在开启AOF持久化后，每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入`server.aof_buf`中，根据配置文件决定何时将其写入硬盘。

三种AOF持久化方式：

```shell
appendfsync always    #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度
appendfsync everysec  #每秒钟同步一次，显式地将多个写命令同步到硬盘
appendfsync no        #让操作系统决定何时进行同步
```

### AOF日志是如何实现的？

关系型数据库（如MySQL）通常都是执行命令之后记录日志，而AOF是在执行完命令之后在记录日志。

**为什么？**

- 避免额外的检查开销，AOF记录不用对命令语法进行检查；
- 在命令执行完之后再记录，不会阻塞当前的命令执行。

但是，也会带来风险：

- Redis在执行完命令后宕机会导致相应的修改丢失；
- 可能会阻塞后续其他命令的执行（AOF记录日志是在Redis主线程中进行的）。

### AOF重写

当AOF变得太大时，Redis会读取数据库中的所有键值对，不会对现有的AOF文件产生任何操作。

在执行`BGREWRITEAOF`命令时，Redis 服务器会维护一个 AOF 重写缓冲区，该缓冲区会在子进程创建新 AOF 文件期间，记录服务器执行的所有写命令。当子进程完成创建新 AOF 文件的工作之后，服务器会将重写缓冲区中的所有内容追加到新 AOF 文件的末尾，使得新的 AOF 文件保存的数据库状态与现有的数据库状态一致。最后，服务器用新的 AOF 文件替换旧的 AOF 文件，以此来完成 AOF 文件重写操作。

### RDB Vs AOF

**RDB 比 AOF 优秀的地方** ：

- RDB 文件存储的内容是经过压缩的二进制数据， 保存着某个时间点的数据集，文件很小，适合做数据的备份，灾难恢复。AOF 文件存储的是每一次写命令，类似于 MySQL 的 binlog 日志，通常会必 RDB 文件大很多。当 AOF 变得太大时，Redis 能够在后台自动重写 AOF。新的 AOF 文件和原有的 AOF 文件所保存的数据库状态一样，但体积更小。不过， Redis 7.0 版本之前，如果在重写期间有写入命令，AOF 可能会使用大量内存，重写期间到达的所有写入命令都会写入磁盘两次。
- 使用 RDB 文件恢复数据，直接解析还原数据即可，不需要一条一条地执行命令，速度非常快。而 AOF 则需要依次执行每个写命令，速度非常慢。也就是说，与 AOF 相比，恢复大数据集的时候，RDB 速度更快。

**AOF 比 RDB 优秀的地方** ：

- RDB 的数据安全性不如 AOF，没有办法实时或者秒级持久化数据。生成 RDB 文件的过程是比较繁重的， 虽然 BGSAVE 子进程写入 RDB 文件的工作不会阻塞主线程，但会对机器的 CPU 资源和内存资源产生影响，严重的情况下甚至会直接把 Redis 服务干宕机。AOF 支持秒级数据丢失（取决 fsync 策略，如果是 everysec，最多丢失 1 秒的数据），仅仅是追加命令到 AOF 文件，操作轻量。
- RDB 文件是以特定的二进制格式保存的，并且在 Redis 版本演进中有多个版本的 RDB，所以存在老版本的 Redis 服务不兼容新版本的 RDB 格式的问题。
- AOF 以一种易于理解和解析的格式包含所有操作的日志。你可以轻松地导出 AOF 文件进行分析，你也可以直接操作 AOF 文件来解决一些问题。比如，如果执行`FLUSHALL`命令意外地刷新了所有内容后，只要 AOF 文件没有被重写，删除最新命令并重启即可恢复之前的状态。

## 常见阻塞原因

### O(n) 命令

使用` O(n)` 命令可能会导致阻塞，例如`keys *` 、`hgetall`、`lrange`、`smembers`、`zrange`、`sinter` 、`sunion` 命令。这些命令时间复杂度是 O(n)，有时候也会全表扫描，随着 n 的增大耗时也会越大从而导致客户端阻塞。

不过， 这些命令并不是一定不能使用，但是需要明确 N 的值。有遍历的需求可以使用 `hscan`、`sscan`、`zscan` 代替。

### Save创建RDB快照

Redis 提供了两个命令来生成 RDB 快照文件：

- `save` : 同步保存操作，会阻塞 Redis 主线程；
- `bgsave` : fork 出一个子进程，子进程执行，不会阻塞 Redis 主线程，默认选项。

默认情况下，Redis 默认配置会使用 `bgsave` 命令。如果手动使用 `save` 命令生成 RDB 快照文件的话，就会阻塞主线程。
