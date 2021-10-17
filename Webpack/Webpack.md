## Webpack的使用

### 一、搭建webpack环境

1. node环境

2. 全局安装webpack

   > npm install webpack webpack-cli -g
   >
   > webpack -v

3. 项目内安装webpack

   > npm install webpack webpack-cli --save--dev

### 二、webpack处理核心loader

#### （1）、使用loader打包静态资源（图片等）

##### file-loader

**将文件发送到输出文件夹，并返回（相对）URL**

```js
//webpack.config.js
module.exports = {
    module:{
        rules:[
        	{/*
        	*处理文件类型较多:
        	/\.(可选文件后缀名|文件后缀名...)$/
        	*/
            	test:/\.(jpg|png|gif)$/,
         		use:{
                	loader:'file-loader',
                	options:{
                    	name:'[name]_[hash].[ext]',//ext:文件后缀名，输出的时候文件名为name_hash值.后缀
                    	outputPath:'images/' //打包之后文件输出到的文件路径、下面会具体解释
                    	limit: 2048 //文件限制大小
                	}
            	}
        	}        
    	]
    }    
}
```

##### url-loader

像 file loader 一样工作，但如果文件小于限制，可以返回 data URL；图片大于limit，会生成对应文件

```js
//webpack.config.js
module.exports={
    module:{
        rules:[
            {
            	test: /\.jpg$/,
            	use:{
                loader:'url-loader',
                options:{
                    name: '[name]_[hash].[ext]',
                    outputPath: 'images/',
                    limit:2048
                	}
            	}
        	}
    	]
    }    
}
```



#### （2）、使用loader打包静态资源（样式）

##### 1、使用loader处理css文件或ts文件

1. 概念：webpack使用loader来预处理文件https://v4.webpack.docschina.org/concepts/loaders，loader用于对模块的源码进行转换。loader可以使你在`import`或`加载`模块时预处理文件

2. 例如可以使用loader告诉webpack加载css文件，或者将typescript转为js，首先先安装相应的loader

   1. `npm install --save-dev css-loader`     
   2.  `npm install --save-dev ts-loader`    
   3. 然后在webpack的全局文件中进行配置

   ```js
   //webpack.config.js
   module.exports={
       module:{
           rules: [
               {test: /\.css$/,use:'css-loader'},
               //固定格式：/\.文件后缀名$/  匹配文件处理文件
               {test: /\.ts$/,use:'ts-loader'}
           ]
       }
   }
   ```

   **webpack配置当中允许多个loader使用，loader从右到左（从后往前）执行**

   3.打包css使用多个loader

   ```js
   //webpack.config.js
   module.exports={
       module:{
          rules:[
              test:/\.css$/,
              use:['style-loader','css-loader']
       	] 
       }
   }
   ```



##### 2、打包scss

例如：下列执行顺序：`sass-loader=>css-loader=>style-loader`

**原理**：sass-loader将sass文件解析后。使用import导出然后css-loader解析导出的模块，解析后导出，style-loader在解析导出，导出的模块作为样式添加到DOM中

```js
module.exports={
    module:{
        rules:[
            {
                test:/\.css$/,
                use:[
                    {loader:'style-loader'},
                    {loader:'css-loader',options:{modules:true}},
                    {loader:'sass-loader'}
                    
                ]
                //简写use
                use:[
                'style-loader',
                'css-loader',
                'sass-loader'
                ]
            }
        ]
    }
}
```



- loader还有内联的使用，但是一般不采用内联（内联是对import的模块进行处理）
- loader还有cli的使用，也不常用

##### 3、使用postcss-loader

> PostCSS利用js的强大的编程能力对css代码进行转换。数以百计的postcss插件可以用来为css属性添加特定于浏览器厂商的前缀，支持未来css语法、模块化、代码检测等

`npm insatll postcss-loader autoprefixer -D`

在项目根目录新建postcss.config.js,配置使用的插件

```js
module.exports = {
    plugins:[require('autoprefixer')]
}
```

在打包之后的css自动添加浏览器厂商前缀，如：-webkit-transform

##### 4、css-loader配置

在scss中引入scss时，这种情况会导致新引入的scss不会走之前的loader，我们要使用**importLoaders**

- 查询参数`importLoaders`，用于配置[`css-loade`r作用于@import的资源之前]有多少个loader

- css模块化modules：true

  ```js
  const path = require('path')
  
  module.exports = {
      module:{
          rules:{
              test: /\.scss$/,
          	use:[
              	'style-loader',
              	{
                  	loader:'css-loader',
                  	options:{
                      	importLoaders:2,
                      	modules:true
                  	}
              	},
              	'sass-loader',
              	'postcss-loader'
          	]
          }
      }
}
  ```
  
  

##### 5、打包字体图标

使用file-loader当作静态资源打包

