### 1.安装和卸载

- **安装**

  ```bash
  # 安装服务器
  sudo apt install mysql-server
  sudo service mysql start
  sudo service mysql stop
  sudo service mysql restart
  ps ajx|grep mysql
   
  # 安装客户端
  sudo apt install mysql-client
  ```

- **配置**

  ```bash
  # 配置MySQL
  sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
  # 数据库授权
  grant all privileges on *.* to 'root'@'192.168.199.133' identified by 'root' with grant option;
  flush privileges;
  # 修改数据库密码
  sudo cat /etc/mysql/debian.cnf
  # 这条指令的密码输入是输入第一条指令获得的信息中的 password = ZCt7QB7d8O3rFKQZ 得来。
  mysql -u debian-sys-maint -p
  # 修改密码，本篇文章将密码修改成 root , 用户可自行定义。
  use mysql;
  update mysql.user set authentication_string=password('root') where user='root' and Host ='localhost';
  update user set plugin="mysql_native_password"; 
  flush privileges;
  quit;
  # 重新启动mysql:
  sudo service mysql restart
  ```

- **卸载**

  ```bash
  # 首先用dpkg --list|grep mysql查看自己的mysql有哪些依赖
  sudo apt-get remove mysql-common
  sudo apt-get autoremove --purge mysql-server-5.0 
  # 再用dpkg --list|grep mysql查看，还剩什么就卸载什么
  # 最后清楚残留数据：
  dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
  ```

- 数据库授权

  - 创建用户

    ```mysql
    CREATE USER 'username'@'host' IDENTIFIED BY 'password';
    ```

    说明：

    - username：你将创建的用户名
    - host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，如果想让该用户可以**从任意远程主机登陆**，可以使用通配符`%`
    - password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器

    例如：

    ```mysql
    CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
    CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456';
    CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
    CREATE USER 'pig'@'%' IDENTIFIED BY '';
    CREATE USER 'pig'@'%';
    ```

  - 授权

    ```mysql
    GRANT privileges ON databasename.tablename TO 'username'@'host'
    ```

    说明：

    - privileges：用户的操作权限，如`SELECT`，`INSERT`，`UPDATE`等，如果要授予所的权限则使用`ALL`
    - databasename：数据库名
    - tablename：表名，如果要授予该用户对所有数据库和表的相应操作权限则可用`*`表示，如`*.*`

    例如：

    ```mysql
    GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
    GRANT ALL ON *.* TO 'pig'@'%';
    GRANT ALL ON maindataplus.* TO 'pig'@'%';
    ```

    注意:

    > 用以上命令授权的用户不能给其它用户授权，如果想让该用户可以授权，用以下命令:

    ```
    GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
    ```

  - 设置与更改用户密码

    - 命令:

    ```mysql
    SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');
    ```

    - 如果是当前登陆用户用:

    ```mysql
    SET PASSWORD = PASSWORD("newpassword");
    ```

  - 撤销用户权限

    - 命令

    ```mysql
    REVOKE privilege ON databasename.tablename FROM 'username'@'host';
    ```

  - 删除用户

    - 命令

    ```mysql
    DROP USER 'username'@'host';
    ```
  
- 远程访问数据库

  ```bash
  1. 控制台 开放端口3306
  2. 修改配置
  	vim /etc/mysql/mysql.conf.d/mysqld.cnf
  	注释#bind-address = 127.0.0.1
  3. 给用户授权允许远程访问:
  	grant all privileges on *.* to root@"%" identified by "pwd" with grant option;
  	flush privileges;
  4. 重启mysql
  	当mysql重启不了的时候,看看日志目录比如/var/log/mysql 是否存在,属组和属主是否是mysql
  	如果没有,创建目录,并更改目录的所有者 chown mysql:mysql 
  ```

#### 1) 快速开始

