# 1. 搭建项目

- 简单demo搭建

  ```python
  from flask import Flask
  
  app = Flask(__name__)  # 更多参数查看源码
  
  # 加载与获取配置
  app.config.from_object(Config)
  # app.config.from_pyfile('config.py')
  # app.config.from_envar('ENVCONFIG')
  debug_config = app.config['DEBUG']
  
  @app.route('/haha')
  def hello_world():
      return "helloworld!"
  
  
  if __name__ == '__main__':
      app.run(host=127.0.0.1 ,port=8888, debug=True)  # 三个均为缺省参数
  ```

- 大型项目目录结构

  - /`.gitignore`
  - /`.config.py`
  - /`manage.py`
  - /`requirements.txt`
  - /logs/`xxx`
  - /app
    - libs
    - modules
      - admin
        - `__init__.py`
        - `views.py`
    - static
    - templates
    - utils
    - `models.py`
    - `constants.py`
    - `__init__.py`

- 大型项目搭建

  `/.gitignore`

  ```python
  .idea/
  *.py[cod]
  logs/log*
  migrations
  .qiniu*
  ```

  `/.manage.py`

  ```python
  from flask_script import Manager
  from flask_migrate import Migrate, MigrateCommand
  from info import create_app, db, models
  
  # manage.py是程序启动的入口，只关心启动的相关参数以及内容，不关心具体该
  # 如果创建app或者相关业务逻辑
  
  # 通过指定的配置名字创建对应配置的app
  # create_app 就类似于工厂方法
  from info.models import User
  
  app = create_app('development')
  
  manager = Manager(app)
  # 将 app 与 db 关联
  Migrate(app, db)
  # 将迁移命令添加到manager中
  manager.add_command('db', MigrateCommand)
  
  
  @manager.option('-n', '-name', dest="name")
  @manager.option('-p', '-password', dest="password")
  def createsuperuser(name, password):
  
      if not all([name, password]):
          print("参数不足")
  
      user = User()
      user.nick_name = name
      user.mobile = name
      user.password = password
      user.is_admin = True
  
      try:
          db.session.add(user)
          db.session.commit()
      except Exception as e:
          db.session.rollback()
          print(e)
  
      print("添加成功")
  
  
  if __name__ == '__main__':
      manager.run()
  ```

  `/config.py`

  ```python
  import logging
  from redis import StrictRedis
  
  
  class Config(object):
      """项目的配置"""
  
      SECRET_KEY = "iECgbYWReMNxkRprrzMo5KAQYnb2UeZ3bwvReTSt+VSESW0OB8zbglT+6rEcDW9X"
  
      # 为数据库添加配置
      SQLALCHEMY_DATABASE_URI = "mysql://root:mysql@127.0.0.1:3306/information27"
      SQLALCHEMY_TRACK_MODIFICATIONS = False
      # 在请求结束时候，如果指定此配置为 True ，那么 SQLAlchemy 会自动执行一次 db.session.commit()操作
      SQLALCHEMY_COMMIT_ON_TEARDOWN = True
  
      # Redis的配置
      REDIS_HOST = "127.0.0.1"
      REDIS_PORT = 6379
  
      # Session保存配置
      SESSION_TYPE = "redis"
      # 开启session签名
      SESSION_USE_SIGNER = True
      # 指定 Session 保存的 redis
      SESSION_REDIS = StrictRedis(host=REDIS_HOST, port=REDIS_PORT)
      # 设置需要过期
      SESSION_PERMANENT = False
      # 设置过期时间
      PERMANENT_SESSION_LIFETIME = 86400 * 2
  
      # 设置日志等级
      LOG_LEVEL = logging.DEBUG
  
  
  class DevelopmentConfig(Config):
      """开发环境下的配置"""
      DEBUG = True
  
  
  class ProductionConfig(Config):
      """生产环境下的配置"""
      DEBUG = False
      LOG_LEVEL = logging.WARNING
  
  
  class TestingConfig(Config):
      """单元测试环境下的配置"""
      DEBUG = True
      TESTING = True
  
  
  config = {
      "development": DevelopmentConfig,
      "production": ProductionConfig,
      "testing": TestingConfig
  }
  ```

  `requirements.txt`

  ```python
  alembic==0.9.9
  blinker==1.4
  certifi==2018.4.16
  chardet==3.0.4
  Flask==0.10.1
  Flask-Migrate==2.1.1
  Flask-MySQLdb==0.2.0
  Flask-Script==2.0.6
  Flask-Session==0.3.1
  Flask-SQLAlchemy==2.3.2
  Flask-WTF==0.14.2
  idna==2.6
  itsdangerous==0.24
  Jinja2==2.10
  Mako==1.0.7
  MarkupSafe==1.0
  mysqlclient==1.3.12
  Pillow==5.1.0
  python-dateutil==2.7.2
  python-editor==1.0.3
  qiniu==7.2.0
  redis==2.10.6
  redis-py-cluster==1.3.4
  requests==2.18.4
  six==1.11.0
  SQLAlchemy==1.2.6
  urllib3==1.22
  Werkzeug==0.14.1
  WTForms==2.1
  ```

  `app/__init__.py`

  ```python
  import logging
  from logging.handlers import RotatingFileHandler
  
  from flask import Flask
  # 可以用来指定 session 保存的位置
  from flask import g
  from flask import render_template
  from flask.ext.session import Session
  from flask.ext.sqlalchemy import SQLAlchemy
  from flask.ext.wtf import CSRFProtect
  from flask.ext.wtf.csrf import generate_csrf
  from redis import StrictRedis
  
  from config import config
  
  db = SQLAlchemy()
  
  # https://www.cnblogs.com/xieqiankun/p/type_hints_in_python3.html
  redis_store = None  # type: StrictRedis
  
  
  # redis_store: StrictRedis = None
  
  
  def setup_log(config_name):
      # 设置日志的记录等级
      logging.basicConfig(level=config[config_name].LOG_LEVEL)  # 调试debug级
      # 创建日志记录器，指明日志保存的路径、每个日志文件的最大大小、保存的日志文件个数上限
      file_log_handler = RotatingFileHandler("logs/log", maxBytes=1024 * 1024 * 100, backupCount=10)
      # 创建日志记录的格式 日志等级 输入日志信息的文件名 行数 日志信息
      formatter = logging.Formatter('%(levelname)s %(filename)s:%(lineno)d %(message)s')
      # 为刚创建的日志记录器设置日志记录格式
      file_log_handler.setFormatter(formatter)
      # 为全局的日志工具对象（flask app使用的）添加日志记录器
      logging.getLogger().addHandler(file_log_handler)
  
  
  def create_app(config_name):
      # 配置日志,并且传入配置名字，以便能获取到指定配置所对应的日志等级
      setup_log(config_name)
      # 创建Flask对象
      app = Flask(__name__)
      # 加载配置
      app.config.from_object(config[config_name])
      # 通过app初始化
      db.init_app(app)
      # 初始化 redis 存储对象
      global redis_store
      redis_store = StrictRedis(host=config[config_name].REDIS_HOST, port=config[config_name].REDIS_PORT,
                                decode_responses=True)
      # 开启当前项目 CSRF 保护，只做服务器验证功能
      # 帮我们做了：从cookie中取出随机值，从表单中取出随机，然后进行校验，并且响应校验结果
      # 我们需要做：1. 在返回响应的时候，往cookie中添加一个csrf_token，2. 并且在表单中添加一个隐藏的csrf_token
      # 而我们现在登录或者注册不是使用的表单，而是使用 ajax 请求，所以我们需要在 ajax 请求的时候带上 csrf_token 这个随机值就可以了
      CSRFProtect(app)
      # 设置session保存指定位置
      Session(app)
  
      # 初始化数据库
      #  在Flask很多扩展里面都可以先初始化扩展的对象，然后再去调用 init_app 方法去初始化
      from info.utils.common import do_index_class
      # 添加自定义过滤器
      app.add_template_filter(do_index_class, "index_class")
  
      from info.utils.common import user_login_data
  
      @app.errorhandler(404)
      @user_login_data
      def page_not_fount(e):
          user = g.user
          data = {"user": user.to_dict() if user else None}
          return render_template('news/404.html', data=data)
  
      @app.after_request
      def after_request(response):
          # 生成随机的csrf_token的值
          csrf_token = generate_csrf()
          # 设置一个cookie
          response.set_cookie("csrf_token", csrf_token)
          return response
  
      # 注册蓝图
      from info.modules.index import index_blu
      app.register_blueprint(index_blu)
  
      from info.modules.passport import passport_blu
      app.register_blueprint(passport_blu)
  
      from info.modules.news import news_blu
      app.register_blueprint(news_blu)
  
      from info.modules.profile import profile_blu
      app.register_blueprint(profile_blu)
  
      from info.modules.admin import admin_blu
      app.register_blueprint(admin_blu, url_prefix="/admin")
  
      return app
  ```

  `/app/models.py`

  ```python
  from datetime import datetime
  from werkzeug.security import generate_password_hash, check_password_hash
  
  from info import constants
  from . import db
  
  
  class BaseModel(object):
      """模型基类，为每个模型补充创建时间与更新时间"""
      create_time = db.Column(db.DateTime, default=datetime.now)  # 记录的创建时间
      update_time = db.Column(db.DateTime, default=datetime.now, onupdate=datetime.now)  # 记录的更新时间
  
  
  # 用户收藏表，建立用户与其收藏新闻多对多的关系
  tb_user_collection = db.Table(
      "info_user_collection",
      db.Column("user_id", db.Integer, db.ForeignKey("info_user.id"), primary_key=True),  # 新闻编号
      db.Column("news_id", db.Integer, db.ForeignKey("info_news.id"), primary_key=True),  # 分类编号
      db.Column("create_time", db.DateTime, default=datetime.now)  # 收藏创建时间
  )
  
  tb_user_follows = db.Table(
      "info_user_fans",
      db.Column('follower_id', db.Integer, db.ForeignKey('info_user.id'), primary_key=True),  # 粉丝id
      db.Column('followed_id', db.Integer, db.ForeignKey('info_user.id'), primary_key=True)  # 被关注人的id
  )
  
  
  class User(BaseModel, db.Model):
      """用户"""
      __tablename__ = "info_user"
  
      id = db.Column(db.Integer, primary_key=True)  # 用户编号
      nick_name = db.Column(db.String(32), unique=True, nullable=False)  # 用户昵称
      password_hash = db.Column(db.String(128), nullable=False)  # 加密的密码
      mobile = db.Column(db.String(11), unique=True, nullable=False)  # 手机号
      avatar_url = db.Column(db.String(256))  # 用户头像路径
      last_login = db.Column(db.DateTime, default=datetime.now)  # 最后一次登录时间
      is_admin = db.Column(db.Boolean, default=False)
      signature = db.Column(db.String(512))  # 用户签名
      gender = db.Column(  # 订单的状态
          db.Enum(
              "MAN",  # 男
              "WOMAN"  # 女
          ),
          default="MAN")
  
      # 当前用户收藏的所有新闻lazy="dynamic"
      collection_news = db.relationship("News", secondary=tb_user_collection, lazy="dynamic")  # 用户收藏的新闻
      # 用户所有的粉丝，添加了反向引用followed，代表用户都关注了哪些人
      followers = db.relationship('User',
                                  secondary=tb_user_follows,
                                  primaryjoin=id == tb_user_follows.c.followed_id,
                                  secondaryjoin=id == tb_user_follows.c.follower_id,
                                  backref=db.backref('followed', lazy='dynamic'),
                                  lazy='dynamic')
  
      @property
      def password(self):
          raise AttributeError("当前属性不允许读取")
  
      @password.setter
      def password(self, value):
          # self.password_hash = 对value加密
          self.password_hash = generate_password_hash(value)
  
      def check_password(self, password):
          """校验密码"""
          return check_password_hash(self.password_hash, password)
  
      # 当前用户所发布的新闻
      news_list = db.relationship('News', backref='user', lazy='dynamic')
  
      def to_dict(self):
          resp_dict = {
              "id": self.id,
              "nick_name": self.nick_name,
              "avatar_url": constants.QINIU_DOMIN_PREFIX + self.avatar_url if self.avatar_url else "",
              "mobile": self.mobile,
              "gender": self.gender if self.gender else "MAN",
              "signature": self.signature if self.signature else "",
              "followers_count": self.followers.count(),
              "news_count": self.news_list.count()
          }
          return resp_dict
  
      def to_admin_dict(self):
          resp_dict = {
              "id": self.id,
              "nick_name": self.nick_name,
              "mobile": self.mobile,
              "register": self.create_time.strftime("%Y-%m-%d %H:%M:%S"),
              "last_login": self.last_login.strftime("%Y-%m-%d %H:%M:%S"),
          }
          return resp_dict
  
  
  class News(BaseModel, db.Model):
      """新闻"""
      __tablename__ = "info_news"
  
      id = db.Column(db.Integer, primary_key=True)  # 新闻编号
      title = db.Column(db.String(256), nullable=False)  # 新闻标题
      source = db.Column(db.String(64), nullable=False)  # 新闻来源
      digest = db.Column(db.String(512), nullable=False)  # 新闻摘要
      content = db.Column(db.Text, nullable=False)  # 新闻内容
      clicks = db.Column(db.Integer, default=0)  # 浏览量
      index_image_url = db.Column(db.String(256))  # 新闻列表图片路径
      category_id = db.Column(db.Integer, db.ForeignKey("info_category.id"))
      user_id = db.Column(db.Integer, db.ForeignKey("info_user.id"))  # 当前新闻的作者id
      status = db.Column(db.Integer, default=0)  # 当前新闻状态 如果为0代表审核通过，1代表审核中，-1代表审核不通过
      reason = db.Column(db.String(256))  # 未通过原因，status = -1 的时候使用
      # 当前新闻的所有评论
      comments = db.relationship("Comment", lazy="dynamic")
  
      def to_review_dict(self):
          resp_dict = {
              "id": self.id,
              "title": self.title,
              "create_time": self.create_time.strftime("%Y-%m-%d %H:%M:%S"),
              "status": self.status,
              "reason": self.reason if self.reason else ""
          }
          return resp_dict
  
      def to_basic_dict(self):
          resp_dict = {
              "id": self.id,
              "title": self.title,
              "source": self.source,
              "digest": self.digest,
              "create_time": self.create_time.strftime("%Y-%m-%d %H:%M:%S"),
              "index_image_url": self.index_image_url,
              "clicks": self.clicks,
          }
          return resp_dict
  
      def to_dict(self):
          resp_dict = {
              "id": self.id,
              "title": self.title,
              "source": self.source,
              "digest": self.digest,
              "create_time": self.create_time.strftime("%Y-%m-%d %H:%M:%S"),
              "content": self.content,
              "comments_count": self.comments.count(),
              "clicks": self.clicks,
              "category": self.category.to_dict(),
              "index_image_url": self.index_image_url,
              "author": self.user.to_dict() if self.user else None
          }
          return resp_dict
  
  
  class Comment(BaseModel, db.Model):
      """评论"""
      __tablename__ = "info_comment"
  
      id = db.Column(db.Integer, primary_key=True)  # 评论编号
      user_id = db.Column(db.Integer, db.ForeignKey("info_user.id"), nullable=False)  # 用户id
      news_id = db.Column(db.Integer, db.ForeignKey("info_news.id"), nullable=False)  # 新闻id
      content = db.Column(db.Text, nullable=False)  # 评论内容
      parent_id = db.Column(db.Integer, db.ForeignKey("info_comment.id"))  # 父评论id
      parent = db.relationship("Comment", remote_side=[id])  # 自关联
      like_count = db.Column(db.Integer, default=0)  # 点赞条数
  
      def to_dict(self):
          resp_dict = {
              "id": self.id,
              "create_time": self.create_time.strftime("%Y-%m-%d %H:%M:%S"),
              "content": self.content,
              "parent": self.parent.to_dict() if self.parent else None,
              "user": User.query.get(self.user_id).to_dict(),
              "news_id": self.news_id,
              "like_count": self.like_count
          }
          return resp_dict
  
  
  class CommentLike(BaseModel, db.Model):
      """评论点赞"""
      __tablename__ = "info_comment_like"
      comment_id = db.Column("comment_id", db.Integer, db.ForeignKey("info_comment.id"), primary_key=True)  # 评论编号
      user_id = db.Column("user_id", db.Integer, db.ForeignKey("info_user.id"), primary_key=True)  # 用户编号
  
  
  class Category(BaseModel, db.Model):
      """新闻分类"""
      __tablename__ = "info_category"
  
      id = db.Column(db.Integer, primary_key=True)  # 分类编号
      name = db.Column(db.String(64), nullable=False)  # 分类名
      news_list = db.relationship('News', backref='category', lazy='dynamic')
  
      def to_dict(self):
          resp_dict = {
              "id": self.id,
              "name": self.name
          }
          return resp_dict
  
  ```

  





