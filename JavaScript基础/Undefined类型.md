### 掌握Undefined和Null的使用

1、undefined（未定义）：当一个变量进行了声明，但是没有赋值，这个时候他的值就是undefined

```javascript
var flag；
console.log(flag);
```

![image-20200712110700557](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200712110700557.png)

![image-20200712110746004](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200712110746004.png)

2、null（空）

```javascript
var info = {name : "why", age : 18};
info = null;
```

![image-20200712110858345](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200712110858345.png)

![image-20200712111003982](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200712111003982.png)

null方便释放空间，当info不使用之后赋值null可以释放它所占内存