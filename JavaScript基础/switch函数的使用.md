### switch分支语句

![image-20200717153952079](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717153952079.png)

![image-20200717154000085](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717154000085.png)

例题1：

```JavaScript
var holiday = prompt("请输入今天的节日")；
switch(holiday){
    case "情人节":
        console.log("买玫瑰，看电影");
        break;
    case "平安夜":
        console.log("买苹果");
        break;
    case "生日会":
        console.log("买蛋糕");
        break;
    default:
        console.log("工作");
}
```

![image-20200717154448734](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717154448734.png)

所以一定要写入break关键字；