# 2. 路由分发

- Get

  ```python
  # 用装饰器定义定义
  @app.route('/')
  def index():
      return 'index'
  ```

- Post

  ```python
  @app.route('/', methods=['POST'])
  def index():
      return 'index'
  ```



# 3. 获取请求参数



## 3.1 请求

- request

  > 在视图函数中直接使用代表当前本次请求 `from flask import request`

- request可获得的属性

  `data, form, args, cookies, headers, method, url, files`

## 3.1 路径参数

- 路由定义 (int为转换器)

  ```python
  @app.route('/user/<int:user_id>')
  ```

- 视图中获取

  ```python
  @app.route('/user/<int:user_id>')
  def user(user_id):
      pass
  ```

## 3.2 路径参数过滤 ==> 转换器

- 视图

  ```python
  from werkzeug.routing import BaseConverter
  
  # 定义转换器
  class RegexConverter(BaseConverter):
      def __init__(self, url_map, *args):
          super(RegexConverter, self).__init__(url_map)
          # 获取正则
          self.regex = args[0]
          
      def to_python(self, value):
      	# 对获取的参数进行处理,并返回
  		return value
      
      def to_url(self, value):
          # 使用url_for时,对传入的参数先处理,再匹配路由
          return value
  
  
  # 在app中注册转换器
  app.url_map.converters['re'] = RegexConverter
  
  # 对路径参数使用转换器
  def index(/user/<re('[0-9]{6}'):user_id>)
  ```

