# 如何学习框架

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

# 1. DRF概述

- DRF --> Django REST framework

- [官方文档](http://www.django-rest-framework.org/)
- [Github源码](https://github.com/encode/django-rest-framework/tree/master)

- Web应用模式
  - 前后端不分离
    - 浏览器请求动态页面 -->  应用服务器查询数据库,渲染模板,返回HTML页面或重定向页面
  - 前后端分离
    - 浏览器请求静态页面 --> 静态文件服务器返回静态文件
    - 浏览器运行JS请求后端数据填充到页面中 --> 通过对外统一的API通过请求数据,返回JSON/XML数据

- RESTful设计方法

  - 域名

    > 在API下部署域名

    ```python
    https://api.example.com
    # 如果API简单,可放在主域名下
    https://example.com/api/
    ```

  - 版本

    > 应该将API的版本号放在URL中

    ```python
    http://www.example.com/api/1.0/foo
    http://www.example.com/api/1.1/foo
    
    # 也可以将版本号放在RequestHead中 (github使用此模式)
    Accept: vnd.example-com.foo+json; version=1.0
    Accept: vnd.example-com.foo+json; version=1.1
    ```

  - 路径

    > 路径又称为终点(Endpoint),是一种资源, 往往与数据库中的表对应
    >
    > API中只能有名词不能有动词, 且为名词复数
    >
    > 使用HTTP方法分离网址中的资源名称的操作

    ```python
    GET /products ：将返回所有产品清单
    POST /products ：将产品新建到集合
    GET /products/4 ：将获取产品 4
    PATCH（或）PUT /products/4 ：将更新产品 4
    ```

  - HTTP动词

    - GET（SELECT）：从服务器取出资源（一项或多项）。
    - POST（CREATE）：在服务器新建一个资源。
    - PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
    - DELETE（DELETE）：从服务器删除资源。

    还有三个不常用的HTTP动词。

    - PATCH（UPDATE）：在服务器更新(更新)资源（客户端提供改变的属性）。
    - HEAD：获取资源的元数据。
    - OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

    ```python
    GET /zoos：列出所有动物园
    POST /zoos：新建一个动物园（上传文件）
    GET /zoos/ID：获取某个指定动物园的信息
    PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
    PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
    DELETE /zoos/ID：删除某个动物园
    GET /zoos/ID/animals：列出某个指定动物园的所有动物
    DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
    ```

  - 过滤信息

    常见的过滤参数

    ```python
    ?limit=10：指定返回记录的数量
    ?offset=10：指定返回记录的开始位置。
    ?page=2&per_page=100：指定第几页，以及每页的记录数。
    ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
    ?animal_type_id=1：指定筛选条件
    ```

  - 状态码

    ```python
    200 OK - [GET]：服务器成功返回用户请求的数据
    201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
    202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
    204 NO CONTENT - [DELETE]：用户删除数据成功。
    400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作
    401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
    403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
    404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
    406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
    410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
    422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
    500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
    ```

  - 错误处理（Error handling）

    > 如果状态码是4xx，服务器就应该向用户返回出错信息。
    >
    > 一般来说，返回的信息中将error作为键名，出错信息作为键值

    ```json
    {
        error: "Invalid API key"
    }
    ```

  - 返回结果

    > 服务器返回的数据格式，应该尽量使用JSON，避免使用XML。

    ```python
    - GET /collection：返回资源对象的列表（数组）
    - GET /collection/resource：返回单个资源对象
    - POST /collection：返回新生成的资源对象
    - PUT /collection/resource：返回完整的资源对象
    - PATCH /collection/resource：返回完整的资源对象
    - DELETE /collection/resource：返回一个空文档
    ```

  - 超媒体（Hypermedia API）

    > 参照[api.github.com](https://api.github.com/)

- REST接口开发的核心任务

  > **序列化**: 将程序中的一个数据结构类型转换为其他格式（字典、JSON、XML等）
  >
  > **反序列化**: 将其他格式（字典、JSON、XML等）转换为程序中的数据

  - 将数据库数据序列化为前端所需要的格式，并返回；
  - 将前端发送的数据反序列化为模型类对象，并保存到数据库中。

# 2. 安装配置

- 安装DRF

  ```bash
  pip install djangorestframework
  ```

- 配置

  `settings.py`

  ```python
  INSTALLED_APPS = [
      'rest_framework',
  ]
  ```

# 3. DRF使用示例

- **创建序列化器**

  `serializers.py`

  ```python
  class BookInfoSerializer(serializers.ModelSerializer):
      """图书数据序列化器"""
      class Meta:
          model = BookInfo  # 数据字段从模型类BookInfo参考生成
          fields = '__all__'  # 包含模型类中的哪些字段，'__all__'指明包含所有字段
  ```

- **编写视图**

  `views.py`

  ```python
  from rest_framework.viewsets import ModelViewSet
  from .serializers import BookInfoSerializer
  from .models import BookInfo
  
  class BookInfoViewSet(ModelViewSet):
      queryset = BookInfo.objects.all()  # 指明该视图集在查询数据时使用的查询集
      serializer_class = BookInfoSerializer  # 指明该视图在进行序列化或反序列化时使用的序列化器
  ```

- **定义路由**

  `urls.py`

  ```python
  from . import views
  from rest_framework.routers import DefaultRouter
  
  router = DefaultRouter()  # 可以处理视图的路由器
  router.register('books', views.BookInfoViewSet, name='books')  # 向路由器中注册视图集
  urlpatterns += router.urls  # 将路由器中的所以路由信息追到到django的路由列表中
  ```

- **测试**

  > 127.0.0.1:8000 可以获取所有数据的接口

# 4. Serializer序列化器

- **定义Serializer**

  > serializer不是只能为数据库模型类定义，也可以为非数据库模型类的数据定义。

  `models.py`

  ```python
  class BookInfo(models.Model):
      btitle = models.CharField(max_length=20, verbose_name='名称')
      bpub_date = models.DateField(verbose_name='发布日期', null=True)
      bread = models.IntegerField(default=0, verbose_name='阅读量')
      bcomment = models.IntegerField(default=0, verbose_name='评论量')
      image = models.ImageField(upload_to='booktest', verbose_name='图片', null=True)
  ```

  `serializer.py`

  ```python
  class BookInfoSerializer(serializers.Serializer):
      """图书数据序列化器"""
      id = serializers.IntegerField(label='ID', read_only=True)
      btitle = serializers.CharField(label='名称', max_length=20)
      bpub_date = serializers.DateField(label='发布日期', required=False)
      bread = serializers.IntegerField(label='阅读量', required=False)
      bcomment = serializers.IntegerField(label='评论量', required=False)
      image = serializers.ImageField(label='图片', required=False)
  ```

- **定义模型类序列化器**

  ```python
  class BookInfoSerializer(serializers.ModelSerializer):
      """图书数据序列化器"""
      class Meta:
          model = BookInfo  # 指明参照哪个模型类
          # fields = ('id', 'btitle', 'bpub_date')
          fields = '__all__'  # 指明为模型类的哪些字段生成
          exclude = ('image',)  # 排除掉哪些字段
          depth = 1  # 关联层级  默认使用主键作为关联字段, 指定depth使用关联对象嵌套序列化
          read_only_fields = ('id', 'bread', 'bcomment')  #　指明仅用于序列化输出的字段
          extra_kwargs = {	# 添加额外参数
              'bread': {'min_value': 0, 'required': True},
              'bcomment': {'min_value': 0, 'required': True},
          }
  ```

  

- **字段与选项**

  常用字段类型：

  | 字段                    | 字段构造方式                                                 |
  | ----------------------- | ------------------------------------------------------------ |
  | **BooleanField**        | BooleanField()                                               |
  | **NullBooleanField**    | NullBooleanField()                                           |
  | **CharField**           | CharField(max_length=None, min_length=None, allow_blank=False, trim_whitespace=True) |
  | **EmailField**          | EmailField(max_length=None, min_length=None, allow_blank=False) |
  | **RegexField**          | RegexField(regex, max_length=None, min_length=None, allow_blank=False) |
  | **SlugField**           | SlugField(max_length=50, min*length=None, allow_blank=False) 正则字段，验证正则模式 [a-zA-Z0-9*-]+ |
  | **URLField**            | URLField(max_length=200, min_length=None, allow_blank=False) |
  | **UUIDField**           | UUIDField(format='hex_verbose')  format:  1) `'hex_verbose'` 如`"5ce0e9a5-5ffa-654b-cee0-1238041fb31a"`  2） `'hex'` 如 `"5ce0e9a55ffa654bcee01238041fb31a"`  3）`'int'` - 如: `"123456789012312313134124512351145145114"`  4）`'urn'` 如: `"urn:uuid:5ce0e9a5-5ffa-654b-cee0-1238041fb31a"` |
  | **IPAddressField**      | IPAddressField(protocol='both', unpack_ipv4=False, **options) |
  | **IntegerField**        | IntegerField(max_value=None, min_value=None)                 |
  | **FloatField**          | FloatField(max_value=None, min_value=None)                   |
  | **DecimalField**        | DecimalField(max_digits, decimal_places, coerce_to_string=None, max_value=None, min_value=None) max_digits: 最多位数 decimal_palces: 小数点位置 |
  | **DateTimeField**       | DateTimeField(format=api_settings.DATETIME_FORMAT, input_formats=None) |
  | **DateField**           | DateField(format=api_settings.DATE_FORMAT, input_formats=None) |
  | **TimeField**           | TimeField(format=api_settings.TIME_FORMAT, input_formats=None) |
  | **DurationField**       | DurationField()                                              |
  | **ChoiceField**         | ChoiceField(choices) choices与Django的用法相同               |
  | **MultipleChoiceField** | MultipleChoiceField(choices)                                 |
  | **FileField**           | FileField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL) |
  | **ImageField**          | ImageField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL) |
  | **ListField**           | ListField(child=, min_length=None, max_length=None)          |
  | **DictField**           | DictField(child=)                                            |

  选项参数：

  | 参数名称            | 作用             |
  | ------------------- | ---------------- |
  | **max_length**      | 最大长度         |
  | **min_lenght**      | 最小长度         |
  | **allow_blank**     | 是否允许为空     |
  | **trim_whitespace** | 是否截断空白字符 |
  | **max_value**       | 最小值           |
  | **min_value**       | 最大值           |

  通用参数：

  | 参数名称           | 说明                                          |
  | ------------------ | --------------------------------------------- |
  | **read_only**      | 表明该字段仅用于序列化输出，默认False         |
  | **write_only**     | 表明该字段仅用于反序列化输入，默认False       |
  | **required**       | 表明该字段在反序列化时必须输入，默认True      |
  | **default**        | 反序列化时使用的默认值                        |
  | **allow_null**     | 表明该字段是否允许传入None，默认False         |
  | **validators**     | 该字段使用的验证器                            |
  | **error_messages** | 包含错误编号与错误信息的字典                  |
  | **label**          | 用于HTML展示API页面时，显示的字段名称         |
  | **help_text**      | 用于HTML展示API页面时，显示的字段帮助提示信息 |

