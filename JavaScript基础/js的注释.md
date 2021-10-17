### js的注释

1、单行注释

```javascript
//
```

2、多行注释

```javascript
/*
*/
```

3、文档注释（对自己写的某个函数或者某个对象进行的注释内容）

```java
/**
*
*/
```

**特别适用于多人开发，交流封装工具函数，这样就不需要重新看代码进行查看代码功能**

```javascript
/**
*这是一个测试函数
*在下次引用的时候可以在提升中发现这个文档注释
*/
function test(){
    
}
test();//在这里使用的时候，会有一个提示，如下图顺序操作
```

![image-20200711102738842](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200711102738842.png)

![image-20200711102751503](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200711102751503.png)