## 3.3 获取表单参数

- 视图

  ```python
  def index():
      username = request.form.get('username')
  ```

## 3.4 获取查询参数

- 视图

  ```python
  image_code_id = request.args.get('imageCodeId', None)
  ```

  

## 3.5 获取文件参数

- 视图

  ```python
  def index():
      file = request.forms.get('pic')
      file.save()
      return success
  ```

## 3.6 获取请求体json数据

- 视图

  ```python
  param_dict = request.json
  mobile = param_dict.get("mobile")
  password = param_dict.get("password")
  ```



# 4. 返回响应

## 4.1 返回固定状态码响应

- 视图

  ```python
  def index():
      return 'index', 666  # 返回固定状态码
  ```

## 4.1 返回JSON响应

- 视图

  ```python
  def index():
  	json_dict = {
          'name': 'zs'
      }
      
  	return jsonify(json_dict)
  	# 底层: json_str = json.dumps(json_dict)  # json.loads json ==> dict
  ```

## 4.2 重定向

- 视图

  ```python
  def index():
      return redirect(url_for('user', user_id=123))  # 重定向到user视图
  ```

## 4.3 返回模板

- 视图

  ```python
  def index():
  	return render_template()
  ```

## 4.4 返回响应并设置响应头

- 视图

  ```python
  def index():
      response = make_response("Content-Type") = 'image/jpg'
      return response
  ```

