# Flask项目

## 一. 项目分析

- Flask 前后端不分离
- 使用到的flask扩展
  - flask1.0.2
  - 
- Python3.0 + Flask + Redis + MySQL + 七牛云文件存储 + 云通信短信
- 布署: ubuntu16.04 + Gunicorn + Nginx

## 二. 项目框架搭建

### 2.2 Git管理代码

- Git init

    ```bash
    git init
    git config user.email 'xxxx@xxx.com'
    git config user.name 'xxx'
    git add *
    git commit -m 'init version'
    git branch
    git checkout -b dev
    git push origin dev:dev
    ```

### 2.3 项目基本配置

- SQLAlchemy

    ```python
    from flask_sqlalchemy import SQLAlchemy
    
    	SQLALCHEMY_DATABASE_URI = 'mysql://root:root@127.0.0.1:3306/information'
        SQLALCHEMY_TRACK_MODIFICATIONS = False
        SQLALCHEMY_COMMIT_ON_TEARDOWN = True  # 自动执行db.session.commit()
        
    db = SQLAlchemy(app)
    ```

- Redis

    ```python
    from redis import StrictRedis
    
    	REDIS_HOST = '127.0.0.1'
        REDIS_PORT = 6379
        
    redis_store = StrictRedis(host=Config.REDIS_HOST, port=Config.REDIS_PORT)
    ```

- CSRFProtect

    ```python
    from flask_wtf import CSRFProtect
    
    CSRFProtect(app)
    ```

- Session

    ```python
    from flask import Flask, session
    from flask_session import Session
    
        SECRET_KEY = base64.b64encode(os.urandom(48))
        
        # 配置session保存信息
        SESSION_TYPE = 'redis'
        # 指定Session保存redis
        SESSION_REDIS = StrictRedis(host=REDIS_HOST, port=REDIS_PORT)
        # session 签名
        SESSION_USE_SIGNER = 'True'
        # session是否需要过期
        SESSION_PERMANENT = 'False'
        # 设置过期时间
        PERMANENT_SESSINO_LIFETIME = 3600
        
    # 配置session保存在redis中
    Session(app)
    
    @app.route('/')
    def index():
        session['username'] = 'zs'
        return 'index'
    ```

- Flask-Script

    ```python
    from flask_script import Manager
    
    # 集成Flask-Script
    manager = Manager(app)
    
    if __name__ == '__main__':
        manager.run()
    ```

- Flask-Migrate

    ```python
    from flask_migrate import Migrate, MigrateCommand
    
    # 集成Flask-Migrate
    Migrate(app, db)
    manager.add_command('db', MigrateCommand)
    ```

- 日志配置

    ```python
    import logging
    from logging.handlers import RotatingFileHandler
    
    def create_app(config_name):
        # app工厂 配置项目日志
        setup_log(config_name)
        return app
    
    def setup_log(config_name):
        """配置日志"""
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
    ```

    `config`

    ```python
    class Config(object):
        LOG_LEVEL = logging.DEBUG
    ```

    `使用日志记录`

    ```python
    import logging
    
    @app.route('/')
    def index():
        logging.log(logging.ERROR, 'test error')
        return 'index'
    ```

- 蓝图的使用

    `index/__init__.py` 创建蓝图

    ```python
    from flask import Blueprint
    
    index_blue = Blueprint("index", __name__)
    from . import views
    ```

    `app/__init__.py` 注册蓝图

    ```python
    def create_app(config_name):
        # 注册蓝图
        from info.modules.index import index_blue
        app.register_blueprint(index_blue)
    ```

    `index/views.py` 使用蓝图

    ```python
    from info.modules.index import index_blue
    
    
    @index_blue.route('/')
    def index():
        session['username'] = 'zs'
        logging.log(logging.ERROR, 'test error')
        return 'index'
    ```

### 2.4 数据库配置

- UML模型图
  
    - <http://staruml.io/>
    
- 编写models.py和constants.py, 详见<https://gitee.com/jackiezz/information/commit/61b8bdf09f38755dd77e92b0c5742523978fdd99>

- 数据库迁移

    ```bash
    python manage.py db init
    python manage.py db migrate -m 'initial migration'
python manage.py db upgrade
    ```
    
    

## 三. 后端业务逻辑

### 3.1 根路由相关

#### 3.1.1 根路由模板渲染

- 视图

    ```python
    @index_blue.route('/')
    def index():
        return render_template('index.html')
    ```

#### 3.1.2 加载favicon.ico图标

