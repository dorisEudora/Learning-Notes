## Vue核心技术和相关知识

### vue工程

1、构建的 vue-cli 工程都到了哪些技术，它们的作用分别是什么？

> 1、**vue.js**:vue-cli工程的核心，主要特点是**双向数据绑定**和**组件系统**
>
> 2、vue-router：vue官方推荐的路由框架
>
> 3、vuex：为vue.js应用项目开发的状态管理器，主要用于维护vue组件间**共用的**一些变量和方法
>
> 4、**axios**（或者fetch、ajax）：用于发起get、post等http请求，基于promise设计
>
> 5、vant（等）：为vue设计的移动端UI组件库
>
> 6、创建一个emit.js文件，用于vue事件机制的管理
>
> 7、webpack：模块加载和vue-cli工程打包器

2、vue-cli 工程常用的 npm 命令有哪些？

> 1、下载node_moudles资源包的命令：npm insatll
>
> 2、启动vue-cli开发环境的npm命令：npm run dev(有时候不是这个，具体看packge.json当中的配置)
>
> 3、vue-cli生成生产环境部署资源的·npm命令：npm run build
>
> 4、用于查看vue-cli生产环境部署资源文件大小的npm命令：npm run build --report
>
> 命令效果：在浏览器上自动弹出一个 展示 vue-cli 工程打包后 app.js、manifest.js、vendor.js 文件里面所包含代码的页面。可以具此优化 vue-cli 生产环境部署的静态资源，提升 页面 的加载速度。

3、请说出vue-cli工程中每个文件夹和文件的用处

> 答案链接：https://www.kancloud.cn/hanxuming/vue-iq/728307

4、config文件夹 下 index.js 的对于工程 开发环境 和 生产环境 的配置

> 1. build 文件夹：用于存放 webpack 相关配置和脚本。开发中仅 偶尔使用 到此文件夹下 webpack.base.conf.js 用于配置 less、sass等css预编译库，或者配置一下 UI 库。
>
> 2. config 文件夹：主要存放配置文件，用于区分开发环境、线上环境的不同。 常用到此文件夹下 config.js 配置开发环境的 端口号、是否开启热加载 或者 设置生产环境的静态资源相对路径、是否开启gzip压缩、npm run build 命令打包生成静态资源的名称和路径等。
>
> 3. dist 文件夹：默认 npm run build 命令打包生成的静态资源文件，用于生产部署。
>
> 4. node_modules：存放npm命令下载的开发环境和生产环境的依赖包。
>
> 5. src: 存放项目源码及需要引用的资源文件。
>
> 6. src下assets：存放项目中需要用到的资源文件，css、js、images等。
>
> 7. src下componets：存放vue开发中一些公共组件：header.vue、footer.vue等。
>
> 8. src下emit：自己配置的vue集中式事件管理机制。
>
> 9. src下router：vue-router vue路由的配置文件。
>
> 10. src下service：自己配置的vue请求后台接口方法。
>
> 11. src下page：存在vue页面组件的文件夹。
>
> 12. src下util：存放vue开发过程中一些公共的.js方法。
>
> 13. src下vuex：存放 vuex 为vue专门开发的状态管理器。
>
> 14. src下app.vue：使用标签`<route-view></router-view>`渲染整个工程的.vue组件。
>
> 15. src下main.js：vue-cli工程的入口文件。
>
> 16. index.html：设置项目的一些meta头信息和提供`<div id="app"></div>`用于挂载 vue 节点。
>
> 17. package.json：用于 node_modules资源部 和 启动、打包项目的 npm 命令管理。
>
>     https://www.kancloud.cn/hanxuming/vue-iq/728307

5、请你详细介绍一些 package.json 里面的配置

- scripts：npm run xxx 命令调用node执行的 .js 文件
- dependencies：生产环境依赖包的名称和版本号，即这些 依赖包 都会打包进 生产环境的JS文件里面
- devDependencies：开发环境依赖包的名称和版本号，即这些 依赖包 只用于 代码开发 的时候，不会打包进 生产环境js文件 里面。