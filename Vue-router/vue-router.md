# Vue-router

## 1、vue-router的实现原理：实现单页面应用，spa模式

spa的核心是：

**更新视图而不重新请求页面**

## 2、vue-router实现单页面前端路由的两种方式：

#### 1-hash模式：

**vue-router默认的模式，使用url的hash来模拟一个完整的url，于是当url改变时，页面不会重新加载。hash出现在url中，但不会被包含在http请求中，对后端完全没有影响，因此改变hash不会重新加载页面；hash模式通过锚点值的改变，根据不同的值，渲染指定DOM位置的不同数据。hash模式的原理是onhashchange事件（监测hash值变化），可以在window对象上监听这个事件**

#### 2-history模式：

**这种模式充分利用了html5 history interface 中新增的 pushState() 和 replaceState() 方法。这两个方法应用于浏览器记录栈，在当前已有的 back、forward、go 基础之上，它们提供了对历史记录修改的功能。只是当它们执行修改时，虽然改变了当前的 URL ，但浏览器不会立即向后端发送请求**。

> 当使用history模式的时候，URL就像正常的url

```js
//main.js
const router = new VueRouter({
    mode:'history',
    routes:[
        ....
    ]
})

//or常用/src/router/index.js，因为这样不会导致main.js杂乱
export const routes = [
    {
        path:'/',
        name:'homeLink',
        component:Home  //当URL输入错误或者URL无法匹配任何静态资源，就自动跳到home页面
    },
    {
        path:'/index',
        name:'index',
        component: () => import('@/src/index')
    },
    {
        path:'/login',
        name:'login',
        component:login
    },
    {
        path:'*',	//这里的path:'*'就是输入地址不匹配时，自动显示出Error.vue的文件内容
        redirect:'/'
    }    
]
```

## 3、使用路由模块实现页面跳转的方式

#### 1-直接修改地址栏

#### 2-this.$router.push('路由地址')

#### 3-：<router-link to="路由地址"></router-link>

## 4、使用vue-router

1. `npm i vue-router -S`

2. main.js中引入 `import VueRouter from 'vue-router'`

3. 安装插件`Vue.use(VueRouter)`

4. 创建路由对象并设置路由规则


一般我们不会在main.js里面写入router，而是在/src/router/index.js下，使用export导出，然后在main.js中引入，之后的操作与main.js里的router操作一样

```js
///src/router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)

const routes = [
    {
        path:'index',
        name:'index',
        component:index
    },
    //.....
]
const router = new VueRouter({
    mode:"history", //设置路由模式为history
    base: process.env.BASE_URL,//环境
    routes
})
export default router
```

```js
//main.js
//import  ......
import VueRouter from 'vue-router'
import router from '/router/index.js'

//new vue 启动
new Vue({
    el:'#app',
    //让vue知道我们的路由规则
    router,
    //.....还可以放入store的内容
    render:c=>c(App),
})
```

```vue
<!-- /src/app.vue -->
<template>
	<div>
        <router-view></router-view>
    </div>
</template>
<script>
    //......
</script>
```



## 5、vue-router的嵌套（多级路由）

```js
///src/router/index.js
//.....
routes:[
    {
        path:'/',
        name:'helloWorld',
        component: HelloWorld,
        //子路由
        children:[
            {
                path:'/ch1',
                name:'ch1',
                component:h1
                children:[]
            },
            {
                path:'ch2',
                name:'ch2',
                component:ch2
            },
            //.....
        ]
    }
]
```

## 6、**单页面多路由**区域操作

### 6.1、router-link实现单页面多路由跳转

**to：跳转目标**，可以使用path设定或name设定

1. 某一单页面文件，在`<router-view>`下面新写了两行`<router-view>`标签,并加入了些CSS样式

   ```vue
   <template>
     <div id="app">
       <img src="./assets/logo.png">
         
         <!--router-link-->
          <router-link :to="{name:'HelloWorld'}"><h1>H1</h1></router-link>
          <router-link :to="{name:'H1'}"><h1>H2</h1></router-link>
     </div>
   </template>
   ```

   

2. 需要在路由里配置这三个区域，配置主要是在components字段里进行

   ```js
   ///src/router/index.js
       export default new Router({
       routes: [
         {
           path: '/',
           name: 'HelloWorld',
           components: HelloWorld,
           children:[
             {
           	path: '/h1',
           	name: 'H1',
           	components: H1
         	},
              {
           	path: '/h2',
           	name: 'H2',
           	components: H2
         	} 
             ]
           }
         },  
           
       ]
     })
   ```

## 7、路由参数传递

1. 使用name传递参数,在路由文件src/router/index.js里配置name属性

   ```js
   ///src/router/index.js
   routes:[
       {
           path:'/index',
           name:'index',
           component:index
       }
   ]
   
   
   //模板src/app.vue用$route.name来接收，比如：
   //<p>{{$route.name}}<p>
   ```

   

2. 常用path来传递参数路径

   ```js
   //......
   routes:[
       path:'@/src/index.vue',
       name:'name',
       component:index
   ]
   ```


### 7.1、命名路由

给路由对象加上name属性，一般在router-link里实现多级跳转时，可以不用path，用name指定跳转目标(这样简化了代码)

