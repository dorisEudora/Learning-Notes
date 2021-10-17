### 转化成Boolean类型

#### ![image-20200713110843626](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713110843626.png)

#### 1、使用Boolean（）函数

```javascript
console.log(Boolean("123"));//true
console.log(Boolean(""));//false
console.log(Boolean(0));//false
console.log(Boolean(null));//false
console.log(Boolean(undefined));//false
console.log(Boolean(NaN));//false
//除了这五种false，其他类型转化都转化成true！
```

![image-20200713111325248](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200713111325248.png)



