### for循环的嵌套

#### 1、![image-20200717171754044](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717171754044.png)

```javascript
function xin(){
            document.write("❤");
        }
        for(var i = 0;i < 6;i++){
            for(var j = 0;j < 5;j++){
                xin();
                
            }
            document.write("</br>");
        }
```

![image-20200717171838920](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717171838920.png)

#### 2、![image-20200717171915657](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717171915657.png)

```JavaScript
for(i = 1;i <= 5;i++){
            for(j = 0;j < i;j++){
                xin();
            }
            document.write("</br>");
        }
```

![image-20200717172216011](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20200717172216011.png)

#### 3、循环的跳转continue

默认情况下循环都是依次执行，使用continue可以在特殊情况下进行跳转

