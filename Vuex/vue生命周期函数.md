# vue生命周期函数

![image-20210726104914027](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210726104914027.png)

## 1、beforeCreate：

没有data，只是起步阶段，不能监听数据

## 2、create：

创建了实例，有data数据，可以监听数据变化，但是页面没有加载出来

## 3、el：挂载节点  or mounted

## 4、beforeMount：

看不到页面，准备挂载页面，render函数首次被调用

## 5、mounted：

可以看到页面了，页面挂载完毕

## 6、beforeUpdate：

若有数据被修改，则触发update，beforeUpdata是数据更新之前，也就是虚拟dom打补丁之前

## 7、updated:

数据更新完成，再回到挂载到挂载完毕

## 8、beforeDestroy：

离开当前页面被调用的函数，可以清楚定时器和第三方的dom结构

## 9、destroyed:

实例已经完全被销毁