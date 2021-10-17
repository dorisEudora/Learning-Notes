# vue当中使用echarts的简单可视化

echarts做可视化数据视图很优美直观，echarts[的官网有示例的代码](https://echarts.apache.org/examples/zh/index.html)

## 一、全局引入echarts

npm install echarts --save

## 二、单页面当中使用局部echarts

//editTaskItem.vue

```vue
<template>
<!-- echart需要操作dom来实现-->
	<div>
        <div rel="myEcharts" style=""></div>
    </div>
</template>
<script>
    const echarts = require('echarts')
    export defualt{
        data(){
            
        },
        mounted(){
            this.setMyEcharts()
        },
        methods:{
           setMyEcharts(){
               let mychart = echarts.init(this.$rels.myEcharts)
               let option = {
                   //x轴
                   xAxis:{
                       type:'category',
                       //x轴上的值
                       data:[]
                   },
                   //y
                   yAxis:{
                     type:'value',
                     //可以不设置y的值，若不设置y轴上的值，会使用echarts默认的         
                     data:[]
                     
                   },
                   series:[{
                       //x轴上每个对应的值
                       data:[],
                       type:'bar',
                       showBackground:true,
                       backgroundStyle:{
                           color:'xxx'
                       }
                   }]
               }
               mychart.setOption(option)
           }     
        },
        
    }
</script>
<style>
</style>
```

上面的写法不够完善，会出现一种情况：当dom元素还未加载的时候，option就获取元素，echarts.init（）就开始执行了，所以会出现`Initialize failed: invalid dom`

### 解决dom未加载出echarts开始初始化

```vue
<template>
<!-- echart需要操作dom来实现-->
	<div>
        <div rel="myEcharts" style=""></div>
    </div>
</template>
<script>
    //引入echarts
    const echarts = require('echarts')
    export defualt{
        data(){
            
        },
        mounted(){
            this.setMyEcharts()
        },
        methods:{
           setMyEcharts(){
               let pr = new Promise(resolve =>{
                   resolve()
               })
               pr.then(()=>{
                   let mychart = echarts.init(this.$rels.myEcharts)
                   //......
                   mychart.setOption(option)
               })                     
           }     
        },        
    }
</script>
<style>
</style>
```

办法很简单，就是一个异步操作

## 三、定时刷新数据

使用定时器，定时获取数据，并且渲染

```vue
<template>
<div>
    <div rel="mycharts" style="">
        
    </div>
    </div>
</template>
<script>
    const echarts = require('echarts')
    export defult{        
        data(){
            //假设x，y，x对应的data都要变
            x:[],
            inputData:[],
             y:[],
        },
        methods:{
            getInputData(){
                //从后端获取数据
                getAction('xxxx').then(res =>{
                    if(res.success){
                        this.x = res.x,
                        this.y = res.y,
                        this.inputData = res.data
                        this.setMycharts()
                    }else{
                        this.$messgae(res.error)
                    }
                })
            },
            setMycharts(){
                let pr = new Promise(resolve =>{
                    resolve()
                })
                pr.then(()=>{
                    let mycharts = echarts.init(this.$rels.mycharts)
                    let option ={
                        xAxis:{
                            type:'category',
                            data:this.x
                        },
                        yAxis:{
                            type:'value',
                            data:this.y
                        },
                        series:{
                            type:'bar',
                            data:this.inputData
                        }
                    }
                    mycharts.setOption(option)
                })
            }
        },
        mounted(){
            //定时器
            this.timer = setInterval(this.getInputData,10000)
        },
        beforeDestroy(){
            //销毁定时器
            clearInterval(this.timer)
        }
    }
</script>
```

## 四、x轴多条数据解决x轴标签文字过多导致显示不全/x轴旋转/x轴显示完全

echarts x轴标签文字过多导致显示不全/x轴旋转/x轴显示完全
这主要涉及两点：

#### 1.x轴**rotate** 设置

下面直接放在echarts官网的的普通柱状图实例里，调整就明白了。注意对比去掉grid和axisLabel的不同效果。

##### interval：

坐标轴刻度标签的显示间隔(在类目轴中有效)，默认会采用标签不重叠的方式显示标签（默认会将部分文字显示不全），可以设置为0强制显示所有标签，如果设置为1，表示隔一个标签显示一个标签，如果为3，表示隔3个标签显示一个标签，以此类推

##### rotate：

标签倾斜的角度，在类目轴的类目标签显示不全时可以通过旋转防止标签重叠旋转的角度是-90到90度

> 问题又来了，这个名称x轴的文字如果太长会受到遮挡，还是显示不全，这个时候可以用grid属性解决

```js
option = {
    xAxis: {
        type: 'category',
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat3333', 'Su332'],
        axisLabel: {
      	rotate: 45
    	}
    },
    yAxis: {
        type: 'value'
    },
    series: [{
        data: [120, 200, 150, 80, 70, 110, 130],
        type: 'bar'
    }]
};
```

#### 2.解决办法1：**grid**设置距下端有距离

旋转以后，文字如果名字太长就会被截断，这时设置axisLabel的height与lineHeight都没任何作用，这时就需要grid来解决

```js
option = {
    xAxis: {
        type: 'category',
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat3333', 'Su332'],
        axisLabel: {
      		rotate: 45
    	}
    },
      grid: {
      	left: "3%",
      	top: 130,
      	right: 60,
      	bottom: 50,
      	containLabel: true,
    },
    yAxis: {
        type: 'value'
    },
    series: [{
        data: [120, 200, 150, 80, 70, 110, 130],
        type: 'bar'
    }]
};
```

结果如图：

![image-20210714135837269](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210714135837269.png)

#### 3、解决办法2：调用formatter文字竖直显示

axisLabel中使用**formatter**回调，formatter有两个参数，使用方法是这样的formatter:function(value,index){} ，value是类目（x轴上的data）,index 是类目索引。      

```js
  axisLabel: {
      interval: 0, 
      formatter:function(value){
      return value.split("").join("\n");
      }
  }
```

**这样的结果可以使文字都变成竖直的**：

![image-20210714135739108](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210714135739108.png)

## 五、webpack版本

经过我的失败，我发现打包echarts需要版本更高的，需要>webpack4.1.0版本以上的

**重新安装webpack需要先卸载之前的：**

npm unstall webpack

npm install webpack@4.1.0 --save-dev

提醒：别安装最新版本的webpack，若自己原先的版本较低（比如我这次是3.8，语法跟最新版有差别，就会出问题，出现版本冲突）

## 六、各种图，series的type属性

- **type: 'bar'**：柱状/条形图
- **type: 'line'**：折线/面积图
- **type: 'pie'**：饼图
- **type: 'scatter'**：散点（气泡）图
- **type: 'effectScatter'**：带有涟漪特效动画的散点（气泡）
- **type: 'radar'**：雷达图
- **type: 'tree'**：树型图
- **type: 'treemap'**：树型图
- **type: 'sunburst'**：旭日图
- **type: 'boxplot'**：箱形图
- **type: 'candlestick'**：K线图
- **type: 'heatmap'**：热力图
- **type: 'map'**：地图
- **type: 'parallel'**：平行坐标系的系列
- **type: 'lines'**：线图
- **type: 'graph'**：关系图
- **type: 'sankey'**：桑基图
- **type: 'funnel'**：漏斗图
- **type: 'gauge'**：仪表盘
- **type: 'pictorialBar'**：象形柱图
- **type: 'themeRiver'**：主题河流
- **type: 'custom'**：自定义系列