- **使用Serializer**

  > Serializer(instance=None, data=empty, **kwarg)
  >
  > 1）用于序列化时，将模型类对象传入**instance**参数
  >
  > 2）用于反序列化时，将要被反序列化的数据传入**data**参数
  >
  > 3）除了instance和data参数外，在构造Serializer对象时，还可通过**context**参数额外添加数据，如
  >
  > `serializer = AccountSerializer(account, context={'request': request})`
  >
  > **通过context参数附加的数据，可以通过Serializer对象的context属性获取。**

# 5. 序列化

- **序列化**

  ```python
  book = BookInfo.objects.get(id=2)
  serializer = BookInfoSerializer(book)
serializer.data  # 获取单个序列化数据
  
  serializer = BookInfoSerializer(book_qs, many=True)  # 获取QuerySet序列化数据
  serializer.data
  ```
  
- **关联对象嵌套序列化定义**

  ```python
  # PrimaryKeyRelatedField  此字段将被序列化为关联对象的主键
  # 参数: read_only=True ==> 该字段该字段将不能用作反序列化使用
  hbook = serializers.PrimaryKeyRelatedField(label='图书', read_only=True)
  # 参数: queryset ==> 该字段被用作反序列化时参数校验使用
  hbook = serializers.PrimaryKeyRelatedField(label='图书',
                                             queryset=BookInfo.objects.all())
  
  # 使用关联对象的序列化器
  hbook = BookInfoSerializer()
  
  # StringRelatedField 字段将被序列化为关联对象的字符串表示方式（即__str__方法的返回值）
  hbook = serializers.StringRelatedField(label='图书')
  ```

  > 关联对象有多个(如: 一对多时): 在以上三种方式中加参数`many=True`指明

