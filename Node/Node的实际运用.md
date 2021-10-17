## Nodejs

### 一、认识Node.js

- **Node.js既不是一门语言,也不是一个库或框架,而只是一个构建在Chrome的V8引擎上的一个JS的运行环境**,可以解析和执行JS代码,在以前只能在浏览器中解析执行JS代码,而现在可以完全脱离浏览器来运行

- **在Node.js的中的JS没用DOM和BOM,只有ECMAScript和额外为JS提供的一些服务器级别的API**（原生js由ECMAscript、DOM、BOM构成）
- **Node.js使用事件驱动（I/O),异步模型,具有轻量高效的特点**
- **Node.js使用npm进行包管理,绝大多数JS相关的包都存放在npm上,**这样做的目的是为了让开发人员更方便去下载使用

#### **1.1、node与浏览器的异同**

- 架构一样,都是基于事件驱动的异步架构
- 浏览器主要是通过事件驱动来进行服务器交互
- node主要是通过事件驱动来服务(I/O),进行用户间的数据交互

#### 1.2、node当中的模块

![image-20210321110407048](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321110407048.png)

### 二、node的作用

#### 2.1 执行JS脚本文件

**在命令行的父目录中使用node 文件名的方法解析和运行JS脚本文件**

**注意:**JS的文件名不能node.js,不然不会执行该脚本,而是会直接打开该文件

**详细的Node源码分析查看笔记Node源码分析**



### 三、npm包管理器

#### 1、安装插件

- vscode一个插件：live serve，然后点击html文件右键，

![image-20210320175350258](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320175350258.png)

![image-20210320175635564](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320175635564.png)

- 装typings：安装：`cnpm i typings -g` 初始化：  `typings init`
- nodemon：`cnpm i nodemon -g`
- typings是为了写node有提示,提示语法和功能、模块，有一些提示
- nodemon执行js文件，可以进行热更新

#### 2、npm操作（这里讲的cnpm，同理

- 安装 cnpm i xxx -S

比如安装jQuery：cnpm i jquery -S    安装vue：cnpm i vue -S

- 卸载cnpm un XXX -S

cnpm un jquery -S         cnpm un vue -S

- 安装指定版本的库

cnpm i jquery@3.4.1 -S   //这里安装的就是3.4.1版本的jquery

#### 3、命令行的后缀-S，-B

- -S：运行依赖
- -D：开发依赖

类比env.development  env.production

- -g：全局安装,一个文件引用一个模块，会先从同级文件中开始寻找，依次往上级中查找，最高级就是全局-g

  ![image-20210320184155023](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320184155023.png)

#### 4、在node环境中运行js

注意两点：

1. 写的js模块要放在node_modules文件夹下面

   ![image-20210320183144580](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320183144580.png)

2. js文件的命名是index.js的话，可以在引用模块的时候不用这样写demo4/index.js，如果命名不是index，则要写详情路径，比如demo4/demo.js

   

   1. index文件内容示例：![image-20210320183427596](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320183427596.png)

   1. 引用文件示例：![image-20210320183528150](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320183528150.png)or![image-20210320183548526](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320183548526.png)


### 四、配置文件package.json

举例如图：

dependencies：对应-D

![image-20210320185330867](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320185330867.png)

**package.json显示项目中需要或者已经下载的模块和工具**，执行npm install  或者cnpm install 就会在node_modules中有相关的模块和插件

![image-20210320185558700](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210320185558700.png)



### 五、模块module

#### 1、模块是写入module.exports当中的

#### 2、举例如图：

- 在demo1.js当中引入了demo4下的index模块（require引用模块

- 同时demo1.js当中书写了b、Person类并且导出

**（想要引用一个模块内容，必须要导出作为一个模块）**

`module.exports = {}`

![image-20210321163722547](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321163722547.png)

- 在demo2.js当中引用demo1.js当中的模块

![image-20210321110204059](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321110204059.png)

- 这里创建类的时候，不是使用Person而是person，因为在demo2.js中给person赋值Person，**不能直接使用demo1.js里的**

（**注意require的路径写法**，nodejs源码分析当中有分析

- 运行demo2.js![image-20210321162430688](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321162430688.png)



### 六、文件夹操作、路径path

#### 1、__dirname：当前文件所属文件夹的路径

#### 2、__filename：当前文件的路径

```js
//demo2.js
let demo1 = require('./demo1')
let person = demo1.Person
console.log(new person().sayHi())

let path = require('path')//node的基础模块，处理路径问题

console.log(__dirname)//当前文件所属文件夹的路径
console.log(__filename)//当前文件的路径

console.log(path.resolve('./'))
```

- 执行demo2.js

![image-20210321164025744](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321164025744.png)