- 视图

    `current_app.send_static_file() 查找指定的静态文件`

    ```python
    @index_blue.route('/favicon.ico')
    def favicon():
        return current_app.send_static_file('news/favicon.ico')
    ```

### 3.2 用户相关

#### 3.2.1 注册

##### 3.2.1.1 图片验证码

###### 3.2.1.1.1 逻辑分析

1. Client发起获取验证码图片的请求
    1. 请求方式: GET
    2. 请求头: Request URL: /image_code?imageCodeId=xxx
2. Server生成图片验证码发送给Client
    1. 生成图片验证码
    2. 保存图片验证码内容(K-V)到redis中

###### 3.2.1.1.2 生成UUID

- 示例

    ```js
    function generateUUID() {
        var d = new Date().getTime();
        if(window.performance && typeof window.performance.now === "function"){
            d += performance.now(); //use high-precision timer if available
        }
        var uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
            var r = (d + Math.random()*16)%16 | 0;
            d = Math.floor(d/16);
            return (c=='x' ? r : (r&0x3|0x8)).toString(16);
        });
        return uuid;
    }
    ```

###### 3.2.1.1.3 前端实现

- js

    ```js
    var imageCodeId = ""
    
    // 生成一个图片验证码的编号，并设置页面中图片验证码img标签的src属性
    function generateImageCode() {
        imageCodeId = generateUUID();
        var imageCodeUrl = "/image_code?imageCodeId="+imageCodeId
        $(".get_pic_code").attr("src", imageCodeUrl)
    }
    ```

###### 3.2.1.1.4 后端逻辑实现

- views

    ```python
    @passport_blue.route('/image_code')
    def image_code():
        """
        生成图片验证码并返回
        :return: 图片验证码
        """
        # 如果没有id, 直接返回403
        image_code_id = request.args.get('imageCodeId', None)
        if not image_code_id:
            return abort(403)
        # 生成验证码
        name, text, image = captcha.generate_captcha()
        # 保存到redis
        try:
            redis_store.set("image_code_" + image_code_id, text, IMAGE_CODE_REDIS_EXPIRES)
        except Exception as e:
            current_app.logger.error(e)
            return abort(500)
        # 设置数据类型, 以便Client更加容易识别数据类型
        response = make_response(image)
        response.headers["Content-Type"] = "image/jpg"
        return image
    ```

##### 3.2.1.2 短信验证码

###### 3.2.1.2.1逻辑分析

1. Client发起获取短信验证码的请求
    1. 请求方式: POST
    2. 请求头: /passport/send_sms_code
    3. 请求体: JSON格式数据:  imageCodeId=xxx  mobile=xxx
2. Server向用户发送短信验证码并发送给Client
    1. 验证验证码是否有效
    2. 生成短信验证码保存在redis中
    3. 返回响应 errno, errmsg

###### 3.2.1.2.2 后端逻辑实现

- views

    ```python
    @passport_blue.route('/image_code')
    def image_code():
        """
        生成图片验证码并返回
        :return: 图片验证码
        """
        # 如果没有id, 直接返回403
        image_code_id = request.args.get('imageCodeId', None)
        if not image_code_id:
            return abort(403)
        # 生成验证码
        name, text, image = captcha.generate_captcha()
        current_app.logger.debug("图片验证码内容为%s" % text)
        # 保存到redis
        try:
            redis_store.set("image_code_" + image_code_id, text, constants.IMAGE_CODE_REDIS_EXPIRES)
        except Exception as e:
            current_app.logger.error(e)
            return abort(500)
        # 设置数据类型, 以便Client更加容易识别数据类型
        response = make_response(image)
        response.headers["Content-Type"] = "image/jpg"
        return response
    ```

###### 3.2.1.2.3 前端实现

- main.js

    ```js
        params = {
            "mobile": mobile,
            "image_code": imageCode,
            "image_code_id": imageCodeId
        };
        $.ajax({
            url: "/passport/send_sms_code",
            type: "post",
            data: JSON.stringify(params),
            contentType: "application/json",
            success: function (response) {
                if (response.errno == 0) {
                    //发送成功
                    var num = 60;
                    var t = setInterval(function () {
                        if (num == 1) {
                            clearInterval(t);
                            $(".get_code").html("点击获取验证码");
                            $(".get_code").attr("onclick", "sendSMSCode();");
                        } else {
                            num -= 1;
                            $(".get_code").html(num+"秒")
                        }
                    }, 1000)
                } else {
                    //发送失败
                    alert(response.errmsg)
                    $(".get_code").attr("onclick", "sendSMSCode();");
                }
            }
        })
    ```

##### 3.2.1.3 注册