# 6. 反序列化

- **验证**

  ```python
  data = {'bpub_date': 123}  # 反序列化数据
  serializer = BookInfoSerializer(data=data)  # 构造反序列化器
  serializer.is_valid()  # 验证
  serializer.errors  # 验证失败的错误信息
  serializer.validated_data  # 验证通过的数据
  ```

- **保存**

  ```python
  class BookInfoSerializer(serializers.Serializer):
      def create(self, validated_data):
          """新建"""
          return BookInfo.objects.create(**validated_data)
  
      def update(self, instance, validated_data):
          """更新，instance为要更新的对象实例"""
          instance.btitle = validated_data.get('btitle', instance.btitle)
          instance.bpub_date = validated_data.get('bpub_date', instance.bpub_date)
          instance.bread = validated_data.get('bread', instance.bread)
          instance.bcomment = validated_data.get('bcomment', instance.bcomment)
          instance.save()
          return instance
  ```

  ```python
  #　如果创建序列化器对象的时候，没有传递instance实例，则调用save()方法的时候，create()被调用，相反，如果传递了instance实例，则调用save()方法的时候，update()被调用。
  book = serializer.save()
  
  # 创建
  from db.serializers import BookInfoSerializer
  data = {'btitle': '封神演义'}
  serializer = BookInfoSerializer(data=data)
  serializer.is_valid()  # True
  serializer.save()  # <BookInfo: 封神演义>
  
  # 更新
  from db.models import BookInfo
  book = BookInfo.objects.get(id=2)
  data = {'btitle': '倚天剑'}
  serializer = BookInfoSerializer(book, data=data)
  serializer.is_valid()  # True
  serializer.save()  # <BookInfo: 倚天剑>
  book.btitle  # '倚天剑'
  
  # 部分更新  默认序列化器必须传递所有required的字段  partial允许部分字段更新
  serializer = CommentSerializer(comment, data={'content': u'foo bar'}, partial=True)
  ```

