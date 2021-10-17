### if—else语句

例题1：![image-20200717105049144](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717105049144.png)

```javascript
var score = prompt("小明输入分数");
if(score > 90)
    {
        console.log("去游乐场玩");
    }else if(score > 80){	//不需要再写&&score<=90,因为判断从上到下进行，如果大于80，在第一处就执行了
        console.log("玩手机游戏一天");
    }else if(score > 60){
        console.log("优先做家庭作业");
    }else{
        console.log("天天上补习班");
    }
        
```

例题2：![image-20200717105634002](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717105634002.png)

```javascript
var holidday = prompt('请输入今天的节日');

//使用严格相等
if(holidday === "情人节"){
    console.log("买玫瑰，看电影");
}else if(holidday === "平安夜"){
    console.log("买苹果，吃大餐");
}else if(holidday === "生日会"){
    console.log("买蛋糕，开party");
}else{
    console.log("上班工作");
}
    
```