- 执行`console.log(path.resolve('./'))`之后，这里的**./**指的是执行当前命令行运行的路径
- **因为我现在是在firstclass下进行命令行操作**，所以`console.log(path.resolve('./'))`会打印出的内容是如图最下面一行内容

![image-20210321164557035](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321164557035.png)

#### 3、path的api

[path模块api](http://nodejs.cn/api/path.html)

- **path.basename(' '):获取一个路径的文件名(默认包含扩展名)**

- **path.dirname(' '):获取一个路径的目录部分**

- **path.parse(' '):把一个路径转为对象**

  **对象属性：**

  - root:根路径

  - dir:目录

  - base:包含后缀名的文件名

  - ext:后缀名

  - name:不包含后缀名的文件名

  - ```js
    const path = require('path')
    const pathObj = path.parse(__filename)
    console.log(pathObj)
    /*
    {
      root: 'E:\\',
      dir: 'E:\\fore-end\\课堂资源\\nodejs班\\first class',
      base: 'demo2.js',
      ext: '.js',
      name: 'demo2'
    }
    */
    ```

- **path.join(''):**当**需要进行路径拼接**的时候,推荐使用这个方法
- **path.isAbsolute(''):**判断一个路径是否是绝对路径

### 七、文件操作fs

**浏览器当中的js（日常写的js）是没有操作文件的能力的，但是node当中的js是有操作文件的能力**

- fs是file-system的简写,就是文件系统,在node中如果想进行文件操作,就要引入fs这个核心模块,这个核心模块中提供了所以文件相关的API操作

#### 1、主要功能

nodejs当中操作文件都有同步和异步的方法，加了**Sync**的就是异步操作

- 写入文件

  ```js
  const fs = require('fs')
  fs.writeFile('1.txt', 'hello nodejs', err => {
      /*
  		如果文件写入成功,error是null
  		如果文件写入失败,error是错误对象
  	*/
      if (err) {
          console.log(err)
      } else {
          console.log('写入成功')
      }
  })
  /*
  	第一个参数:文件路径
  	第二个参数:文件内容,可以在文件里面写入任何的数据类型,所以数据都会被隐式转换为字符串
  	第三个参数:options选项,包括写入文件的方式(这个参数可以不写)
  	第四个参数:回调函数
  */
  
  //系统默认覆盖文件，如果要追加，就要写option
  ```

  ![image-20210321172601894](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321172601894.png)

  - 系统默认覆盖文件，如果要追加，就要写option
  - 文件内容追加：

  ```js
  //a表示append，追加
  fs.writeFile('1.txt', 'hello nodejs',{ flag: 'a' }, err => {
      if (err) {
          console.log(err)
      } else {
          console.log('写入成功')
      }
  })
  ```

  

- 异步写入文件

  ```js
  const fs = require('fs')
  fs.writeFileSync('1.txt', 'hello nodejs', err => {
      if (err) {
          console.log(err)
      } else {
          console.log('写入成功')
      }
  })
  ```

  

- 读取文件

  ```js
  /**
  第一个参数是要读取的文件路径
  第二个参数是options选项,包括度入文件的编码(这个参数可以不写)
  第三个参数是回调函数,这里回调函数有两个参数err与data
  **/
  fs.readFile('1.txt', 'utf-8', (err, data) => {
      if (err) {
  		console.log(err)
      } else {
          console.log(data)
      }
  })
  
  //读取文件时不管里面是什么文件,得到的都会是Buffer格式,除非在第二个参数写入了文件编码
  ```

  ![image-20210321174209591](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321174209591.png)

  - 这种读取方式只适合内容少的文件

- **读写流操作**

  ```js
  let fs = require('fs')
  let rs = fs.createReadStream('a.txt');//流体式打开一个文件
  let ws = fs.createWriteStream('b.txt');//流体式写入一个文件
  
  rs.resum();//通过该方法可以让可读流中的数据流动,否则不会进行读取操作
  
  //也可已通过绑定data事件让可读流运动
  rs.on('data',(chunk) => {
      //chunk就是读取到的数据,通过可写流可以直接写入可读流读取的数据
      ws.write(chunk)
  })//通过这样的方法复制文件是通过两根管子进行复制的
  
  /*直接使用pipe函数可以只通过一个函数进行复制
  rs.pipe(ws);
  */
  rs.on('end',() => {//全部读取完成后才会执行该方法
      console.log('读取完成')
  })
  rs.on('error',()=>{
      //....传输失败的操作
  })
  
  
  //完整操作
  var fs=require('fs')
  var rs = fs.createReadStream("a.txt")
  var ws = fs.createWriteStream("b.txt")
  
  //resum与on两者之间选一个
  //rs.resum();
  rs.on('data',(chunk)=>{//chunk是读一次传输的数据
      ws.write(chunk);
  })
  rs.on('end',()=>{
      console.log("读取完成")
  })
  
  rs.on('error',()=>{
      //....
  })
  ```

  ![image-20210321213936621](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321213936621.png)

- 删除文件

  ```js
  //使用fs.unlink删除文件,只能是一个文件，如果是文件夹必须是空文件夹
  let fs=require("fs");
  fs.unlink('1.txt',err => {
      //....
  })
  fs.unlinkSync("");
  ```

  

- 读取文件名

  ```js
  //,....
  fs.rename('1.txt','2.txt', err => {
      //....
  })
  ```

  

- 复制文件

  ```js
  //一次性读一次性写
  fs.copyFile('1.txt','2.txt', err => {
      //.....
  })
  ```

- 创建文件夹

  ```js
  //第一个参数是文件名，第二个参数是回调函数
  fs.mkdir('test', err =>{
      
  })
  ```

- 读取一个文件夹

  ```js
  fs.readdir('test',(err,data) => {
      if(err){
          
      }else{
          console.log(data)//获得一个数组，当前文件夹下内容目录，如下图
      }
  })
  ```

  ![image-20210321181601834](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321181601834.png)

  打印内容：

  ![image-20210321181546259](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321181546259.png)

- 删除文件夹

  ```js
  //非空文件夹可直接删除，非空文件夹不可以直接删除
  fs.rmdir('test',err =>{
  
  })
  ```

  

- **删除非空文件夹**

  ```js
  //使用递归调用删除目录
  function removeDir(path) {
      let data = fs.readdirSync(path);//获取目录下的所有文件信息（是数组
      data.forEach(item => {
          let url = path + '/' + item //获取每个文件的绝对路径
          let state = fs.statSync(url)//判断文件夹状态
          if (state.isDirectory()) {//判断是否为文件夹
              removeDir(url)
          } else {
              fs.unlinkSync(url)
          }
      })
      fs.rmdirSync(path)//文件夹下的文件都删完了可以删除文件夹了
  }
  
  removeDir('test')
  ```

  ![image-20210321213459247](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210321213459247.png)



### 七、爬虫

安装插件：express、cheerio（像使用jquery一样）、url



### 八、http请求

nodejs就是允许js代码能够像后端的语言那样，然后http请求可以使js写的后端数据可以让前端代码使用axios（或ajax等）访问使用，就像前后端请求一样

![image-20210328102427243](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210328102427243.png)

#### 1、原始方法写：

1. 客户端：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!--引入vue和axios -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
</head>
<body>
    <div id="app">
        <button @click='getMethod()'>请求后端</button>
    </div>
    <script>
        let vm = new Vue({
            el:'#app',
            methods:{
                getMethod(){
                    axios.get('http://localhost:5500/index').then(res => {
                        console.log(res)
                        if(res.success){
                            console.log(res)
                        }else{
                            res.$message.error(res.message);
                        }
                    })
                }
            }
        })
    </script>
</body>
</html>
```