- 初始化数据库操作

  ```bash
  create database meiduo_mall default charset=utf8;
  
  # 创建用户
  create user meiduo identified by 'meiduo';  
  # 授权meiduo_mall数据库下的所有表（meiduo_mall.*）的所有权限（all）给用户meiduo在以任何ip访问数据库的时候（'meiduo'@'%'）
  grant all on meiduo_mall.* to 'meiduo'@'%';  
  # 刷新生效用户权限
  flush privileges;
  ```

  

### 2. MySQL数据类型和约束

- **常用数据类型**

  - 整数：int，bit
  - 小数：decimal
  - 字符串：varchar,char
  - 日期时间: date, time, datetime
  - 枚举类型(enum)

  > decimal表示浮点数，如decimal(5,2)表示共存5位数，小数占2位
  > char表示固定长度的字符串，如char(3)，如果填充'ab'时会补一个空格为'ab '
  > varchar表示可变长度的字符串，如varchar(3)，填充'ab'时就会存储'ab'
  > text字符串表示存储大文本，当字符大于4000时推荐使用
  > 对于图片、音频、视频等文件，不存储在数据库中，而是上传到某个服务器上，然后在表中存储这个文件的保存路径



- **约束**

  - 主键primary key：物理上存储的顺序
  - 非空not null：此字段不允许填写空值
  - 惟一unique：此字段的值不允许重复
  - 默认default：当不填写此值时会使用默认值，如果填写时以填写为准
  - 外键foreign key：对关系字段进行约束，当为关系字段填写值时，会到关联的表中查询此值是否存在，如果存在则填写成功，如果不存在则填写失败并抛出异常

  > 说明:
  >
  > 虽然外键约束可以保证数据的有效性，但是在进行数据的crud（增加、修改、删除、查询）时，都会降低数据库的性能，所以不推荐使用，那么数据的有效性怎么保证呢？答：可以在逻辑层进行控制



### 3.对数据库及数据的操作

- **登录及版本**

  ```bash
  mysql -uroot -p  # 登录数据库
  quit exit ctrl+d  # 退出数据库
  select version();  # 查看当前版本
  select now();  # 查看当前时间
  ```

- 操作数据库

  ```bash
  show databases;  # 查看所有数据库
  use test1;  # 使用数据库
  create database test1 charset=utf8;  # 创建数据库
  select database();  # 查看当前使用的数据库
  drop database test1;  # 删除数据库
  ```

- 操作数据表

  ```sql
  show tables;  # 查看数据库中所有表
  desc classes;  # 查看表结构
  show create table classes;  # 查看表创建语句
  # 创建表
  create table classes(
      id int unsigned auto_increment primary key not null,
      name varchar(10)
  );
  create table students(
      id int unsigned auto_increment primary key not null,
      name varchar(20) default '',
      age tinyint unsigned default 0,
      gender enum('男','女','人妖','保密'),
      cls_id int unsigned default 0
  );
   
  # 修改表
  alter table students add birthday datetime;  # 添加字段
  alter table students modify birthday varchar(200);  # 修改字段属性,原属性丢失,modify修改时字段必须带有数据类型可以带有属性,也可以没有
  alter table students modify birthday date after name;  # 将birthday移到name后面
  alter table students modify birthday date first;  # 将birthday移到第一个
  alter table students change birthday birth datetime not null;  # 重命名修改表字段
  alter table students alter age set default 18;  # 给字段添加默认值
  alter table students alter age drop default;  # 删除默认值
  alter table students add primary key(id);  # 添加主键
  alter table students drop primary key;  # 删除主键
  alter table students drop birthday;   # 删除字段
   
  # 删除表
  drop table students;
  ```

- **数据备份和恢复**

  ```sq
  mysqldump -uroot -p test1 > python.sql  # 备份
  mysql -uroot -p test2 < python.sql  # 恢复
  ```

