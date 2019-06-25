# HTML&CSS

### 开发工具的使用VScode

- 配置

- 1. 文件 首选项 设置 用户设置 在字典中添加

     “editor.renderLineHighlight”: “none”,

     “editor.matchBrackets”: false,

     “editor.hover”: false

  2. 中文界面

     Ctrl + Shift + p  à config display language à zh-CN

     添加chinese simple扩展, 重启生效

  3. 安装插件 openchrome

  4. 设置自动保存  文件 à 自动保存

### 前端开发概述

1. 单机软件  界面: Tk, pyQt, wxPython库
2. C/S架构:  界面: Tk, pyQt, wxPython
3. B/S架构:  界面: 网页

前端开发: 为B/S架构的软件提供界面解决方案

## 一、HTML

### 1. HTML基本概念

超文本标记语言

- 基本结构

  ```html
  <!DOCTYPE html>  <!--文档申明-->
  <html>  <!--html文档开始标签-->
      <head> <!--html设置标签，html编码设置，以及标题和外链资源-->
          <meta charset="UTF-8" > <!--设置网页编码-->
          <title>index</title> <!--设置网页标题 -->
      </head>
      
      <body> <!--html主体标签，网页显示的内容写在这个标签中-->
          index page
      </body>
  </html>
  ```

  > vscode快速生成结构 ！+ enter

- html注释
  ​	`<!-- 这是一段注释  -->`

### 2. HTML标签

#### 1) 常用的块元素标签

- 标题标签

  ```html
  <h1>、<h2>、<h3>、<h4>、<h5>、<h6>标签
  ```

- 段落标签

  > 含有默认的外边框

  ```html
  <P>段落</P>
  ```

- 换行标签

  ```html
  <br>
  ```

- 块元素div

  ```html
  <div>  
  ```

#### 2) 内联元素标签

- 超链接标签

  ```html
  <a href="#" target="_blank" />
  ```

  > target="_self" 缺省值，新页面替换原来的页面，在原来位置打开target="_blank" 新页面会在新开的一个浏览器窗口打开

- 图像标签

  ```html
  <img src="images/pic.png" alt="图片" />
  ```

  > src为图片地址， alt为加载图片失败或鼠标悬停时显示的内容
  >
  > 图片超链接可以组合 a 和 img

- 字符实体

  ```html
  大于>: &gt  小于: &lt  空格: &nbsp
  ```

- 含样式的标签

  ```html
  <i>, <b>, <strong>
  ```

- 通用内联元素标签

  ```html
  <span>
  ```

  > 用于设置样式

#### 3) 列表标签

- 标签写法

  ```html
  有序
  <ol>
      <li></li>
  </ol>
  无序
  <ul>
      <li></li>
  </ul>
  ```

  1. 列表创建的快捷键： ul>(li>a{列表文字})*8
  2. 多行操作：点击一行alt不放点击其他行，进行同时编辑
  3. 列表标签样式：list-style:none  去掉小圆点

#### 4) 表格标签

- 表格示例

  ```html
  <table>
      属性
      border属性 定义表格的边框，设置值是数值
      cellpadding属性 定义单元格内容与边框的距离，设置值是数值
      cellspacing属性 定义单元格与单元格之间的距离，设置值是数值
      align属性 设置整体表格相对于浏览器窗口的水平对齐方式,设置值有：left | center | right
      <tr>
          定义表格中的一行
          <td>&<th>
          定义一行中的一个单元格，td代表普通单元格，th表示表头单元格
          属性
          align 设置单元格中内容的水平对齐方式,设置值有：left | center | right
          valign 设置单元格中内容的垂直对齐方式 top | middle | bottom
          colspan 设置单元格水平合并，设置值是数值
          rowspan 设置单元格垂直合并，设置值是数值
  ```

#### 5) 表单标签

- 示例

  ```html
  <!--表单标签-->
  <form action="#" method="get" >
      <input type="text">
      标签属性
      form
      action属性 定义表单数据提交地址
      method属性 定义表单提交的方式，一般有“get”方式和“post”方式
      label
      为表单元素定义文字标注
      input
      type="text" 定义单行文本输入框
      type="password" 定义密码输入框
      type="radio" 定义单选框
      type="checkbox" 定义复选框
      type="file" 定义上传文件
      type="submit" 定义提交按钮
      type="reset" 定义重置按钮
      type="button" 定义一个普通按钮
      type="image" 定义图片作为提交按钮，用src属性定义图片地址
      type="hidden" 定义一个隐藏的表单域，用来存储值
      value属性 定义表单元素的值
      name属性 定义表单元素的名称，此名称是提交数据时的键名
  </form>
  <textarea>
      定义多行文本输入框
  <select>
      定义下拉表单元素
      <option>与<select>标签配合，定义下拉表单元素中的选项
  ```

### 3. HTML元素分类

- 块元素
  `div、p、ul、li、h1~h6、dl、dt、dd`

  > 盒子占据一行、即使设置了宽度 
  >
  > 如果没有设置宽度，默认的宽度为父级宽度100%

- 内联元素
  `a、span、em、b、strong、i`

  > 盒子并在一行
  >
  > 支持部分样式（不支持宽、高、margin上下、padding上下）宽高由内容决定
  >
  > 代码换行，盒子之间会产生间距

