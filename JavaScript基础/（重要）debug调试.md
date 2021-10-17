### 断点调试，debug

![image-20200718215424186](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718215424186.png)

#### 1、console.log

开发中最常用的检查代码的方法，不过会相对繁琐

#### 2、断点调试

<img src="C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718212812306.png" alt="image-20200718212812306" style="zoom:50%;" />

在source中查看源码，然后在每一行进行**点击**就会形成断点

![image-20200718213021360](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718213021360.png)

![image-20200718213031943](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718213031943.png)



**形成断点之后就只能执行到断点处**![image-20200718214109541](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718214109541.png)

##### 2.1、单步执行![image-20200718214157528](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718214157528.png)

只执行到断点下一行，利用这个进行一步一步的调试



##### 2.2、跳过本次断点![image-20200718214212212](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718214212212.png)

将从断点处跳过两个断点间的内容，从一个断点直接跳跃到另外一个断点

![image-20200718214343603](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718214343603.png)

如图从30跳到33



##### 2.3、跳入函数![image-20200718214459729](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718214459729.png)—>step into function

##### 2.4、跳出函数![image-20200718214745968](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200718214745968.png)

#### 3、代码中写debugger

**优点：便于在过多的代码中查找需要debug的地方**

```javascript
var str = "1234";
for (var i = 0;i <10;i++){
    console.log(i);
}

debugger;//这里打了一个断点

let num = 1;
while(num < 11){
    num += 2;
}
```

