# Vue-django

## 1. vue组件系统

全局组件的使用

```html
<script src="https://unpkg.com/axios@0.21.4/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<div id="app">
  <global-component></global-component>

</div>


<script>
    Vue.component("global-component",{
        template:`
		<div>
		<h1>hello,vue</h1>
		</div>
			`
    })


    new Vue({
        el:'#app',

    })
</script>
```



2.   局部组件的使用

```html
<script src="https://unpkg.com/axios@0.21.4/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<div id="app">


</div>


<script>
    let Header=({
        template: `<div>{{name}}</div>`,
        data(){
          return {
              name:'???'
          }
        },
    })

    let App={
        template:`<div-header></div-header>`,
        components:{
            'div-header':Header
        }
    }


    new Vue({
        el:'#app',
        template:`<App></App>`,
        components: {
            App,
        }

    })

</script>
```

1.   创建一个javascript object
2.   注册在vue根实例中，通过components属性
3.   使用 在div或者跟实例中的template里面都可以



3.   父子组件之间的数据通信

```html
<script src="https://unpkg.com/axios@0.21.4/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<div id="app">


</div>


<script>
    let Header=({
        template: `<div>
              <h1>
                {{fatherData}}
              </h1>

        </div>`,
        data(){
          return {
              name:'???'
          }
        },
        props:['fatherData']
    })

    let App={
        template:`<div-header v-bind:fatherData="name"></div-header>`,
        components:{
            'div-header':Header
        },

        data() {
            return {
                name:'小明'
            };
        }

    }


    new Vue({
        el:'#app',
        template:`<App></App>`,
        components: {
            App,
        }

    })

</script>
```



4.   子父组件数据传递

```html
<script>
    // 通过$emit("自定义事件名称"：’hhhhh‘)
    let Header=({
        template: `<div>
        <a @mouseenter="myenter">鼠标移入传入数据</a>
        </div>`,
        data(){
            return {
                name:''
            }
        },
        methods: {
            myenter:function () {
                this.$emit('mysite',0.1)
            }
        }
    })

    let App={
        template:`<div-header v-on:mysite="myclick"></div-header>`,
        components:{
            'div-header':Header
        },
        data() {
            return {
                name:'myti'
            };
        },

        methods:{
            myclick:function (value) {
                alert(value)
            }
        }
    }


    new Vue({
        el:'#app',
        template:`<App></App>`,
        components: {
            App,
        }

    })

</script>
```

父子组件的数据传递使用props

父子组件的数据通信使用$emit



5.   混入

     ```html
     <script>
         let mixs={
             methods:{
                 show:function () {
                     alert(1)
                 },
                 hide:function () {
                     alert(2)
                 }
             }
         }
     
         // 通过$emit("自定义事件名称"：’hhhhh‘)
         let fuck=({
             template: `
                     <div>
                       <button @click="show">点击显示</button>
                       <button @click="hide">点击隐藏</button>
                     </div>
             `,
     
             mixins:[mixs]
     
         })
     
         let App={
             template:`
             <div>
                 <button @mouseenter="show">移入显示</button>
                 <button @mouseleave="hide">移入隐藏</button>
             </div>
             `,
             mixins:[mixs]
         }
     
     
     
     
         new Vue({
             el:'#app',
             template:`
                       <div>
                           <App></App>
                           <fuck></fuck>
                       </div>
             `,
             components: {
                 'App':App,
                 'fuck':fuck
             }
     
         })
     
     </script>
     ```

6.插槽

```html
<script>
  Vue.component('global-component',{
      template:`<div>
                  <slot></slot>
                 </div>`,

  })

  new Vue({
      el:'#app'
  })
</script>
```

插槽传递‘





7.   具名插槽

```html
<div id="app">

  <global-component>
    <div slot="header">头</div>
    <div slot="footer">尾</div>
  </global-component>



</div>


<script>
  Vue.component('global-component',{
      template:`<div>

                  <slot name="footer"></slot>
                  <slot name="header"></slot>

                </div>`,

  })

  new Vue({
      el:'#app'
  })
</script>
```





## 2. vue实例生命周期

beforeCreate

create

beforeMount

mounted

beforeupdate

updated

beforeDestory

destoryed



知识点

beforeCreate实例创建之前出标签外 所有vue实例需要的事件都不存在

create 实例被创建之后 data和事件还没有被解析到 el还没有被找到

beforeMount 开始找标签 数据还没有被渲染，事件也没有被监听

mounted 开始渲染数据 开始监听事件

beforeUpdate 数据已经被修改在虚拟DOM中 但是没有被渲染到页面上

updated 开始使用Diff算法，将虚拟DOM的修改应用到页面上  此时真是DOM中的数据被修改了

beforeDestroy 所有数据都存在

destroy 所有的数据都有（虚拟DOM中找的）

<keep-alive></keep-alvie> vue 提供的用来缓存被消除的标签

用activated 和deactivated 取代了 beforeDestroy 和destroy







## 3.vue router

 

1.   vue router的实现原理
2.   vuerouter的安装
3.   vue router 命名路由
4.   vue router之路由参数
5.   vue router 之路由参数的实现原理
6.   vue router之子路由
7.   vue router之子路由append
8.   vue router之路由重定向
9.   vue router之路由的钩子函数
10.   vue router之路由钩子实现登录验证
11.   vue router实现路飞前端页面





## 4. vue 脚手架 vue-cli

1.   node.js







2.   npm





3.   