###### 3.2.1.3.1 注册逻辑分析

1. 请求
    1. 请求方式: POST
    2. 请求参数: sms_code, mobile, password
2. 响应
    1. 保存用户信息
    2. 返回JSON响应 errno, errmsg

###### 3.2.1.3.2 注册后端实现

- views

    ```python
    @passport_blue.route('/register', methods=['POST'])
    def register():
        """
        注册逻辑实现
        :return:
        """
        # 获取参数
        params_dict = request.json
        mobile = params_dict.get('mobile')
        sms_code = params_dict.get('smscode')
        password = params_dict.get('password')
    
        # 校验参数
        if not all([mobile, sms_code, password]):
            return jsonify(errno=RET.PARAMERR, errmsg="参数有误")
        if not re.match(r"^1[3578]\d{9}", mobile):
            return jsonify(errno=RET.PARAMERR, errmsg="手机号不正确")
        try:
            real_sms_code = redis_store.get(mobile+"_sms_code")
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.DBERR, errmsg="数据库查询错误")
        if real_sms_code != sms_code:
            return jsonify(errno=RET.DATAERR, errmsg="短信验证码错误")
    
        # 保存数据
        user = User()
        user.mobile = mobile
        user.nick_name = mobile
        user.last_login = datetime.now()
        user.password = password
        try:
            db.session.add(user)
            db.session.commit()
        except Exception as e:
            current_app.logger.error(e)
            db.session.rollback()
            return jsonify(errno=RET.DBERR, errmsg="数据库保存错误")
    
        # 处理用户登录
        session['user_id'] = user.id
        session['mobile'] = user.mobile
        session['nick_name'] = user.nick_name
    
        return jsonify(errno=RET.OK, errmsg="注册成功")
    ```

###### 3.2.1.3.3 注册前端实现

- main.js

    ```js
        $(".register_form_con").submit(function (e) {
            // 阻止默认提交操作
            e.preventDefault()
    
            // 取到用户输入的内容
            var mobile = $("#register_mobile").val()
            var smscode = $("#smscode").val()
            var password = $("#register_password").val()
    
            if (!mobile) {
                $("#register-mobile-err").show();
                return;
            }
            if (!smscode) {
                $("#register-sms-code-err").show();
                return;
            }
            if (!password) {
                $("#register-password-err").html("请填写密码!");
                $("#register-password-err").show();
                return;
            }
    
            if (password.length < 6) {
                $("#register-password-err").html("密码长度不能少于6位");
                $("#register-password-err").show();
                return;
            }
    
            // 发起注册请求
            params = {
                "mobile": mobile,
                'smscode': smscode,
                'password': password
            };
            $.ajax({
                url: 'passport/register',
                type: 'post',
                data: JSON.stringify(params),
                contentType: 'application/json',
                success: function (response) {
                    if (response.errno == 0) {
                        // 注册成功
                        window.location.reload()
                    } else {
                        // 注册失败
                        alert(response.errmsg)
                        $("#register-password-err").html(response.errmsg);
                        $("#register_password-err").show()
                    }
                }
            })
        });
    ```

#### 3.2.2 登录登出

##### 3.2.2.1 登录逻辑分析

1. 请求
    1. 请求方式: POST
    2. 请求参数: mobile, password
2. 响应
    1. 保存用户登录信息(session)
    2. 返回JSON响应 errno, errmsg

##### 3.2.2.2 后端逻辑实现

- views

    ```python
    @passport_blue.route('/login', methods=['GET', 'POST'])
    def login():
        """
        用户登录
        :return:
        """
        if request.method == "POST":
            # 获取参数
            params_dict = request.json
            mobile = params_dict.get('mobile')
            password = params_dict.get('password')
    
            # 校验参数
            if not all([mobile, password]):
                return jsonify(errno=RET.PARAMERR, errmsg="参数错误")
            if not re.match(r"^1[3578]\d{9}", mobile):
                return jsonify(errno=RET.PARAMERR, errmsg="手机号不正确")
            try:
                user = User.query.filter(User.mobile == mobile).first()
            except Exception as e:
                current_app.logger.error(e)
                return jsonify(errno=RET.DBERR, errmsg="数据库查询失败")
            if not user:
                return jsonify(errno=RET.NODATA, errmsg="用户不存在")
            if not user.check_passowrd(password):
                return jsonify(errno=RET.PWDERR, errmsg="用户名或密码错误")
    
            # 保存session 处理用户登录
            session['user_id'] = user.id
            session['mobile'] = user.mobile
            session['nick_name'] = user.nick_name
    
            # 返回响应
            return jsonify(errno=RET.OK, errmsg="登录成功")
    ```

