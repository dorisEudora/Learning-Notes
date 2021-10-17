### **arguments参数**

1、简单的函数传参

```javascript
function sum(num1, num2){
	return num1 + num2;
}
var sum = sum(20, 30);//sum = 50
```

2、传了多个参数

```JavaScript
function sum(num1, num2){
	return num1 + num2;
}
var total = sum(20,30,40,50,60);
console.log(total);//50
```

#### 3、arguments对象：在函数中

![image-20200721110313259](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200721110313259.png)

可以理解为：**将传入的函数参数作为一个数组对象来进行调用**

![image-20200721110415871](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200721110415871.png)

##### arguments的使用方法与数组一样

```javascript
function sum(num1, num2){
	return num1 + num2;
    console.log(arguments);
    console.log(arguments[2]);
}
var total = sum(20,30,40,50,60);
//arguments:[20,30,40,50,60]
//arguments[2]:40
console.log(total);//50
```

##### 4、练习题

4.1求参数和

```javascript
function newSum(){
    var sum = 0;
    console.log(arguments);//打印arguments对象
    for(var i = 0; i < arguments.length;i++){
        console.log(arguments[i]);//打印每个参数
        sum += arguments[i];
    }
    return sum;
}
console.log(newSum(10,20,30,40,50));
```

![image-20200721110938155](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200721110938155.png)