# Vue的API(常用)

所有的[API](https://cn.vuejs.org/v2/api)

### 1、全局配置

`Vue.config`是一个对象，包含了Vue的全局配置。可以在启动应用之前修改下列property：

#### 1.1、silent:是否取消 Vue 所有的日志与警告。

默认值：false

```js
Vue.config.silent = true
//取消 Vue 所有的日志与警告
```

#### 1.2、optionMergeStrategies:自定义合并策略选项

```js
Vue.config.optionMergeStrategies._my_option = function(parent,child,vm){
    return child+1
}

const Profile = Vue.extend({
    _my_option:1
})

//Profile.option._my_option = 2
```

合并策略选项分别接收在父实例和子实例上定义的该选项的值作为第一、第二个参数，最后一个参数是一个Vue实例

#### 1.3、devtools：是否允许vue-devtools检查代码

开发版本默认值为true，生产版本默认为false

```js
// 务必在加载 Vue 之后，立即同步设置以下内容
Vue.config.devtools = true
```

#### 1.4、keyCodes：给v-on设置`自定义键位`别名

```js
Vue.config.keyCodes = {
    v:86,
    f1:112,
    //camelCase 不可用
    mediaPlayPause:179,
    // 取而代之的是 kebab-case 且用双引号括起来
    "media-play-pause":179,
    up:[38,87]
}
```

```html
<input type="text" @keyup.media-play-pause="methods"></input>
```

#### 1.5、performance

默认值：false

设置为 `true` 以在浏览器开发工具的性能/时间线面板中启用对组件初始化、编译、渲染和打补丁的性能追踪。只适用于开发模式和支持 [`performance.mark`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API 的浏览器上。

#### 1.6、productionTip：设置为 `false` 以阻止 vue 在启动时生成生产提示

默认值：true

### 2、全局API

**Vue的全局api就是vue自己封装的函数，可以在vue实例和vue项目当中直接使用，其实就是Vue自带的全局共用函数**

#### 2.1、Vue.extend(options)

options：Object

基础的Vue构造器，创建一个“子类”，参数是一个包含组件选项的对象

`data`选项是特例，在extend（）中它必须是一个**函数**

```html
<div id="mount-point">
    
</div>
```

```js
//creat 构造器
var Profile = Vue.extend({
    template:'<p>hello world</p>',
    data:function(){
        return {
            fisrtName:'doris',
            lastName:'eduora'
        }
    }
})

//创建Profile实例，并挂载到一个元素上
new Profile().$mount('mount-point')
```

结果就是在mount-point下有<p>hello world</p>

其实就是挂载组件的一种方法

#### 2.2、Vue.nextTick([callback],[context])

[callback]：function

[context]：Object

**在下次DOM更新循环结束之后执行延迟回调。在修改数据之后立即使用，获得最新的DOM**

```js
// 修改数据
vm.msg = 'hello'
// DOM 还没有更新
vm.msg === 'hello' //false
Vue.nextTick(function(){
    // DOM 更新了
    vm.msg === 'hello' //true
})

// 作为一个 Promise 使用(常用)
Vue.nextTick().then(function(){
    //dom更新了
    //一些操作
})
```

> 如果没有提供回调且在支持 Promise 的环境中，则返回一个 Promise。请注意 Vue 不自带 Promise 的 polyfill，所以如果你的目标浏览器不原生支持 Promise (IE：你们都看我干嘛)，你得自己提供 polyfill。

[异步更新队列](https://cn.vuejs.org/v2/guide/reactivity.html#%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0%E9%98%9F%E5%88%97)

#### 2.3、Vue.set(target,propertyName/index,value)

向**响应式对象**中添加一个 property，并确保这个新 property 同样是响应式的，且**触发视图更新**。它必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增 property (比如`this.myObject.newProperty = 'hi'`)

**！注意：**对象不能是Vue实例，或者Vue实例的根数据对象

#### 2.4、Vue.delete( target, propertyName/index )

删除对象的 property。如果对象是响应式的，确保删除能触发更新视图。这个方法主要用于避开 Vue 不能检测到 property 被删除的限制，但是你应该很少会使用它。

**！注意：**对象不能是Vue实例，或者Vue实例的根数据对象

#### 2.5、Vue.directive(id,[definition])

[definition]：function/Object

注册或获取全局指令

```js
//注册
Vue.directive('my-dir',{
    bind:function(){},
    inserted:function(){},
    upadte:function(){},
    componentUpdate:function(){},
    unbind:function(){}
})

//注册（指令函数）
Vue.directive('my-dir',function(){
    // 这里将会被 `bind` 和 `update` 调用
})
// getter，返回已注册的指令
var myDirective = Vue.directive('my-directive')
```

#### 2.6、Vue.filter( id, [definition] )

- **参数**：

  - `{string} id`
  - `{Function} [definition]`

- **用法**：

  注册或获取全局过滤器。

  ```js
  // 注册
  Vue.filter('my-filter', function (value) {
    // 返回处理后的值
  })
  
  // getter，返回已注册的过滤器
  var myFilter = Vue.filter('my-filter')
  ```

#### 2.7、Vue.component( id, [definition\] )

- **参数**：

  - `{string} id`
  - `{Function | Object} [definition]`

- **用法**：

  注册或获取全局组件。注册还会自动使用给定的 `id` 设置组件的名称

  ```js
  // 注册组件，传入一个扩展过的构造器
  Vue.component('my-component', Vue.extend({ /* ... */ }))
  
  // 注册组件，传入一个选项对象 (自动调用 Vue.extend)
  Vue.component('my-component', { /* ... */ })
  
  // 获取注册的组件 (始终返回构造器)
  var MyComponent = Vue.component('my-component')
  ```

#### 2.8、Vue.use(plugins)

plugins:Object/function

安装 Vue.js 插件。如果插件是一个对象，必须提供 `install` 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。

该方法需要在调用 `new Vue()` 之前被调用。

当 install 方法被同一个插件多次调用，插件将只会被安装一次。



#### 2.9、Vue.compile( template )

- **参数**：

  - `{string} template`

- **用法**：

  将一个模板字符串编译成 render 函数（[渲染函数](https://cn.vuejs.org/v2/guide/render-function.html)）**只在完整版时可用**。

  ```js
  var res = Vue.compile('<div><span>{{ msg }}</span></div>')
  
  new Vue({
    data: {
      msg: 'hello'
    },
    render: res.render,
    staticRenderFns: res.staticRenderFns
  })
  ```

2.10、[Vue.observable( object )](https://cn.vuejs.org/v2/api/#Vue-observable)

> 2.6.0 新增

- **参数**：

  - `{Object} object`

- **用法**：

  让一个对象可响应。Vue 内部会用它来处理 `data` 函数返回的对象。

  返回的对象可以直接用于[渲染函数](https://cn.vuejs.org/v2/guide/render-function.html)和[计算属性](https://cn.vuejs.org/v2/guide/computed.html)内，并且会在发生变更时触发相应的更新。也可以作为最小化的跨组件状态存储器，用于简单的场景：

  ```js
  const state = Vue.observable({ count: 0 })
  
  const Demo = {
    render(h) {
      return h('button', {
        on: { click: () => { state.count++ }}
      }, `count is: ${state.count}`)
    }
  }
  ```

  在 Vue 2.x 中，被传入的对象会直接被 `Vue.observable` 变更，所以如[这里展示的](https://cn.vuejs.org/v2/guide/instance.html#数据与方法)，它和被返回的对象是同一个对象。在 Vue 3.x 中，则会返回一个可响应的代理，而对源对象直接进行变更仍然是不可响应的。因此，为了向前兼容，我们推荐始终操作使用 `Vue.observable` 返回的对象，而不是传入源对象。