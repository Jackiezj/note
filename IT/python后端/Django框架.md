# 如何学习框架

- **如何学习框架**
  - 如何搭建工程程序
    - 工程的组建
    - 工程的配置
    - 路由定义
    - 视图函数定义
  - 如何获取请求数据（request对象）
  - 如何构造响应数据（response对象）
  - 如何使用中间层
  - 框架提供的其他功能组件的使用
    - 数据库
    - 模板
    - 表单
    - admin

- **Django文档**
  
  - [官方网站](https://www.djangoproject.com/)
  - [1.11版英文文档](https://docs.djangoproject.com/en/1.11/)
  - [1.11版中文文档](https://yiyibooks.cn/xx/Django_1.11.6/index.html)
  - [Django Book 教程](http://djangobook.com/)
  
- **Django开发包**

  ```bash
  # Django
  pip install django=1.11.11
  
  # djangorestframework
  pip install djangorestframework
  
  # PyMySQL
  pip install PyMySQL
  
  # redis
  pip install django-redis
  
  # 图片相关
  pip install pillow
  ```

  

# 1. 搭建工程

- 通过bash搭建

  ```bash
  mkvirtualenv django_py3_1.11 --python=/usr/bin/python3  # 创建独立环境
  pip install django==1.11.11  # 安装django包
  django-admin startproject hello_world  # 创建工程
  python manage.py startapp users  # 创建应用
  
  # users/apps.py
  class UserConfig(AppConfig):
  	name = 'users'  # 用于注册应用
  	verbose_name = '用户管理'  # 用于admin站点的管理
  ```

- Django工程的启动

  ```bash
  python manage.py runserver ip:端口
  ```

- **Django工程目录结构调整**

  > - celery_tasks  # 异步任务
  > - front_end_pc   # 前端资源
  > - django_project  # 后端工程
  >   - docs  # 文档
  >   - logs  # 日志
  >   - django_project  # 与项目同名子应用
  >     - apps  # 项目应用
  >       - users
  >     - libs  # 第三方
  >     - settings  # 应用配置
  >     - utils  # 工具
  >   - scripts  # 脚本
  >   - manage.py

# 2. 工程配置

- `manage.py`

  ```python
  # 指定项目的配置文件
  os.environ.setdefault("DJANGO_SETTINGS_MODULE", "meiduo_mall.settings.dev")
  ```

- `settings.py`

  ```python
  # 指定项目以什么目录为根目录 dirname:获取目录路径,abspath:获取文件路径
  BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))  # 获取项目目录
  
  # 添加导包路径, 导包时可从新增加的路径向下寻找,如: BASE_DIR/apps
  import sys
  sys.path.insert(0, os.path.join(BASE_DIR, 'apps'))
  
  # 项目开启DEBUG模式
  DEBUG = True  # 项目开启DEBUG模式
  
  # 注册app
  INSTALL_APPS = [
      # 指定session的保存位置,3选1
  	SESSION_ENGINE = 'django.contrib.sessions.backends.db'  # session默认保存在数据库中 
  	SESSION_ENGINE = 'django.contrib.sessions.backends.cache'  # session保存在缓存中
  	SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'  # 先缓存,没有再数据库
      
      'rest_framework',
      'users.apps.UsersConfig',
  ]
  
  # 允许在哪些服务器上访问项目接口, '*' 表示任意
  ALLOWED_HOSTS = ['api.meiduo.site', '127.0.0.1', 'localhost']
  
  # 设置语言与时区
  LANGUAGE_CODE = 'zh-hans'  # 设置语言
  TIME_ZONE = 'Asia/Shanghai'  # 设置时区
  
  # 设置静态文件访问
  STATIC_URL = '/static/'  # 通过静态路径访问静态资源  非DEBUG模式下不提供访问静态文件的功能
  STATICFILES_DIRS = [  # 决定静态资源的文件夹
  	os.path.join(BASE_DIR, 'static_files')
  ]  # 在根目录下创建static_files文件夹
  
  # 中间件
  MIDDLEWARE = [
  	'django.midd.....csrf'  # 默认开启CSRF, 注释关闭
      'django.contrib.sessions...'  # 默认开启session
  ]
  
  # redis存储session
  # pip install django-redis
  CACHES = {
  	"default": {
  		"BACKEND": "django_redis.cache.RedisCache",
  		"LOCATION": "redis://127.0.0.1:6379/1",
  		"OPTIONS": {
  			"CLIENT_CLASS": "django_redis.client.DefaultClient",
  			"PASSWORD": ""
  		}
  	}
  }
  SESSEION_ENGINE = "django.contrib.sessions.backends.cache"
  SESSION_CACHE_ALIAS = "default"
  
  # 数据库配置
  DATABASES = {
  	'default': {
  		'ENGINE': 'django.db.backends.mysql',
  		'HOST': '127.0.0.1',
  		'PORT': 3306,
  		'USER': 'root',
  		'PASSWORD': 'mysql',
  		'NAME': 'django_demo'
  	}
  }
  
  # 配置日志
  LOGGING = {
      'version': 1,
      'disable_existing_loggers': False,  # 是否禁用已经存在的日志器
      'formatters': {  # 日志信息显示的格式
          'verbose': {
              'format': '%(levelname)s %(asctime)s %(module)s %(lineno)d %(message)s'
          },
          'simple': {
              'format': '%(levelname)s %(module)s %(lineno)d %(message)s'
          },
      },
      'filters': {  # 对日志进行过滤
          'require_debug_true': {  # django在debug模式下才输出日志
              '()': 'django.utils.log.RequireDebugTrue',
          },
      },
      'handlers': {  # 日志处理方法
          'console': {  # 向终端中输出日志
              'level': 'INFO',
              'filters': ['require_debug_true'],
              'class': 'logging.StreamHandler',
              'formatter': 'simple'
          },
          'file': {  # 向文件中输出日志
              'level': 'INFO',
              'class': 'logging.handlers.RotatingFileHandler',
              'filename': os.path.join(os.path.dirname(BASE_DIR), "logs/meiduo.log"),  # 日志文件的位置
              'maxBytes': 300 * 1024 * 1024,
              'backupCount': 10,
              'formatter': 'verbose'
          },
      },
      'loggers': {  # 日志器
          'django': {  # 定义了一个名为django的日志器
              'handlers': ['console', 'file'],  # 可以同时向终端与文件中输出日志
              'propagate': True,  # 是否继续传递日志信息
              'level': 'INFO',  # 日志器接收的最低日志级别
          },
      }
  }
  
  # DRF异常处理
  REST_FRAMEWORK = {
      'EXCEPTION_HANDLER': 'meiduo_mall.utils.exceptions.exception_handler',
  }
  
  AUTH_USER_MODEL = 'users.User'  # 告知Django认证系统使用我们自定义的模型
  ```

- 项目同名子目录下`__init__.py`增加数据库的配置

  ```python
  import pymysql
  
  pymysql.install_as_MySQLdb()
  ```

- `utils/exceptions.py`

  > 修改Django REST framework的默认异常处理方法，补充处理数据库异常和Redis异常。

  ```python
  from rest_framework.views import exception_handler as drf_exception_handler
  import logging
  from django.db import DatabaseError
  from redis.exceptions import RedisError
  from rest_framework.response import Response
  from rest_framework import status
  
  # 获取在配置文件中定义的logger，用来记录日志
  logger = logging.getLogger('django')
  
  def exception_handler(exc, context):
      """
      自定义异常处理
      :param exc: 异常
      :param context: 抛出异常的上下文
      :return: Response响应对象
      """
      # 调用drf框架原生的异常处理方法
      response = drf_exception_handler(exc, context)
  
      if response is None:
          view = context['view']
          if isinstance(exc, DatabaseError) or isinstance(exc, RedisError):
              # 数据库异常
              logger.error('[%s] %s' % (view, exc))
              response = Response({'message': '服务器内部错误'}, status=status.HTTP_507_INSUFFICIENT_STORAGE)
  
      return response
  ```

  







# 3. 路由

- **定义路由**

  ```python
  # url包含关系写法
  urlpatterns = [
      url(r'^', include('users.urls'))  # 不使用前缀的写法
  	url(r'^users/', include('users.urls', namespace='users'))  # 包含命名空间
  ]
  
  # 定义具体路由
  # 工程/应用/urls.py
  from . import views
  urlpatterns = [
      # 定义函数视图路由
  	url(r'^index/$', views.index, name='index')  # 定义普通路由,及路由名称
      # 定义类视图路由
      url(r'^index/$', views.RegisterView.as_view(), name='register')
  ]
  ```

- **反解析路由**

  ```python
  # 路由反解析, 可以没有命名空间
  url = reverse("index")
  url = reverse("users:index")
  ```

  

# 4. 视图

- **函数视图**

  ```python
  def index(request)
  	"""
  	:params request: 用于接收请求HttpRequest对象
  	:return: 返回响应HttpResponse对象
  	"""
  	return HttpResponse('hello Django')
  ```


- **类视图**

  ```python
  from django.views.generic import View
  
  class RegisterView(View):
  	def get(self, request):
  		"""处理GET请求"""
  		return render(request, 'register.html')
  		
  	def post(self, request):
  		"""处理POST请求"""
  		return HttpResponse('pose注册逻辑')
  ```

- **装饰器装饰视图**

  ```python
  # 装饰器
  def my_decorator(view_func):
  	def wrapper(request, *args, **kwargs):
  		print('装饰器被调用')
  		return view_func(request, *args, **kwargs)
  	return wrapper
  
  
  class RegisterView(View):
      # 装饰到一个指定函数中
  	@method_decorator(my_decorator)
  	def get(self, request):
  		return render(request, 'register.html')
  		
  	def post(self, request):
  		return HttpResponse('pose注册逻辑')
  
  
  class RegisterView(View):
      # 重写dispatch方法实现全部装饰
  	@method_decorator(my_decorator)
  	def dispatch(self, request, *args, **kwargs)
  		return super().dispatcher(request, *args, **kwargs)
  	
  	def get(self, request):
  		return render(request, 'register.html')
  		
  	def post(self, request):
  		return HttpResponse('pose注册逻辑')
  
  # 在class上实现装饰
  @method_decorator(my_decorator, name='get')  # 在get上添加装饰
  @method_decorator(my_decorator, name='dispatcher')  # 在所有方法上添加装饰
  class RegisterView(View):
  
  	def dispatch(self, request, *args, **kwargs)
  		return super().dispatcher(request, *args, **kwargs)
  	
  	def get(self, request):
  		"""处理GET请求"""
  		return render(request, 'register.html')
  		
  	def post(self, request):
  		return HttpResponse('pose注册逻辑')
  ```

- **构造Mixin扩展类装饰视图**

  ```python
  class BaseViewMixin(object):
  	@classmethod
  	def as_view(cls, *args, **kwargs):
  		view = mydecorator(super().as_view(cls, *args, **kwargs))
  		return view
  
  class BaseView2Mixin(object):
  	@classmethod
  	def as_view(cls, *args, **kwargs):
  		view = mydecorator2(super().as_view(cls, *args, **kwargs))
  		return view
  
  class RegisterView(BaseViewMixin, BaseView2Mixin, View):
  	pass
  ```

  > 路由中的as_view方法会找到BaseViewMixin的as_view重写方法,由于python的多继承机制,BaseViewMixin和BaseView2Mixin和view都继承自object同一个父类,所以又会调用BaseView2Mixin的as_view方法,最后调用View的as_view方法,从而保证所有装饰器的调用













# 5. 获取请求参数

- **封装的request对象**

  > 视图中

  ```python
  def index(request):
      return HttpResponse('index')
  ```

- **获取URL路径参数**

  ```python
  # 不命名参数提取参数
  url(r'^weather/([a-z]+)/(\d{4})/$', views.weather)
  def weather(request, city, year):  # 按顺序一个一个提取
  	return HttpResponse("%s %s"%(city, year))
  
  # 命名参数写法 ?P<year> 提取参数
  url(r'^weather/(?P<city>[a-z]+)/(?P<year>\d{4})/$', views.weather)
  def weather(request, year, city):  # 顺序不定,可以不用按照规定顺序
  	retrun
  ```

- **获取查询字符串参数**

  ```python
  # /weather/beijing/2018/?key1=1&key1=11&key2=2
  value2 = request.GET.get('key2')  # value2:
  value2_list = request.GET.getlist('key1')  # ['1', '11']
  ```

- **获得表单格式的请求体数据**

  ```python
  value1 = request.POST.get('key1')  # value1:1
  value1_list = request.POST.getlist('key1')  # a:['1', '2']
  ```

- **获取非表单格式请求体数据 如:`json/xml`等格式的数据**

  ```python
  json_bytes = request.body
  json_str = json_bytes.decode()
  req_dict = json.loads(json_str)
  value3 = req_dict.get('c')
  value4 = req_dict.get('d')
  ```

- **获取请求头数据**

  ```python
  request_header_data = request.META.get('CONTENT_TYPE')
  ```

  > 常用请求头
  >
  > - CONTENT_LENGTH – The length of the request body (as a string).
  > - CONTENT_TYPE – The MIME type of the request body.
  > - HTTP_ACCEPT – Acceptable content types for the response.
  > - HTTP_ACCEPT_ENCODING – Acceptable encodings for the response.
  > - HTTP_ACCEPT_LANGUAGE – Acceptable languages for the response.
  > - HTTP_HOST – The HTTP Host header sent by the client.
  > - HTTP_REFERER – The referring page, if any.
  > - HTTP_USER_AGENT – The client’s user-agent string.
  > - QUERY_STRING – The query string, as a single (unparsed) string.
  > - REMOTE_ADDR – The IP address of the client.
  > - REMOTE_HOST – The hostname of the client.
  > - REMOTE_USER – The user authenticated by the Web server, if any.
  > - REQUEST_METHOD – A string such as "GET" or "POST".
  > - SERVER_NAME – The hostname of the server.
  > - SERVER_PORT – The port of the server (as a string).
  >
  > 用户自定义请求头a, 获取方式为:
  >
  > - HTTP_A 	

- **获取其它请求信息**

  ```python
  request.method  # GET, POST
  request.user  # 请求用户对象
  request.path  # str请求页面的完整路径,不包含域名和参数部分
  request.encoding  # 一个提交数据的编码方式
  ```

# 6. 构造响应数据

- **`HttpResponse`响应**

  ```python
  import django.http.HttpResponse
  
  resp =  HttpResponse(content='{"name":"zs"}',        # 响应体
                      content_type='application/json', # 响应体数据类型
                      status=400)						 # 状态码
  resp['it'] = 'python'  # 设置响应头
  ```

  > content_type:   
  >
  > - text/html	html
  >
  > - text/plain    普通文本
  >
  > - application/json    json

- **`HttpResponseRedirect` 重定向**

  ```python
  from django.shortcuts import redirect
  
  def resp(request):
      url = reverse('index')  # 动态生成URL地址，解决url硬编码维护麻烦的问题。
  	return redirect(url)
  ```

- **`JsonResponse` 返回json数据**

  > 帮助我们将dict转换为json字符串，再返回给客户端
  >
  > 自动设置响应头 Content-Type 为 application/json

  ```python
  from django.http import JsonResponse
  
  resp = JsonResponse({'city': 'beijing', 'subject': 'python'},
                      json_dumps_params={'ensure_ascii':False})  # 返回中文,而非中文编码
  ```

  > 中文: 张三
  >
  > 中文编码: \u5f20\u4e09

# 7. 中间件

## 7.1 状态保持

- **Cookie**

  ```python
  # 设置Cookie
  HttpResponse.set_cookie(key, value, max_age=6)  # 设置名为key的cookie值为value,有效期为6s
  # 读取Cookie
  request.COOKIES.get('key')
  # 删除Cookie
  response.delete_cookie('mark')
  # 是否有某个cookie
  request.COOKIES.has_key('mark')  # true False
  ```

- **Session**

  > 配置请参照工程配置部分

  ```python
  # 存储session
  request.session['key1'] = value1
  
  # 读取session
  request.session.get('key1', default1)  # 没有对应key1的session时返回默认值default1
  
  # 清除所有session 值部分
  request.session.clear()
  
  # 清除所有session 数据
  request.session.flush()
  
  # 删除指定session
  del request.session['key1']
  
  # 设置有效期
  request.session.set_expiry(time)
  	# time为一个整数,默认None 2周过期,0表示浏览器关闭时过期
  	# 在settings.py中设置SESSION_COOKIE_AGE来设置全局默认值,单位s
  ```

## 7.2 请求勾子

- **定义中间件**

  `middleware.py`

  ```python
  def simple_middleware(get_response):
  	# 此处处理Django第一次配置和初始化的时候执行
  	
  	def middleware(request):
  		# 此处编写代码会在每个请求处理视图前被调用
  		
  		response = get_response(request)
  		# 此处编写的代码会在每次请求处理之后被调用
  		
  		return response
  	return middleware
  ```

- **注册中间件**

  `settings.py`

  > 多个中间件的执行顺序: 视图执行前,自上而下调用, 视图执行后,自下而上调用

  ```python
  MIDDLEWARE = [
  	'middleware.simple_middleware'
  ]
  ```

# 8. 模板

- **使用示例**

  `settings.py`

  ```python
  TEMPLATES = [
  	'DIRS': [os.path.join(BASE_DIR, 'templates')],
  ]
  ```

  `视图`

  ```python
  # Django原始方法
  def index(request):
  	# 1.获取模板
  	template = loader.get_template('booktest/index.html')
  	# 2.定义上下文 (django1.11版本,此步不需要, context直接是一个字典)
  	context = RequestContext(request, {'city':'beijing'}
  	# 3.渲染模板
  	return HttpResponse(template.render(context))
  
  # Django封装了 render(request, 模板文件路径, 模板数据字典) 返回模板
  def index(request):
  	data = {
  		'city': 'beijing',
  		'alist': [1,2,3],
  		'adict': {
  			'name': 'python'
  		}
          'adate': datetime.datetime.now()
  	}
  	render(request, index.html, data)
  ```

- **模板语法**

  - 模板变量

    ```jinja2
    {{ city }}
    {{ alist.0 }}  # 列表元素的取值
    {{ adict.0 }}  # 字典元素取值
    {{ adict.name }}  # 字典元素取值
    ```

  - `if, for` 控制语句

    ```jinja2
    {% for item in 列表 %}
    	{{forloop.counter}}  # 表示循环到第几次,从1开始,forloop.counter0表示从0开始
    {% empty %}
    	# 列表为空或不存在时执行此逻辑
    {% endfor %}
    
    {% if ... %}
    {% elif ... %}
    {% else %}
    {% endif %}
    # if的条件: == != > < >= <= and or not
    ```

    > 注意if控制中必须格式正确, {% if num==1 %}报错,必须写成{% if num == 1 %}

  - 过滤器

    ```jinja2
    {# 语法: 变量|过滤器:参数 #}{#  #}
    {{ city|safe }}  {# safe: 禁止html转义(显示html标签样式，而不是字符串) #}
    {{ city|length }}  {# 长度(str,list,dict个数或长度) #}
    {{ city|default:'beijing' }}  {# 给变量设置默认值 #}
    {{ adate|date:"Y年m月j日 H时i分s秒" }}  {# Y(2019)y(19)m(01-12)d(01-31)j(1-31)H(0-23)h(0-12)i(0-59)s(0-59) #}
    ```

  - 注释

    ```jinja2
    {# 单行注释 #}
    
    {% comment %}
    多选注释
    {% endcomment %}
    ```

  - 继承

    ```jinja2
    {% block b1 %}
    父模板block
    {% endblock b1 %}
    
    
    {% extends 父模板 %}
    {% block b1 %}
    	# 子模板显示父模板的内容
    	{{ block.super }}
    	# 子模板重写父模板内容
    {% endblock b1 %}
    ```

# 9.数据库

## 9.1 配置迁移

- **使用shell工具**

  ```bash
  python manage.py shell
  # 配置好环境,可直接对数据库进行CRUD操作 类似于ipython3
  ```

- **数据库配置**

  ```python
  # 安装mysql数据库驱动 pip install PyMySQL
  
  # 在Django工程同名子目录__init__.py中添加如下代码
  from pymysql import install_as_MySQLdb
  install_as_MySQLdb()
  
  # settings.py中配置DABABASES,详情查看工程配置部分
  ```

- **数据库迁移**

  ```bash
  python manage.py makemigrations
  python manage.py migrate
  ```

## 9.2 ORM

- **定义模型类**

  > django自动生成主键,若自定义则覆盖默认

  - 字段类型

  | 字段类型                                           | 特殊参数              | 说明                                                         |
  | :------------------------------------------------- | :------------------- | :----------------------------------------------------------- |
  | AutoField                                          |                      | 主键, 通常不用指定,Django自动会生成主键                        |
  | BooleanField                                       |              | True或False                                                  |
  | NullBooleanField                                   |                      | Null,True,False三种字段                                  |
  | CharField                     | max_length=10 |                                                              |
  | TextField                                          |            | 一般超过4000时使用                                           |
  | IntegerField                                       |                  |                                                              |
  | DecimalField | max_digits=None<br/>decimal_places=None | max_digists:总位数<br/>decimal_places:小数位数            |
  | FloatField                                         |                |                                                              |
  | DateField       | auto_now=False<br/>auto_now_add=False | auto_now:修改时间<br>auto_now_add:创建的时间<br>两者不可同时指定 |
  | TimeField                                          | 同上          |                                                      |
  | DateTimeField                                      | 同上          |                                                      |
  | FileField                                          |          |                                                              |
  | ImageField                                         | upload_to='booktest' |                                     |
| ForeignKey | | 一对多 |
  | ManyToManyField | | 多对多 |

  - 选项(字段共有参数)
  
  | 选项(参数)  | 含义                       | 注意                                           |
  | :---------- | :------------------------- | :--------------------------------------------- |
  | null        | 是否允许为空               | True允许,False不允许,默认False                 |
  | blank       | 是否允许为空白             | 同上                                           |
  | db_column   | 字段名称                   | 默认为属性名称                                 |
  | db_index    | 是否在表中为此字段创建索引 | 默认False                                      |
  | default     | 字段默认值                 |                                                |
  | primary_key | 是否为主键                 | 默认False,一般不用指明,Django自动生成主键      |
| unique      | 是否唯一                   | 默认False(不唯一)                              |
  | on_delete   | 是否级联删除               | CASCADE:级联删除, PROTECT:保护,删除时抛出异常, |

  - 示例
  
  ```python
  class BookInfo(models.Model):
  	btitle = models.CharField(max_length=20, verbose_name='名称')  # verbose_name admin显示名称
	bpub_date = models.DateField(verbose_name='发布日期')
  	bread = models.IntegerField(default=0, verbose_name='阅读量')
  	bcomment = models.IntegerField(default=0, verbose_name='评论量')
  	is_delete = models.BooleanField(default=False, verbose_name='逻辑删除')
  	
  	class Meta: #元信息类
  		db_table = 'tb_books'  # 数据库表名
  		verbose_name = '图书'  # admin显示表名
          verbose_name_plural = verbose_name  # 指明人性化复数显示
  		
  class HeroInfo(models.Model):
  	GENDER_CHOICES = (
  		(0, 'male')
  		(1, 'femle')
  	)
  	hgender = models.SmallIntegerField(choices=GENDER_CHOICES, default=0, verbose_name='性别')
  	
  	hbook = models.ForeignKey(BookInfo, on_delete=models.CASCADE, verbose_name='图书')  # CASCADE级联删除,默认不允许直接删除
  ```

- **操作模型类**

  > 模型类管理器: objects实现对数据的操作

  | objects方法                | 返回类型     | 作用                                                         |
  | -------------------------- | ------------ | ------------------------------------------------------------ |
  | 模型类.objects.create()    | 模型类对象   | 新增一条数据                                                 |
  | 模型类.objects.get()       | 模型类对象   | 查询一个对象<br>若查到多个或空,则抛出异常                    |
  | 模型类.objects.all()       | QuerySet     | 查询所有的对象                                               |
  | 模型类.objects.count()     | 数字         | 查询总共有多少条数据                                         |
  | 模型类.objects.filter()    | QuerySet     | 查询满足条件的对象                                           |
  | 模型类.objects.exclude()   | QuerySet     | 查询不满条件的对象                                           |
  | 模型类.objects.order_by()  | QuerySet     | 对查询结果集进行排序                                         |
  | 模型类.objects.aggregate() | 字典，例如： | 进行聚合操作 Sum, Count, Max, Min, Avg<br>{'salary__avg': 9500.0} |

  - 自定义管理器

  ```python
  class BookInfoManager(models.Manager):
  	# 自定义管理器类,修改过滤方法
  	def all(self):
  		retrun super().filter(is_delete=False)
  	
  	def create_book(self, title, pubdate):
  		# 设置特定的过滤方法
  		book = self.model()  # 创建模型类对象
  		book.btitle = title
  		book.bpub_date = pub_date
  		book.bread = 0
  		book.bcomment = 0
  		book.is_delete = False
  		book.save()  # 将数据插入数据库表
  		return book
  
  
  class BookInfo():
  	objs = BookInfoManager()  # 自定义管理器
  ```

## 9.3 Django用户模型类

- **创建自定义用户模型类**

  `models.py`

  ```python
  from django.contrib.auth.models import AbstractUser
  
  class User(AbstractUser):
      """用户模型类"""
      mobile = models.CharField(max_length=11, unique=True,
                                verbose_name='手机号')
  
      class Meta:
          db_table = 'tb_users'
          verbose_name = '用户'
          verbose_name_plural = verbose_name
  ```

  `settings.py`

  ```python
  AUTH_USER_MODEL = 'users.User'  # 告知Django认证系统使用我们自定义的模型
  ```

  

## 9.4 数据的增删改

- **增加**

  ```python
  # 方法一: save
  hero = HeroInfo(hname='zhangsan')  # 模型类对象
  hero.save()  # 保存模型类对象到数据库相应数据
  
  # 方法二: create
  HeroInfo.objects.create(
  	hname='zhangsan'
  )
  ```

- **修改**

  ```python
  # 方法一: save
  hero = HeroInfo.objects.get(hname='song')
  hero.hname = 'wu'
  hero.save()
  
  # 方法二: update
  HeroInfo.objects.filter(hname='song').update(hname='wu')
  ```

- **删除**

  ```python
  # 方法一: delete
  hero.delete()
  
  # 方法二: delete
  HeroInfo.objects.filter(id=1).delete()
  ```

## 9.5 查询数据

- **基本查询**

  > all, get, count

  ```python
  all_book = BookInfo.objects.all()  # all查询所有结果
  b1 = all_book[0]  # 取出查询集中第一个元素
  bs = all_book[0:2]  # 切片操作取出元素
  
  b2 = BookInfo.objects.get(pk=1)
  b2 = BookInfo.objects.get(id=1)  # 与pk一样
  b2 = BookInfo.objects.get(btitle='xiyou')
  
  bcount = BookInfo.objects.count()  # 返回个数
  ```

- **过滤查询**

  > filter

  ```python
  # filter
  books = BookInfo.objects.filter(id__exact=1)  # 相等exact
  books = BookInfo.objects.filter(id=1)  # 效果同上
  books = BookInfo.objects.filter(btitle__contains='you')[0]  # 包含:contains
  books = BookInfo.objects.filter(btitle__startswith='you')  # startswith endswith
  books = BookInfo.objects.filter(btitle__isnull=False)  # 判空: isnull
  books = BookInfo.objects.filter(id__in=[1,2,3,4])  # 范围查询: in
  books = BookInfo.objects.filter(id__gt=10)  # 比较查询 gt, gte, lt, lte (> >= < <=)
  books = BookInfo.objects.filter(id__exclude=10)  # 不等 exclude
  books = BookInof.objects.filter(bpub_date__year=1980)  # 日期查询 year month, day, week_day, hour, minute, second
  from datetime import date
  books = BookInfo.objects.filter(bpub_date__gt=date(1990,1,1))
  ```

- **F对象Q对象**

  > F对象: 比较两个字段的值
  >
  > Q对象: 多个查询条件

  ```python
  from django.db.models import F, Q
  
  # F()
  BookInfo.objects.filter(bread_gte=F('bcomment'))
  BookInfo.objects.filter(bread_gte=F('bcomment')*2)
  
  # 与 Q() & Q()
  BookOnfo.objects.filter(id=5, btitle__contains='you')
  BookInfo.objects.filter(Q(id=5) & Q(bread__gte=5))
  
  # 或 Q() | Q()
  BookInfo.objects.filter(Q(id=5) | Q(bread__gte=5))
  
  # 非 !Q()
  BookInfo.objects.filter(~Q(id=5))
  ```

- **聚合函数**

  ```python
  # 聚合函数: Avg，Max，Min，Sum，Count
  BookInfo.objects.aggregate(Sum('bread'))
  ```

- **排序查询**

  ```python
  BookInfo.objects.all().order_by('bread')  # 升序
  BookInfo.objects.all().order_by('-bread')  # 降序
  ```

- **关联查询**

  ```python
  # 一查多  一的一方模型类对象.多的一方模型类名小写_set.all()
  heros = book.heroinfo_set.all()
  # 多查一  多的一方模型类对象.多的一方定义的一的一方的属性名
  book = hero.hbook
  # 跨表关联查询
  books = BookInfo.objects.filter(heroinfo__hname__contains('song'))
  heros = HeroInfo.objects.filter(hbook__btitle__contails('you'))
  ```

# 10. Admin站点

- **创建Admin超级管理员**

  ```bash
  python manage.py createsuperuser
  ```

- **注册模型类**

  `admin.py`

  ```python
  admin.site.register(BookInfo, BookInfoAdmin)
  admin.site.register(HeroInfo)
  
  class BookInfoAdmin(admin.ModelAdmin):  # @admin.register(BookInfo) 也可以装饰器注册
  	pass
  ```

- **自定义管理页面**

  - 展示对象信息

    ```python
    class BookInfo():
    	def __str__(self):
    		return self.btitle
    ```

  - 调整列表展示页面

    ```python
    class BookInfoAdmin(admin.ModelAdmin)
    	# 每页展示的数据个数
    	list_per_page = 10
    	
    	# `操作选项` 的位置
    	action_on_top = True
    	action_on_bottom = True  # top和bottom可以同时存在
    	
    	# 展示信息字段
    	list_display = ['id', 'btitle']
    	
    	# 把方法当为一列展示
    	list_display = ['id', 'btitle', 'pub_date']
    	# 模型类中定义方法
    	class BookInfo():
    		def pub_date(self):
    			return self.bpub_date.strftime('%Y年%m月%d日')  # strftime时间转为字符串
    		# 给函数对象添加属性改变展示名称
    		pub_date.short_description = '发布日期'
    		# 给函数对象添加属性使其可排序
    		pub_date.admin_order_field = 'bpub_date'
    		
    # 展示关联对象
    Class HeroInfo():
    	list_display = ['id', 'hbook', 'read']
    	def read(self):
    		return self.hbook.bread
    	read.short_description= ''
    	
    # 右侧栏过滤器
    # list_filter = []
    Class HeroInfoAdmin():
    	list_filter = ['hbook', 'hgender']
    	
    # 搜索框
    Class HeroInfoAdmin():
    	search_fields = ['hname']
    ```

  - 调整编辑页面

    ```python
    # 控制编辑显示字段
    class BookInfoAdmin():
    	fields = ['btitle']
    	
    # 分组显示
    	fieldset = (
    		('组1标题', {'fields':('字段1','字段2')}),
    		('组2标题', {
    					'fields':('字段1','字段2')}
    					'classes': ('collapse')  # 是否折叠显示
    					)
    	)
    	
    # 关联对象
    # StackedInline 以块形式嵌入
    # TabularInline 以表格的形式嵌入
    class HeroInfoStackInline(admin.StackedInline):
    	model = HeroInfo  # 要编辑的对象
    	extra = 1  # 附加编辑的数量
    	
    class BookInfoAdmin(admin.ModelAdmin):
    	inlines = [HeroInfoStackInline]
    ```

  - 调整整体页面标题等

    ```python
    admin.site.site_header  # 设置网站页头
    admin.site.site_title  # 设置页面标题
    admin.site.index_title  # 设置首页标语
    ```

# 11. 表单

- **定义表单**

  `forms.py`

  ```python
  from django import forms
  
  class BookForm(forms.Form):
  	title = forms.ChaiField(label='书名', required=True, max_length=50')
  	pub_date = forms.DateField(label='出版日期', required=True)
  	
  	class Meta:
  		model = BookInfo  # 指明属于哪个模型类
  		field = ('btitle', 'bpub_date')  # 指明向表单中添加模型类的哪个字段
  ```

- **视图处理**

  ```python
  class BookView(View):
  	def get(self, request):
  		book_form = BookForm()
  		return render(request, 'book.html', {'form': book_form})
  		
  	def post(self, request):
  		book_form = BookForm(request.POST)
  		if form.is_valid():  # 验证表单数据
  			data = form.cleaned_data  # 获取验证后的表单数据
  			return HttpResponse('OK')
  		else:
  			return render(request, 'book.html', {'form': book_form})
  ```

- **模板处理**

  ```django
  <form method='post'>
  	{% csrf_token %}
  	{{ form }}
  	<input type='submit'>
  </form>
  ```

# 12. Redis的使用

- **视图中使用Redis**

  ```python
  from django_redis import get_redis_connection
  
  # 获取redis连接对象
  redis_conn = get_redis_connection('verify_codes')  # 参数verify_codes在settings.py中指定
  # 新增数据
  redis_conn.setex("img_%s" % image_code_id,
                   constants.IMAGE_CODE_REDIS_EXPIRES, 
                   text)
  # 查询数据
  real_image_code_text = redis_conn.get('img_%s' % image_code_id)
  
  # 删除数据
  redis_conn.delete('img_%s' % image_code_id)
  
  # 同时执行多条命令用管道
  pl = redis_conn.pipeline()
  pl.setex('sms_%s' % mobile, constants.SMS_CODE_REDIS_EXPIRES, sms_code)
  pl.setex('send_flag_%s' % mobile, constants.SEND_SMS_CODE_INTERVAL, 1)
  pl.execute()
  ```
# 读取数据
  real_image_code_text = redis_conn.get('img_%s' % image_code_id)
  ```
  
  






























































































  ```