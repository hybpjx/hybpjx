# 一.vue基础

## 1.vue简介

1.JavaScript框架

2.简化dom操作

3.响应式数据驱动

## 2.第一个vue 程序

https://cn.vuejs.org



1.导入开发版本的vue.js

```html
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```



2.创建vue实例对象 设置el熟悉和data属性

3.使用简介的模板语法 吧数据渲染到页面上



```html

<div id="app">
  {{ message }}
</div>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```



## 3.el 挂载点

```html
<div id="app">
  {{ message }}
</div>
```

```html
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

1.vue 会管理el选项命中的元素 以及其内部的后代元素



2.可以使用其他的选择器 但是建议使用id选择器



3.可以使用其他的双标签 但是不能使用html和body





## 4.data：数据对象

```
<div id="app">
  {{ message }}
</div>
```



```
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
    array:[],
    obj:{}
  }
})
```



1.vue中用到的数据定义在data中



2.data中可以写复杂类型的数据



3.渲染复杂类型的数据时 遵守js的语法即可





# 二.本地应用

## 1.内容绑定，事件绑定

### 1.v-text

```html
<div id="app">
    <h2 v-text='message+"!"'>深圳</h2>
    <h2 v-text='info+"!"'>深圳</h2>
    <h2>{{message+"!"}}深圳</h2>

</div>
```



v-text 可以简单理解是全部替换 而{{}}则是局部替换  深圳前两个不显示 



```html
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<script>
    var app=new Vue({
        el:'#app',
        data:{
            message:'你好吗'，
            info:'不好'
        
        }

    })


</script>
```



1.v-text 的指令作用是： 设置标签的内容（textContent）



2.默认写法会替换全部内容 而使用差值表达式{{}} 可以替换指定内容

### 2.v-html

```html
<div id="app">

    <p v-html="context"></p>

</div>
```



```html
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app=new Vue({
        el:'#app',
        data:{
           // context:'黑马程序员'
            context: "<a href='https://www.baidu.com'>黑马程序员</a>"
        }

    })

</script>
```



1.v-html 指令的作用是： 设置元素的innerHTML



2.内容中有html标签的会被解析为标签



### 3.v-on基础

为元素绑定事件

```html
<div id="app">
    <input type="button" value="v-on指令" v-on:click="doIT">
    <input type="button" value="v-on简写" @click="doIT">
    <input type="button" value="双击事件" @dblclick="doIT">
    <h2 v-on:click="change_food">{{food}}</h2>

</div>
```

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app=new Vue({
        el:'#app',
        data:{
          food:'西兰花炒蛋'
        },
        methods:{
            doIT:function () {
                alert('做it')
            },
            change_food:function () {
                // alert(this.food)
                this.food+='好好吃'
            }
        }

    })
</script>
```



1.v-on指令的作用是：为元素绑定事件



2.事件名不需要写on



3.指令可以简写为@



4.绑定的方法定义在methods属性中



5.方法内部可以通过this 关键字访问定义在data中的数据



### 4.计数器

```html
<div id="app">
    <button v-on:click="sub">-</button>
    <span>{{num}}</span>
    <button v-on:click="add">+</button>
</div>
```



```html
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<script>
    var app=new Vue({
        el:'#app',
        data:{
            num:1
        },
        methods:{
            add:function () {

                if (this.num<10){
                    this.num+=1
                }
                else {
                    alert('太大了')
                }
            },

            sub:function () {
                if (this.num>0){
                    this.num-=1
                }
                else {
                    alert('该数值不能小于0')
                }
            }


        }

    })
</script>
```



1.创建vue 示例时 el挂载点 data数据 methos方法



2.v-on指令作用时绑定 简写为@



3.方法中通过this 关键字获取data中的数据



4、v-text的指令作用时 设置元素的文本值 简写为{{}}



### 5. v-show 

```html
<div id="app">
    <button v-on:click="sub">-</button>
    <span>{{num}}</span>
    <button v-on:click="add">+</button>
</div>
```