## 4.5 返回静态资源

- 视图

  ```python
  def index():
  	return current_app.send_static_file('new/favicon.ico')
  ```

  















# 5. 视图常用逻辑

## 5.1 异常响应

- 主动抛出异常

  ```python
  def index():
      abort(404)
  ```

- 异常处理

  ```python
  # 捕获全局错误状态码 500, 400
  @app.errorhandler(404)
  def page_not_found(error):
      # error中包含错误信息
      return 'page not found'
  
  # 捕获错误类型
  @app.errorhander(Exception):
  def exception_error(error)
      return 'Exception'
  ```

## 5.2 请求勾子

- 视图

  ```python
  @before_first_request
  def before_f_r():
      """第一次请求前执行"""
      pass
  
  @before_request
  def bofore_r():
      """每次请求前执行"""
      # 如果return不会执行视图,直接返回return响应
      return
  
  @after_request
  def after_r(value):
      """每次请求结束后对响应数据处理,并返回"""
      return value
  
  @teardown_request
  def teardown_r(error):
      """异常发生时,抛出错误"""
      pass
  ```

  

## 5.3 Cookie&Session

- 设置Cookie与删除

  ```python
  def index():
      response = make_response('success')
      response.set_cookie('user_id', '1', max_age=3600)  # 更多参数查看源码
      response.delete_cookie('user_id')
      return response
  ```

