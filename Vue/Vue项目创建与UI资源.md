# Vue项目创建打包与UI资源

## 1.使用vue-cli创建Vue项目

### 1.1 vue-cli脚手架

**vue-cli是一个基于vue的构建工具,用于搭建vue项目的环境,有着兼容,方便,快速的优点,能够完全遵循前后端分离的原则,用vue开发单网页项目(SPA)的能力尤其的好**

**注:**可以不用脚手架(vue-cli)就可以基于 webpack 打包工具 ,webpack最终会把整个项目打包成一个js文件但需要自己进行配置各个版本兼容问题,正因为这样,前端有一个专门的配置工程师



### 1.2 下载Node.js

**去Node.js的官网下载最新版的node,需要用到其包管理工具npm** ([Node.js官网](https://nodejs.org/en/))



### 1.3 配置淘宝镜像

**因为npm是国外的,在国内用会特别慢,所以需要先用淘宝的cnpm代替npm**

在命令行窗口输入   ` npm install -g cnpm --registry=https://registry.npm.taobao.org`   配置淘宝镜像



### 1.4  安装vue-cli（4.x）

#### **已经安装好了不需要再次安装**

在命令行窗口输入 `cnpm i -g vue-cli` 全局安装vue-cli脚手架

或者执行`npm i -g @vue/cli`

如果你想指定版本安装：`npm install -g vue-cli@版本号`

**注:**安装完成后可以使用 `vue -V` 查看是否安装成功

### 1.5 安装项目文件，选择插件

1. `vue create vue-demo` vue-demo是项目名，自取

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406162551380.png)

   - 我这里选择的手动配置

   ![img](https://img-blog.csdnimg.cn/20210406162617913.png)

2. 手动配置features

   - 上下键移动，空格选择或取消插件
   - 常用选择：babel、PWA、router、vuex、css
     ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406163145661.png)
   - 如果你想选择ts书写代码，选择typescript
   - **如果选择features选择错误，`Ctrl+c`重新created 项目**

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406162856113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N3ZWV0X2Nvb2tpZQ==,size_16,color_FFFFFF,t_70)

3. 选择以后回车，继续操作

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406163320349.png)

   - 一般不会保存为将来项目的预设，选择n，有需要选y

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406163632818.png)

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406163757144.png)

   ![image-20210722102026842](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210722102026842.png)

4. 继续enter

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040616384399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N3ZWV0X2Nvb2tpZQ==,size_16,color_FFFFFF,t_70)

### 1.6 运行项目文件

> 涉及npm还是yarn，具体看项目搭建后的following commands
>
> ![image-20210407161110110](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210407161110110.png)

- cd 项目 //进入项目
- npm：`npm run serve`
  yarn: `yarn serve`
- 成功之后

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040616463151.png)

### 1.7 打包项目

在项目文件夹中运行 `npm run build` 将项目打包 ,打包后的文件将会保存在该文件的ldst文件夹中

**注意**:要点击打开dist中的`index.html`,需要修改两个地方

- 找到 ` config->index.js`

  ![img](https://img-blog.csdn.net/20180410235639689?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzA4OTkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- 找到`build->utils.js`

  ![img](https://img-blog.csdn.net/20180411000014630?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzA4OTkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





## 2.UI资源

- **ElementUI**
  **介绍：**Element，一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的桌面端组件库
  **链接：**https://element.eleme.cn/#/zh-CN

- **Mint UI**
  **介绍：**基于 Vue.js 的移动端组件库
  **链接：**https://mint-ui.github.io/#!/zh-cn

- **Mui**
  **介绍：**最接近原生APP体验的高性能前端框架
  **链接：**http://dev.dcloud.net.cn/mui/ui/

- **Aui 框架**
  **介绍：**使用了大量弹性响应式布局，采用容器+布局结构+控件的嵌套形式,借鉴了市场上其他优秀UI框架
  **链接：**http://www.auicss.com/

- **we-vue**
  **介绍：**使用 Vue2.x + weui1.x 开发的组件
  **链接：**https://github.com/tianyong90/we-vue

- **Vue-Layout**
  **介绍：**vue可视化布局
  **链接：**https://jaweii.github.io/Vue-Layout/dist/#/

- **muse-ui**

  **链接：**https://muse-ui.org/#/zh-CN/tabs

## 3.Vite快速构建vue项目

1. npm init @vitejs/app <project name>
2. cd <project name>
3. npm install
4. npm run dev

## 4.创建vue项目

### 4.1.main.js文件

main.js文件是vuecli默认的入口文件，全局组件注册、插件的引入、router的注册都在这里

### 4.2.组件嵌套：

1. 全局调用组件

   1. 在main.js里引入组件

   2. 使用vue.component("",xx)注册全局组件

   3. 在其他页面直接调用全局组件

      ```js
      //1.引入组件
      import Users from "./components/Users";
      
      //2.注册全局组件
      Vue.component("users", Users);
      ```

2. 局部调用组件

   1. 在某一页面引入要使用的局部组件

   2. 注册局部组件

      ```js
      //1.引入组件
      import users from "./components/Users.vue";
      //2.注册局部组件
      export default {
        name: "app",
        components: {
          users,
        },
      };
      ```

      

### 4.3.组件css样式

#### scoped

作用：由于组件嵌套，可能会引起样式之间的相互影响，为了不使组件之间样式的混杂，在style标签里加入一个`scoped`保证样式的独立性，这样就算有组件嵌套也不会互相影响css