```html
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<script>
    var app=new Vue({
        el:'#app',
        data:{
            num:1
        },
        methods:{
            add:function () {

                if (this.num<10){
                    this.num+=1
                }
                else {
                    alert('太大了')
                }
            },

            sub:function () {
                if (this.num>0){
                    this.num-=1
                }
                else {
                    alert('该数值不能小于0')
                }
            }


        }

    })
</script>
```



1.   根据表达值的真假 切换元素的显示和隐藏



2.   原理是修改元素的 display 实现实时隐藏



3.   指令修改后面的内容 最终都会解析为布尔值



4.   值为true 显示 值为false元素则隐藏



5.   数据改变时 对应的元素的显示状态会同步更新



### 6. v-if

```html
<div id="app">
    <input type="button" value="切换显示" v-on:click="toggleIsShow">

    <p v-if="isShow">我是胡汉三</p>
    <p v-show="isShow">我是胡汉三 v-show 操作</p>  <!--添加 display none-->

    <p v-if="temperature>=35">热死了</p>

</div>
```



```html
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<script>

    var app=new Vue({
        el:'#app',
        data:{
            isShow:false,
            temperature:44
        },
        methods:{
            toggleIsShow:function () {
                this.isShow=!this.isShow
            }
        }


    })

</script>
```



1.v-if 指令的作用时：根据表达式 的真假切换元素的显示状态



2.本质时通过操作dom元素来切换显示状态



3.表达式的值为true 元素村子啊与dom树中，为false 从dom树中移除



4.   频繁的切换 v-show 反之则v-if  前者切换消耗小



### 7. v-bind

-   v-bind指令的作用是：为元素绑定属性。

-   完整写法是v-bind：属性名

-   简写的花可以直接省略v-bind 只保留：属性名
-   需要动态的增删class 建议使用对象的方式。

### 8.图片切换

-   列表数据可以使用数组保存
-   v-bind指令可以设置元素，比如src
-   v-show和v-if都可以切换元素的显示状态 频繁的切换用v-show

###  9. v-for

-   v-for 指令的作用是 根据数据生成列表结构
-   数组经常和v-for使用
-   语法是（item,index）in 数据
-   item 和index 可以结合其他指令一起使用
-   数组长度的更新会同步到页面上 是响应式的。





### 10. v-on 补充

-   事件绑定的方法写成函数盗用的形式 可以传入自定义参数
-   定义方法是需要定义形参来接受传入的实参
-   事件的后面跟上.修饰符可以对事件进行限制
-   .enter 可以限制触发 的案件为回车
-   事件修饰符有多种



### 11. v-model

-   获取和设置表单元素的值（双向数据绑定   ）  
-   绑定的数据 回合表单元素值相关联
-   绑定的数据———表单元素的值





## 2. 小黑记事本

### 1. 介绍

1.   新增
2.   删除
3.   统计
4.   清空
5.   隐藏



### 2. 新增

生成列表结构（v-for数组）

1.   获取用户输入  （v-model 双向数据绑定）
2.   回车，新增数据



-   v-for 指令
-   v-model指令
-   v-on指令





### 3. 删除

点击删除指定内容（v-on: splice）索引

1.   数据改变 和数据绑定的元素 同步改变
2.   事件的自定义参数
3.   splice 方法的作用





### 4. 统计

统计信息个数（v-text length）

1.   基于数据的开发模式
2.   v-text指令的作用





### 5. 清空



点击清除所有信息（v-on）

1.   基于数据开发
2.   直接给list 赋值空列表
3.   不用管DOM



### 6. 隐藏

没有数据时 隐藏元素（v-show v-if 数组非空）



1.   直接用 v-if 和v-show 做逻辑判断 不等于0 则直接隐藏



### 7. 总结

-   列表接口 可以对v-for 指令 结合 数据生成

-   v-on 可以结合事件修饰符可以对事件进行限制 比如.enter
-   v-on 在绑定时间时可以传递自定义参数
-   通过v-model 可以快速设置和获取表单元素的值
-   基于数据开发方式





