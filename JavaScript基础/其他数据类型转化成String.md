### 转化成字符串类型的数据转化方式

#### 1、变量.toString()

```javascript
var num = 123;
var str = num.toString();
```

![image-20200713103402950](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713103402950.png)

**不适用于null与undefined**

#### 2、String（变量）

```javascript
var num = 1234;
var str = String(num);
```

![image-20200713110234792](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713110234792.png)

#### 3、字符串进行拼接（隐式转化）

![image-20200713110401286](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713110401286.png)

```javascript
var num = 12;
var b = true;
var obj = null;
console.log(num + "");
console.log(b + "");
console.log(obj + "");
console.log(flag + "");
```

