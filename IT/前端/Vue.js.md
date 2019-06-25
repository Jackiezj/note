## 一、Vue安装简介

### 1.1 安装

- [开发版本](http://vuejs.org/js/vue.js)：包含完整的警告和调试模式 
- [生产版本](http://vuejs.org/js/vue.min.js)：删除了警告，23.55kb min+gzip
- Vue.js文档: https://cn.vuejs.org/v2/guide/
- vscode 插件安装: Vetur、ivue、Vue 2 Snippets

### 1.2 快速开始

- Vue的js写法

  ```js
  var vm = new Vue({
  	el: '#app',
  	data: {
  		// 保存数据
  		d1: '',
  	},
      // 加载js即执行
  	mounted: function(){
  		
          this.demo_fun();
  	},
      // 函数定义
      methods: {      
          demo_fun: function () {
              // 函数与数据的使用方式
              this.d1 = this.demo_fun2()
          },
          
          demo_fun2: function () {
              return 'd'
          }
      },
      // 计算属性  html内容为{{reversedMessage}}时,将执行computed中对应函数
  	computed: {
  		reversedMessage: function () {
  			return this.message.split('').reverse().join("")
  		},
  	},
  	// 侦听属性  当data中的数据message变化时,将执行watch中其对应函数
  	watch: {
  		message: function (newval, oldval) {
  			console.log(newval + "|" + oldval);  // 在浏览器的检查 --> console中查看
  		},
  	},
      // 定义局部过滤器  {{ price | RMB }}
  	filters: {
  		RMB: function (value) {
  			if (value == '') {
  				return;
  			}
  			return '$' + value
  		},
  	},
  });
  //定义全局过滤器  {{ price | Yuan}}
  Vue.filter('Yuan', function (value) {
      if (value == '') {
          return;
      }
      return '$' + value + '元'
  });
  ```

- **Vue页面数据展示**

  ```html
  var vm = new Vue({
  	el: '#app',
  	data: {
  		num: 3,
  		username: '',
  		show: true,
  
  		items: ["java", "python", "php"],
          oPerson: {
          	"name": "zs",
          	'age': '16',
          },
  
  		a_href: "https://www.baidu.com",
  	},
  	methods: {
  		demo_fun: function(){}
  axios
  
  Vue没有Ajax, 使用axios.js库实现https://github.com/axios/axios/releases
  
  ￼
  axios({
      method: 'post',
      url: '/user/12345',
      data: {
          firstName: 'S',
          lastName: 'Z'
      }
  }).then(response=>{
      this.name = this.name+'zs'
  }).catch(error=>{
      this.age = this.age+12
  })
  	}
  })
  
  
  
    <!---->
  
  
  <!--数据展示-->
  {{ username }}  <!--基本数据展示-->
  {{ num + 1 }}  <!--数值操作-->
  {{ username.split("").reverse().join("") }}  <!--字符串操作-->
  {{ show ? 'show is true' : 'show is false' }}  <!--数据根据条件展示-->
  
  
  <!--元素的选择性展示-->
  <span v-show='show_html'></span>  <!--show_html为true展示该元素,为false隐藏该元素-->
  <span v-if='num==3'></span>  <!--v-if='true'显示,反之隐藏, v-if会删除标签,v-show不会删除-->
  <span v-else-if='num==4'></span>
  <span v-else>not 3 4</span>
  
  
  <!--数据双向绑定, js中变化,html跟着变化-->
  <span v-model='username'></span>  <!--双向数据绑定, html元素值和js中username值相同-->
  
  
  <!--绑定class-->
  <span :class="{class-ative:show}"></span>  <!--绑定类class属性-->
  <span :class="[{class-active:show}, 'class-hidden']"></span> <!--既有绑定类,又有固定类-->
  
  
  <!--列表数据循环展示-->
  <ul v-for="(item, index) in items">
      <li>{{ index+1 }}. {{ item }}</li>
  </ul>
  <!--字典数据循环展示-->
  <ul v-for="(value, key) in oPerson" >
      <li>{{ key }} --- {{ value }}</li>
  </ul>
  
  
  <!--指令:绑定元素属性-->
  <a :href="a_href" >超链接</a><br>  <!-- : 绑定一个变量-->
  <button @click="demo_fun" >修改</button><br>  <!-- @ 绑定一个变量处理(如:num+=1)或方法-->
  
  
  <!--事件处理-->
  <!-- 阻止事件继续传播, 事件传播是指向内层传播, 如 外层div的@click会对内层div起作用, 此时就需要在内层div上加上 @click.stop  -->
  <input @click.stop="count += 1" type="button" value="增加1">
  <!-- 阻止默认提交行为 -->
  <form @submit.prevent="demo_fun"></form>
  <!-- 修饰符可以串连 -->
  <a @click.stop.prevent="demo_fun">doThis</a>
  ```

- **axios**

  > Vue没有Ajax, 使用axios.js库实现<https://github.com/axios/axios/releases>

  ```js
  axios({
      method: 'post',
      url: '/user/12345',
      data: {
          firstName: 'S',
          lastName: 'Z'
      }
  }).then(response=>{
      this.name = this.name+'zs'
  }).catch(error=>{
      this.age = this.age+12
  })
  ```

  

## 二. 语法

### 2.1 HelloWorld

- 示例

    ```vue
    <script src="../js/vue.js" ></script>
    
    <div id="app" >
        {{ msg }}
        <input type="button" value="变换" @click="fnChange" >
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                msg: "hello world",
            },
            methods: {
                fnChange: function(){
                    this.msg = this.msg.split("").reverse().join("")
                }
            },
        })
    </script>
    ```

### 2.2 插入值及指令(:, @)

- 示例

    ```vue
    <div id="app" >
        <!-- 插入值 -->
        {{ num + 1 }}<br>
        {{ msg }}<br>
        {{ ok ? 'yes' : 'no' }}<br>
        {{ message }}<br>
        {{ message.split("").reverse().join("") }}<br><br>
    
        <!-- 指令 -->
        <a v-bind:href="a_href" >a超链接</a><br>
        <a :href="a_href" >a超链接</a><br>
        <button v-on:click="fnChangeMessage" >修改button:v-on</button><br>
        <button @click="fnChangeMessage" >修改button:@</button><br>
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                num: 1,
                msg: "message",
                ok: false,
                message: "this is message",
                a_href: "https://www.baidu.com",
            },
            methods: {
                fnChangeMessage: function(){
                    this.message = this.message.split("").reverse().join("")
                }
            },
        })
    </script>
    ```

### 2.3 Class绑定

- 示例

    ```vue
    <body>
        <div id="app" >
            <!-- 对象的形式 -->
            <div :class="{big:isBig, red:isRed}" >1 content</div>
    
            <!-- 列表形式 -->
            <!-- 注意引号, 没有定义的data要加 '' -->
            <div :class="['big', 'red']" >2 content</div>
            <div :class="[isBig?'big':'', 'red']" >2 content</div>
            <div :class="[{big:isBig}, 'red']" >3 content</div>
        </div>    
    </body>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                isBig: false,
                isRed: true,
            },
        })
    </script>
    
    <style type="text/css" >
        .big{
            font-size: 40px
        }
        .red{
            color: red;
        }
    </style>
    ```

### 2.4 案例 -- 选项卡

- 示例

    ```vue
    <div id="app">
        <div class="tab_con">
            <input type="button" value="一" :class="{current: iNow==0}" @click="iNow=0">
            <input type="button" value="二" :class="{current: iNow==1}" @click="iNow=1" >
            <input type="button" value="三" :class="{current: iNow==2}" @click="iNow=2">
        </div>
        <div class="tab_cons">
            <div :class="{current: iNow==0}">第一个</div>
            <div :class="{current: iNow==1}">第二个</div>
            <div :class="{current: iNow==2}">第三个</div>
        </div>
    </div>
    
    <script type="text/javascript">
        var vm = new Vue({
            el: "#app",
            data: {
                iNow: 2
            },
        })
    </script>
    
    <style type="text/css">
        .current {
            font-size: 30px;
            color: red;
        }
    </style>
    ```

### 2.5 条件循环下的数据渲染

- 条件

    `v-if, v-show`

    ```vue
    <div id="app">
        <!-- v-if 条件控制 -->
        <div v-if="type=='A'">A</div>
        <div v-else-if="type=='B'">B</div>
        <div v-else>NOT A B</div>
    
        <!-- v-show不会直接删除标签, v-if直接删除再创建-->
        <div v-show="isOk">OK</div>
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                type: 'A',
                isOk: true,
            }
        })
    </script>
    ```

- 列表与字典循环渲染

    ```vue
    <div id="app">
        <!-- 渲染列表 -->
        <ul v-for="item in items">
            <li>{{ item }}</li>
        </ul><hr>
        <!-- 加上索引 -->
        <ul v-for="(item, index) in items">
            <li>{{ index+1 }}. {{ item }}</li>
        </ul><hr>
        <!-- 渲染字典, 注意: value在前,key在后, 只有一个参数时取值为value -->
        <ul v-for="(value, key) in oPerson" >
            <li>{{ key }} --- {{ value }}</li>
        </ul><hr>
    </div>
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                items: ["java", "python", "php"],
                oPerson: {
                    "name": "zs",
                    'age': '16',
                },
            },
        })
    </script>
    ```

### 2.6 事件处理(v-on)

- 示例

    ```vue
    <div id="app">
        <p>{{ count }}</p>
        <!-- 简单事件可在v-on中直接处理逻辑 -->
        <input v-on:click="count += 1" type="button" value="增加1">
        <!-- v-on <==> @ -->
        <input @click="fnShow" type="button" value="弹出count的值">
    
        <!-- 阻止事件继续传播, 事件传播是指向内层传播, 如 外层div的@click会对内层div起作用, 此时就需要在内层div上加上 @click.stop  -->
        <input @click.stop="count += 1" type="button" value="增加1">
        <!-- 阻止默认提交行为 -->
        <form @submit.prevent="onSubmit"></form>
        <!-- 修饰符可以串连 -->
        <a @click.stop.prevent="doThis">doThis</a>
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                count: 0
            },
            methods: {
                fnShow: function(){
                    alert("count的值为: " + this.count)
                }
            }
        })
    </script>
    ```

### 2.7 弹窗示例

- 示例

    ```vue
    <div id="app">
        <input type="button" value="弹窗" @click="aShow=true">
        <div v-show="aShow">这是一个广告</div>
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                aShow: false,
            }
        })
    </script>
    ```

### 2.8 表单输入绑定

- 用v-model指令**双向数据绑定**`<input>`与`textarea`

    ```vue
    <div id="app">
        <form>
            <!-- v-model 内容显示在输入框中 双向数据绑定 -->
            <!-- 绑定单行, 多行数据 -->
            <p>{{ message }}</p>
            <input v-model="message" placeholder="edit me">
            <textarea v-model="message" placeholder="addmultiple lines"></textarea>
    
            <!-- 绑定单选框 -->
            <p>{{ xinbie }}</p>
            <input type="radio" value="0" name="gender" v-model="xinbie">男
            <input type="radio" value="1" name="gender" v-model="xinbie">女
    
            <!-- 绑定复选框 -->
            <p>{{ aihao }}</p>
            <input type="checkbox" name="hobby" value="study" v-model="aihao">
            <input type="checkbox" name="hobby" value="basket" v-model="aihao">
            <input type="checkbox" name="hobby" value="foot" v-model="aihao">
    
            <!-- 绑定下拉框 -->
            <p>{{ site }}</p>
            <select v-model="site">
                <option value="0">北京</option>
                <option value="1">上海</option>
                <option value="2">天津</option>
                <option value="3">成都</option>
            </select>
        </form>
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                message: "msg",
                xinbie: "0",
                aihao: [],
                site: "3",
            }
        })
    </script>
    ```

### 2.9 计算属性和侦听属性

- 示例

    ```vue
    <div id="app">
        <div id="example">
            <!-- 计算属性 -->
            <!-- 对于复杂的逻辑表达式, 可以使用计算属性, 如下面两个是等价的 -->
            <p>{{ message.split("").reverse().join("") }}</p>
            <p>{{ reversedMessage }}</p>
    
            <!-- 侦听属性 -->
            <!-- 当某个值或对象改变后, 触发对应的方法 -->
            <input type="button" value="侦听button" @click="fnChangeMessage">
    
        </div>
    </div>
    
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                "message": "this is a message"
            },
            // 计算属性
            computed: {
                reversedMessage: function () {
                    return this.message.split('').reverse().join("")
                },
            },
            // 侦听属性
            watch: {
                message: function (newval, oldval) {
                    console.log(newval + "|" + oldval);  // 在浏览器的检查 --> console中查看
                }
            },
            methods: {
                fnChangeMessage: function () {
                    this.message = this.message + " new"
                }
            }
        })
    </script>
    ```

### 2.10 过滤器

- 示例

    ```vue
    <div id="app">
        <!-- 过滤器实现上是一个函数 -->
        <p>{{ price | RMB }}</p>
        <p>{{ price | Yuan}}</p>
        <div :id="rawId | formatId"></div>
    </div>
    
    <script>
        <!--定义全局过滤器-->
        Vue.filter('Yuan', function (value) {
            if (value == '') {
                return;
            }
            return '$' + value + '元'
        });
        var vm = new Vue({
            el: "#app",
            data: {
                price: 30,
                rawId: 2,
            },
            // 定义局部过滤器
            filters: {
                RMB: function (value) {
                    if (value == '') {
                        return;
                    }
                    return '$' + value
                }
            }
        })
    </script>
    ```

### 2.11 Vue生命周期

- mounted方法

    - 初始化操作, 相当于ready方法, 加载js后自动执行

    ```vue
    <script>
    	var vm = new Vue({
            el: "#app",
            data: {
                price: 30,
                rawId: 2,
            },
            mounted: function () {  // 加载js时自动执行
                this.price = this.price + 10
            },
    	}
    </script>
    ```

### 2.12 axios (Ajax)

- Vue没有Ajax, 使用axios.js库实现

- 下载地址: https://github.com/axios/axios/releases

- 完整写法

    ```js
    axios({
        method: 'post',
        url: '/user/12345',
        data: {
            firstName: 'S',
            lastName: 'Z'
        }
    })
        .then(function(response){
        console.log(response)
    })
        .catch(function(error){
        console.log(error)
    });
    
    // 上面的写法function中不能写this, 要使用vue对象,即var vm = el: 'vm'中的vm
    // 用箭头函数即可使用this
    .then(response=>{
        this.name = this.name+'zs'
    })
    .catch(error=>{
        this.age = this.age+12
    })
    ```

- GET请求

    ```vue
    <!-- 整个axios只有一个";", 是一个整体 -->
    <!-- .then处理请求成功的响应, .catch处理请求失败的响应 -->
    <!-- get参数写在params中 -->
    <javascript>
        axios.get('/user', {
            params: {
    			ID: 12345
            }
        })
        .then(function(response){
        	console.log(response)
        })
        .catch(function(error){
        	console.log(error)
        });
    </javascript>
    ```

- POST请求

    ```vue
    <!-- post的参数直接写在{}中 -->
    <javascript>
        axios.post('/user', {
        	firstName: 'Fred',
        	lastName: 'Flint',
        })
        .then(function(response){
        	console.log(response)
        })
        .catch(function(error){
        	console.log(error)
        });
    </javascript>
    ```

    













## 三. Vue.js组件开发

### 3.1 ES6语法

> ES6又叫ES2015, 是js的最新版本语法

- 变量声明

    ```js
    alert(iNow1)  // undefined  有预解析
    var iNow1 = 11;
    alert(iNow2)  // 没有预解析,在console中报错
    let iNow2 = 22; 
    alert(iNow3)  // 没有预解析,在console中报错
    const iNow3 = 33;  // 定义一个常量,修改常量的值报错
    ```

- 导入与导出

    ```js
    // 方法一: 导出再导入
    // index.js 中导出
    var person = {name: 'tom', age:18}
    export default {person}
    // index.js 中导入model.js中的person
    import person from 'js/model.js'
    
    //方法二: {}直接导入
    import {pserson} from 'js/model.js'
    ```

- 简写

    ```js
    name = 'zs';
    age = 18;
    let person = {
        name:name,
        age:age,
        showname:function(){
    		alert(this.name)
        },
        showage: function(){
            alert(this.age)
        }
    };
    
    // 简写为:
    let person = {
        name,
        age,
        showname(){
            alert(this.name)
        },
        showage(){
            alert(this.age)
        }
    };
    
    // 调用:
    person.showname();
    person.showage();
    ```

- 箭头函数

    ```js
    // 一般定义函数
    function fnRs(a, b){
        alert(a + b)
    }
    fnRs(1, 2)
    
    //匿名函数赋值定义
    var fnRs = function(a,b){
        alert(a + b)
    }
    
    // 箭头函数定义
    var fnRs = (a, b)=>{
        alert(a+b)
    }
    var fnRs2 = a=>{
        alert(a)
    }
    ```

### 3.2 vue.js组件的定义和使用

> 组件: 结构样式js封装在一起, 提高复用

- 示例

    ```html
    <!-- 定义组件 -->
    <javascript>
        Vue.component('breadcrumb', {
            // html, css, js
        	props: ['pos'],
        	template: '<div :class="{crumb:true, hot:isHot}" @click="hot=!isHost">
        					{{ pos }}
        			   </div>',
            data: function(){  // 独立每个组件的数据
                return {
    				isHot: true
                }
            },
            .hot{  // 定义样式
    			color: red;
            }
        	
        })
    </javascript>
    
    <!-- 使用组件(div必须用Vue接管) -->
    <div id='example'>
        <breadcrumb pos='1'></breadcrumb>  <!--面包屑导航-->
        <breadcrumb pos='2'></breadcrumb>
    </div>
    <javascript>
        var vm = new Vue({
        	el: '#example'
        })
    </javascript>
    ```

### 3.3 单文件组件

.vue文件

- `template.vue`

    ```html
    <template>
    	<div :class="{crumb:true, hot:isHot}" @click="hot=!isHost">
    		{{ pos }}
    	</div>
    </template>
    
    <script>
        export default{
            props: ['pos'],
            data: function(){
                return {
    				isHot: true
                }
            },
        }
    </script>
    
    <style>
    	.hot{  // 定义样式
    		color: red;
        }
        .crumb{
            width: 58px;
        }
    </style>
    ```

- 使用

    .vue ==> .js

    - 终端操作

    ```bash
    # 环境安装
    node.js  # 新的后端语言,语法和js类似
    npm  # npm是nodejs的一个包管理器, 类似于 在pip在python环境中安装包
    vue-cli  # 安装vue-cli包
    npm install --global vue-cli
    
    # 生成Vue单页面应用项目目录
    vue init webpack my-project
    cd my-project
    npm run dev
    ```

    









































































































































































































