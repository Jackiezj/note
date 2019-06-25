# Linux的终端命令

**linux终端命令格式**

- `command [-options] [parameter]`

**查询命令帮助**

```bash
command --help
man command
```

## 一. 目录相关命令

- **ls命令**

  ```bash
  ls -a  # 展示当前文件夹所有内容
  ls -l  # 列表显示
  ls -h  # 人性化展示大小
  ls -alh  # 缩写
  ll  # 缩写
  la  # 缩写形式
  ```

  > 通配符

  ```bash
  *  # 任意个字符
  ?  # 一个字符
  [abc] [1-9] [a-z]  # 其中任意一个字符
  
  # 与ls结合使用
  ls *.txt  # 展示当前目录下所有以 .txt 结尾的文件
  ```

- **cd**命令

  ```bash
  cd 或 cd ~  # 家目录
  cd -  # 上一个目录
  cd .  # 当前目录
  cd ..  # 上一级目录
  ```

## 二. 文件相关命令

- 文件命令

  ```bash
  touch file  # 创建文件名为file的文件, 文件存在时修改日期
  mkdir -p folder  # 创建目录 -p为递归创建目录 eg: mkdir -p a/a/b
  rm -rf file/folder  # 删除文件fole或文件夹folder, -r为递归删除, -f为强制删除,不提示
  tree -d [folder] # 树状图展示文件目录, -d只显示目录
  cp -ri file/folder folder2  # 复制file/folder到folder2 -r递归,-i覆盖前提示
  mv -i file/folder folder2  # 重命名 或 移动file/folder到folder2 -i覆盖前提示
  cat file  # 查看全部文件内容 -b对非空行编号, -n对所有行编号
  more file  # 分页查看文件 f b q /hello 向前,向后,退出, 查找hello字符串
  grep ^he$ -n a.txt  # 在a.txt上查找he字符串, -n显示行号 -i忽略大小写 -v取反 ^以开头, $以结尾
  
  find [路径] -name *.py  # 查找指定路径下所有的 .py文件及目录
  ln -s 源文件 链接文件  # 创建文件的软链接, -s为软链接,不写为硬链接
  ```

- 其它

  ```bash
  echo  # 回声命令 终端中的命令显示在终端中
  echo hello > 1.txt  # 将hello重定向到1.txt中 覆盖
  echo hello >> 1.txt  # 追加hello到1.txt中
  
  |  # 管道:一个命令的输出作为另一个命令的输入
  more 1.txt | grep hello
  ls | grep txt
  ```

## 三. 远程管理命令

- 命令

  ```bash
  shutdown -r [now/00:00/+10] # 关机 -r重启 默认1min后执行,now表示立刻执行
  shutdown -c  # 取消shutdown计划
  
  ifconfig  # 查看ip地址
  ping ip  # 上标ip是否连接正常
  
  ssh [-p port] user@remote  # 连接过程服务器  port默认22, remote为IP/域名/别名
  exit  # 退出当前用户登录
  
  # 远程传输文件, -P(大写)指定端口
  scp [-P port] local_file user@remote:Desktop/local_file  # file 本地 -> 远程
  scp -r local_folder user@remote:Desktop  # folder 本地 -> 远程
  scp user@remote:Desktop/01.py 01.py  # file 远程 -> 本地
  scp -r user@remote:Desktop/folder folder  # folder 远程 -> 本地
  
  SSH免密码登录和配置别名
  ssh-keygen  # 生成ssh钥匙 放在 ~/.ssh 下
  ssh-copy-id -p port user@remote  # 远程记住公钥 copy到远程 ~/.ssh 下
  # ~/.ssh/config 中追加 追加后可用 ssh bieming 登录
  	Host bieming
  		HostName 远程IP
  		User 远程user
  		Port 22
  ```

  

## 四. 用户相关命令(了解)

```bash
chmod +/- rwx file  # 增加或取消读/写/执行权限
chmod 777 # 改变权限为777, rwx分别对应421,没有则为0
sudo  # 超级管理员

# 组相关命令
groupadd 组名  # 创建组
groupdel 组名  # 删除组
cat /etc/group  # 查看组
chgrp 组名 文件/目录  # 修改文件或目录的所属组

# 用户相关
usermod -g 组名 用户名  # 修改用户主组
usermod -G 组名 用户名  # 修改用户附加组
usermod -s /bin/bash  # 修改用户登录Shell
su - 用户名  # 切换用户, - 同时切换家目录
exit  # 返回之前的用户


# 用户管理相关命令
useradd -m -g 组名 用户名  # -m自动创建家目录, -g指定组名
passwd 用户名  # 修改用户密码
userdel -r 用户名  # 删除用户 -r删除家目录
cat /etc/passwd|grep 用户名  # 查看用户信息

# 查看用户信息
id [用户名]  # 用户ID信息
who  # 登录用户列表
whoami  # 当前登录用户

```

## 五. 系统信息相关

```bash
# 时间相关
date  # 查看系统时间
cal  # 查看日历

# 磁盘信息
df -h  # 显示磁盘剩余空间
du -h [目录]  # 目录下的文件大小

# 进程相关命令
ps -ef | grep test  # 查找test相关的进程
ps -aux  # 同上
top  # 查看系统的cpu memory等信息, htop更直观
kill -9 进程ID  # -9强制终止进程
```

## 六. 打包压缩

```bash
tar -zxvf xxx.tar.gz -C 路径  # 将文件解压到指定路径
tar -zcvf xxx.tar.gz 内容  # 压缩文件

tar -jxvf xxx.tar.bz2 -C 路径  # 将文件解压到指定路径
tar -jcvf xxx.tar.bz2 内容  # 压缩文件
```

## 七. 其它

#### 1) 双系统Ubuntu访问window资源

```bash
df -h  # 查看ubuntu系统硬盘
sudo fdisk -l  # 查看本地硬盘
sudo mount /dev/sda1 ~/win  # 这样便把sda1也就是D盘挂载到~/win了
cd /mnt/thinkii  # 可查看win中的文件
```

#### 5) linux的shell脚本命令

```bash
vim hello.sh
# 打开文件后输入
    #！ /bin/bash
    mkdir hello
    cd hello
    echo "helloworld"
# 运行 ./hello.sh 或者 source ./hello.sh 就可以把三条命令一起执行了
```