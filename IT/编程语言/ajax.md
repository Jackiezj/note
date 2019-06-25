# json&ajax

### 1. json

> json是 JavaScript Object Notation :  javascript对象表示法，这里说的json指的是类似于javascript对象的一种数据格式，目前这种数据格式比较流行，逐渐替换掉了传统的xml数据格式。

- javascript自定义对象：

```js
var oMan = {
    name:'tom',
    age:16,
    talk:function(s){
        alert('我会说'+s);
    }
}
```

- json格式的数据：

```js
{
    "name":"tom",
    "age":18
}
```

> 与json对象不同的是，json数据格式的属性名称和字符串值需要用**双引号**引起来
>
> 用单引号或者不用引号会导致读取数据错误。

- json的另外一个数据格式是数组，和javascript中的数组字面量相同。

```
["tom",18,"programmer"]
```

### 2. ajax

> ajax技术的目的是让javascript发送http请求，与后台通信，获取数据和信息。
>
> ajax技术的原理是实例化xmlhttp对象，使用此对象与后台通信。ajax通信的过程不会影响后续javascript的执行，从而实现异步。

**同步和异步** 

> 现实生活中，同步指的是同时做几件事情，异步指的是做完一件事后再做另外一件事，程序中的同步和异步是把现实生活中的概念对调，也就是程序中的异步指的是现实生活中的同步，程序中的同步指的是现实生活中的异步。

**局部刷新和无刷新** 

> ajax可以实现局部刷新，也叫做无刷新，无刷新指的是整个页面不刷新，只是局部刷新，ajax可以自己发送http请求，不用通过浏览器的地址栏，所以页面整体不会刷新，ajax获取到后台数据，更新页面显示数据的部分，就做到了页面局部刷新。

**同源策略** 

> ajax请求的页面或资源只能是同一个域下面的资源，不能是其他域的资源，这是在设计ajax时基于安全的考虑。特征报错提示：

```
XMLHttpRequest cannot load https://www.baidu.com/. No  
'Access-Control-Allow-Origin' header is present on the requested resource.  
Origin 'null' is therefore not allowed access.
```

**$.ajax使用方法** 

- 常用参数：

  | url      | 请求地址                                                   |
  | -------- | ---------------------------------------------------------- |
  | type     | 请求方式，默认是'GET'，常用的还有'POST'                    |
  | dataType | 设置返回的数据格式，常用的是'json'格式，也可以设置为'html' |
  | data     | 设置发送给服务器的数据                                     |
  | success  | 设置请求成功后的回调函数                                   |
  | error    | 设置请求失败后的回调函数                                   |
  | async    | 设置是否异步，默认值是'true'，表示异步                     |
  |          |                                                            |

- 以前的写法：

  ```js
  $.ajax({
      url: 'js/data.json',
      type: 'GET',
      dataType: 'json',
      data:{'aa':1}
      success:function(data){
          alert(data.name);
      },
      error:function(){
          alert('服务器超时，请重试！');
      }
  });
  ```

- 新的写法(推荐)：

  ```js
  $.ajax({
      url: 'js/data.json',
      type: 'GET',
      dataType: 'json',
      data:{'aa':1}
  })
  .done(function(data) {
      alert(data.name);
  })
  .fail(function() {
      alert('服务器超时，请重试！');
  });
  
  // data.json里面的数据： {"name":"tom","age":18}
  ```

### 3. **jsonp** 

> ajax只能请求同一个域下的数据或资源，跨域请求数据要用到jsonp，它的原理主要是利用了<script>标签可以跨域链接资源的特性。jsonp和ajax原理完全不一样，不过jquery将它们封装成同一个函数。

```js
$.ajax({
    url:'js/data.js',
    type:'get',
    dataType:'jsonp',
    jsonpCallback:'fnBack'
})
.done(function(data){
    alert(data.name);
})
.fail(function() {
    alert('服务器超时，请重试！');
});

// data.js里面的数据： fnBack({"name":"tom","age":18});
```

