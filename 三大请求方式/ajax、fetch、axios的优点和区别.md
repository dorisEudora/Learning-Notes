# Ajax、fetch、axios的优点和不同

### 1.背景

前端的技术发展速度非常的快，异步请求也是其重要的体现之一，从最早的原生XHR，再到JqueryAjax的统治时代，再到近来，fetch、axios等技术也开始出现并大量投入使用。

### 2.原生Ajax：

Ajax是指一种创建交互式网页应用的网页开发技术，并且可以做到无需重新加载整个网页的情况下，能够更新部分网页，也叫作局部更新。

### 3.XMLHttpRequest对象：

使用ajax发送请求是依靠一个对象，叫`xmlhttprequest`对象，通过这个对象我们可以从服务器获取到数据，然后渲染到我们的页面上。现在几乎所有的浏览器都有这个对象，只有IE7以下没有，而是通过`activexobject`这个对象来创建的

| 方法                           | 描述                                                        |
| ------------------------------ | ----------------------------------------------------------- |
| abort()                        | 停止当前请求                                                |
| getAllresponseHeaders()        | 把http请求的所以响应的首部作为键值对返回                    |
| getResponseHeaders()           | 返回指定响应的首部的串值                                    |
| open(method,url)               | 建立对服务器的调用，还有3个可选参数，是否异步，用户名、密码 |
| send(content)                  | 向服务器发送请求                                            |
| setRequestHeader(header,value) | 把指定首部设置为所提供的值                                  |
|                                |                                                             |

#### 3.1、Ajax使用步骤：

1. 创建XMLHTTPRequest对象
2. 调用open方法设置基本请求信息
3. 设置发送的数据，发送请求
4. 注册监听的回调函数
5. 拿到返回值，对页面进行更新

#### 3.2、Ajax的get/post请求：

```html
<!-- get -->
<script>
    //步骤一:创建异步对象
    var xhr = new XMLHttpRequest();
    //步骤二:设置请求的基本参数
    xhr.open('get','test.php');
    //步骤三:发送请求
    xhr.send();
    //步骤四:注册onreadystatechange监听事件，只要状态改变就会调用
    xhr.onreadystatechange = function(){
        //步骤五 如果能够进到这个判断 说明数据完美到手
        if(ajax.readystate == 4 && ajax.status == 200){
            //操作返回内容
            console.log(ajax.responseText)
        }
    }
</script>
```

```html
<!-- post -->
<script>
    //创建异步对象
    var xhr = new XMLHttpRequest();
    //设置请求基本信息，并加上请求头
    xhr.setRequestHeader("Conten-type","application/x-www-form-urlencoded")
    xhr.open('post','https://www.baidu.com/');
    //发送请求
    xhr.send();    
    xhr.onreadystatechange = function(){
        //这步判断为服务器是否正确
        if(ajax.readystate == 4 && ajax.status == 200){
            console.log(ajax.getResponseHeaders())
        }
    }
</script>
```

##### onreadystatechange:监听函数，是来监听readystate这个状态值

| 状态值 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| 0      | 请求还未初始化，还未调用open()                               |
| 1      | 请求已经建立但未发送，还未调用send()                         |
| 2      | 接受原始响应数据，为解析做准备                               |
| 3      | 正在解析数据，根据响应头部返回的mime类型把数据转化为responseText等形式存取的格式 |
| 4      | 响应完成，数据解析完成                                       |

### 4.jQueryAjax：

其实Jquery的ajax就是在原生的ajax的基础上进行了封装，其实没有太多好说的东西，不过加上了对`JSONP`的支持，附上例子：

```vue
<script>
    var loginBtn = document.getElementsByTagName("button")[0];
    loginBt.onclik = function(){
        ajax({
            type:"post",
            url:"test.php",
            data:"name = lan&pwd = 12345",
            success:function(data){
                console.log(data)
            }
        })
    }
</script>
```

ajax方法里面需要传入一个对象参数，里面包括了一些常见的参数，其实jquery维护了这么多年了，ajax请求其实已经很方便了，如果非要说有什么缺点的话，就是依然对回调嵌套不方便，然后就是如果为了一个JqueryAjax就引入整个jquery，文件比较大，成本过高。

### 5.Fetch：

Fetch是ajax非常好的一个替代品，很有可能将来会完全代替ajax的地位。我们先来看下浏览器的支持情况：

