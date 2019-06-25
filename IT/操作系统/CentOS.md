### 1. CentOS7安装python3

- 安装python3

  ```python
  # 默认Centos7中是有python安装的，但是是2.7版本，我们需要安装py3。我们去看一下默认的py2.7在哪里。
  [root@localhost bin]# cd /usr/bin
  [root@localhost bin]# ls python*
  python  python2  python2.7 
  
  # 查看python python2 python2.7之间的关系
  [root@localhost bin]# ls -al  python*
      python -> python2
      python2 -> python2.7
      python2.7
  [root@localhost bin]#
  
  # 备份原python
  [root@localhost bin]# mv python python.bak
  
  # 下载
  wget命令下载 --> 解压 --> 安装
  
  官网：https://www.python.org/ftp/python/
  这里选择下载： Python-3.6.3.tgz
  拼接：https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz
  
  [root@localhost bin]# mkdir /usr/local/python3
  [root@localhost bin]# cd /usr/local/python3
  [root@localhost python3]# wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz
  
  #解压
  [root@localhost python3]# tar -xvf Python-3.6.3.tgz 
  
  # 安装
  [root@localhost python3]# cd Python-3.6.3/
  # 指定安装目录  好卸载
  [root@localhost Python-3.6.3]# ./configure --prefix=/usr/local/python3Dir
  [root@localhost Python-3.6.3]# make
  [root@localhost Python-3.6.3]# make install
  [root@localhost Python-3.6.3]# cd /usr/local/python3Dir/
  
  # 创建一个软链接
  [root@localhost python3Dir]# cd /usr/bin
  [root@localhost bin]# ln -s /usr/local/python3Dir/bin/python3 /usr/bin/python
  
  # 让yum依然用原来的2.7版本
  [root@localhost bin]# vi /usr/bin/yum
  	#!/usr/bin/python改成#!/usr/bin/python2.7  这样就可以了。
  [root@localhost bin]# ll -a python*
  ```

  

### 2. CentOS更换yum源为ali

- 更换yum

  ```bash
  mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo_bak
  
  wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
  
  yum makecache
  
  yum -y update 
  ```

  

### 3. CentOS使用virtualenv搭建独立的Python环境-python虚拟环境

- 安装虚拟环境

  ```bash
  yum install python-virtualenv
  # 用easy_install或者pip安装virtualenv
  easy_install virtualenv
  easy_install virtualenvwrapper
  
  # 创建一个文件夹，用于存放所有的虚拟环境：
  mkdir py_vm   (目录自定义)
  # 设置环境变量，把下面两行添加到~/.bashrc里。
  vi ~/.bashrc
      export WORKON_HOME=/home/work/py_vm (绝对路径)
      source /usr/bin/virtualenvwrapper.sh
  source ~/.bashrc
  ```

- 创建虚拟环境

  ```bash
  # 创建方式一
  virtualenv --python=/usr/local/python-2.7.8/bin/python2.7 env1
  # mysql异常执行：
  	virtualenv --system-site-packages env1
  	
  # 创建方式二
  mkvirtualenv env2
  	# --python来指定python版本
   
  ```

- 启动与退出虚拟环境

  ```bash
  # 方式一 
  cd env1/
  source bin/activate
  python -V
  
  # 退出虚拟环境：
  deactivate
  
  
  # 方式二
  进入
  workon [虚拟环境名称]
  删除 
  rmvirtualenv [虚拟环境名称]
  复制
  cpvirtualenv env1 env3 (复制env1到env3)
  ```

  

### 4. CentOS下Redis的安装

- 下载安装

  ```bash
  wget http://download.redis.io/releases/redis-5.0.3.tar.gz
  tar xzf redis-5.0.3.tar.gz
  cd redis-5.0.3
  make
  make install
  # 目录：
  /usr/local/bin/
  ```

- 启动和停止

  直接运行redis-server即可启动Redis

  ```bash
  [root@localhost bin]# redis-server
  ```

  通过初始化脚本启动