##### 3.2.2.3 前端逻辑实现

- main.js

    ```js
    	$(".login_form_con").submit(function (e) {
            e.preventDefault()
            var mobile = $(".login_form #mobile").val()
            var password = $(".login_form #password").val()
    
            if (!mobile) {
                $("#login-mobile-err").show();
                return;
            }
    
            if (!password) {
                $("#login-password-err").show();
                return;
            }
    
            // 发起登录请求
            params = {
                'mobile': mobile,
                'password': password,
            }
            $.ajax({
                url: "/passport/login",
                type: "post",
                data: JSON.stringify(params),
                contentType: 'application/json',
                success: function (response) {
                    if (response.errno == 0) {
                        location.reload();
                    } else {
                        alert(response.errmsg);
                        $("#login-password-err").html(response.errmsg);
                        $("#login-password-err").show();
                    }
                }
            })
        })
    ```

##### 3.2.2.4 登录主页右上角显示

- views

    ```python
    @index_blue.route('/')
    def index():
        user_id = session.get("user_id", None)
        user = None
        try:
            user = User.query.get(user_id)
        except Exception as e:
            current_app.logger.error(e)
    
        data = {
            "user": user.to_dict() if user else None
        }
        return render_template('index.html', data=data)
    ```

- index.html

    ```python
                {% if not data.user %}
                    <div class="user_btns fr">
                        <a href="javascript:;" class="login_btn">登录</a> / 
                		<a href="javascript:;" class="register_btn">注册</a>
                    </div>
                {% else %}
                    <!-- 用户登录后显示下面，隐藏上面 -->
                    <div class="user_login fr">
                        <img src="../static/news/images/person01.png"
                             class="lgin_pic">
                        <a href="#">Hello {{ data.user.nick_name }}</a>
                        <a href="javascript:;" class="logout_btn">退出</a>
                    </div>
                {% endif %}
    ```

##### 3.2.2.4 登出

- views

    ```python
    @passport_blue.route('/logout', methods=['GET'])
    def logout():
        """
        用户登出
        :return:
        """
        session.pop("user_id", None)
        session.pop("nick_name", None)
        session.pop('mobile', None)
    
        return jsonify(errno=RET.OK, errmsg="登出成功")
    ```

- main.js

    ```js
    function logout() {
        $.ajax({
            url: '/passport/logout',
            type: 'get',
            success: function (response) {
                alert(response.errmsg);
                location.reload()
            }
        })
    }
    ```

#### 3.2.3 CSRF实现

- create_app

    ```python
        # CSRF配置
        # 需要在表单和cookie中设置csrf_token, 校验自动处理了,返回校验结果
        CSRFProtect(app)
    
        @app.after_request
        def after_request(response):
            csrf_token = generate_csrf()
            response.set_cookie('csrf_token', csrf_token)
            return response
    ```

- ajax

    ```js
        $.ajax({
            url: '/passport/logout',
            headers: {
                "X-CSRFToken": getCookie("csrf_token")
            },
        })
    ```

### 3.3 数据展示

#### 3.3.1 首页点击排行

- view.py

    ```python
    @index_blue.route('/')
    def index():
        user_id = session.get("user_id", None)
        user = None
        try:
            user = User.query.get(user_id)
        except Exception as e:
            current_app.logger.error(e)
    
        # 右侧新闻数据
        news_list = []
        news_list = News.query.order_by(News.clicks.desc()).limit(6)
        news_dict_list = []
        for news in news_list:
            news_dict_list.append(news.to_basic_dict())
    
        data = {
            "user": user.to_dict() if user else None,
            "news_dict_list": news_dict_list
        }
        return render_template('index.html', data=data)
    ```

- index.html

    ```html
    <ul class="rank_list">
        {% for news in data.news_dict_list %}
        <li><span class="{{ loop.index | index_class }}">{{ loop.index }}</span><a href="#">{{ news.title }}</a></li>
        {% endfor %}
    </ul>
    ```

##### 3.3.1.1 过滤器实现class

- utils/common.py

    ```python
    def do_index_class(index):
        """
        返回指定索引对应的类名
        :param index:
        :return:
        """
        if index == 1:
            return "first"
        elif index == 2:
            return "second"
        elif index == 3:
            return "third"
        return ""
    ```

- 注册过滤器

    ```python
        app.add_template_filter(do_index_class, "index_class")
    ```

#### 3.3.2 首页新闻数据展示

##### 3.3.2.1 后端逻辑实现

