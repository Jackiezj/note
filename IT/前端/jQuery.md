# jquery介绍

### jQuery介绍

> jQuery是目前使用最广泛的javascript函数库。
>
> jquery的口号和愿望 Write Less, Do More（写得少，做得多）

jquery是一个函数库，一个js文件，页面用script标签引入这个js文件就可以使用。

```js
<script type="text/javascript" src="js/jquery-1.12.2.js"></script>
```

<http://jquery.com/> 官方网站
<https://code.jquery.com/> 版本下载

### 1. jquery文档加载完再执行

> 将获取元素的语句写到页面头部，会因为元素还没有加载而出错，jquery提供了ready方法解决这个问题，它的速度比原生的 window.onload 更快。

```js
<script type="text/javascript">
    $(document).ready(function(){
         ......
    });
</script>
```

简写为：

```js
<script type="text/javascript">
    $(function(){
         ......
    });
</script>
```

### 2. jquery选择器

> **jquery用法思想一** 
> ​	选择某个网页元素，然后对它进行某种操作

**jquery选择器** 

> jquery选择器可以快速地选择元素，选择规则和css样式相同，使用length属性判断是否选择成功。

```js
$('#myId') //选择id为myId的网页元素
$('.myClass') // 选择class为myClass的元素
$('li') //选择所有的li元素
$('#ul1 li span') //选择id为为ul1元素下的所有li下的span元素
$('input[name=first]') // 选择name属性等于first的input元素
```

**对选择集进行过滤**

```js
$('div').has('p'); // 选择包含p元素的div元素
$('div').not('.myClass'); //选择class不等于myClass的div元素
$('div').eq(5); //选择第6个div元素
```

**选择集转移**

```js
$('#box').prev(); //选择id是box的元素前面紧挨的同辈元素
$('#box').prevAll(); //选择id是box的元素之前所有的同辈元素
$('#box').next(); //选择id是box的元素后面紧挨的同辈元素
$('#box').nextAll(); //选择id是box的元素后面所有的同辈元素
$('#box').parent(); //选择id是box的元素的父元素
$('#box').children(); //选择id是box的元素的所有子元素
$('#box').siblings(); //选择id是box的元素的同级元素
$('#box').find('.myClass'); //选择id是box的元素内的class等于myClass的元素
```

**判断是否选择到了元素** 

> jquery有容错机制，即使没有找到元素，也不会出错，可以用length属性来判断是否找到了元素,length等于0，就是没选择到元素，length大于0，就是选择到了元素。

```js
var $div1 = $('#div1');
var $div2 = $('#div2');
alert($div1.length); // 弹出1
alert($div2.length); // 弹出0
......
<div id="div1">这是一个div</div>
```

### 3. jquery样式操作

> **jquery用法思想二** 
> ​	同一个函数完成取值和赋值

**操作行间样式**

```js
// 获取div的样式
$("div").css("width");
$("div").css("color");

//设置div的样式
$("div").css("width","30px");
$("div").css("height","30px");
$("div").css({fontSize:"30px",color:"red"});
```

> **特别注意** 
> ​	选择器获取的多个元素，获取信息获取的是第一个，比如：$("div").css("width")，获取的是第一个div的width。

**操作样式类名**

```js
$("#div1").addClass("divClass2") //为id为div1的对象追加样式divClass2
$("#div1").removeClass("divClass")  //移除id为div1的对象的class名为divClass的样式
$("#div1").removeClass("divClass divClass2") //移除多个样式
$("#div1").toggleClass("anotherClass") //重复切换anotherClass样式
```

### 4. 绑定click事件

```js
$('#btn1').click(function(){
    // 内部的this指的是原生对象
    // 使用jquery对象用 $(this)
})
```

**获取元素的索引值** 

> 有时候需要获得匹配元素相对于其同胞元素的索引位置，此时可以用index()方法获取

```js
var $li = $('.list li').eq(1);
alert($li.index()); // 弹出1
......
<ul class="list">
    <li>1</li>
    <li>2</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
</ul>
```

### 5. jquery动画

> 通过animate方法可以设置元素某属性值上的动画，可以设置一个或多个属性值，动画执行完成后会执行一个函数。

```js
/*
    animate参数：
    参数一：要改变的样式属性值，写成字典的形式
    参数二：动画持续的时间，单位为毫秒，一般不写单位
    参数三：动画曲线，默认为‘swing’，缓冲运动，还可以设置为‘linear’，匀速运动
    参数四：动画回调函数，动画完成后执行的匿名函数

*/

$('#div1').animate({
    width:300,
    height:300
},1000,'swing',function(){
    alert('done!');
});
```

### 6. jquery特殊效果

```js
fadeIn() 淡入

    $btn.click(function(){

        $('#div1').fadeIn(1000,'swing',function(){
            alert('done!');
        });

    });

fadeOut() 淡出
fadeToggle() 切换淡入淡出
hide() 隐藏元素
show() 显示元素
toggle() 切换元素的可见状态
slideDown() 向下展开
slideUp() 向上卷起
slideToggle() 依次展开或卷起某个元素
```

### 7. jquery链式调用

> jquery对象的方法会在执行完后返回这个jquery对象，所有jquery对象的方法可以连起来写：