```js
rules:[
    {
        test:/\.(eot|ttf|svg|woff)$/,
        use: 'file-loader'
    }
]
```



### 三、plugins(插件）让打包更加快捷

plugins可以在webpack运行到某个时刻自动帮我们做一些事情

**插件**是 webpack 的 [支柱](https://github.com/webpack/tapable) 功能。webpack 自身也是构建于，你在 webpack 配置中用到的**相同的插件系统**之上！

#### HtmlWebpackPlugin

> HtmlWebpackPlugin简化了html文件的创建，以便为你的webpack包提供服务。对于在文件名中包含每次会**随着编译而变化的哈希的webpack bundle** 尤其有用。你可以让插件为你生成一个html文件，使用lodash模板提供你自己的模板，或使用你自己的loader

作用：

HtmlWebpackPlugin会在打包结束后，自动生成一个html文件，并把打包生成的js自动引入html中

`npm install --save--dev html-webpack-plugin`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')//通过npm安装
const webpack = require('webpack')//webpack内置插件
const path = require('path')

module.exports = {
    entry:'./src/index.js',
    output:{
        filename:'my-webpack.bundle.js',
        path: path.resolve(_dirname,'dist')
    },
    module:{
        rules:[
            {
                test:/\.(js|jsx)$/,
                use: 'bable-loader'
            }
        ]
    }
    plugins:[
    	new HtmlWebpackPlugin({
        	template:'src/index.html' //设置模板
    	}),
        new Webpack.ProgressPlugin() //内置插件
	]
}
```

#### CleanWebpackPlugin

打包删除上次打包结果

`npm install clean-webpack-plugin`

```js
const CleanWebpackPlugin = require('clean-webpack-plugin')

pulgins:[
    new CleanWebpackPlugin()
]
```

### 四、Entry与Output的基础配置

#### （1）entry：入口起点

**入口起点(entry point)**指示 webpack 应该使用哪个模块，来作为构建其内部 *[依赖图(dependency graph)](https://v4.webpack.docschina.org/concepts/dependency-graph/)* 的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

默认值是 `./src/index.js`，但你可以通过在 [webpack configuration](https://v4.webpack.docschina.org/configuration) 中配置 **entry** 属性，来指定一个（或多个）不同的入口起点。



```js
module.exports = {
    entry:{
    	a: './src/index.js',
    	b: './src/index.js'
	},
}

```

#### （2）output：输出

**output** 属性告诉 webpack 在哪里输出它所创建的 *bundle*，以及如何命名这些文件。主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。

你可以通过在配置中指定一个 `output` 字段，来配置这些处理过程：

```js
module.exports = {
    entry:{
    	a: './src/index.js',
    	b: './src/index.js'
	},
	output:{
    	//公共路径，不知道最终输出文件的 publicPath 是什么地址，则可以将其留空
    	publicPath: 'http://cdn.com.cn',
    	//输出文件：template string 占位符
    	filename: '[name].js',
    	//输出路径
   		path: path.join(_dirname,'dist')
	}
}
```

在上面的示例中，我们通过 `output.filename` 和 `output.path` 属性，来告诉 webpack bundle 的名称，以及我们想要 bundle 生成(emit)到哪里。可能你想要了解在代码最上面导入的 path 模块是什么，它是一个 [Node.js 核心模块](https://nodejs.org/api/modules.html)，用于操作文件路径。

### 五、SourceMap的配置

> 此选项控制是否生成，以及如何生成 source map。
>
> 选择一种 [source map](http://blog.teamtreehouse.com/introduction-source-maps) 格式来增强调试过程。不同的值会明显影响到构建(build)和重新构建(rebuild)的速度

sourceMap是一种映射关系，从打包后的文件，找到对应映射的源代码的位置

[devtool](https://webpack.docschina.org/configuration/devtool/)

`devtool： 'source-map'`

可以生成：bundle.js 和 bundle.js.map

### 六、使用WebpackDevServer

每次修改完文件，我们都需要重新打包，刷新网页，效率低

#### （1）做法一：watch监听文件修改

```json
//package.json中
"scripts": {
    "bundle": "webpack",
    "watch": "webpack --watch"
  },

//或者webpack.config.js中
module.exports = {
    //.....
    watch: true
}
```

#### （2）watchOptions：一组用来定制 watch 模式的选项

```js
//webpack.config.js
module.exports = {
    //...
    watchOptions:{
        aggregateTimeout:200,
        poll: 1000，
        ignored: /node_modules/  // 或者 'node_modules/**'
    }
}
```

**aggregateTimeout**：当第一个文件更改，会在重新构建前增加延迟。这个选项允许 webpack 将这段时间内进行的任何其他更改都聚合到一次重新构建里。以毫秒为单位

**ignored**：对于某些系统，监听大量文件会导致大量的 CPU 或内存占用。可以使用正则排除像 `node_modules` 如此庞大的文件夹,可以忽略一个或多个

多个：

```js
//webpack.config.js
module.exports = {
    //...
    watchOptions:{
        aggregateTimeout:200,
        poll: 1000，
        ignored: ['files/**','node_modules/**']
    }
}
```

**poll**：通过传递 `true` 开启 [polling](https://whatis.techtarget.com/definition/polling)，或者指定毫秒为单位进行轮询。

简单就是：定时间段就检查代码是否更新

```js
//webpack.config.js
module.exports = {
    //...
    watchOptions:{
        aggregateTimeout:200,
        poll: 1000，//每秒检查一次变动
        ignored: ['files/**','node_modules/**']
    }
}
```

#### （3）做法二：使用devServer：**提供一个本地开发服务器**

通过 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 的这些配置，能够以多种方式改变其行为。这是一个简单的示例，利用 `gzips` 压缩 `dist/` 目录当中的所有内容并提供一个本地服务(serve)：

`npm install webpack-dev-server -D`

```js
//webpack.config.js
const path = require('path')

module.exports = {
    //...
    devServer:{
        contentBase: path.join(__dirname,'dist') //或者 './dist', 
        compress:true, // 为每个静态文件开启 gzip compression
        port: 8080,
        open: true
    }
}
```

**contentBase**:告诉服务器内容的来源。仅在需要提供静态文件时才进行配置.建议使用绝对路径。

**compress:**为每个静态文件开启 [gzip compression](https://betterexplained.com/articles/how-to-optimize-your-site-with-gzip-compression/)

**port:**指定端口号以侦听以下请求

**open:**告诉 dev-server 在服务器启动后打开浏览器。 将其设置为 `true` 以打开默认浏览器。

devServer的更多属性功能：https://webpack.docschina.org/configuration/dev-server/#devserverport

#### （4）derServer.proxy：vue react脚手架都以此实现

！使用后端在 `localhost:3000` 上，可以使用它来启用代理：

```js
module.exports = {
    //...
    devServer:{
        proxy:{
            '/api': 'http://localhost:3000'
        }
    }
}
```

现在，对 `/api/users` 的请求会将请求代理到 http://localhost:3000/api/users。

如果不希望传递`/api`，则需要重写路径:

```js
module.exports = {
    //...
    devServer:{
        //...
        proxy:{
            '/api':{
                target: 'http://localhost:3000',
                pathRewrite:{ '^/api' : '' },
                //默认情况下，将不接受在 HTTPS 上运行且证书无效的后端服务器。 如果需要，可以这样修改配置:secure
                secure: false
            }
        }
    }
}
```

### 七、热模块替换 Hot Module Replacement

场景：我们修改css只会替换css修改的内容，方便调试

使用热模块之前通过热模块插件 [`HotModuleReplacementPlugin`](https://webpack.docschina.org/plugins/hot-module-replacement-plugin)

```js
const webpack = require('webpack')

module.exports = {
    //....
    devServer:{
        contentBase: './dist',
        hot: true,
        hotOnly:true
    }
    plugins:[
    	//使用热模块插件
    	new webpack.HotModuleReplacementPlugin()
    ]
}
```

**accept：**接受(accept)给定 `依赖模块(dependencies)` 的更新，并触发一个 `回调函数` 来响应更新

js手动HMR，accept方法：

```js
if(module.hot){
    //触发回调函数
    module.hot.accept('./number',() => {
 document.boby.removeChild(document.getElementById('number'));
        number();
    })
}
```

### 八、使用babel处理es6语法

babel-loader是webpack loader在打包过程中处理

@babel/core是babel核心库

`npm install --save-dev babel-loader @babel/core`
`npm install @babel/preset-env --save-dev`

```js
//webpack.config.js
module.exports = {
    //...
    module: {
        rules:[
            test: /\.js$/,
            exclude: /node_modules/,
            use:{
            loader: 'babel-loader',
            options:{
            	presets:[ '@babel/preset-env' ]
            	}
            }
        ]
    }
}
```

到此，我们只能处理部分es6语法。但是Promise 数组的 map方法等都不能处理，我们还需要**@babel/polyfill**

`npm install --save @babel/polyfill`

在index.js中引入

```js
import '@babel/polyfill'
```

`useBuiltIns`配置低版本的特性按需引入

`target`设置目标浏览器，判断是否需要引入低版本不支持的特性，从而减少体积

```js
//webpack.config.js
module.exports = {
    //...
    module: {
        rules:[
            test: /\.js$/,
            exclude: /node_modules/,
            use:{
            loader: 'babel-loader',
            options:{
            	presets:[ 
            		'@babel/preset-env'，
            		{
            			useBuiltIns: 'usage',
        				targets: {
          					edge: '17',
	      					firefox: '60',
	      					chrome: '67',
                            safari: '11.1'
            			}
					}
        		]
            }
            }
        ]
    }
}
```