- 块和内联元素的相互转换
  `display属性`
  display属性是用来设置元素的类型及隐藏的，常用的属性有：
  1、none 元素隐藏且不占位置
  2、block 元素以块元素显示
  3、inline 元素以内联元素显示
  4、inline-block 元素以内联块元素显示

## 二、CSS

### 4. CSS基础知识

- 语法

  ```css
  选择器 { 属性:值; 属性:值; 属性:值;}
  div{ 
      width:100px; 
      height:100px; 
      color:red 
  }
  ```

- 页面引用

  ```html
  <!--外联式-->
  <link rel="stylesheet" type="text/css" href="css/main.css">
  <!--嵌入式-->
  <style type="text/css">
      div{ width:100px; height:100px; color:red }
      ......
  </style>
  <!--内联式-->
  <div style="width:100px; height:100px; color:red ">......</div>
  ```

- CSS文本及颜色设置

  ```css
  /*文本*/
  color:red;
  font-size:12px;
  font-family:'微软雅黑';
  font-style:'normal'; 设置不倾斜，font-style:'italic';设置文字倾斜
  font-weight:bold; 设置加粗 font-weight:normal 设置不加粗
  line-height:24px;
  text-decoration:none; 将文字下划线去掉
  text-indent:24px; 设置文字首行缩进24px
  text-align:center 设置文字水平居中
  /*合并*/
  font:normal 12px/36px '微软雅黑';
  /*颜色表示*/
  red & rgb(255,0,0) & #ff0000
  ```

### 5. CSS选择器

```html
<!--标签选择器-->
div{color:red}   
<!--id选择器-->
#box{color:red} 
<div id="box">....</div> 
<!--类选择器-->
.red{color:red}
.big{font-size:20px}

<div class="red">....</div>
<h1 class="red big">....</h1>
<!--层级选择器-->
.box span{color:red}

<div class="box">
    <span>....</span>
</div>
<!--组选择器-->
.box1,.box2{width:100px;height:100px}

<div class="box1">....</div>
<div class="box2">....</div>

```

### 6. CSS盒子模型

> 盒子的真实尺寸: content+pading+border

- 设置content宽高

  ```css
  width:200px; 
  height:200px; 
  
  ```

- 设置边框border

  ```css
  border-top-color:red;    /* 设置顶部边框颜色为红色 */  
  border-top-width:10px;   /* 设置顶部边框粗细为10px */   
  /* 设置顶部边框的线性为实线，常用的有：solid(实线) dashed(虚线) dotted(点线); */
  border-top-style:solid;  
  /*简写*/ 
  border-top:10px solid red;
  /*四个边如果设置一样*/
  border:10px solid red;
  
  ```

- 设置内间距padding

  ```css
  padding-top：20px;     /*设置顶部内间距20px */ 
  padding-left:30px;     /* 设置左边内间距30px */ 
  padding-right:40px;    /* 设置右边内间距40px */ 
  padding-bottom:50px;   /* 设置底部内间距50px */
  /*简写:*/
  padding：20px 40px 50px 30px; /* 四个值按照顺时针方向，分别设置的是 上 右 下 左   */
  padding：20px 40px 50px; /* 设置顶部内边距为20px，左右内边距为40px，底部内边距为50px */ 
  padding：20px 40px; /* 设置上下内边距为20px，左右内边距为40px*/ 
  padding：20px; /* 设置四边内边距为20px */
  
  ```

- 设置外间距margin

  同pading

- 盒子模型使用技巧

  - margin相关技巧​			
    1、设置元素水平居中： margin:x auto;
    2、margin负值让元素位移及边框合并
  - css元素溢出
    ​			![002](/Pictures/002.png)
    overflow的设置项： 
    1、visible 默认值。内容不会被修剪，会呈现在元素框之外。
    2、hidden 内容会被修剪，并且其余内容是不可见的，此属性还有清除浮动、清除margin-top塌陷的功能。
    3、scroll 内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。
    4、auto 如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。
    5、inherit 规定应该从父元素继承 overflow 属性的值。

- background属性

  > 负责给盒子设置背景图片和背景颜色 

  background-color 设置背景颜色
  background-image 设置背景图片地址
  background-repeat 设置背景图片如何重复平铺
  background-position 设置背景图片的位置
  background-attachment 设置背景图片是固定还是随着页面滚动条滚动

### 7. 浮动

- 浮动特性
  1、浮动元素有左浮动(float:left)和右浮动(float:right)两种

  2、浮动的元素会向左或向右浮动，碰到父元素边界、其他元素才停下来

  3、相邻浮动的块元素可以并在一行，超出父级宽度就换行

  4、浮动让行内元素或块元素自动转化为行内块元素(此时不会有行内块元素间隙问题)

  5、浮动元素后面没有浮动的元素会占据浮动元素的位置，没有浮动的元素内的文字会避开浮动的元素，形成文字饶图的效果

  6、父元素如果没有设置尺寸(一般是高度不设置)，父元素内整体浮动的元素无法撑开父元素，父元素需要清除浮动

  7、浮动元素之间没有垂直margin的合并

- 清除浮动
  `{ clear:both;}`