- **操作数据**

  - 基本操作CRUD

  ```sql
  -- 基本查询
  select * from classes;
  select id,name from classes;
  -- 增加
  insert into students values(0,'张三',1,'甘肃','2018-1-1');  -- 全列插入  主键用0或者 default 或者 null 来占位
  insert into students(name,hometown,birthday) values('黄蓉','桃花岛','2018-1-1');  -- 部分插入
  insert into classes values(0,'python1'),(0,'python2');
  insert into classes(name) values('java'),('php'),('c')
  -- 修改
  update students set gender=0,hometown='北京' where id=5;
  -- 删除
  delete from student where id=5;
  update students set isdelete=1 where id=1;  -- 逻辑删除
  ```

  - 查询操作

    - 基础查询

    ```sql
    select * from 表名   -- 查询所有字段
    select 列1, 列2, ... from 表名;  -- 查询指定字段
    select s.id as 序号, s.name as 名字 from students as s  -- as起别名
    select distinct 列1, ... from 表名;  -- 消除重复行
    ```

    - 条件查询

    ```sql
    -- 比较查询    = > >= < <= !=
    select * from students where id > 3;
    -- 逻辑运算符 and or not
    select * from students where id > 3 and gender=0;
    -- 模糊查询  like (%任意多个任意字符, _任意一个字符)
    select * from students where name like '黄%';
    -- 范围查询   in between...and...
    select * from students where id in(1,3,8);
    select * from students where id between 3 and 8;
    -- 空判断	null    is not null
    select * from students where height is null;
    ```

    - 排序

    ```sq
    select * from 表名 order by 列1 asc|desc [,列2 asc|desc,...]
    ```

    - 聚合函数

    ```sql
    count(*)  -- 计算总行数
    max(列)  -- 求此列最大值
    min(列)  -- 求此列最小值
    sum(列)  -- 求此列的和
    avg(列)  -- 求此列的平均值
    eg:  select avg(id) from students where is_delete=0 and gender=2;
    ```

    - 分组

    ```sql
    -- 1.group by + group_concat(name)
    eg: select gender,group_concat(name) from students group by gender;
    +--------+-----------------------------------------------------------+
    | gender | group_concat(name)                                        |
    +--------+-----------------------------------------------------------+
    | 男     | 彭于晏,刘德华,周杰伦,程坤,郭靖                                 |
    | 女     | 小明,小月月,黄蓉,王祖贤,刘亦菲,静香,周杰                        |
    | 中性   | 金星                                                       |
    | 保密   | 凤姐                                                       |
    +--------+-----------------------------------------------------------+
    
    -- 2.group by +  聚合函数
    select gender,avg(age) from students group by gender;  -- 分别统计性别为男女的平均年龄
    -- 3.group by + having
    select gender,count(*) from students group by gender having count(*)>2;
    -- 4.group by + with rollup
    	-- with rollup的作用是：在最后新增一行，来记录当前列里所有记录的总和
    select gender,count(*) from students group by gender with rollup;
    +--------+----------+
    | gender | count(*) |
    +--------+----------+
    | 男     |        5 |
    | 女     |        7 |
    | 中性   |        1 |
    | 保密   |        1 |
    | NULL   |       14 |
    +--------+----------+
    ```

    - 分页

    ```sql
    select * from 表名 limit start,count  # 从start开始，获取count条数据
    -- 求第n页的数据
    select * from students where is_delete=0 limit (n-1)*m,m
    ```

    - 连接查询

    ```sql
    -- 内连接查询
    select * from students inner join classes on students.cls_id = classes.id;  -- 公共部分
    -- 右连接查询
    select * from students as s right join classes as c on s.cls_id = c.id;  --公共+B  字段变多了
    -- 左连接查询
    select * from students as s right join classes as c on s.cls_id = c.id;  -- 公共+A
    -- 自关联
    	-- 创建areas表
        create table areas(
            aid int primary key,
            atitle varchar(20),
            pid int
        );
        -- 查询省的名称为“山西省”的所有城市
    select city.* from areas as city inner join areas as province on city.pid=province.aid where province.atitle='山西省';
    ```

    - 子查询

    ```sql
    -- 概念: 在一个 select 语句中,嵌入了另外一个 select 语句, 那么被嵌入的 select 语句称之为子查询语句
    -- 标量子查询
    select * from students where age > (select avg(age) from students);
    -- 列级子查询
    select name from classes where id in (select cls_id from students);
    -- 行级子查询
    select * from students where (height,age) = (select max(height),max(age) from students);
    ```

    - 完整的select语句

    ```sql
    select distinct *
    from 表名
    where ....
    group by ... having ...
    order by ...
    limit start,count
    
    ```

