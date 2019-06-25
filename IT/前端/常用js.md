### 生成UUID

- **生成UUID**

  ```js
  // 生成uuid
  function generate_uuid() {
      var d = new Date().getTime();
      if (window.performance && typeof window.performance.now === "function") {
          d += performance.now(); //use high-precision timer if available
      }
      var uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
          var r = (d + Math.random() * 16) % 16 | 0;
          d = Math.floor(d / 16);
          return (c == 'x' ? r : (r & 0x3 | 0x8)).toString(16);
      });
      return uuid;
  }
  ```

### ajax请求(json数据传输)

- post请求

  ```js
  var params = {
      "mobile": mobile,
      "image_code":imageCode,
  };
  $.ajax({
      url: "/passport/sms_code",
      type: "post",
      data: JSON.stringify(params),
      headers: {
          "X-CSRFToken": getCookie('csrf_token')
      },
      contentType: "application/json",
      success: function (response) {
          if (response.errno == "0") {
              // 代表发送成功
          }else {
      		// 代表发送失败
  		}
      }
  })
  ```

- get请求

  ```js
  var params = {
      "mobile": mobile,
      "image_code":imageCode,
  };
  $.get("/news_list", params, function (resp) {
      if (resp.errno == "0") {
          // 代表请求成功
      }else {
          // 请求失败
          alert(resp.errmsg)
      }
  })
  ```

  

- ajax处理CSRF

  ```js
  headers: {
      "X-CSRFToken": getCookie('csrf_token')
  }
  // 表单中
  {{ form.csrf_token() }}
  ```

- ajax处理json请求体

  ```js
  var params = {
      "key": "value"
  }
  $.ajax({
      data: JSON.stringify(params),
  	contentType: "application/json",
  })
  ```

### 倒计时js

- 代码

  ```js
  var num = 60
  var t = setInterval(function () {
      if (num == 1) {
          // 代表倒计时结束清除倒计时
          clearInterval(t)
          // 设置显示内容
          $(".get_code").html("点击获取验证码")
          // 添加点击事件
          $(".get_code").attr("onclick", "sendSMSCode();");
      }else {
          num -= 1
          // 设置 a 标签显示的内容
          $(".get_code").html(num + "秒")
      }
  }, 1000)
  ```

### 重新加载页面

- 代码

  ```js
  location.reload()
  ```

  