```js
routes:[
    {
        path:'/index',
        //name属性
        name:'index',
        component:index
    }
]
```

```html
<!--没有name-->
<router-link :to="{path:'/Home/h1/index'}">
    <h1>H1</h1>
</router-link>

<!--有name-->
<router-link :to="{name:'HelloWorld'}">
    <h1>H1</h1>
</router-link>
```

### 7.2、query参数

在跳转路由的时候把想要传的参数传输过去

**发送：**

- path传递

```html
//静态值path传递
<router-link :to="/Home/h1/index?id=666&title='helloWorld">
</router-link>

//动态值path传递
//to的字符串写法
<router-link :to="
                  `/Home/h1/index?id=${m.id} & title=${m.title} `
                  ">
</router-link>

//to的对象写法
<router-link :to="{
                 path:'/Home/h1/index',
                  query:{
                  id:m.id,
                  title:m.title
                  }
                  }">
</router-link>
```

**接收：**this.$route.query.xxx(参数)

```vue
<h1>
    {{$route.query.id}}
</h1>
```



### 7.2、params参数

在跳转路由的时候把想要传的参数传输过去

**发送：**

**必须先在路由的path声明参数**

![image-20210810094943320](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210810094943320.png)

```html
//静态传递
<router-link :to="/Home/h1/index/666/helloWorld">
</router-link>

//动态值传递
//to的字符串写法
<router-link :to="
                  `/Home/h1/index/${m.id}/${m.title}`
                  ">
</router-link>

//to的对象写法,注意不能使用path，只能name
<router-link :to="{
                 name:'params',
                 params:{
                  id:m.id,
                  title:m.title
                  }
                  }">
</router-link>
```

**接收：**this.$route.query.xxx(参数)

```vue
<h1>
    {{$route.query.id}}
</h1>
```



## 8、$route与$router的区别

![img](https://user-gold-cdn.xitu.io/2019/3/25/169b57e897915e4c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![img](https://user-gold-cdn.xitu.io/2019/3/25/169b57e897b4717b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- **$route 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数**

  **① `$route.path`** 字符串，对应当前路由的路径，总是解析为绝对路径，如 "/order"。

  **② `$route.params`** 一个 key/value 对象，包含了 动态片段 和 全匹配片段， 如果没有路由参数，就是一个空对象。

  **③ `$route.query`** 一个 key/value 对象，表示 URL 查询参数。 例如，对于路径 /foo?user=1，则有 $route.query.user为1， 如果没有查询参数，则是个空对象。

  **④ `$route.hash`** 当前路由的 hash 值 (不带 #) ，如果没有 hash 值，则为空字符串。

  **⑤ `$route.fullPath`** 完成解析后的 URL，包含查询参数和 hash 的完整路径。

  **⑥ `$route.matched`** 数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。

  **⑦ `$route.name`   当前路径名字**

  

- **$router 是“路由实例”对象，即使用 new VueRouter创建的实例，包括了路由的跳转方法，钩子函数等。**

```vue
<template>
    <button @click="goToMenu" class="btn btn-uccess">Let's order！
    </button>
</template>
<script>
  export default{
    methods:{
      goToMenu(){
        this.$router.go(-1)//跳转到上一次浏览的页面
        this.$router.replace('/menu')//指定跳转的地址
        this.$router.replace({name:'menuLink'})//指定跳转路由的名字下
        this.$router.push('/menu')//通过push进行跳转
        this.$router.push({name:'menuLink'})//通过push进行跳转路由的名字下
      }
    }
  }
</script>
```

#### **`$router.push`和`$router.replace`的区别**：

- 使用push方法的跳转会向 history 栈添加一个新的记录，当我们点击浏览器的返回按钮时可以看到之前的页面。
- 使用replace方法不会向 history 添加新记录，而是替换掉当前的 history 记录，即当replace跳转到的网页后，‘后退’按钮不能查看之前的页面

## 9、设置404页面

用户会经常输错页面，当用户输错页面时，我们希望给他一个友好的提示页面，这个页面就是我们常说的404页面。vue-router也为我们提供了这样的机制。

1-设置我们的路由配置文件（/src/router/index.js）

```js
//index.js
{
    path:'*',	//这里的path:'*'就是输入地址不匹配时，自动显示出Error.vue的文件内容
    component:Error
}
```

```vue
//Error.vue
<template>
	<div>
        <h2>
            {{msg}}
    </h2>
    </div>
</template>
<script>
    export default{
        data(){
            msg:'Error:404'
        }
    }
</script>
```

此时我们随意输入一个错误的地址时，便会自动跳转到404页面

## 10、设置meta字段=》路由元信息

```js
import VueRouter from 'vue-router'
import vue from 'vue'

const routes = [
    {
        path:'/home',
        name:'home',
        component:Home
    }
    {
        path:'/index',
        name:'index',
    //这样不用上方import组件
        component: ()=>import('@/src/index'),
        meta:{
            //主要是用于校验
            requiresAuth:true
        }
    }
]
```

​      **定义路由的时候可以配置 meta 字段，用于一些校验，比如在导航守卫中进行校验，定义的值可以在$route中访问**