- 获取Cookie

  ```python
  def index():
      user_id = request.cookies.get('user_id')
      return 'success'
  ```

- 设置Session与删除

  ```python
  app.config['SECRET_KEY'] = 'lfjweioano'
  
  def index():
      session['user_id'] = '1'
      session.pop('user_id', None)
  ```

- 获取Session

  ```python
  user_id = session['user_id']
  ```

  

## 5.4 上下文

- 请求上下文 --- 一个视图中有效
  - request
  - session
- 应用上下文 --- 整个应用中都有效
  - current_app
  - g变量



## 5.5 CSRF

> 跨站请求伪造 ==> 简单理解: 第三方网站通过用户浏览器访问主网站, 携带主网点的cookie, 导致第三方访问主网站请求成功, 通过在cookie和表单中设置一个相同的值在后台进行等值校验以解决问题

- 生成随机字符串

  ```python
  bytes.decode(base64.b64encode(os.uramdom(48)))
  ```

- 防止CSRF攻击

  > 通过在cookie和表单中设置一个相同的值在后台进行等值校验以解决问题

- Flask中解决CSRF

  ```python
  app.secret_key = "#此处可以写随机字符串#"
  
  # 开启csrf
  from flask.ext.wtf import CSRFProtect
  CSRFProtect(app)
  
  
  # 在cookie中设置csrf
  @app.after_response
  def csrf_protect(response):
      response.set_cookie('csrf_token', generate_csrf())
      return response
  
  
  # 在表单中设置csrf
  <form method="post" action="/">
      <input type="hidden" name="csrf_token" value="{{ csrf_token() }}" />
  </form>
  # 或ajax中设置csrf
  $.ajax({
      headers: {
          "X-CSRFToken": getCookie('csrf_token')
      },
  })
  
  ```



