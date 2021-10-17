### JavaScript的函数基础

- 面向对象编程：java类->对象/C++
- 面向过程编程：C语言
- 函数式编程：lisp（天才程序员会使用的语言-黑客与画家-人工智能）scheme/JavaScript
- JavaScript：函数式编程/面向对象编程（基于对象）——函数是js最重要和最主要的部分

#### 一、函数的概念：某段代码的封装，完成某一个功能

步骤：

##### 1、定义函数——封装独立的功能

![image-20200720094729772](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200720094729772.png)

##### 2、调用函数——享受封装的成果

![image-20200720094747378](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200720094747378.png)

##### 3、作用：![image-20200720094828736](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200720094828736.png)



#### 二、定义、调用函数

function 函数名（）{

封装函数的代码

}

![image-20200720095125813](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200720095125813.png)

**调用了函数之后，封装的函数代码才能执行；**

#### 三、函数参数传递

```
function 函数名(参数1，参数2，参数3，....){
	封装的代码可以直接使用参数，名字为参数名
};
```

```JavaScript
//name,age,sex是形参
function printf(name,age,sex){
    console.log(name);
    console.log(age);
    console.log(sex);
};

//"kiki",12,"女"是实参
var print1 = printf("kiki",12,"女");
var print2 = printf("tony",18,"男");
```

![image-20200720095821288](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200720095821288.png)

#### 四、形参与实参![image-20200720095932874](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200720095932874.png)