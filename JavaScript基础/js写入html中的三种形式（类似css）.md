### js写入html中的三种形式（类似css）

1、直接在元素或者标签中写入js代码

![image-20200711094815887](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200711094815887.png)

```html
<a href = "" onclick = "alert('helloWorld')">弹出弹框</a>
<a href = "javascript:alert('helloworld')">弹出弹框</a>
```

非常不推荐这种写法，但是需要了解，类似css写入标签中

2、书写到script标签中（在学习js阶段可以这样操作）

![image-20200711095148883](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200711095148883.png)

```html
<scrpit>
    alert('helloworld');
</scrpit>
```

同时注意script标签一般放到body的底部

3、外部js文件的引入

![image-20200711095307480](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200711095307480.png)

```html
<scrpit src = "./js/test.js"></scrpit>
```

```javascript
alert('helloworld');//test.js
```

外部js文件编写内容，采用相对路径进行引入js文件到html文件中，在实际的项目开发中采用这类方法

同时文件也是采用script标签引入