# 7. 视图

- **APIView**

  > 继承自Django  View  定义get(), post()等方法+序列化器+request,response实现
  >
  > ##### 支持定义的属性：
  >
  > - **authentication_classes** 列表或元祖，身份认证类
  > - **permissoin_classes** 列表或元祖，权限检查类
  > - **throttle_classes** 列表或元祖，流量控制类

  ```python
  from rest_framework.views import APIView
  from rest_framework.response import Response
  
  # url(r'^books/$', views.BookListView.as_view()),
  class BookListView(APIView):
      def get(self, request):
          books = BookInfo.objects.all()
          serializer = BookInfoSerializer(books, many=True)
          return Response(serializer.data)
  ```

- **GenericAPIView**

  > 继承自APIView, 增加数据库查询方法, 配合Mixin扩展类使用

  ```python
  """GenericAPIView提供的属性和方法"""
  
  serializer_class  # 指明视图使用的序列化器
  get_serializer_class(self)  # 返回序列化器类,默认返回serializer_class, 可重写
  get_serializer(self, args, *kwargs)  # 返回序列化器对象
  
  queryset  # 指明使用的数据查询集
  get_queryset(self)  # 返回视图查询集
  get_object(self)  # 返回详情视图所需的模型类数据对象
  ```

- **Mixin扩展类**

  - **ListModelMixin**

    ```python
    """ListModelMixin提供的方法"""
    
    list(request, *args, **kwargs)  # 快速实现列表视图，返回200状态码。会对数据进行过滤和分页。
    ```

    使用示例:

    ```python
    from rest_framework.mixins import ListModelMixin
    
    class BookListView(ListModelMixin, GenericAPIView):
        queryset = BookInfo.objects.all()
        serializer_class = BookInfoSerializer
    
        def get(self, request):
            return self.list(request)
    ```

  - **CreateModelMixin**

    ```python
    """CreateModelMixin提供的方法"""
    
    create(request, *args, **kwargs)  # 快速实现创建资源的视图，成功返回201,序列化器对前端发送的数据验证失败，返回400错误。
    ```

    示例 :

    ```python
    from rest_framework.mixins import CreateModelMixin
    
    class BookListView(CreateModelMixin, GenericAPIView):
        serializer_class = BookInfoSerializer
    
        def create(self, request):
            return self.create(request)
    ```

  - **RetrieveModelMixin**

    ```python
    """RetrieveModelMixin提供的方法"""
    
    retrieve(request, *args, **kwargs)  # 返回一个存在的数据对象。如果存在返回200否则返回404。
    ```

    示例:

    ```python
    class BookDetailView(RetrieveModelMixin, GenericAPIView):
        queryset = BookInfo.objects.all()
        serializer_class = BookInfoSerializer
    
        def get(self, request, pk):
            return self.retrieve(request)
    ```

  - **UpdateModelMixin**

    ```python
    """UpdateModelMixin提供的方法"""
    
    update(request, *args, **kwargs)  # 更新一个存在的数据对象
    partial_update(request, *args, **kwargs)  # 实现局部更新
    # 成功返回200，序列化器校验数据失败时，返回400错误。
    ```

    示例:

    ```python
    class BookDetailView(UpdateModelMixin, UpdateModelMixin):
        queryset = BookInfo.objects.all()
        serializer_class = BookInfoSerializer
    
        def get(self, request, pk):
            return self.update(request)
    ```

  - **DestroyModelMixin**

    ```python
    """DestroyModelMixin提供的方法"""
    
    destroy(request, *args, **kwargs)  # 删除一个存在的数据对象。成功返回204，不存在返回404。
    ```

    示例:

    ```python
    class BookDetailView(DestroyModelMixin, UpdateModelMixin):
        queryset = BookInfo.objects.all()
        serializer_class = BookInfoSerializer
    
        def get(self, request, pk):
            return self.destroy(request)
    ```

