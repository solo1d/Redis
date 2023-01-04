### 目录

- [启动和停止Redis](#启动和停止Redis)
  - [启动的三种方式](#启动的三种方式)
    - [1、直接启动](#1、直接启动)
    - [2、脚本启动](#2、脚本启动)
    - [配置运行方式和持久化文件、日志文件和存储位置的启动方式](#配置运行方式和持久化文件、日志文件和存储位置的启动方式)
  - [停止Redis](#停止Redis)
- [Redis命令行客户端](#Redis命令行客户端)
  - [Redis命令](#Redis命令)
  - [数据库相关命令](#数据库相关命令)
  - [修改运行时配置的命令](#修改运行时配置的命令)
  - 







# 启动和停止Redis

```bash
$redis-server     # Redis 服务器
$redis-cli    		# Redis 命令行客户端
$redis-benchmark  # Redis 性能测试工具
$redis-check-aof  # -> redis-server , Redis AOF 文件修复工具
$redis-check-rdb  # -> redis-server , Redis
$redis-sentinel   # -> redis-server , sentinel 服务器 
```

### 启动的三种方式

#### 1、直接启动

```bash
# 可直接执行 服务器命令启动
$redis-server   # 服务器默认使用 6379 端口

# 可指定端口来进行启动
$redis-server --port 6380    
```

#### 2、脚本启动

```bash
#!/bin/bash
REDISPORT=6379		# 启动指定的监听端口号
EXEC=/usr/local/bin/redis-server
CLIEXEC=/usr/local/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/etc/redis/${REDISPORT}.conf"

case "$1" in
        start)
                if [ -f $PIDFILE ]
                then
                        echo "${PIDFILE} exists, process is already running or crashed"
                else
                        echo "Starting Redis server..."
                fi
                ;;
        stop)
                if [ ! -f $PIDFILE ]
                then
                        echo "$PIDFILE does not exist, process is not running"
                else
                        PID=$(cat $PIDFILE)
                        echo "Stopping..."
                        $CLIEXEC -p ${REDISPORT} shutdown
                        while [ -x /proc/${PID} ]
                        do 
                                echo "Waiting for Redis to shutdown ..."
                                sleep 1
                        done
                        echo "Redis stopped"
                fi
                ;;
        *)
                echo "Please use start or stop as first argument"
                ;;
        esac
```



#### 配置运行方式和持久化文件、日志文件和存储位置的启动方式

```bash
1、配置初始化脚本，将其复制到 /etc/init.d 目录中，文件名为 redis_端口号 
			 端口号表示让Ridis监听的端口号，客户端通过该端口号连接 Redis
2、建立需要的文件夹
			 /etc/redis				  # 存放 Redis的配置文件
			 /var/redis/端口号   # 存放 Redis的持久化文件
3、修改配置文件。将配置文件模板(位于源代码目录中，名为 redis.conf)复制到 /etc/redis 目录中。 并以端口号命名（如: 6379.conf) 

# 配置文件需要修改的内容：
	daemonize     值为 yes                          使Redis 以守护进程模式运行
	pid           值为 /var/run/redis_端口号.pid     设置Redis 的PID文件位置
	port          值为 端口号                        设置Redis 监听的端口号
	dir           值为 /var/redis/端口号             设置持久化文件存放位置

#可使用 /etc/init.d/redis_端口号 start 来启动 Redis
$ /etc/init.d/redis_端口号  start
$ sudo update-rc.d redis_端口号 defaults    # 使 Redis 随系统自动启动


# 启动时需要将配置文件传递给服务器， 这个配置文件是以修改过的
$ redis-server /路径/端口号.conf
```



### 停止Redis

```bash
# 正常停止 Redis  ,直接执行的话会自动连接本地127.0.0.1 端口号为6379 的Redis服务器
$redis-cli  SHUTDOWN
```



## Redis命令行客户端

redis-cli 可将命令内容作为参数执行，也可以进入交互式模式下再执行。

```bash
# 指定端口号和IP 地址
$ redis-cli  -h IP地址  -p  端口号

#PING 测试客户端和Redis 的连接是否正常
$ redis-cli PING 
   # 或者  $ redis-cli  进入交互模式后，再执行 PING
```

### Redis命令

```bash
# 先连接，并进入交互模式
$ redis-cli 

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



#### 数据库相关命令

```bash
# 切换数据库命令 ,数据库默认从0开始， 而且默认支持16个数据库，可以通过配置来进行修改
# 先连接，并进入交互模式
$ redis-cli 

# 切换库
127.0.0.1:6379[1]> SELECT 0   # 由目前所在的1号数据库 切换到 0号数据库


# 清空数据库中的所有内容
127.0.0.1:6379> FLUSHALL   #  FLUSHALL 命令会清空数据库中的所有内容
OK
```



#### 修改运行时配置的命令

```bash
# 先连接，并进入交互模式
$ redis-cli 

redis> CONFIG SET loglevel warning    # 配置日志等级

127.0.0.1:6379> CONFIG GET loglevel   # 获得日志等级配置
1) "loglevel"   											# 第一个回复的是选项名， 第二行是选项值
2) "notice"

127.0.0.1:6379> CONFIG GET databases		# 获得默认数据库的数量，可以修改为其他值，来增大数据库
1) "databases"	
2) "16"

```