## 5.6 蓝图

- 蓝图的使用

  ```python
  # 定义蓝图对象
  admin=Blueprint('admin',__name__)  # 更多参数查看源码
  # 注册蓝图
  app.register_blueprint(admin, url_prefix='/admin')
  # 使用蓝图
  @admin.route('/')
  def admin_home():
      return 'admin_home'
  ```

## 5.7 操作数据库

- 操作redis

  ```python
  try:
      redis_store.set("sms", "save content", 3600)
      sms = redis_store.get('sms')
  except Exception as e:
      current_app.logger.error(e)
      return jsonify(errno=123, errmsg='数据库查询失败')
  ```

- 操作mysql

  ```python
  try:
      db.session.xxx
      db.session.commit()
  except Exception as e:
      current_app.logger.error(e)
      db.session.rollback()
      return jsonify(errno=456, errmsg="数据库操作失败")
  ```

  

## 5.8 记录日志

- 视图

  > error, debug...

  ```python
  current_app.logger.error(e)
  ```

  

## 5.9 装饰器和g变量的使用

- 视图

  ```python
  def user_login_data(f):
      @functools.wraps(f)  # 保证被装饰的函数名称不变化
      def wrapper(*args, **kwargs):
          g.user = None
          return f(*args, **kwargs)
      return wrapper
  
  @user_login_data
  def login():
      user = g.user
  ```

  











# 6. 模板

## 6.1 Flask数据展示

- 视图

  ```python
  @app.route('/')
  def index():
      # 往模板中传入的数据
      my_str = 'Hello 黑马程序员'
      my_int = 10
      my_array = [3, 4, 2, 1, 7, 9]
      my_dict = {
          'name': 'xiaoming',
          'age': 18
      }
      return render_template('temp_demo1.html',
                             my_str=my_str,
                             my_int=my_int,
                             my_array=my_array,
                             my_dict=my_dict
                             )
  ```

- html

  ```jinja2
  我的模板html内容
  <br/>{{ my_str }}
  <br/>{{ my_int }}
  <br/>{{ my_array }}
  <br/>{{ my_dict }}
  
  相关运算和取值
  {{ my_int + 10 }}
  {{ my_array[0] }}
  <br/>{{ my_array.1 }}
  {{ my_dict['name'] }}
  {{ my_dict.age }}
  ```

## 6.2 过滤器

