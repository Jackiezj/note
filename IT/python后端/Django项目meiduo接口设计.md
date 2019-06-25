# 接口设计

- **域名**

  - 前端: www.meiduo.site

  - 后端: api.meiduo.site

- **域名本地化访问**

  ```bash
  sudo vim /etc/hosts
  	127.0.0.1   api.meiduo.site
  	127.0.0.1   www.meiduo.site
  ```



# 图片验证码接口设计

- **访问方式**

  ```HTTP
  GET /verifications/image_codes/(?P<image_codes_id>).(64)/
  ```

- **请求参数**: 路径参数

  | 参数           | 类型 | 是否必须 | 说明   |
  | -------------- | ---- | -------- | ------ |
  | image_codes_id | str  | 是       | 图片id |

- **返回数据**: 

  | 返回值 | 类型      | 是否必须 | 说明         |
  | ------ | --------- | -------- | ------------ |
  | image  | image/png | 是       | 图片格式数据 |

- **视图原型**

  ```python
  # url(r'^image_codes/(?P<image_codes_id>).(64)/$', views.ImageCodes.as_view()),
  class ImageCodes(APIView):
      def get(self, request, image_codes_id):
          """
          # 获取图片验证码
          :param request:请求对象
          :param image_codes_id:图片验证码id
          :return image:图片验证码
          """
          pass
  ```




# 短信验证码接口设计

- **访问方式**

  ```Http
  GET /verifications/sms_code/(?P<mobile>1[3-9]\d{9})/
  ```

- **请求参数**: 路径参数与查询字符串参数d

  | 参数          | 类型 | 是否必须 | 说明                             |
  | ------------- | ---- | -------- | -------------------------------- |
  | mobile        | str  | 是       | url路径参数  手机号              |
  | image_code    | str  | 是       | 查询字符串参数  输入的验证码内容 |
  | image_code_id | str  | 是       | 查询字符串参数  生成的验证码id   |

- **返回数据**: JSON

  | 返回值  | 类型 | 是否必须 | 说明         |
  | ------- | ---- | -------- | ------------ |
  | message | str  | 否       | OK, 发送成功 |

- **视图原型**

  ```python
  # url('^sms_codes/(?P<mobile>1[3-9]\d{9})/$', views.SMSCodeView.as_view()),
  class SMSCodes(APIView):
      def get(self, request):
          """
          # 生成短信验证码并发送到手机
          :param request:请求对象
          :param mobile:用户手机号
          :param image_code:输入验证码内容
          :param image_code_id:验证码id
        :return message:是否发送成功
          """
          pass
  ```
  
  





# 接口设计

- **访问方式**

  ```bash
  
  ```

- **请求参数**

  | 参数 | 类型 | 是否必须 | 说明 |
  | ---- | ---- | -------- | ---- |
  |      |      |          |      |

- **返回数据**

  | 返回值 | 类型 | 是否必须 | 说明 |
  | ------ | ---- | -------- | ---- |
  |        |      |          |      |

- **视图原型**

  ```python
  # url(r'^$', views.xxx.as_view()),
  class Demo(APIView):
      def get(self, request):
          """
          # 
          :param request:请求对象
          :return :
          """
          pass
  ```

  