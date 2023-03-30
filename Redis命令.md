### 目录

- [Redis数据类型和实例](#Redis数据类型和实例)
- [Redis命令](#Redis命令)
- 



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
      # 设置字符串
      redis 127.0.0.1:6379> SET key1 "字符串"
      OK
      #获取字符串
      redis 127.0.0.1:6379> GET key1
      "字符串"
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
      
      # 获取哈希1，直接返回字符串
      127.0.0.1:6380> HGET hashKey field1
      "hello"
      127.0.0.1:6380> HGET hashKey field2
      "world"
      
      # 获取哈希2，返回字符串和标签
      127.0.0.1:6380> HMGET hashKey field2
      1) "world"
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
      
      # 实例
      
      ```

    - 

  - Sorted Set: 有序集合





## Redis命令

```bash
# 先连接，并进入交互模式
$ redis-cli  -c  -p 端口号  -h ip

127.0.0.1:6379> SET key bar   #设置键 key ,值为 bar ,默认是字符串 ， 如果不存在就默认新增
OK

127.0.0.1:6379> GET foo    # 获得键内容
"1"                        # 获得的字符串由双引号包裹。如果不存在时会返回  (nil)  ,字符串回复


127.0.0.1:6379> KEYS *    #  KEYS 命令是获取数据库中符合指定规则的键名，支持通配符规则
1) "key"									# 多行字符串回复，请求一个非字符串类型键的元素列表时 会收到多行字符串回复。
2) "foo"

127.0.0.1:6379> EXISTS foo   # EXISTS 判断一个键是否存在。 
(integer) 1									 # 返回为1时， 表示存在


127.0.0.1:6379> DEL foo      # 删除键，后面可以跟多个键。 但不支持通配符。可以通过命令行来实现
(integer) 1		 	             # 返回为1时， 表示删除成功

127.0.0.1:6379> TYPE foo	   # TYPE 获得键值的数据类型
string										   # 代表是 foo 字符串

127.0.0.1:6379> INCR foo    #INCR 会递增 foo 键值的的内容（返回整数），原子操作
(integer) 1									#返回内容， integer 代表返回的是整数 ,整数回复


127.0.0.1:6379> DBSIZE     # DBSIZE 命令会获取当前数据库中键的数量
(integer) 1									#返回内容， integer 代表返回的是整数, 整数回复


```

