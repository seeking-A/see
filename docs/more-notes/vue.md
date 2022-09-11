---
title: VUE快速入门
tags:
 - 前端技术
categories:
 - 学习笔记
---


## Vue 基础

### Vue简介

- JavaScript 框架
- 简化 DOM 操作
- 响应式数据驱动



### 第一个Vue程序

[官网地址](https://cn.vuejs.org)

1. 导入开发版本的Vue.js创建Vue实例对象

```js
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```

2. 设置el属性和data属性

```js
<script type="text/javascript">
    var app = new Vue({
        el:"#app",
        data:{
        message:"hello world!"
        }
    })
</script>
```

3. 使用简洁的模板语法把数据渲染到页面上

```html
<div id='app'>{{message}}</div>
```



### El : 挂载点

1. el 是用来设置Vue实例挂载（管理）的元素
2. Vue 会管理 el 选项命中的元素及其内部的后代元素
3. 可以使用其他的选择器，但是建议使用 ID 选择器
4. 可以使用其他的双标签，不能使用 HTML 和 BODY

```html
<body>
    {{ message }}
    <div id='app' class="app">
        <span>{{message}}</span>
    </div>
</body>
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script type="text/javascript">
    var app = new Vue({
        //el:"#app",
        //el:".app",
        el: "div",
        data:{
            message:"EL:挂载点"
        }
    })
</script>

```



### data : 数据对象

1. Vue中用到的数据定义在data中
2. data中可以写复杂类型的数据
3. 渲染复杂类型数据时，遵守js的语法即可

```html
<body>
    <div id="app">
        {{ message }}
        <h2>{{ school.name }} {{ school.mobile }}</h2>
        <ul>
            <li>{{ campus[0] }}</li>
        </ul>
    </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script type="text/javascript">
    var app = new Vue({
        el:"#app",
        data:{
            message:"data:数据对象",
            school:{
                name: "b站大学",
                mobile: "400-618-9090"
            },
            campus:["影视区","生活区","数码区"]
        }
    })
</script>
```



## 本地应用

### v-text 指令

1. v-text指令的作用是：设置标签的内容 (textContent)
2. 默认写法会替换全部内容，使用差值表达式{}可以替换指定内容
3. 内部支持写表达式

```html
<body>
    <div id="app">
        <h2 v-text="message+'!'">哈哈</h2>
        <h2 v-text="info+'!'">哈哈</h2>
        <h2>{{message + '!'}}哈哈</h2>
    </div>
</body>
<!--开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            message:"学习快乐",
            info:"我爱学习"
        }
    })
</script>
```



### v-html 指令

1. v-html 指令的作用是：设置元素的 innerHTML
2. 内容中有 html 结构会被解析为标签
3. v-text 指令无论内容是什么，只会解析为文本
4. 解析文本使用v-text,需要解析html结构使用 v-html

```html
<body>
    <div id="app">
        <p v-html="content"></p>
        <p v-text="content"></p>
    </div>
</body>
<!--开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            //content:"B站大学",
            content:"<a href='https://www.bilibili.com/video/BV12J411m7MG'>我在B站学VUE</a>"
        }
    })
</script>
```



### v-on 指令

1. v-on指令的作用是：为元素绑定事件
2. 事件名不需要写 on
3. 指令可以简写为 @
4. 绑定的方法定义在 (methods) 属性中
5. 方法内部通过 this 关键字可以访问定义在 data 中数据

```html
<body>
    <!-2.html结构->
    <div id="app">
        <input type="button" value="v-on" v-on:click="doIt">
        <input type="button" value="v-on" @click="doIt">
        <input type="button" value="双击事件" @dblclick="doIt">
        <h2 @click="changeFood">{{ food }}</h2>
    </div>
</body>
<!--1.开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    //3.创建Vue示例
    var app=new Vue({
        el: "#app",
        data: {
            food: "西兰花炒蛋"
        },
        methods: {
            doIt: function() {
                alert("我爱吃西兰花炒蛋");
            },
            changeFood: function() {
                //console.log(this.food);
                this.food += "好好吃！"
            }
        }
    })
</script>
```



### v-show 指令

```html
<body>
    <!--htm1结构-->
    <div id="app">
        <!--计数器功能区域-->
        <div class="input-num">
            <button @click="sub">
                -
            </button>
            <span>{{ num }}</span>
            <button @click="add">
                +
            </button>
        </div>
    </div>
</body>
<!--开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<!--编码-->
<script>
    //创建Vue实例
    var app = new Vue({
        el: "#app",
        data: {
            num: 1
        },
        methods:{
            add: function(){
                //console.log('add');
                if(this.num<10){
                    this.num++;
                }else{
                    alert('别点啦，最大啦!')
                }
            },
            sub: function(){
                //console.log('sub');
                if(this.num>0){
                    this.num--;
                }else{
                    alert('别点啦，最小啦!')
                }
            },
        }
    })
</script>
```



### v-show 指令

1. v-show指令的作用是根据真假切换元素的显示状态
2. 原理是修改元素的display,实现显示隐藏
3. 指令后面的内容，最终都会解析为布尔值
4. 值为true元素显示，值为false元素隐藏
5. 数据改变之后，对应元素的显示状态会同步更新

```html
<body>
    <div id="app">
        <input type="button" value="切换显示状态" @click="changeIsShow">
        <input type="button" value="累加年龄" @click="addAge">
        <img v-show="isShow" src="./img/ku.jpg" alt="">
        <img v-show="age>=18" src="./img/ku.jpg" alt="">
    </div>
</body>
<!--1.开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            isShow: false,
            age: 17
        },
        methods: {
            changeIsShow: function() {
                this.isShow=!this.isShow;
            },
            addAge: function() {
                this.age++;
            }
        }
    })
</script>
```



### v-if 指令

1. v-指令的作用是：根据表达式的真假切换元素的显示状态
2. 本质是通过操纵dom元素来切换显示状态
3. 表达式的值为true,元素存在于dom树中，为false,从dom树中移除
4. 频繁的切换v-show,反之使用v-f,前者的切换消耗小

```html
<body>
    <div id="app">
        <input type="button" value="切换显示状态" @click="toggleIsShow">
        <img v-if="isShow" src="./img/ku.jpg" alt="">
    </div>
</body>
<!--1.开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            isShow: false
        },
        methods: {
            toggleIsShow: function(){
                this.isShow = !this.isShow;
            }
        }
    })
</script>
```



### v-bind 指令

1. 列表数据使用数组保存
2. v-bind指令可以设置元素属性，比如 src
3. V-show和v-if都可以切换元素的显示状态，频繁切换用v-show

```html
<head>
    <meta charset="utf-8">
    <title>v-show</title>
    <style type="text/css">
        .active{
            border: 2px solid cyan;
        }
    </style>
</head>
<body>
    <div id="app">
        <img v-bind:src="imgSrc" alt="">
        <br>
        <img :src="imgSrc" alt="" :title="imgTitle"
             :class="isActive?'active':''" 
             @click="toggleActive">
        <br>
        <img :src="imgSrc" alt="" :title="imgTitle"
             :class="{active:isActive}" 
             @click="toggleActive">
    </div>
</body>
<!--1.开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            imgSrc: "./img/ku.jpg",
            imgTitle: "kuimage",
            isActive: false
        },
        methods: {
            toggleActive: function(){
                this.isActive = !this.isActive;
            }
        }
    })
</script>
```



### v-for 指令

1. v-for 指令的作用是：根据数据生成列表结构
2. 数组经常和v-for结合使用
3. 语法是(item,index)in数据
4. item和index可以结合其他指令一起使用
5. 数组长度的更新会同步到页面上，是响应式的

```html
<body>
    <div id="app">
        <input type="button" value="添加数据" @click="add">
        <input type="button" value="移除数据" @click="remove">
        <ul>
            <li v-for="(it,index) in arr">
                {{ index+1 }} B站大学：{{ it }}
            </1i>
        </ul>
    <h2 v-for="item in vegetables" v-bind:title="item.name">
        {{ item.name }}
    </h2>
    </div>
</body>
<!--1.开发环境版木，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            arr: ["影视区", "生活区", "数码区"],
            vegetables: [
                {name: "西兰花炒蛋"},
                {name: "蛋炒西蓝花"}
            ]
        },
        methods: {
            add: function() {
                this.vegetables.push({name:"花菜炒蛋"});
            },
            remove: function() {
                this.vegetables.shift();
            }
        }
    })
</script>
```



### v-on 指令补充

1. 事件绑定的方法写成函数调用的形式，可以传入自定义参数
2. 定义方法时需要定义形参来接收传入的实参
3. 事件的后面跟上 .修饰符 可以对事件进行限制
4. .enter 可以限制触发的按键为回车
5. 事件修饰符有多种

```html
<body>
    <div id="app">
        <input type="button" value="点击" @click="doIt(666,'老铁')">
        <input type="text" value="B站大学" @click="sayHi">
    </div>	
</body>
<!--1.开发环境版木，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        methods: {
            doIt: function(p1,p2){
                console.log(p1);
                console.log(p2);
            },
            sayHi:function(){
                alert("我在B站学Vue")
            }
        }
    })
</script>
```



### v-model 指令

1. v-model指令的作用是便捷的设置和获取表单元素的值
2. 绑定的数据会和表单元素值相关联
3. 绑定的数据→表单元素的值



## 网络应用

axios 是一个功能强大的网络请求库

使用方法：

1. 引入标签库

```htlm
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

2. 请求方法

```js
axios.get(地址:?key=value&key2=values).then(function(response){},function(err){})
axios.post(地址,{key:value,key2:value2}).then(function(response){},function(err){})
```



## 私藏干货

[一篇文章，Vue快速入门](https://blog.csdn.net/qq_45408390/article/details/118151297)