- **继承自Mixin与GenericAPIView的子类视图**

  | 子类                         | 继承的父类                                                   | 提供的方法              |
  | ---------------------------- | ------------------------------------------------------------ | ----------------------- |
  | CreateAPIView                | GenericAPIView、CreateModelMixin                             | post                    |
  | ListAPIView                  | GenericAPIView、ListModelMixin                               | get                     |
  | RetrieveAPIView              | GenericAPIView、RetrieveModelMixin                           | get                     |
  | DestoryAPIView               | GenericAPIView、DestoryModelMixin                            | delete                  |
  | UpdateAPIView                | GenericAPIView、UpdateModelMixin                             | put 和 patch            |
  | RetrieveUpdateAPIView        | GenericAPIView、RetrieveModelMixin、UpdateModelMixin         | get、put、patch         |
  | RetrieveUpdateDestoryAPIView | GenericAPIView、RetrieveModelMixin、UpdateModelMixin、DestoryModelMixin | get、put、patch、delete |

- **ViewSet**

  `mixin`+`GenericViewSet`

  ```python
  from rest_framework import mixins
  from rest_framework.viewsets import GenericViewSet
  from rest_framework.decorators import action
  
  class BookInfoViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin, GenericViewSet):
      queryset = BookInfo.objects.all()
      serializer_class = BookInfoSerializer
      
      
  urlpatterns = [
      url(r'^books/$', views.BookInfoViewSet.as_view({'get': 'list'})),
      url(r'^books/(?P<pk>\d+)/$', views.BookInfoViewSet.as_view({'get': 'retrieve'})),
  ]
  ```

  `ModelViewSet`

  > 继承自`GenericViewSet`，包括ListModelMixin、RetrieveModelMixin、CreateModelMixin、UpdateModelMixin、DestoryModelMixin。

  `ReadOnlyModelViewSet`

  > 继承自`GenericViewSet`，同时包括了ListModelMixin、RetrieveModelMixin。

  `自定义action`

  ```python
  from rest_framework import mixins
  from rest_framework.viewsets import GenericViewSet
  from rest_framework.decorators import action
  
  class BookInfoViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin, GenericViewSet):
      queryset = BookInfo.objects.all()
      serializer_class = BookInfoSerializer
  
      def latest(self, request):
          """
          返回最新的图书信息
          """
          book = BookInfo.objects.latest('id')
          serializer = self.get_serializer(book)
          return Response(serializer.data)
  
      def read(self, request, pk):
          """
          修改图书的阅读量数据
          """
          book = self.get_object()
          book.bread = request.data.get('read')
          book.save()
          serializer = self.get_serializer(book)
          return Response(serializer.data)
      
      
  urlpatterns = [
      url(r'^books/$', views.BookInfoViewSet.as_view({'get': 'list'})),
      url(r'^books/latest/$', views.BookInfoViewSet.as_view({'get': 'latest'})),
      url(r'^books/(?P<pk>\d+)/$', views.BookInfoViewSet.as_view({'get': 'retrieve'})),
      url(r'^books/(?P<pk>\d+)/read/$', views.BookInfoViewSet.as_view({'put': 'read'})),
  ]
  ```

  `根据action属性,判断action`

  ```python
  def get_serializer_class(self):
      if self.action == 'create':
          return OrderCommitSerializer
      else:
          return OrderDataSerializer
  ```