- views

    ```python
    @index_blue.route('/news_list')
    def news_list():
        """
        获取首页数据
        :return:
        """
        # 获取参数
        cid = request.args.get("cid", "1")
        page = request.args.get("page", "1")
        per_page = request.args.get("per_page", "10")
    
        # 校验参数
        try:
            cid = int(cid)
            page = int(page)
            per_page = int(per_page)
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.PARAMERR, errmsg="传入参数有误")
    
        # 查询数据
        filters = []
        if cid != 1:
            filters.append(News.category_id == cid)
        try:
            paginate = News.query.filter(*filters).order_by(News.create_time.desc()).paginate(page, per_page, False)
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.DBERR, errmsg="数据库查询错误")
    
        # 取到当前页数据
        news_list_items = paginate.items  # 返回模型对象列表
        total_page = paginate.pages
        current_page = paginate.page
    
        news_dict_list = []
        for news in news_list_items:
            news_dict_list.append(news.to_basic_dict())
    
        # 返回响应
        data = {
            "total_page": total_page,
            "current_page": current_page,
            "news_dict_list": news_dict_list
        }
        return jsonify(errno=RET.OK, errmsg='请求成功', data=data)
    ```

##### 3.3.2.2 前端展示

- js请求数据, 加载到页面上

    ```js
    $(function () {
        // 界面加载完成加载新闻数据
        updateNewsData();
    });
    
    function updateNewsData() {
        // 更新新闻数据
        var params = {
            "cid": currentCid,
            'page': cur_page,
        };
        $.get("/news_list", params, function (resp) {
            if (resp.errno == 0) {
                // 请求成功
                total_page = resp.data.total_page;
                data_querying = true;
                if (cur_page == 1) {
                    $(".list_con").html("");
                }
                for (var i = 0; i < resp.data.news_dict_list.length; i++) {
                    var news = resp.data.news_dict_list[i]
                    var content = '<li>'
                    content += '<a href="#" class="news_pic fl"><img src="' + news.index_image_url + '?imageView2/1/w/170/h/170"></a>'
                    content += '<a href="#" class="news_title fl">' + news.title + '</a>'
                    content += '<a href="#" class="news_detail fl">' + news.digest + '</a>'
                    content += '<div class="author_info fl">'
                    content += '<div class="source fl">来源：' + news.source + '</div>'
                    content += '<div class="time fl">' + news.create_time + '</div>'
                    content += '</div>'
                    content += '</li>'
                    $(".list_con").append(content)
                }
    
            } else {
                // 请求失败
                alert(resp.errmsg)
            }
    
        })
    }
    ```

##### 3.3.2.3 滚动到底部加载更多

- 页面滚动到底部时加载更多

    ```js
    var currentCid = 1; // 当前分类 id
    var cur_page = 1; // 当前页
    var total_page = 1;  // 总页数
    var data_querying = true;   // 是否正在向后台获取数据
    
    
    $(function () {
        //页面滚动加载相关
        $(window).scroll(function () {
            // 浏览器窗口高度
            var showHeight = $(window).height();
            // 整个网页的高度
            var pageHeight = $(document).height();
            // 页面可以滚动的距离
            var canScrollHeight = pageHeight - showHeight;
            // 页面滚动了多少,这个是随着页面滚动实时变化的
            var nowScroll = $(document).scrollTop();
    
            if ((canScrollHeight - nowScroll) < 100) {
                // TODO 判断页数，去更新新闻数据
                // console.log("页面到底部了")
                if (data_querying) {
                    data_querying = false;
                    // 加载数据
                    if (cur_page < total_page) {
                        cur_page += 1;
                        updateNewsData()
                    }
                }
            }
        })
    });
    ```

#### 3.3.3 数据分类展示

- views

    ```python
        # 上方分类数据展示
        try:
            categories = Category.query.all()
        except Exception as e:
            current_app.logger.error(e)
            return jsonify(errno=RET.DBERR, errmsg="数据库查询错误")
    
        category_list = []
        for category in categories:
            category_list.append(category.to_dict())
    
        data = {
            "user": user.to_dict() if user else None,
            "news_dict_list": news_dict_list,
            "category_list": category_list
        }
    ```

- js

    ```js
        $('.menu li').click(function () {
            var clickCid = $(this).attr('data-cid')
            $('.menu li').each(function () {
                $(this).removeClass('active')
            })
            $(this).addClass('active')
    
            if (clickCid != currentCid) {
                // 记录当前分类id
                currentCid = clickCid
    
                // 重置分页参数
                cur_page = 1
                total_page = 1
                updateNewsData()
            }
        })
    ```

    







































