![img](https://img-blog.csdnimg.cn/20181130112153453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NDA3ODc1,size_16,color_FFFFFF,t_70)

#### 5.1、fetch的写法：

不管是原生的Ajax还是Jquery封装的Ajax都有一个问题就在于回调地狱，fetch很友好的解决了这个问题，fetch大概长这个样子：

```js
fetch(...).then(fun2).then(fun3).then(fun4)....catch(fun)
```

> 类型promise的链式

它给人一种同步的流程来书写非同步的操作，成功的解决了回调地狱的问题。Fetch能做到这一点，是因为Fetch API是基于`Promise`设计的。并且fetch调用非常简单，因为它是挂在BOM上的，属于全局的方法。

#### 5.2、fetch获取数据：

我们使用fetch来获取数据时，会返回给我们一个promise对象：

```vue
<script>
    fetch("http://www.abc.cn/test").then(
        response => {
            console.log(response)
        }
    )
</script>
```

输出：

![img](https://img-blog.csdnimg.cn/20181130114622494.png)

OK就是true，status是200，我们可以看到这里面并没有我们想要的数据，其实数据都在body属性里面，是一种数据流的形式，一般服务器会给我们返回JSON数据的格式，我们可以调用response.json来转化下数据：

```vue
<script>
    fetch("http://www.abc.cn/test").then(response =>{
        response.json()
    }).then(data => console.log(data))
</script>
```

处理之后，我们继续.then就可以拿到处理之后的数据了。所以这么来看的话通过Fetch来获取数据是非常简洁简单的。

#### 5.3、fetch发送数据：

fetch(url,options)

第二个参数会传入一个对象来配置请求的信息。下面先看一个例子：

```vue
<script>
    fetch("http://www.mozotech.cn/bangbang/index/user/login",{
        method:'post',
        headers:{
            'Content-type':'application/x-ww-form-urlencode'
        },
        body: new URLSerchParams([["name","lan"],["age",18]])
    }).then(res =>{
        console.log(res)
        return res.json()
    }).then(data =>{
        console.log(data)
    })
</script>
```

fetch默认的post的header是`Content-type：text/plain;charset=UF-8,`不过通常我们的post请求是通过表单orjson的形式提交的，所以我们需要把header修改为：`Content-Type：application/www-form-urlencoded`   or `Content-Type：application/json`

#### 5.4、Cookie传递：

fetch发送请求时，不像XHR，因为它默认是不带上Cookie的，如果站点依赖于维护，所以我们需要Cookie带上，只需要加上`credential:'include'`

```vue
<script>
    fetch('https://www.abc.com/search',{
        method:'GET',
        credentials:'include',
        headers:{
            'Content-type':'application/www-form-urlencode'
        }
    }).then(res => {
        return res.text()
    }).then(data => {
        console.log(data)
    })
</script>
```

#### 5.5、Fetch优缺点

| 优缺点 | 描述                                           |
| ------ | ---------------------------------------------- |
| 优点   | 解决回调地狱                                   |
| 优点   | 使用起来更加简洁                               |
| 缺点   | API 偏底层，需要封装                           |
| 缺点   | 默认不带Cookie，需要手动添加                   |
| 缺点   | 浏览器支持情况不是很友好，需要第三方的ployfill |

### 6、Axios:

其实axios也是对原生XHR的一种封装，不过是Promise实现版本。
它是一个用于浏览器和 nodejs 的 HTTP 客户端，符合最新的ES规范。简单看下如何使用即可

```vue
<script>
    axios({
       method:'post',
        url:'/abc/login',
        data:{
            userName:'lan',
            password:'123'
        }
    }).then(res => {
        console.log(res)
    }).catch(err => {
        console.log(err)
    })
</script>
```

#### 6.1、**`axios`的特点**

1. 支持浏览器和node.js
2. 支持promise
3. 能**拦截**请求和响应
4. 能转换请求和响应数据
5. 能**取消**请求
6. **自动转换JSON数据**
7. 浏览器端支持**防止CSRF**(跨站请求伪造)

#### 6.2、如何将`axios`异步请求同步化处理?

```js
async getHistoryData(data){
    try{
        let res = await axios.get('/api/suvery/list',{
            params:data
        })
        this.tableDta = res.data.result
        this.totalDta = res.data.count
    }catch(err){
        console.log(err)
        alert('请求出错！')
    }
}
```



## 总结

其实有这么多种的请求方法和解决方案，总结一下：原生XHR几乎很少开发会用，JqueryAjax属于老当益壮的那种，虽然很老，但是很好用，Fetch是属于初生牛犊，还需要慢慢成长，axios就目前来说，算是非常好的了，无脑使用即可