# 三.网络应用

Vue 结合网络数据开发应用



1.   axios

网络请求库

2.   axios +vue

3.   axios 天气预报案例

     

## 1. axios 基本

1.   axios必须先导入才可以使用
2.   使用get 或者post 即可发送对应的请求
3.   then 方法中的回调函数 会在请成功或者失败时触发
4.   通过回调函数的形参 可以获取相应内容或者错误信息



## 2.axious +vue

axios 结合vue开发应用

-   axios回调函数中的this以及改变，无法访问到data中的数据
-   把this 保存起来 回调函数中直接使用保存的this 即可
-   和本地应用最大的区别就是改变了数据来源



## 3. 天知道

-   回车查询
    -   按下回车（v-on.enter）
    -   查询数据 (axios 接口 v-model)
    -   渲染数据 (v-for 数组 that)





1.   应用的逻辑代码建议和页面分离 使用单独的js文件编写一份
2.   axios回调函数中this 指向改变了 需要额外的保存一份
3.   服务器返回的数据比较复杂时 获取的时候需要注意层级结构



-   点击查询
    -   点击城市（v-on 自定义参数）
    -   查询数据 
    -   渲染数据



1.   自定义参数 可以让代码的复用性更高
2.   methods 定义的方法内部，可以通过关键字点出其他的方法





# 四.综合应用



## 1.  悦听app

开发音乐播放

1.   歌曲搜索
2.   歌曲播放
3.   歌曲封面
4.   歌曲评论
5.   播放动画
6.   mv 播放





## 2. 歌曲搜索

1.   按下回车(keyup.enter)
2.   查询数据(axious 接口 v-model )
3.   渲染数据( v-for 数组 that)





-   服务器返回的数据比较复杂时 获取的时候需要注意层级结构
-   通过审查元素 快速定位到需要操作的元素





## 3. 歌曲播放



1.   点击播放(增加逻辑)
2.   歌曲地址获取(接口 歌曲id)
3.   歌曲地址设置(v-bind)



-   找到元素 绑定
-   v-bind v-model





## 4.  歌曲封面

-   在vue中通过v-bind 操纵属性
-   本地无法获取的数据,基本都会有对应的接口







## 5. 歌曲评论

1.   点击播放(增加逻辑)
2.   歌曲评论获取(接口 歌曲id)
3.   歌曲评论渲染(v-for)



在vue中通过v-for 生成列表





## 6. 播放动画

1.   监听音乐播放 (v-on play)
2.   监听音乐暂停 (v-on pause)
3.   操纵类名 (v-bind 对象)



-   audio 标签的play 事件会在音频播放的时候触发
-   audio 标签pause 事件会在音频暂停的时候触发
-   通过对象的方式设置类名,类名生效与否取决于后面的真假





## 7. 播放mv



1.   mv图标显示(v-if)
2.   mv地址获取(接口 mvid)
3.   遮罩层(v-show v-on)
4.   mv 地址设置(v-bind)



# 五. 总结

1,2,3,4,  





# 六. 补充



## 1. v-if v-else v-else-if



## 2. 计算属性

```html
<script>
    const app=new Vue({
        el:'#app',
        data:{
            [1,2,3]
        }
        computed:{
        // 可以直接计算 当成一个常量 而不是调用函数 本质和methods一样
    }
        
    })
    
</script>
```





## 3. var let区别

es6不用var  通通用let

let 有自带的if 和for 的作用域 而 var 没有



## 4. const 和let区别



const一般做常量不可改变 比如函数 

而liet 一般可变 用于let i 循环



## 5. const 增强写法

```html
<script>
    
    const name='lzc';
    const age=18;
    const height=3;
    
    
    const obj= {
        name,
        age,
        height
    }
    
</script>
```



函数增强写法

```html
<script>
    const obj={
        run(){
            
        }
    }
    
    
</script>

```



# 6. html 标准

缩进两格  注释 一行一句   语言 utf-8



