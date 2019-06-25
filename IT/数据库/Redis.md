# Redis

### 简介

- 什么是NoSQL

  > NoSQL --> not only sql, 泛指非关系型数据库
  >
  > - 不支持SQL语法
  > - NoSQL中存储的数据都是KV形式
  > - NoSQL产品: Mongodb, Redis, Hbase hadoop, Cassandra hadoop

- Redis特性

  - Redis支持数据持久化
  - Redis不仅仅支持K-V，同时还提供list，set，zset，hash等数据结构的存储。
  - Redis支持数据的备份，即master-slave模式的数据备份。

- Redis网站

  - [redis官方网站](https://redis.io/)
  - [redis中文官网](http://redis.cn/)

### 1. 安装配置

#### 1) 安装

- 当前redis最新稳定版本是4.0.9

- 最新稳定版本下载链接： <http://download.redis.io/releases/redis-4.0.9.tar.gz>

- step1:下载

  > wget <http://download.redis.io/releases/redis-4.0.9.tar.gz>

- step2:解压

  > tar xzf redis-4.0.9.tar.gz

- step3:移动，放到usr/local⽬录下

  > sudo mv ./redis-4.0.9 /usr/local/redis/

- step4:进⼊redis⽬录

  > cd /usr/local/redis/

- step5:生成

  > sudo make

- step6:测试,这段运⾏时间会较⻓

  > sudo make test

  - step6中常见错误:

  1. You need tcl 8.5 or newer in order to run the Redis test	

     ```bash
     # 安装tcl
     wget http://downloads.sourceforge.net/tcl/tcl8.6.1-src.tar.gz  
     sudo tar xzvf tcl8.6.1-src.tar.gz  -C /usr/local/  
     cd  /usr/local/tcl8.6.1/unix/  
     sudo ./configure  
     sudo make  
     sudo make install   
     ```

  2. /bin/sh: cc: command not found

     ```bash
     sudo apt install gcc
     ```

  3. recipe for target 'test' failed

     ```bash
     内存问题,1Gb内存不成功,2Gb成功
     ```

     

- step7:安装,将redis的命令安装到/usr/local/bin/⽬录

  > sudo make install

- step8:安装完成后，我们进入目录/usr/local/bin中查看

  > cd /usr/local/bin
  > ls -a

  > redis-server redis服务器
  >
  > - redis-cli redis命令行客户端
  > - redis-benchmark redis性能测试工具
  > - redis-check-aof AOF文件修复工具
  > - redis-check-rdb RDB文件检索工具

- step9:配置⽂件，移动到/etc/⽬录下

  > 配置⽂件⽬录为/usr/local/redis/redis.conf

  > sudo cp /usr/local/redis/redis.conf /etc/redis/

- Mac 上安装 Redis:

  - 安装 Homebrew：

  > <https://brew.sh/>

  - 使用 brew 安装 Redis

  > <https://www.cnblogs.com/cloudshadow/p/mac_brew_install_redis.html>

#### 2) 配置

> sudo vi /etc/redis/redis.conf

- 核心配置选项

  - 绑定ip：如果需要远程访问，可将此⾏注释，或绑定⼀个真实ip

    > bind 127.0.0.1

  - 端⼝，默认为6379

    > port 6379

  - 是否以守护进程运⾏

    - 如果以守护进程运⾏，则不会在命令⾏阻塞，类似于服务
    - 如果以⾮守护进程运⾏，则当前终端被阻塞
    - 设置为yes表示守护进程，设置为no表示⾮守护进程
    - 推荐设置为yes

    > daemonize yes

  - 数据⽂件

    > dbfilename dump.rdb

  - 数据⽂件存储路径

    > dir /var/lib/redis

  - ⽇志⽂件  '有问题, 未配置成功'

    > logfile "/var/log/redis/redis-server.log"

  - 数据库，默认有16个

    > database 16

  - 主从复制，类似于双机备份。

    > slaveof

- 配置参考资料

  - <http://blog.csdn.net/ljphilp/article/details/52934933>

### 2. serveice和cli命令

- 服务器端命令

  - 服务器端的命令为

    ```bash
    redis-server /etc/redis/redis.conf  # 指定加载的配置文件开启redis
    redis-server --help  # 查看服务器命令
    ```

  - 常用命令

    ```bash
    ps aux | grep redis  # 查看redis服务器进程
    sudo kill -9 pid  # 杀死redis服务器
    sudo redis-server /etc/redis/redis.conf  # 指定加载的配置文件
    ```

  - 后台启动，开启配置文件中的 守护进程  daemonize yes

- 客户端的命令

  - 连接服务器

    ```bash
    redis-cli  # 连接redis
    redis-cli --help  # 查看命令帮助
    ping  # 测试连接
    ```

  - 切换数据库

    > 数据库没有名称，默认有16个，通过0-15来标识，连接redis默认选择第一个数据库

    ```bash
    select 10
    ```

### 3. 数据操作



#### 1) string类型

- 简介

  > string是 Redis 中最为基础的数据存储类型，是二进制安全的，所以可以接受任何格式的数据，如JPEG图像数据或Json对象描述信息等。
  >
  > 在Redis中字符串类型的Value最多可以容纳的数据长度是512M。

- 保存

  如果设置的键不存在则为添加，如果设置的键已经存在则修改

  ```python
  # 设置键值
  set key value
  # 设置键值及过期时间，以秒为单位
  setex key seconds value
  # 设置多个键值
  mset key1 value1 key2 value2 ...
  # 追加值
  append key value
  ```

- 查询

  ```python
  # 获取：根据键获取值，如果不存在此键则返回nil
  get key
  # 根据多个键获取多个值
  mget key1 key2 ...
  ```

- 删除

  ```bash
  # 删除键时会将值删除
  del key1 key2 ...
  ```

#### 2) 键命令

- 常用操作

  ```bash
  # 查找键，参数⽀持正则表达式
  keys pattern  # eg: keys *,  keys 'a*'
  # 判断键是否存在，如果存在返回1，不存在返回0
  exists key1
  # 查看键对应的value的类型
  type key
  # 删除键及对应的值
  del key1 key2 ...
  # 设置过期时间，以秒为单位,如果没有指定过期时间则⼀直存在，直到使⽤DEL移除
  expire key seconds
  # 查看有效时间，以秒为单位
  ttl key
  
  ```

#### 3) hash

- 简介

  > hash⽤于存储对象，对象的结构为属性、值
  >
  > 值的类型为string

- 增加、修改

  ```bash
  # 设置单个属性
  hset key field value
  # 设置多个属性
  hmset key field1 value1 field2 value2 ...
  ```



- MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error.

- Redis被配置为保存数据库快照，但它目前不能持久化到硬盘。用来修改集合数据的命令不能用

- 原因：

  - 强制关闭Redis快照导致不能持久化。 解决方案：
  - 运行config set stop-writes-on-bgsave-error no　命令后，关闭配置项stop-writes-on-bgsave-error解决该问题。

- 查询

  ```bash
  # 获取指定键所有的属性
  hkeys key
  # 获取⼀个属性的值
  hget key field
  # 获取多个属性的值
  hmget key field1 field2 ...
  # 获取所有属性的值
  hvals key
  ```

- 删除

  ```bash
  # 删除整个hash键及值，使⽤del命令
  
  # 删除属性，属性对应的值会被⼀起删除
  hdel key field1 field2 ...
  ```



#### 4) list

- 简介

  - 列表的元素类型为string
  - 按照插⼊顺序排序

- 增加

  ```bash
  # 在左侧插⼊数据
  lpush key value1 value2 ...
  # 在右侧插⼊数据
  rpush key value1 value2 ...
  # 在指定元素的前或后插⼊新元素
  linsert key before或after 现有元素 新元素
  ```

- 修改

  ```bash
  # 设置指定索引位置的元素值
  # 索引从左侧开始，第⼀个元素为0
  # 索引可以是负数，表示尾部开始计数，如-1表示最后⼀个元素
  lset key index value
  ```

- 查询

  ```bash
  # 返回列表⾥指定范围内的元素
  # start、stop为元素的下标索引
  lrange key start stop
  ```

- 删除

  ```bash
  # 将列表中前count次出现的值为value的元素移除
  # count > 0: 从头往尾移除
  # count < 0: 从尾往头移除
  # count = 0: 移除所有
  lrem key count value
  
  ```

#### 5) set

- 简介

  - ⽆序集合
  - 元素为string类型
  - 元素具有唯⼀性，不重复
  - 说明：对于集合没有修改操作

- 增加

  ```bash
  # 添加元素
  sadd key member1 member2 ...
  
  ```

- 查询

  ```bash
  # 返回所有的元素
  smembers key
  
  ```

- 删除

  ```bash
  # 删除指定元素
  srem key
  
  ```

#### 6) zset

- 简介

  - sorted set，有序集合
  - 元素为string类型
  - 元素具有唯⼀性，不重复
  - 每个元素都会关联⼀个double类型的score，表示权重，通过权重将元素从⼩到⼤排序
  - 说明：没有修改操作

- 增加

  ```bash
  zadd key score1 member1 score2 member2 ...
  
  ```

- 查询

  ```bash
  # 返回指定范围内的元素
      # start、stop为元素的下标索引
      # 索引从左侧开始，第⼀个元素为0
      # 索引可以是负数，表示从尾部开始计数，如-1表示最后⼀个元素
  zrange key start stop
  # 返回score值在min和max之间的成员
  zrangebyscore key min max
  # 返回成员member的score值
  zscore key member
  
  ```

- 删除

  ```bash
  # 删除指定元素
  zrem key member1 member2 ...
  # 删除权重在指定范围的元素
  zremrangebyscore key min max
  
  ```



### 4. 与python交互

​	安装Redis的有3种方式<https://github.com/andymccurdy/redis-py>

- 安装Redis

  ```bash
  # 方式一
  pip install redis
  # 方式二
  easy_install redis
  # 方式三: 源码安装
  wget <https://github.com/andymccurdy/redis-py/archive/master.zip>
  unzip master.zip
  cd redis-py-master
  sudo python setup.py install
  
  ```

- 使用

  ```python
  from redis import *
  # redis模块中提供了StrictRedis对象(Strict严格)，⽤于连接redis服务器，并按照不同类型提供 了不同⽅法，进⾏交互操作
  try:
  	sr = StrictRedis(host='localhost', port=6379, db=0)
  excepy Exception as e:
      print(e)
  
  ```

  > 根据不同的类型，拥有不同的实例⽅法可以调⽤，与前⾯学的redis命令对应，⽅法需要的参数与命令的参数⼀致

  - string

    set, setex, mset, append, get, mget, key

  - keys

    exists, type, delete, expire, getrange, ttl

  - hash

    hset, hmset, hkeys, hget, hmget, hvals, hdel

  - list

    lpush, rpush, linsert, lrange, lset, lrem

  - set

    sadd, smembers, srem

  - zset

    zadd, zrange, zrangebyscore, zscore, zrem, zremrangebyscore

- 对String类型数据的操作

  ```python
  # 获取键name的值
  result = sr.get('name')
  # 设置键name的值，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
  result = sr.set('name','itcast')
  # 删除键
  result = sr.delete('name')
  # 获取所有的键
  result=sr.keys()
  
  ```

### 5. 搭建主从服务器

- 简介

  > master-slave
  >
  > ⼀个master有多个slave，⼀个slave有多个slave，形成多级服务器集群架构
  >
  > master用来写数据，slave用来读数据, 读写比率是10:1
  >
  > 通过主从配置可以实现读写分离

- 主从配置

  - 配置主

    1. 修改/etc/redis/redis.conf文件

       ```bash
       sudo vi redis.conf
       bind 192.168.199.113
       
       ```

    2. 重启redis服务

       ```python
       sudo service redis stop
       redis-server redis.conf
       
       ```

  - 配置从

    1. 复制/etc/redis/redis.conf文件

       ```bash
       sudo cp redis.conf ./slave.conf
       
       ```

    2. 修改/etc/redis/slave.conf文件

       ```bash
       sudo vi slave.conf
       
       ```

    3. 编辑内容

       ```bash
       bind 192.168.26.128
       slaveof 192.168.26.128 6379
       port 6378
       
       ```

    4. redis服务

       ```bash
        sudo redis-server slave.conf
       
       ```

    5. 查看主从关系

       ```bash
        redis-cli -h 192.168.26.128 info Replication
       
       ```

- 数据操作

  - 在master和slave分别执⾏info命令，查看输出信息 进入主客户端

    > redis-cli -h 192.168.26.128 -p 6379

  - 进入从的客户端

    > redis-cli -h 192.168.26.128 -p 6378

  - 在master上写数据

    > set aa aa

  - 在slave上读数据

    > get aa



### 6. 搭建集群

- 集群的概念

  > 集群是一组相互独立的、通过高速网络互联的计算机，它们构成了一个组，并以单一系统的模式加以管理。一个客户与集群相互作用时，集群像是一个独立的服务器。集群配置是用于提高可用性和可缩放性。
  >
  > 
  >
  > 当请求到来首先由负载均衡服务器处理，把请求转发到另外的一台服务器上
  >
  > - redis集群搭建 <http://www.cnblogs.com/wuxl360/p/5920330.html>
  > - [Python]搭建redis集群 <http://blog.5ibc.net/p/51020.html>

- 集群分类

  - 软件层面：只有一台电脑，在这一台电脑上启动了多个redis服务。
  - 硬件层面：存在多台实体的电脑，每台电脑上都启动了一个redis或者多个redis服务。

- 配置机器1

  - 在演示中，172.16.179.130为当前ubuntu机器的ip

  - 在172.16.179.130上进⼊Desktop⽬录，创建conf⽬录

  - 在conf⽬录下创建⽂件7000.conf，编辑内容如下

    ```
    port 7000
    bind 172.16.179.130
    daemonize yes
    pidfile 7000.pid
    cluster-enabled yes
    cluster-config-file 7000_node.conf
    cluster-node-timeout 15000
    appendonly yes
    
    ```

  - 在conf⽬录下创建⽂件7001.conf，编辑内容如下

    ```
    port 7001
    bind 172.16.179.130
    daemonize yes
    pidfile 7001.pid
    cluster-enabled yes
    cluster-config-file 7001_node.conf
    cluster-node-timeout 15000
    appendonly yes
    
    ```

  - 在conf⽬录下创建⽂件7002.conf，编辑内容如下

    ```
    port 7002
    bind 172.16.179.130
    daemonize yes
    pidfile 7002.pid
    cluster-enabled yes
    cluster-config-file 7002_node.conf
    cluster-node-timeout 15000
    appendonly yes
    
    ```

  - 总结：三个⽂件的配置区别在port、pidfile、cluster-config-file三项

  - 使⽤配置⽂件启动redis服务

    ```
    redis-server 7000.conf
    redis-server 7001.conf
    redis-server 7002.conf
    
    ```

- 配置机器2

  - 在演示中，172.16.179.131为当前ubuntu机器的ip

  - 在172.16.179.131上进⼊Desktop⽬录，创建conf⽬录

  - 在conf⽬录下创建⽂件7003.conf，编辑内容如下

    ```
    port 7003
    bind 172.16.179.131
    daemonize yes
    pidfile 7003.pid
    cluster-enabled yes
    cluster-config-file 7003_node.conf
    cluster-node-timeout 15000
    appendonly yes
    
    ```

  - 在conf⽬录下创建⽂件7004.conf，编辑内容如下

    ```
    port 7004
    bind 172.16.179.131
    daemonize yes
    pidfile 7004.pid
    cluster-enabled yes
    cluster-config-file 7004_node.conf
    cluster-node-timeout 15000
    appendonly yes
    
    ```

  - 在conf⽬录下创建⽂件7005.conf，编辑内容如下

    ```
    port 7005
    bind 172.16.179.131
    daemonize yes
    pidfile 7005.pid
    cluster-enabled yes
    cluster-config-file 7005_node.conf
    cluster-node-timeout 15000
    appendonly yes
    
    ```

  - 总结：三个⽂件的配置区别在port、pidfile、cluster-config-file三项

  - 使⽤配置⽂件启动redis服务

    ```
    redis-server 7003.conf
    redis-server 7004.conf
    redis-server 7005.conf
    
    ```



- 创建集群

  - redis的安装包中包含了redis-trib.rb，⽤于创建集群

  - 接下来的操作在172.16.179.130机器上进⾏

  - 将命令复制，这样可以在任何⽬录下调⽤此命令

    ```
    sudo cp /usr/share/doc/redis-tools/examples/redis-trib.rb /usr/local/bin/
    
    ```

  - 安装ruby环境，因为redis-trib.rb是⽤ruby开发的

    > sudo apt-get install ruby

  - 在提示信息处输⼊y，然后回⻋继续安装 ![安装](/assets/p1_61.png)

  - 运⾏如下命令创建集群

    ```
    redis-trib.rb create --replicas 1 172.16.179.130:7000 172.16.179.130:7001 172.16.179.130:7002 172.16.179.131:7003 172.16.179.131:7004 172.16.179.131:7005
    
    ```

  - 执⾏上⾯这个指令在某些机器上可能会报错,主要原因是由于安装的 ruby 不是最 新版本!

  - 天朝的防⽕墙导致⽆法下载最新版本,所以需要设置 gem 的源

  - 解决办法如下

    ```
    -- 先查看⾃⼰的 gem 源是什么地址
    gem source -l -- 如果是https://rubygems.org/ 就需要更换
    -- 更换指令为
    gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
    -- 通过 gem 安装 redis 的相关依赖
    sudo gem install redis
    -- 然后重新执⾏指令
    
    ```

    ```
    redis-trib.rb create --replicas 1 172.16.179.130:7000 172.16.179.130:7001 172.16.179.130:7002 172.16.179.131:7003 172.16.179.131:7004 172.16.179.131:7005
    
    ```

  - 提示如下主从信息，输⼊yes后回⻋ 

  - 提示完成，集群搭建成功

- 验证连接到集群

  > redis-cli -h 172.16.179.131 -c -p 7002

- 集群下python交互

  > pip install redis-py-cluster
  >
  > redis-py-cluster源码地址<https://github.com/Grokzen/redis-py-cluster>

  - 创建⽂件redis_cluster.py，示例码如下

    ```python
    from rediscluster import *
    if __name__ == '__main__':
      try:
        # 构建所有的节点，Redis会使⽤CRC16算法，将键和值写到某个节点上
        startup_nodes = [
            {'host': '192.168.26.128', 'port': '7000'},
            {'host': '192.168.26.130', 'port': '7003'},
            {'host': '192.168.26.128', 'port': '7001'},
        ]
        # 构建StrictRedisCluster对象
        src=StrictRedisCluster(startup_nodes=startup_nodes,decode_responses=True)
        # 设置键为name、值为itheima的数据
        result=src.set('name','itheima')
        print(result)
        # 获取键为name
        name = src.get('name')
        print(name)
      except Exception as e:
        print(e)
    
    ```

### 7. redis安全

- error

  > (error) NOAUTH Authentication required

  ```bash
  127.0.0.1:6379> config get requirepass  # 查看是否设置密码
  1) "requirepass"
  2) "root"  # 密码
  127.0.0.1:6379> CONFIG set requirepass ""  # 删除设置密码
  OK
  127.0.0.1:6379> config get requirepass
  1) "requirepass"
  2) ""
  127.0.0.1:6379> AUTH root  # 有密码时用 AUTH password 登录
  
  ```

  

