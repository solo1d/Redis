### 目录

- [Redis数据类型和实例](#Redis数据类型和实例)
- [Redis命令](#Redis命令)
  - [键key](#键key)
  - 


```bash
# 指定端口号和IP 地址
# --raw  可以避免中文乱码。
$ redis-cli --raw  -h IP地址  -p  端口号  -a 密码
	需要登录后使用 AUTH 密码   进行密码验证

# 指定端口号和IP 地址, 并进入集群 redis （-c 参数）
$ redis-cli  -c -h IP地址  -p  端口号
```



## Redis数据类型和实例

**注意：**一个键最大能存储 512MB

**Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。**



- Redis 数据类型：

  - String: 字符串

    - ```bash
      string 是 redis 最基本的类型，你可以理解成与 Memcached 一模一样的类型，一个 key 对应一个 value。
      string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。
      string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。
      
      # 实例
      # 设置字符串  (显示中文需要连接时加上 --raw 参数)
      127.0.0.1:6379> SET key1 "redis"
      OK
      
      # 同时设置一个或多个 key-value 对。
      127.0.0.1:6380> MSET key1 var1 key2 var2 key3 var3 key4 var4
      OK
      
      
      #获取字符串
      127.0.0.1:6379> GET key1
      "redis"
      
      # GETRANGE 获取存储在指定 key 中字符串的子字符串。字符串的截取范围由 start 和 end 两个偏移量决定(包括 start 和 end 在内)。
      127.0.0.1:6380> GETRANGE key1 0 1  # 显示下标 0 和 1 的字符串内容
      re
      
      # GETSET 用于设置指定 key 的值，并返回 key 的旧值。  
      127.0.0.1:6380> GETSET key1 db
      redis					# 这个时候 key1 这个键 对应的实值 就变成了 db
      
      # STRLEN 返回 key 所储存的字符串值的长度。
      127.0.0.1:6380> STRLEN key1
      2
      
      # 将 key 中储存的数字值增一。 如果存储内容为非数字，则会失败
      127.0.0.1:6380> INCR key1
      2				# key1 原本的值为 1  
      
      # 将 key 所储存的值加上给定的增量值。 a1=9 ,加上了2 就等于了11
      127.0.0.1:6380> INCRBY a1 2
      11
      
      
      # 将 key 所储存的值加上给定的浮点增量值 。 fla1被设置为  SET fla1 1.1
      127.0.0.1:6380> INCRBYFLOAT fla1 2.2
      3.30000000000000027
      
      # 将 key 中储存的数字值减一 , a1 是stirng， 减去了1
      127.0.0.1:6380> DECR a1
      10
      
      # key 所储存的值减去给定的减量值 .  a1 是stirng， 减去了2
      127.0.0.1:6380> DECRBY a1 2
      8
      
      
      # 如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾
      #   a1原本等于字符串 8 ， 执行后 a1 等于 81 ,在末尾拼接了一个 1 字符串
      127.0.0.1:6380> APPEND a1 1
      2				# GET a1  =  81
      ```
  
  - Hash: 散列(哈希)
  
    - ```bash
      Redis hash 是一个键值(key=>value)对集合。
      Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。
      
      实例中我们使用了 Redis HMSET, HGET 命令，HMSET 设置了两个 field=>value 对, HGET 获取对应 field 对应的 value。
      每个 hash 可以存储 (2^32) -1 个键值对（40多亿）。
      
      # 实例
      # 设置哈希
      127.0.0.1:6380> HMSET hashKey field1 "hello" field2 "world"
      
      # 获取哈希1，直接返回字符串（单个）
      127.0.0.1:6380> HGET hashKey field1
      "hello"
      127.0.0.1:6380> HGET hashKey field2
      "world"
      
      # 获取哈希2，返回字符串和标签 (单个或多个)
      127.0.0.1:6380> HMGET hashKey field2 field1
      1) "world"
      2) "hello"
      127.0.0.1:6380> HMGET hashKey field1
      1) "hello"
      ```
  
  - List: 列表
  
    - ```bash
      Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。
      每个 list列表 可以存储 (2^32) -1 个键值对（40多亿）。
      
      # 实例
      # 设置和添加列表
      #   list1 是列表名, redis 是第一个被压入的值(index是0), mongodb 是第一个被压入的值(index是1)
      127.0.0.1:6380> LPUSH list1 redis
      (integer) 1
      127.0.0.1:6380> LPUSH list1 mongodb
      (integer) 2
      
      # 获取列表内容
      #  获取 list1表 末尾的 10个元素(获取列表指定范围内的元素)
      127.0.0.1:6380> lrange list1 0 10
      1) "mongodb"
      2) "redis"
      ```
  
    - 
  
  - Set: 集合
  
    - ```bash
      Redis 的 Set 是 string 类型的无序集合。
      集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
      
      sadd 命令
      添加一个 string 元素到 key 对应的 set 集合中，成功返回 1，如果元素已经在集合中返回 0。
      		sadd key member
      		
      注意：集合内元素的唯一性，第二次插入的元素将被忽略。
      集合中最大的成员数为 232 - 1(4294967295, 每个集合可存储40多亿个成员)。
      
      # 实例
      #   添加
      127.0.0.1:6380> SADD set1 iaso
      (integer) 1
      127.0.0.1:6380> SADD set1 saiox
      (integer) 1
      
      # 获取
      127.0.0.1:6380> smembers set1
      1) "iaso"
      2) "saiox"
      ```
      
    - 
  
  - Sorted Set: 有序集合
  
    - ```bash
      Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
      不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
      zset的成员是唯一的,但分数(score)却可以重复。
      
      zadd 命令
      添加元素到集合，元素在集合中存在则更新对应score
      
      # 实例
      #   添加
      127.0.0.1:6380> zadd soutedSet1 0 jaisdxa
      (integer) 1
      127.0.0.1:6380> zadd soutedSet1 0 jaisdxas
      (integer) 1
      127.0.0.1:6380> zadd soutedSet1 0 jaisdxasxma
      (integer) 1
      
      # 获取
      127.0.0.1:6380> ZRANGEBYSCORE soutedSet1 0 1000
      1) "jaisdxa"
      2) "jaisdxas"
      3) "jaisdxasxma"
      
      ```



## Redis命令

#### 键key

Redis 键命令用于管理 redis 的键。

- Redis 键命令的基本语法如下：

```bash
redis 127.0.0.1:6379> COMMAND KEY_NAME
```

```bash
# 实例
127.0.0.1:6379> SELECT 0      # redis默认使用数据库 0，为了清晰起见，这里再显式指定一次。
OK


#设置键 key ,值为 bar ,默认是字符串 ， 如果不存在就默认新增，已存在的话就覆盖
127.0.0.1:6379> SET key bar   
OK

# 获得键内容
127.0.0.1:6379> GET foo    
"1"                        # 获得的字符串由双引号包裹。如果不存在时会返回  (nil)  ,字符串回复

# 删除键，后面可以跟多个键。 但不支持通配符。可以通过命令行来实现
127.0.0.1:6379> DEL foo      
(integer) 1		 	             # 返回为被删除 key 的数量， 表示删除成功

# 修改 key键 的名称
127.0.0.1:6380> RENAME set1 set2
OK

# 仅当 newkey 不存在时，将 key 改名为 newkey 。(就是先判断新名字是否存在，不存在就正常修改名称)
127.0.0.1:6380> RENAMENX set2 set3
1





#  Scan 命令用于迭代数据库中的数据库键。
# SCAN 命令是一个基于游标的迭代器，每次被调用之后， 都会向用户返回一个新的游标， 用户在下次迭代时需要使用这个新游标作为 SCAN 命令的游标参数， 以此来延续之前的迭代过程。
# SCAN 返回一个包含两个元素的数组， 第一个元素是用于进行下一次迭代的新游标， 而第二个元素则是一个数组， 这个数组中包含了所有被迭代的元素。如果新游标返回 0 表示迭代已结束。

# 获得前500 个key 值
127.0.0.1:6380> SCAN 0 MATCH * COUNT 500

# 按照游标进行迭代
127.0.0.1:6380> SCAN 0		 # 使用 0 作为游标，开始新的迭代
1) "22"											# 第一次迭代时返回的游标
2)  1) "a6"
    2) "s2"
    3) "soutedSet1"
    4) "runoob1"
    5) "s7"
    6) "runoob3"
    7) "a5"
    8) "a2"
    9) "s3"
   10) "s"
127.0.0.1:6380> SCAN 22 # 使用的是第一次迭代时返回的游标 22 开始新的迭代
1) "3"
2)  1) "s0"
    2) "a3"
    3) "a4"
    4) "a0"
    5) "s8"
    6) "a9"
    7) "runoob2"
    8) "s9"
    9) "a8"
   10) "set3"
127.0.0.1:6380> SCAN 3 # 使用的是第二次迭代时返回的游标 3 开始新的迭代
1) "0"									# 返回0了， 代表已经迭代结束了
2) 1) "hashkey1"
   2) "s6"
   3) "a7"
   4) "s1"
   5) "a1"
   6) "s5"
   7) "s4"



# TYPE 返回 key 所储存的值的类型。
127.0.0.1:6380> TYPE s1
string



# 检测 Key String1 这个键值是否存在， 存在返回1
127.0.0.1:6380> EXISTS String1	
1




# 给指定的 String1 键值 设置过期时间 5秒，(以秒为单位)
127.0.0.1:6380> EXPIRE  String1 5		
1

# 给指定的 String1 键值 设置过期时间 Unix时间戳  1680245220 (2023-03-31 14:48:34)
127.0.0.1:6380> EXPIREAT raw  1680245220
		    	   		# Unix 时间戳是从1970年1月1日（UTC/GMT的午夜）开始所经过的秒数，不考虑闰秒。
		    	   		# 命令 date +%s  即可获得当前 Unix时间戳


# 用于移除给定 key 的过期时间，使得 key 永不过期。
127.0.0.1:6380> PERSIST set1 
1

# 以毫秒为单位返回 key 的剩余的过期时间
127.0.0.1:6380> PTTL set1
-1					# 返回 -1 为不过期

# 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
127.0.0.1:6380> TTL set1
-1					# 返回 -1 为不过期




# 查找所有符合给定模式 pattern 的 key  ,  显示 runoob 开头的 key 值名
127.0.0.1:6380> KEYS runoob*
runoob1
runoob3
runoob2


# 默认所在的数据库为0 ， 将 song key值 移动到数据库 1
127.0.0.1:6380> MOVE song 1 
(integer) 1					# 1 成功，0失败



# 从当前数据库中随机返回一个 key 。
127.0.0.1:6380> RANDOMKEY
soutedSet1




```

在以上实例中 **DEL** 是一个命令， **runoobkey** 是一个键。 如果键被删除成功，命令执行后输出 **(integer) 1**，否则将输出 **(integer) 0

|

| 序号 | 命令                                         | 描述                                                         |
| :--- | :------------------------------------------- | ------------------------------------------------------------ |
| 1    | `DEL key`                                    | 该命令用于在 key 存在时删除 key。                            |
| 2    | `DUMP key`                                   | 序列化给定 key ，并返回被序列化的值。                        |
| 3    | `EXISTS key`                                 | 检查给定 key 是否存在。                                      |
| 4    | `EXPIRE key   seconds`                       | 为给定 key 设置过期时间，以秒计。                            |
| 5    | `EXPIREAT key timestamp`                     | EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。 |
| 6    | `PEXPIRE key milliseconds`                   | 设置 key 的过期时间以毫秒计。                                |
| 7    | `PEXPIREAT key milliseconds-timestamp`       | 设置 key 过期时间的时间戳(unix timestamp) 以毫秒计           |
| 8    | `KEYS pattern`                               | 查找所有符合给定模式( pattern)的 key 。                      |
| 9    | `MOVE key db`                                | 将当前数据库的 key 移动到给定的数据库 db 当中。              |
| 10   | `PERSIST key`                                | 移除 key 的过期时间，key 将持久保持。                        |
| 11   | `PTTL key`                                   | 以毫秒为单位返回 key 的剩余的过期时间。                      |
| 12   | `TTL key`                                    | 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。 |
| 13   | `RANDOMKEY`                                  | 从当前数据库中随机返回一个 key 。                            |
| 14   | `RENAME key newkey`                          | 修改 key 的名称                                              |
| 15   | `RENAMENX key newkey`                        | 仅当 newkey 不存在时，将 key 改名为 newkey 。                |
| 16   | `SCAN cursor [MATCH pattern]  [COUNT count]` | 迭代数据库中的数据库键。                                     |
| 17   | `TYPE key`                                   | 返回 key 所储存的值的类型。                                  |



```bash



127.0.0.1:6379> KEYS *    #  KEYS 命令是获取数据库中符合指定规则的键名，支持通配符规则
1) "key"									# 多行字符串回复，请求一个非字符串类型键的元素列表时 会收到多行字符串回复。
2) "foo"

127.0.0.1:6379> EXISTS foo   # EXISTS 判断一个键是否存在。 
(integer) 1									 # 返回为1时， 表示存在




127.0.0.1:6379> TYPE foo	   # TYPE 获得键值的数据类型
string										   # 代表是 foo 字符串

127.0.0.1:6379> INCR foo    #INCR 会递增 foo 键值的的内容（返回整数），原子操作
(integer) 1									#返回内容， integer 代表返回的是整数 ,整数回复


127.0.0.1:6379> DBSIZE     # DBSIZE 命令会获取当前数据库中键的数量
(integer) 1									#返回内容， integer 代表返回的是整数, 整数回复


```



