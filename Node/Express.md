# Express框架

## 一、什么是Express框架

基于Node的一个框架，封装了读取html文件、css文件的操作，直接返回封装好的内容，让开发人员更加注重业务逻辑。

## 二、安装Express

`npm install express`

## 三、框架的使用

### 1.根据请求地址返回给前端内容

```js
const express = require('express')
//创建网址服务器
const app = express()
//监听端口
app.listen(3000)
//接收请求方法和地址
app.get('/',(req,res) => {
    res.send('hello express')
})
app.post('/index',(req,res) => {
    res.send({
        aa:11
    })
})
```

#### res的send方法：

1. 会检测响应内容的类型,是html，css还是js
2. 会自动设置http状态码,200,304,404,
3. 会自动帮我们设置响应的内容类型和编码即type/html,ype/css,编码是utf8
4. 所以send方法直接返回要返回的内容就行了。

### 2.中间件

- 就是一堆方法，可以接收客户端发来的请求，可以对请求做出响应，也可以将请求交给下一个中间件来处理。
- 可以对同一个请求进行多个中间件处理。
- 但是在默认情况下，请求时从上到下匹配中间件的，一旦匹配成功，就终止匹配，达不到一个请求进行多个中间件处理。
  需要用到获取请求的第三个参数next

```js
app.get('/',(req,res,next)=>{
res.send('hello world')
req.name = '张三'
next()
//只要调用这个next(),下面的获取请求的方法也会执行。
})
app.get('/',(req,res,next)=>{
res.send(req.name)
})
```



### 3.use中间件

```js
app.use((req,res,next)=>{
   next()
})
//参数必须传一个处理函数进去.

//也可以这么写
app.use(fn())
function fn(){
return function (req,res,next){
 next()
}
}
//这样可以在fn()里传一个实参,在匿名函数中调用这个实参

//表示不论请求的地址是什么都会进入这个函数，代表接收所有的请求。
```

也可以**指定请求地址**，第一个参数就是请求地址

```js
app.use('/index',(req,res,next) => {
    next()
})
```


**表示不论什么请求方式，只要请求地址是这个，就会进入方法。**

表示不论什么请求方式，只要请求地址是这个，就会进入方法。

### 4.中间件的作用

#### 1.路由保护

客户端在请求用户登录后才能访问的地址时，可以先用中间件判断用户登录状态，如果没有登录，就拦截请求，禁止用户访问。

```js
const express = require('express')
const app = express()

app.use('/admin',(req,res,next) => {
    let login = false
    if(login){
        next()
    }else{
        res.send('您未登录')
    }
})

app.get('/admin',(req,res,next) => {
    res.send('您已经登录')
})
```

#### 2.网站维护

```js
app.use('/admin',(req,res,next)=>{   
  res.send('网站正在维护')
})
```

#### 3.自定义404页面

在最后写

```js
app.use((req,res,next)=>{   
  res.send('网址未找到')
})
```

这样路由一层一层匹配下来后，没找到就回执行这个。

#### 5.更改状态码

```js
app.use((req,res,next) => {
    res.status(404).send('网址未找到')
})
```

#### 6.捕获错误的错误处理中间件

- **文件读取错误，数据库连接错误**。都需要捕获到，不然不会程序继续的执行。
- 理论是在每个可能出错的地方进行判断，express有一个中间件可以集中处理错误。
- 当程序发生错误时，错误处理中间件就会自动执行。
- 但错误处理中间件只能处理同步错误，异步API的错误需要手动触发

Node.js中有一个内置错误构造函数

```js
app.get('/index',(req,res)=>{
        throw new Error('程序发生错误')//当抛出错误时，会自动执行错误中间件
       })

//错误处理中间件，还是用use
app.use((err,req,res,next)=>{
        res.status(500).send(err.message)
})
//err.message就是错误的内容'程序发生错误'
```

**next带了参数表示要触发错误中间件**

```js
//异步api
app.get('/index',(req,res,next) => {
    fs.readFile('./index.html',(err,data) => {
        //如果读取文件出错，err就会存在
        if(err){
            //手动触发错误中间件，将err传过去。next带了参数表示要触发错误中间件
            next(err)
        }
    })
})

app.use((err,req,res,next) => {
    res.status(500).send(err.message)
})
```

**异步函数要用try catch捕获错误**

```js
//异步函数要用try catch捕获错误
app.get('/index',(req,res，next)=>{
     try{
     await User.find({name:'飞'})//发生错误就会执行catch(err)里的next(err)，触发错误处理中间件
}catch(err){
    next(err)
}
})
app.use((err,req,res,next) => {
    res.status(500).send(err.message)
})
```



### 7.构建模块化路由Router

#### 1.嵌套路由

```js
const express = require('express ')
const app = express()
//创建路由对象
const home = express.Router()
//中间件监听请求地址，匹配路由
app.use('/home',home)

//根路由再监听请求地址，返回子路由
home.get('/index',(req,res,next) => {
    res.send('欢迎来到home下的index页面')
})
```

#### 2.模块化路由

```js
//home.js
const express = require('express ')
const home = express.Router()
home.get('/index'),(req.res)=>{
  res.send('欢迎来到博客首页')
})

module.exports = home
```

```js
//user.js
const express = require('express ')
const user= express.Router()
home.get('/index'),(req.res)=>{
  res.send('欢迎来到用户首页')
})
module.exports = home
```