1. 服务端：

```js
const http = require('http')//网络请求
const url = require('url')//获取客户端请求地址信息
const fs = require('fs')//文件操作
const path = require('path')//路径相关

//第一个参数req是请求报文，第二个参数res是响应报文
const server = http.createServer((req, res) => {
    let urlObj = url.parse(req.url)//将客户端请求的url字符串转成对象
    
    //解决跨域
    res.setHeader("Access-Control-Allow-Origin", "*")//允许任何端口的请求
    res.setHeader("Access-Control-Allow-Origin", "X-Requested-With")
    res.setHeader("Access-Control-Allow-Origin", "PUT,POST,GET,DELETE,OPTIONS")
    
    
    if (urlObj.pathname === '/' || urlObj.pathname === 'index') {//根据请求地址做相应的信息
        
        res.end('ok')//返回内容
    } else {
        res.end('error!')
    }
})

server.listen(3000)//设置并启动端口
```

#### 2、使用express框架

1. 安装express：**cnpm i express -S**  或  **npm i express -S**
2. express解决了判断问题

```js
const express = require('express')
let app = express()

app.all("*", function (req, res, next) {
    
    //解决跨域问题
    res.header("Access-Control-Allow-Origin", "*")//允许任何端口的请求
    res.header("Access-Control-Allow-Origin", "X-Requested-With")
    res.header("Access-Control-Allow-Origin", "PUT,POST,GET,DELETE,OPTIONS")
    res.header("X-Powered-By", "3.2.1")
    res.header("Content-Type", "application/json;charset=utf-8")
    next();
})

//客户端是get
app.get('/index', (req, res) => {
    res.end('ok')
})

app.listen(3000)
```

