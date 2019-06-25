## 二. 普通异常

### 1. SyntaxError

Syntax Error : Missing parentheses in call to 'print'. Did you mean print("hello world")?

语法错误：调用'print'时缺少括号。 你的意思是打印（“你好世界”）？

### 2. TypeError: 'int' object is not callable

- 错误代码

  ```python
  @app.route('/index/<int:user_id>')
  def index_int(user_id):
      return user_id
  ```

- 错误原因

  - 在return时需要一个str类型
  - 但上面return了一个int类型，所以int类型不能被调用

### 3. Ubuntu  pip3 install mysqlclient报错

- 问题

  ```
   Complete output from command python setup.py egg_info:
      /bin/sh: 1: mysql_config: not found
      Traceback (most recent call last):
        File "<string>", line 1, in <module>
        File "/tmp/pip-build-6z33hc2x/mysqlclient/setup.py", line 16, in <module>
          metadata, options = get_config()
        File "/tmp/pip-build-6z33hc2x/mysqlclient/setup_posix.py", line 53, in get_config
          libs = mysql_config("libs_r")
        File "/tmp/pip-build-6z33hc2x/mysqlclient/setup_posix.py", line 28, in mysql_config
          raise EnvironmentError("%s not found" % (mysql_config.path,))
      OSError: mysql_config not found
      
      ----------------------------------------
  Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-6z33hc2x/mysqlclient/
  ```

- 解决

  ```bash
  sudo apt install libmysqlclient-dev
  ```

  

### 4. redis.exceptions.ResponseError: 

- 问题

  ```
  redis.exceptions.ResponseError: MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error
  ```

- 解决

  ```bash
  config set stop-writes-on-bgsave-error no
  ```

### 5. E: 无法修正错误，因为您要求某些软件包保持现状，就是它们破坏了软件包间的依赖关系

- 解决

  ```bash
  设置 --> 软件和更新 --> 更新 --> 打开重要安全更新和推荐更新
  sudo apt update
  sudo apt upgrade
  ```

### 6. EnvironmentError: mysql_config not found

- 解决

  ```bash
  sudo apt-get install libmysqlclient-dev
  ```

### 7. pip install mysqlclient时报错error: command 'x86_64-linux-gnu-gcc' failed with exit status 1

- 解决

  ```
  for scrapy with Python 3, you'll need
  sudo apt-get install python3 python-dev python3-dev \
       build-essential libssl-dev libffi-dev \
       libxml2-dev libxslt1-dev zlib1g-dev \
       python-pip
  
  with Python 2, you'll need
  sudo apt-get install python-dev  \
       build-essential libssl-dev libffi-dev \
       libxml2-dev libxslt1-dev zlib1g-dev \
       python-pip
  ```



### 8. ImportError: libmysqlclient.so.20: cannot open shared object file: No such file or directory

```bash
pip uninstall mysql-python

pip install mysql-python
```

### 9. ReadTimeoutError：HTTPSConnectionPool(host=‘pypi.python.org，port=443)？

```bash
sudo pip install --default-timeout=100 future
```

### 10. 安装虚拟环境 setuptools pkg_resources pip wheel failed with error code 1

```
sudo apt install python-pip
sudo apt install python3-pip

export LC_ALL=C

sudo pip install pysocks
unset all_proxy

sudo pip install setuptools #安装setuptools
sudo pip install --upgrade setuptools #更新setuptools到最新版本
sudo pip install virtualenv #安装virtualenv
sudo pip install --upgrade virtualenv   #更新virtualenv到最新版本

# 在新终端创建虚拟环境
```

### 11. ssh:  sign_and_send_pubkey: signing failed: agent refused operation

```
eval "$(ssh-agent -s)"
ssh-add
```



## 二. Django异常

###  1. import异常

- 问题

    ```
    ImportError: No module named 'users.apps.UsersConfigverifications'; 'users.apps' is not a package
    ```

- 解决

    ```python
    INSTALLED_APPS = [
        'users.apps.UsersConfig'  # 为了适应用户认证系统
        'verifications.apps.VerificationsConfig'
    ]
    # 在两个注册中间少了","
    ```

## 三. my_blog异常

### 1. 导包异常

- 问题

    ```
    ImportError: No module named 'users.apps.UserConfig'; 'users.apps' is not a package
    ```

- 解决

    ```
    'users.apps.UserConfig' --> 'users.apps.UsersConfig',
    
    分析异常的文件
    1. manage.py中的 os.environ.setdefault(...) 查看配置文件路径
    2. settings.py中的 BASE_DIR的路径和包查找路径, 找到指定包,在sys.path列表中添加
    	sys.path.insert(0, os.path.join(BASE_DIR, 'apps'))
    3. settings.py中的 Installed_apps中路径确保正确
    ```

## 四. Flask项目

### 1. MySQL迁移异常

- 问题

    ```
    ImportError: No module named 'MySQLdb'
    ```

- 解决

    ```
    pip install mysqlclient
    ```

    