```js
$('#div1') // id为div1的元素
.children('ul') //该元素下面的ul子元素
.slideDown('fast') //高度从零变到实际高度来显示ul元素
.parent()  //跳到ul的父元素，也就是id为div1的元素
.siblings()  //跳到div1元素平级的所有兄弟元素
.children('ul') //这些兄弟元素中的ul子元素
.slideUp('fast');  //高度实际高度变换到零来隐藏ul元素
```

### 8. jquery属性操作

1、**html() 取出或设置html内容**

```js
// 取出html内容

var $htm = $('#div1').html();

// 设置html内容

$('#div1').html('<span>添加文字</span>');
```

2、**prop() 取出或设置某个属性的值**

```js
// 取出图片的地址

var $src = $('#img1').prop('src');

// 设置图片的地址和alt属性

$('#img1').prop({src: "test.jpg", alt: "Test Image" });
```

### 9. jquery循环

> 对jquery选择的对象集合分别进行操作，需要用到jquery循环操作，此时可以用对象上的each方法：

```js
$(function(){
    $('.list li').each(function(i){
        $(this).html(i);
    })
})
......
<ul class="list">
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>

```

### 10. jquery事件

**事件函数列表：**

```js
blur() 元素失去焦点
focus() 元素获得焦点
click() 鼠标单击
mouseover() 鼠标进入（进入子元素也触发）
mouseout() 鼠标离开（离开子元素也触发）
mouseenter() 鼠标进入（进入子元素不触发）
mouseleave() 鼠标离开（离开子元素不触发）
hover() 同时为mouseenter和mouseleave事件指定处理函数
ready() DOM加载完成
submit() 用户递交表单

```

### 11. 事件冒泡

> **什么是事件冒泡** 
> ​	在一个对象上触发某类事件（比如单击onclick事件），如果此对象定义了此事件的处理程序，那么此事件就会调用这个处理程序，如果没有定义此事件处理程序或者事件返回true，那么这个事件会向这个对象的父级对象传播，从里到外，直至它被处理（父级对象所有同类事件都将被激活），或者它到达了对象层次的最顶层，即document对象（有些浏览器是window）。

**事件冒泡的作用** 
事件冒泡允许多个操作被集中处理（把事件处理器添加到一个父级元素上，避免把事件处理器添加到多个子级元素上），它还可以让你在对象层的不同级别捕获事件。

**阻止事件冒泡** 
事件冒泡机制有时候是不需要的，需要阻止掉，通过 event.stopPropagation() 来阻止

```js
$(function(){
    var $box1 = $('.father');
    var $box2 = $('.son');
    var $box3 = $('.grandson');
    $box1.click(function() {
        alert('father');
    });
    $box2.click(function() {
        alert('son');
    });
    $box3.click(function(event) {
        alert('grandson');
        event.stopPropagation();

    });
    $(document).click(function(event) {
        alert('grandfather');
    });
})

......

<div class="father">
    <div class="son">
        <div class="grandson"></div>
    </div>
</div>

```

**阻止默认行为** 
阻止表单提交

```js
$('#form1').submit(function(event){
    event.preventDefault();
})

```

**合并阻止操作** 
实际开发中，一般把阻止冒泡和阻止默认行为合并起来写，合并写法可以用

```js
// event.stopPropagation();
// event.preventDefault();

// 合并写法：
return false;

```

### 12. 事件委托

> 事件委托就是利用冒泡的原理，把事件加到父级上，通过判断事件来源的子集，执行相应的操作，事件委托首先可以极大减少事件绑定次数，提高性能；其次可以让新加入的子元素也可以拥有相同的操作。

**一般绑定事件的写法**

```js
$(function(){
    $ali = $('#list li');
    $ali.click(function() {
        $(this).css({background:'red'});
    });
})
...
<ul id="list">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>

```

**事件委托的写法**

```js
$(function(){
    $list = $('#list');
    $list.delegate('li', 'click', function() {
        $(this).css({background:'red'});
    });
})
...
<ul id="list">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>

```

### 13. jquery元素节点操作

> 元素节点操作指的是改变html的标签结构，它有两种情况：
> ​	1、移动现有标签的位置
> ​	2、将新创建的标签插入到现有的标签中

**创建新标签**

```js
var $div = $('<div>'); //创建一个空的div
var $div2 = $('<div>这是一个div元素</div>');

```

**移动或者插入标签的方法** 
1、append()和appendTo()：在现存元素的内部，从后面放入元素

```js
var $span = $('<span>这是一个span元素</span>');
$('#div1').append($span);
......
<div id="div1"></div>

```

2、prepend()和prependTo()：在现存元素的内部，从前面放入元素

3、after()和insertAfter()：在现存元素的外部，从后面放入元素

4、before()和insertBefore()：在现存元素的外部，从前面放入元素

**删除标签**

```js
$('#div1').remove();

```

### 14. 表单验证

```js
var sTr01 = '123456asdf';
var re01 = /\d+/;
//匹配纯数字字符串
var re02 = /^\d+$/;
alert(re01.test(sTr01)); //弹出true
alert(re02.test(sTr01)); //弹出false

```

**常用正则规则**

```js
//用户名验证：(数字字母或下划线6到20位)
var reUser = /^\w{6,20}$/;

//邮箱验证：        
var reMail = /^[a-z0-9][\w\.\-]*@[a-z0-9\-]+(\.[a-z]{2,5}){1,2}$/i;

//密码验证：
var rePass = /^[\w!@#$%^&*]{6,20}$/;

//手机号码验证：
var rePhone = /^1[34578]\d{9}$/;

```