### 4. Python操作MySQL

```python
import pymysql

conn = pymysql.connect(host='192.168.199.113',
                       port=3306,
                       database='flask_demo',
                       user='root',
                       password='root',
                       charset='utf8')

cs = conn.cursor()  # 获取执行对象
line_count = cs.execute("select * from author")  # 用执行对象执行sql语句,返回受影响的行数
fetchall_tuple = cs.fetchall()  # 获取sql查询的结果

conn.commit()  # 有增改删时, 执行提交
cs.close()  # 关闭
conn.close()  

```

> connection对象
> conn = pymysql.connect(参数列表)
> ​	参数host：连接的mysql主机，如果本机是'localhost'
> ​	参数port：连接的mysql主机的端口，默认是3306
> ​	参数database：数据库的名称
> ​	参数user：连接的用户名
> ​	参数password：连接的密码
> ​	参数charset：通信采用的编码方式，推荐使用utf8
> connection对象的方法
> ​	close()关闭连接
> ​	commit()提交
> ​	cursor()返回Cursor对象，用于执行sql语句并获得结果

- 参数化防止SQL注入

```python
# 对于 sql = 'select * from goods where name="%s" ' % find_name 这样的sql手动写入占位符会造成SQL注入攻击, 如: find_name 为 " or 1=1 or "(双引号也要)时, 可造成攻击
# 安全的方式, 构造参数列表
params = [find_name]
count = cs1.execute('select * from goods where name=%s', params)

```

### 5.MySQL高级

#### 1) 视图

- 概念

  视图就是一条SELECT语句执行后返回的结果集

  作用: 像函数提高重用性

- 定义

  `create view 视图名称 as select语句;`

- 查看已定义的视图

  `show tables;`

- 使用
  `select * from 视图;`

- 删除
  `drop view 视图名称;`

#### 2) 事务

- 概念

  一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位

- 事务四大特性

  - 原子性(Atomicity)
    ​	一个事务必须被视为一个不可分割的最小工作单元
  - 一致性(Consistency)
    ​	从一个一致性的状态转换到另一个一致性的状态
    隔离性(Isolation)
  - 一个事务所做的修改在最终提交以前，对其他事务是不可见的。a=100, 事务提交前,a=100
    持久性(Durability)
  - 一旦事务提交，则其所做的修改会永久保存到数据库

- 开启事务

  开启事务后执行修改命令，变更会维护到本地缓存中，而不维护到物理表中, 所以事务内部数据变更, 事务外部数据不变更

  `begin; | start transaction;`

- 提交事务
  `commit;`

- 回滚事务

  `rollback;`

> 注意
> 修改数据的命令会自动的触发事务，包括insert、update、delete 而在SQL语句中有手动开启事务的原因是：可以进行多次数据的修改，如果成功一起成功，否则一起会滚到之前的数据

#### 4) 索引

- 概念
  索引是一种特殊的文件, 它们包含着对数据表里所有记录的引用指针。
  目的:  提高速度

- 使用

  - 查看索引	

    `show index from 表名`

  - 创建索引

    `create index 索引名称 on 表名(字段名称(长度))`

  - 删除索引

    `drop index 索引名称 on 表名`

  > 注意
  > ​	索引影响更新和插入速度，因为它需要同样更新每个索引文件。
  > ​	对于一个经常需要更新和插入的表格或比较小的表，就没有必要为一个很少使用的where字句单独建立索引了

