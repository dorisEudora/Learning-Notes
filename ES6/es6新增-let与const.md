### ES6新增语法

#### 1、let关键字：变量只要遇到大括号就会形成作用域

> let声明的作用域叫块级作用域，更小
>
> var声明的作用域叫局部作用域

##### 1.1、let的作用域非常的严谨和小

```javascript
//var
for(var i = 0;i < 5;i++){
    setTimeout(function(){
        console.log(i);
    },4000)
}//打印结果4，4,4,4,4
console.log("end");

//let
for(let i = 0;i < 5;i++){
    setTimeout(function(){
        console.log(i);
    },4000)
}
//打印结果0,1,2,3,4
console.log("end");
```

结果不同的原因：

var没有块级作用域，所以当使用延时setTimeout函数，i已经全部执行变成了4，所以输出的i都是4；![image-20200721201513800](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200721201513800.png)

但是let有块级作用域，每个循环都是一个作用域，五个循环，形成五个作用域，延时之后访问，只能对应的访问，i的值分别是0,1,2,3,4，即输出是

![image-20200721201527239](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200721201527239.png)

> `所以for`循环的计数器，就很合适使用`let`命令。
>
> 另外，使用let在`for`循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。

##### 1.2、let不存在变量提升

```javascript
//var的情况
console.log(foo);	//输出undefined
var foo = 2;

//let的情况
console.log(bar);	//报错ReferenceError
let bar = 2;
```

##### 1.3、暂时性死区

> 只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响

```javascript
var temp = 123;

if(true){
    temp = 'abc';
    let temp;//ReferenceError
}
```

> ![image-20200722204745259](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200722204745259.png)
>
> **ES6 明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在`声明`之前就使用这些变量，就会报错**。
>
> 在`let`命令声明变量`tmp`之前，都属于变量`tmp`的“死区”

##### 1.4、不允许在相同作用域内重复声明一个变量

#### 2、const

#####   2.1、const声明变量：变量值只能在声明的时候确定，后续是没有办法修改的

> const声明变量不能更改

```JavaScript
const IP = "10.28.55.578";
console.log(IP);
IP = "xx";
console.log(IP);	//报错，因为这是const类型，不能更改
```

报错：![image-20200721202422525](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200721202422525.png)

##### 2.2、`const`的作用域：

> **与`let`命令相同，只在声明所在的块级作用域内有效**

##### 2.3、暂时性死区

> `const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用

##### 2.4、const存储数据的实质：

> **对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了**

**将一个对象声明为常量必须非常小心**

```javascript
const a = [];
a.push('hello');//可执行
a.length = 0;//可执行
a = ['kik'];//报错

//上面代码中，a是一个常量数组，这个数组本身可写，但是如果将另外一个数组赋值给a，就会报错

//下面类似情况
const foo = {};

foo.prop = 123;//为foo添加属性
console.log(foo.prop);

//将 foo 指向另外一个对象就会报错
foo = {};
```