# 8. 路由

- **SimpleRouter**

  > 对于视图集ViewSet，我们除了可以自己手动指明请求方式与动作action之间的对应关系外，还可以使用Routers来帮助我们快速实现路由信息。

  `urls.py`

  ```python
  from rest_framework import routers
  
  router = routers.SimpleRouter()
  router.register(r'books', BookInfoViewSet, base_name='book')
  
  urlpatterns += router.urls
  # 或
  url(r'^', include(router.urls))
  
  '''形成路由如下
  ^books/$    name: book-list
  ^books/{pk}/$   name: book-detail
  '''
  ```

  



# 8. 获取请求参数

DRF对request对象进行了再次封装

- **路径参数**

  ```python
  # r'image_codes/(?P<image_codes_id>.*)/$'
  
  class ImageCodes(APIView):
      def get(self, request, image_codes_id):
          pass
  ```

- **request.data**

  > 同django中的request.POST

- **request.query_params**

  > 同django中的request.GET

  

# 9. 构造响应

- **Response**

  - 修改响应数据格式

    `settings.py`

    ```python
    REST_FRAMEWORK = {
        'DEFAULT_RENDERER_CLASSES': (  # 默认响应渲染类
            'rest_framework.renderers.JSONRenderer',  # json渲染器
            'rest_framework.renderers.BrowsableAPIRenderer',  # 浏览API渲染器
        )
    }
    ```

  - 构造响应

    ```python
    rest_framework.response.Response
    Response(data, status=None, template_name=None, headers=None, content_type=None)
    ```

    

  













































