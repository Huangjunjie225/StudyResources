###启动 Redis
> redis-server /etc/redis/redis.conf 

###关闭Redis
> redis-cli shutdown

###查看redis是否在运行
> redis-cli

###redis支持5中数据类型
#####字符串
> set name "yiibai"
> get name
> del name
> keys site*
> keys si?e
> keys sit[ey]
> randomkey
> type name
> exists name
> rename site wangzhi
> renamenx site search 重名的话就错
> getrange key start end
> incr age
> decr age
> incrby age 5
> decrby age 5
> incrbyfloat num 2.9
> 


select 1 到一号服务器，默认是到0号服务器
move name 1 把name移到一号服务器

ttl key 查看key的生命周期
expire key n(整数)
persist key 使某个key永久有效


#####哈希
hset->在散列里面关联起给定的键值对
hget->获取指定散列键的值
hgetall->获取散列包含的所有键值对
hdel->如果给定键存在于散列里面，那么移除这个键

> hmset user:1 username yiibai password yiibai points 200(插入数据)
> hgetall user:1(查看)

#####列表
rpush->将给定值推入列表的右端
lrange->获取列表在给定范围上的所有值
lindex->获取列表在给定位置上的单个元素
lpop->从列表的左端弹出一个值，并返回被弹出的值
rpop->从列表的右端弹出一个值，并返回被弹出的
linsert num before 3 2 在3前面插入2

> lpush tutoriallist redis
> lpush tutoriallist mongodb
> lpush tutoriallist rebitmq
> rpush tutoriallist activemq
> lrem answer 1 b 删除1个b
> lrem answer -2 a 从尾部删除2个a
> ltrim answer 2 5 截取链表的一部分
> lindex answer 1 获取第一个的值
> rpoplpush task job 从左边弹出进入job的右边（原子性的）
> brpop job 20 等20秒直到有元素进来
> 
> 
>lrange tutoriallist 0 10

#####集合
sadd->将给定元素添加到集合
smembers->返回集合包含的所有元素
sismember->检查给定元素是否存在于集合中
srem->如果给定的元素存在于集合中，那么移除这个元素


> sadd tutoriallist redis
> sadd tutoriallist mongodb
> sadd tutoriallist rabitmq
> sadd tutoriallist rabitmq
> srem tutoriallist rabitmq
> smembers tutoriallist
> spop key 随机弹出一个元素并删除它
> srandmember tutoriallist 随机取出一个元素但是不删除
> sismemeber tutooiallist key 查看集合是否是有key值
> scard tutoriallist 查看集合中有多少个元素
> smove upper lower A 将A从upper移动到lower去
> sinter upper lower... 求upper和lower的交集
> sunion upper lower... 求upper和lower的并集
> sdiff upper lower... 求upper和lower的差集
> sinterstore result upper lower...将upper,lower的交集的结果赋值给result
> 


#####集合排序
> zadd tutoriallist 0 redis
> zadd tutoriallist 1 mongodb
> zadd tutoriallist 2 rabitmq
> zadd tutoriallist 3 rabitmq
> zrangebyscore tutoriallist 0 1000
> zrangebyscore tutoriallist 1 20 limit 1 2 按score取出从1-20的前两个，去掉第一个 
> zrank tutoriallist redis   查看元素的排名
> zrevrank tutoriallist redis 倒序查看排名
> zremrangebyscore tutoriallist 1 3 删除score在1-3的元素（包含）
> zremrangebyrank tutoriallist 0 1 删除排名0到1 的元素
> zrem tutoriallist mongodb 删除指定元素
> zrange tutoriallist 0 10 wihscores
> zcard tutoriallist 统计人数
> zcount tutoriallist 1 3 统计score在1-3之间的元素的个数
> zinterstore result 2 集合1 集合2 aggregate sum 交集并求和（min）
> zinterstore result 2 集合1 集合2 weights 2 1 aggregate max 带权重的交集
> zunionstore result 2 集合1 集合2 weights 2 1 aggregate max 带权重的并集
> 


#####hash 哈希数据类型
> hset key field value 把key中field的值设为value(若有值，则覆盖)
> hmset key field1 value1 field2 value2  设置field1->N个域 
> hget key field  返回key中field域的值
> hmget key field1 field2  返回key中field1 field2 fieldN域的值
> hgetall key
> hdel user1 age 删除user1中的域age
> hlen user1 查看user1有多少个域
> hexists user1 age 查看user1是否有域age
> hincrby user1 age 1 域age增1
> hincrbyfloat user1 age 0.5
> hkeys user1 查看所有域
> hvals userl 查看所有值

#####排序
sort userIdList desc|asc


#####Redis HyperLogLog(用来做基数统计的算法)
#####在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的
> pfadd tutorials "redis"
> pfadd tutorials "mongodb"
> pfadd tutorials "mysql"

>pfcount tutorials



####Redis发布订阅(Redis订阅和发布实现了通讯系统，发件人（在 Redis 中的术语称为发布者）发送邮件，而接收器（订户）接收它们。信息传输的链路称为通道。Redis 一个客户端可以订阅任意数量的通道。)
> subscibe news(psubscibe new* 通配符)

> publish news 'today is sunshine'

> publish news 'still sunshine'


#####Redis事务
######Redis事务允许一组命令在单一步骤中执行。事务有两个属性，说明如下：
	在一个事务中的所有命令作为单个独立的操作顺序执行。在Redis事务中的执行过程中而另一客户机发出的请求，这是不可以的；
	Redis事务是原子的。原子意味着要么所有的命令都执行，要么都不执行；
> flushdb
> set wang 200
> set zhao 700
> multi
> decrby zhao 100
> incrby wang 100
> exec(discard取消)