**多个路由模块合并使用**

```js
//app.js
const express = require('express')
//创建网址服务器
const app = express();
//监听端口
app.listen(3000)
const home = require('./home.js')
const user = require('./user.js')

app.use('./home',home)//访问/home/index时会触发这个中间件
app.use('./user',user)
```



### 8.获取get请求附带的参数

**req.query**就是get请求方式附带的参数，会自动帮我们转成json格式，类比axios请求当中get附带的参数是params

```js
app.get('/index',(req.res)=>{
  //req.query就是get请求方式附带的参数,会自动帮我们转成json对象形式{}
})
```

### 9.获取post请求附带的参数

需要第三方模块插件

req.body是post请求附带的参数，类比axios请求当中post附带的参数是data

`npm i  body-parser -S` 

```js
const bodyParser = require('body-parser')

//false时会使用node的系统模块querystring来获取参数,true时会使用第三方模块的qs方法来获取参数,推荐用querystring
//配置body-parser模块,拦截所有请求
app.use(bodyParser.urlencoded({extended:false}))

app.post('./add',(req,res)=>{
   console.log(req.body)//返回对象形式的参数
   //如果post请求里携带的参数是{id:1,name:doris}
   //获得id
   console.log(req.body.id)
})
```

### 10.路由占位参数

- 占位参数理解可以用拼接请求路径来理解，一般get请求携带参数，比如/index/id=xxx

- 但是拼接请求路径可以是/index/xxx

- 占位符的效果与拼接路由是一样的，其实就是对应拼接请求路径的请求

```js
/**
:id是一个占位符
此时路由可以也称http://localhost/home/123
:id就对应着123
不用写成http://localhost/home?name =123的形式了
可以在:id后面接着写/:name/:age
id,name,age就是对象的属性
*/

app.get('/find/:id',(req,res)=>{
    // req.paramse就是{id:123}

})
```

### 11.静态资源访问功能

```js
app.use(express.static(静态文件绝对路径))//http:127.0.0.1/public/img/0.1jpg
```

写了之后就开启了静态服务,浏览器可以直接根据地址来访问静态资源.
也可不写真实的路径,指定一个虚拟的路径来访问.

```js
app.use('statc',express.static(静态文件绝对路径))//http:127.0.0.1/statc/public/img/0.1jpg
```


这也就需要在路径上,再加一层statc

### 12.express的模板引擎engine

还是使用art-template
但是art-template在express的基础上又进行了一次封装.比原生的node配合art-template简洁一点
**安装**

`npm install art-template express-art-template`
要把art-template和express-art-template都安装起来.

#### 1.告诉express用的什么模板引擎

```js
const express = require('express')
const app = express();
//表示要渲染后缀为art文件时,使用express-art-template模板引擎
//告诉框架使用什么模板引擎渲染什么后缀的模板文件
//第一个参数是模板的后缀,第二个参数是模板的引擎
//express框架可以使用多个模板引擎
app.engine('art',require('express-art-template'))
```

#### 2.设置模板存放的目录

```js
//第一个参数是固定的views,是express固定的配置,第二个views是文件夹的名字,
//第二个参数是位置,path模块需要引入
app.set('views',path.join(__dirname,'views'))
```

#### 3.渲染模板时不写后缀,默认拼接后缀

```js
//view engine是默认模板的配置项
app.set('view engine','art')
```

#### 4.响应客户端

**render**方法执行了多个操作:

1. 拼接了模板的路径
2. 拼接了模板的后缀
3. 将模板和数据连接
4. 直接将结果返回给数据,都不要使用send了

4.响应客户端

```js

app.get('/',(req,res)=>{
 //第一个参数是模板的名字,第二个参数是模板的对象
 	res.render('index',{
    	msg:'hello'
	})
})


```

模板是

```js
//index.art
{{msg}}

```


将index模板返回给客户端,render方法会自动帮我们拼接好文件

### 13.locals对象

将变量设置到app.locals对象下,这个数据在所有的模板中都可以获取到

```js
app.local.user = [
    {
        name :'张三',
        age:18
    },
    {
        nam:'李四'
        age:20
    }
]
   
```




```js
//index.art
//可以直接使用这个user
{{user}}

```

这样在所有模板中都能访问这个数据,不必在res.render的第二个参数中访问这个数据了

这样在所有模板中都能访问这个数据,不必在res.render的第二个参数中访问这个数据了

### 14.jsonp方法

```js
app.get('/get',(req,res)=>{
    const  fnname = res.query.callback//获取jsonp传递过来的方法名
    const data = JSON.stringify({name:'飞'})
    const  result = fnname+"(" + data + ")"
    res.send(result )
})
//这是正常接受处理jsonp传递过来的方法

app.get('/get',(req,res)=>{
    res.jsonp({
     //这里放的是返回给浏览器的参数
})
//jsonp方法能自动帮我们处理
})
```

jsonp方法会帮我们自动接受处理jsonp请求，我们只需要传递参数即可。

### 15.设置跨域响应头

```js
app.get('./cross',(req,res)=>{
    // 设置请求头为允许跨域
    res.header('Access-Control-Allow-Origin', '*');
    // 设置服务器支持的所有头信息字段
    res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With , yourHeaderFeild, sessionToken');
    // 设置服务器支持的所有跨域请求的方法
    res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS');
```