- 内建过滤器

  | 名称       | 作用                                          |
  | ---------- | --------------------------------------------- |
  | capitalize | 首字母大写，其余字母小写                      |
  | lower      | 转成小写                                      |
  | upper      | 转成大写                                      |
  | title      | 每个单词的首字母都转成大写                    |
  | reverse    | 字符串反转                                    |
  | format     | 格式化输出{{ '%s is %d' \| format('name', 17) |
  | striptags  | 渲染之前把值中所有的HTML标签都删掉            |
  |            |                                               |
  | first      | 列表第一个元素                                |
  | last       | 列表最后一个元素                              |
  | length     | 获取列表长度                                  |
  | sum        | 列表求和                                      |
  | sort       | 列表排序                                      |

  ```jinja2
  {{ "hello world" | reverse | upper }}  {# 支持链式调用 #}
  ```

  ```jinja2
  {% filter upper %}
      #对语句块过滤#
  {% endfilter %}
  ```

- 自定义过滤器

  ```python
  @app.template_filter('lireverse')
  # 或使用注册方式:  app.add_template_filter(do_listreverse,'lireverse')
  def do_listreverse(li):
      # 通过原列表创建一个新列表
      temp_li = list(li)
      # 将新列表进行返转
      temp_li.reverse()
      return temp_li
  ```

  

## 6.3 控制代码块

- if

  ```jinja2
  {% if comments | length > 0 %}
      There are {{ comments | length }} comments
  {% else %}
      There are no comments
  {% endif %}
  ```

- for

  ```jinja2
  {% for post in posts if post.text %}
      <div>
          <h1>{{ post.title }}</h1>
          <p>{{ post.text | safe }}</p>
      </div>
  {% endfor %}
  ```

  for中使用循环计数

  | 变量           | 描述                                           |
  | :------------- | :--------------------------------------------- |
  | loop.index     | 当前循环迭代的次数（从 1 开始）                |
  | loop.index0    | 当前循环迭代的次数（从 0 开始）                |
  | loop.revindex  | 到循环结束需要迭代的次数（从 1 开始）          |
  | loop.revindex0 | 到循环结束需要迭代的次数（从 0 开始）          |
  | loop.first     | 如果是第一次迭代，为 True 。                   |
  | loop.last      | 如果是最后一次迭代，为 True 。                 |
  | loop.length    | 序列中的项目数。                               |
  | loop.cycle     | 在一串序列间期取值的辅助函数。见下面示例程序。 |

  ```python
  {% for post in posts%}
  {{loop.index}}, {{post.title}}
  {% endfor %}
  ```



## 6.4 代码复用

- 宏 (相当于函数)

  > 使用时, 单独使用文件保存宏 macro.html

  ```jinja2
  {# 定义宏 macro.html #}
  {% macro input(label='', type='text', value='') %}
  <label>{{ label }}</label><input type="{{ type }}" value="{{ value }}"></input>
  {% endmacro %}
  
  {# 导入宏 #}
  {% import 'macro.html' as macro %}
   
  {# 使用宏 #}
  {{ macro.input('用户名', type='passowrd') }}
  ```

- 继承

  ```jinja2
  {% extends 'base.html' %}
  
  {# 重写父类中被继承的内容 #}
  {% block xxxx %}
  	{{ super() }}
  
  {% endblock %}
  ```

- 包含

  ```jinja2
  {% include 'base.html' ignore missing %}
  ```

  

## 6.5 模板中特有的变量和函数

- 示例

  | 变量或函数             | 作用                                         | 示例                                                         |
  | ---------------------- | -------------------------------------------- | ------------------------------------------------------------ |
  | config                 | Flask当前的config对象                        | `{{config.SQLALCHEMY_DATABASE_URI}}`                         |
  | request                | 当前请求的request对象                        | `{{request.url}}`                                            |
  | session                | Flask的session对象                           | `{{session.new}}`                                            |
  | g变量                  | 取出g变量的属性值                            | `{{ g.name }}`                                               |
  | url_for()              | 根据视图函数名,返回路由对应URL               | `{{ url_for('home') }}`<br>`{{ url_for('post', post_id=1)}}` |
  | get_flashed_messages() | 闪现视图中flask('xxx')传入的消息的列表的内容 | {%for message in get_flashed_messages()%}<br>     {{message}}<br>{%endfor%} |

  











# 7. 扩展

## 7.1 flask_script

> 运行脚本扩展, 用于启动服务器时,通过命令传入参数

### 安装

```bash
pip install flask_script
```

### 使用

`manage.py`

```python
from flask_script import Manager

manager = Manager(app)  # app为项目应用

if __name__ == '__main__':
    manager.run()
```

`bash`

```python
python manage.py runserver -h xxxx -p xxxx -d
```

## 7.2 flask_wtf

> 生成表单, 并自动完成校验 表单字段与校验参照books

### 安装

```bash
pip install flask_wtf
```

### 使用

- 视图处理

  ```python
  from flask_wtf import FlaskForm
  from wtforms import StringField, PasswordField, SubmitField
  from wtforms.validators import InputRequired
  
  # 测试关闭csrf
  app.config['WTF_CSRF_ENABLED'] = False
  
  
  class RegisterForm(FlaskForm):
      username = StringField('用户名:', 
                             render_kw={'placeholder':'占位文字'}
                             validators=[InputRequired('必输')]
                            )
      password = PasswordField('密码:')
      submit = SubmitField('注册')
      
  @app.route('/register', methods=['POST'])
  def register():
      register_form = RegisterForm()
      # wtf_forms验证表单
      if register_form.validate_on_submit():
          return "success"
      else:
          flash("参数有误或者不完整")
  ```

- 模板

  ```jinja2
  <form method="post">
  	{{ form.csrf_token() }}  # 使用wtf表单,调用此函数直接自动完成csrf校验
      {{ form.username.label }} {{ form.username }}<br/>
      {{ form.password.label }} {{ form.password }}<br/>
      {{ form.password2.label }} {{ form.password2 }}<br/>
      {{ form.submit }}
  </form>
  ```



## 7.3 Flask-SQLAlchemy

### 安装配置

- 安装

  ```bash
  pip install flask-sqlalchemy
  # 连接到mysql时需要安装mysqldb
  pip install flask-mysqldb
  ```

- 配置

  > 更多配置参照books

  ```python
  # 数据库使用URL指定
  app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:mysql@127.0.0.1:3306/test'
  # 动态追踪修改设置，如未设置只会提示警告
  app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
  #查询时会显示原始SQL语句
  app.config['SQLALCHEMY_ECHO'] = True
  ```

### 数据库模型类

> 字段类型,列选项,关系选项 参照books

- 模型类

  ```python
  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy
  
  
  class Role(db.Model):
      # 定义表名
      __tablename__ = 'roles'
      # 定义列对象
      id = db.Column(db.Integer, primary_key=True)
      name = db.Column(db.String(64), unique=True)
      # 定义关联关系
      # backref为类User申明新属性的方法
      # lazy='dynamic'动态查询
      us = db.relationship('User', backref='role', lazy='dynamic')
  
      #repr()方法显示类的描述信息
      def __repr__(self):
          return 'Role:%s'% self.name
  
  class User(db.Model):
      __tablename__ = 'users'
      id = db.Column(db.Integer, primary_key=True)
      name = db.Column(db.String(64), unique=True, index=True)
      email = db.Column(db.String(64),unique=True)
      password = db.Column(db.String(64))
      # 定义关联关系
      role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))
  
      def __repr__(self):
          return 'User:%s'%self.name
  ```

- 一对多

  ```python
  class Role(db.Model):
      ...
      #关键代码
      us = db.relationship('User', backref='role', lazy='dynamic')
      ...
  
  class User(db.Model):
      ...
      role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))
  ```

- 多对多

  ```python
  registrations = db.Table('registrations',  
      db.Column('student_id', db.Integer, db.ForeignKey('students.id')),  
      db.Column('course_id', db.Integer, db.ForeignKey('courses.id'))  
  )  
  class Course(db.Model):
      ...
  class Student(db.Model):
      ...
      courses = db.relationship('Course',secondary=registrations,  
                                      backref='students',  
                                      lazy='dynamic')
  ```



###  数据库CRUD

- 新增数据

  ```python
  # 插入一条数据
  ro1 = Role(name='admin')
  db.session.add(ro1)
  db.session.commit()
  
  # 一次插入多条数据
  ro2 = Role(name='user')
  ro3 = Role(name='student')
  db.session.add_all([ro2, ro3])
  db.session.commit()
  ```

- 查询

  - `filter_by`精确查询

    ```python
    User.query.filter_by(name='wang').all()
    ```

  - `first()`返回查询第一个对象

    ```python
    User.query.first()
    ```

  - `all()`返回查询的所有对象

    ```python
    User.query.all()
    ```

  - `filter`模糊查询

    ```python
    User.query.filter(User.name.endswith('xxx')).all()
    User.query.filter(User.name.startswith('xxx')).all()
    User.query.filter(User.name.contains('xxx')).all()
    # 查询用户在[1,3,5,7,9]的用户
    User.query.filter(User.id.in_([1,3,5,7,9])).all()
    ```

  - `get(id)`参数为主键

    ```python
    User.query.get(1)
    ```

  - 排序查询

    ```python
    User.query.order_by(User.email.asc).all()
    User.query.order_by(User.email.desc).all()
    ```

  - 分页查询

    ```python
    paginate = User.query.paginate(2, 3)  # 查询第2页,每页3个
    paginate.items  # 当前页数据
    paginate.pages  # 总页数
    paginate.page  # 当前页
    ```

  - 聚合查询

    ```python
    User.query.count()
    ```

  - 逻辑与或非

    ```python
    # 逻辑非
    User.query.filter(User.name!='wang').all()
    User.query.filter(not_(User.name=='wang')).all()
    # 逻辑与
    User.query.filter(and_(User.name!='wang', User.email.endwith('163.com'))).all()
    User.query.filter(User.name!='wang', User.email.endwith('163.com')).all()
    # 逻辑或
    User.query.filter(or_(User.name!='wang', User.email.endwith('163.com'))).all()
    ```

  - 关联查询

    ```python
    # 查询角色的所有用户  一对多
    ro1 = Role.query.get(1)
    ro1.us.all()
    
    # 查询用户的角色  多对一
    us1 = User.query.get(3)
    us1.role
    ```

  

- 删除数据

  ```python
  user = User.query.first()
  db.session.delete(user)
  db.session.commit()
  ```

- 更新数据

  ```python
  user = User.query.first()
  user.name = 'dong'
  db.session.commit()
  ```

## 7.4 flask-migrate

> 修改数据库模型时, 在不丢失数据的情况下,配合flask_migrate进行修改

### 安装配置

- 安装

  ```bash
  pip install flask-migrate
  ```

- 配置

  ```python
  #第一个参数是Flask的实例，第二个参数是Sqlalchemy数据库实例
  migrate = Migrate(app, db) 
  
  #manager是Flask-Script的实例，这条语句在flask-Script中添加一个db命令
  manager.add_command('db', MigrateCommand)
  ```

### 迁移数据库

- 迁移

  ```bash
  python manage.py db init  # 创建迁移仓库
  python database.py db migrate -m 'initial migration'  # 创建迁移脚本
  python database.py db upgrade  # 更新数据库
  python app.py db history  # 返回以前的版本
  python app.py db downgrade 版本号
  ```

  