用于订票，监视ticket，一有变动就执行错误
如果前面已经有人锁住了ticket并更改了值，那就执行错误
> watch ticket
> multi
> decr ticket
> decrby money 100
> exec
> get ticket
> get money
> unwatch

#####Redis脚本(Redis 脚本是使用Lua解释脚本用来评估（计算）。从 Redis 2.6.0 版本开始内置这个解释器。命令 EVAL 用于执行 脚本命令。)
> eval "return {keys[1],keys[2],argv[1],argv[2]}" 2 key1 key2 first second

#####Redis连接
> auth "password"
> ping

#####redis 进程关掉
> pkill -9 redis

#####redis备份
> save(在执行此命令之后，将在 redis 目录中创建一个 dump.rdb 文件)

######恢复redis数据
> config get dir
######Bgsave(此命令将启动备份过程，并在后台运行此)
> bgsave

#####Redis安全
> config get requirepass(默认情况下此属性是空的，这意味着此实例没有设置密码。可以通过执行以下命令来修改设置此属性)
> config set requirepass "junjie"
> config get requirepass

> auth "junjie"(输入密码)

#####redis 性能测试
> redis-benchmark -n 100000

#####Redis客户端连接
######客户端最大连接数量(在Redis的配置文件（redis.conf）有一个属性 maxclients ，它描述了可以连接到 Redis 的客户的最大数量)
> redis-server --maxclients 100000


#####主从复制
> cp redis.conf redis6380.conf
> cp redis.conf redis6381.conf
配置redis6380.conf和redis6381.conf文件
让这两个服务器slave of 主服务器
只用设置localhost port（主服务器的地址） and read only
同时设置主服务器的rdb 和 aop都关掉
设置redis6380.conf的aop 和rdb开启
建立多个服务器在同一台主机上
> ./bin/redis-server redis.conf(开启主服务器)
> ./bin/redis-server redis6380.conf(开启隶属服务器)
> ./bin/redis-server redis6381.conf(开启隶属服务器)
> ./bin/redis-cli(连接主服务器的客户端)
> set title redis study
> ./bin/redis-cli -p 6380(连接从服务器的客户端)
> ./bin/redis-cli -p 6381(连接从服务器的客户端)
可以在从服务器上查询相关的记录，但不能写入

从客户端的配置文件设置连接主服务器的密码

#####Redis管道(客户端可以发送多个请求给服务器，而不等待全部响应，最后在单个步骤中读取所有响应)

#####redis 运维
常见命令：
1) time
2) dbsize 查看当前数据库所有key的数量
3) bgrewriteaof   立即重写aof文件
4) save   立即生成rdb文件，直接进行save，会堵塞
5) bgsave 另开一个线程进行save
6) info 具体信息
7）config get requirepass 查看密码
8) config get slowlog-log-slower-than 操作比指定的时间慢，就会生成慢日志
9) config set slowlog-log-slower-than 100
10) config get slowlog-max-len  最多多少条慢日志
11) config set slowlog-max-len 128
12) slowlog get 查看慢日志
13) shutdown save/nosave  关闭终端


#####flushall 后补救
1.shutdown nosave (防止被重写)
2.vim appendonly6379.aof 将最后一次的flushall删除掉（3行）
3.启动服务器

#####替换服务器
1.save 
2.shutdown nosave (防止下面复制的rdb有着原来服务器的句柄，使得复制后的两个rdb是独立的两个文件)
3.pkill -9 redis
4.复制一份rdb   cp dump.rdb dump6380.rdb
5.启动替换服务器


#####模拟动态替换master
1.6379) shutdown
2.6380) slaveof no one
3.6380) config set slave-read-only no
4.6381) slave of localhost 6380

#####使用sentinel来检测master(开启了master和两个slave)
1.从源码中复制一份sentinel.conf 到指定目录 cp /tmp/redis-2.8.3/sentinel.conf ./
2.修改配置文件
3.redis-server sentinel.conf --sentinel
4.6379) shutdown 
5.稍等片刻就可以看到6381成master，有一个slave，而6380的master是6381

#####tip
通过修改redis6380.conf的slave-priority 100设置优先级
就可以替换到6380服务器了（越小越优先被替换成master）

#####配置外网可访问
> iptables -A INPUT -p TCP --dport 6379 -j (/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT)
> service iptables save

//只允许127.0.0.1访问6379
> iptables -A INPUT -s 127.0.0.1 -p tcp --dport 6379 -j ACCEP
> service iptables save

iptables -L -n


#####修改主机名
vim /etc/sysconfig/network
127.0.0.1  localhost  localhost.localdomain  VM_40_47_centos

#####Redis key 设计技巧

	1: 把表名转换为key前缀 如, tag:
	2: 第2段放置用于区分区key的字段--对应mysql中的主键的列名,如userid
	3: 第3段放置主键值,如2,3,4...., a , b ,c
	4: 第4段,写要存储的列名
	
	用户表 user  , 转换为key-value存储
	userid	username	passworde	   email
	  9		 Lisi	     1111111	lisi@163.com

	set  user:userid:9:username lisi
	set  user:userid:9:password 111111
	set  user:userid:9:email   lisi@163.com

	keys user:userid:9*

	2 注意:
	在关系型数据中,除主键外,还有可能其他列也步骤查询,
	如上表中, username 也是极频繁查询的,往往这种列也是加了索引的.

	转换到k-v数据中,则也要相应的生成一条按照该列为主的key-value
	set  user:username:lisi:uid  9  

	这样,我们可以根据username:lisi:uid ,查出userid=9, 
	再查user:9:password/email ...


















