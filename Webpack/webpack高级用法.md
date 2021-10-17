## webpack高级用法

### 1、使用configureWebpack简单的配置

```js
//vue.config.js
module.exports = {
    //.....
    configureWepack:{
        pulgins:[
            new MyWesomeWebpackPlugin()
        ]
    }
}
```

该对象将会被webpack-merge合并入最终的webpack配置。

如果你需要基于环境有条件的配置行为，或者想要直接修改配置，那就换成一个函数（该函数会在环境变量被设置之后懒执行）。该方法的第一个参数会收到已经解析好的配置。在函数内，你可以直接修改配置，或者返回将会被合并的对象

```js
modlue.exports = {
    configureWepack: config => {
        if(process.env.NODE_ENV === 'production'){
            //为生产环境修改配置
        }else{
            //为开发环境修改配置。。。
        }
    }
}
```

### 3、用chainWepack做高级配置

vue-cli内部的webpack是通过webpack-chain维护的。这个库提供了一个webpack原始配置的上层抽象，使其可以定义

