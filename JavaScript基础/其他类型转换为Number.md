### 其他数据类型的转换成数字类型

#### 1、将其他类型转化成Number

```JavaScript
//1.1将字符串转化成number
var message = "123";
var num1 = Number(message);
console.log(num1,typeof(num1));//123,"number"
var str = "hello";
var num2 = Number(str);
console.log(num2,typeof(num2));//NaN,"number"，这里的字符串没法转化成number的值，就是NaN

//1.2将布尔值转化成number
var b1 = true;
var b2= false;
var num3  = Number(b1);
var num4 = Number(b2);
console.log(num3,typeof(num3),num4,typeof(num4));//1,"number",0,"number"

```

![image-20200717094751599](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717094751599.png)

#### 1.3这里需要注意undefined跟null转化成数字类型的区别

**undefined是NaN，null是0**

![image-20200717094812073](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717094812073.png)

#### 2、parseint函数（只能将字符串类型转化成整型）

![image-20200713094608152](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713094608152.png)

```javascript
var str = "123";
var num = parseInt(str);//只能将字符串转化成数字类型，其他数据类型不能
console.log(num,typeof(num));//123 ,number
```

![image-20200713095212232](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713095212232.png)

#### 3、parsefloat函数（只能将字符串类型转化成浮点型）

```javascript
var str = "123.45";
var float = parseFloat(str);
console.log(float,typeof(float));//123.45 ,number
var str2 = "123.45.78";
var float1 = parseFloat(str2);
console.log(float1,typeof(float1));//123.45,number
//从前开始转化自动忽略了后面的.78
```

#### 4、了解parseint的进制转化

![image-20200713101058697](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713101058697.png)

radix是进制的选择

```Javascript
var str = "12345.45";
var num1 = parseInt(str,8);//将str转化为8进制
var num2 = parseInt(str,2);//转化为2进制
var num3 = parseInt(str,16);//转化为16进制
```

