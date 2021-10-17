# async与promise链式的异同



**async是promise的语法糖，使用await会获得一个promise类型的返回值**

await必须写在async函数里

```js
//promise的链式写法
const pro = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('resolve');
    },2000)
}).then((res)=>{
    
}).catch(rej=>{
    
})

//async
const as = async function(){
    const aw = await pro //相当于执行了一次链式里的.then()
    /*
    const pro = new Promise((resolve,reject)=>{
    	setTimeout(()=>{
        	resolve('resolve');
    	},2000)
	}).then()
    */
}
```

## 1、两者捕获异常的区别

### 1.1、**promise**：使用then和catch

```js
//第一种捕获异常
const pro = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve("resolve")
    },2000)
}).then(res=>{//成功
    console.log(res)
},rej=>{//失败
    console.log(rej)
}).then()
//then可以有两个参数

//第二种捕获异常是使用catch
const pro = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve("resolve")
    },2000)
}).then(res=>{
    
}).then(res=>{
    
}).catch(rej=>{
    
})
```

### **1.2、async**:使用try...catch

```js
const as = async function(){
    try{
        const aw = await pro
    }catch(err){
        console.log(err)
    }
}
//async只能使用try catch捕获异常，其实promise内部也用了try catch

//其实之前我也疑惑为什么异步的错误会使用同步错误来捕获，但是我师兄给我举了一个函数例子

function err(){
    setTimeout(()=>{
        throw new error("error!!!")
    },0)    
}
const catchErr = function(){
    try{
        err();
    }catch(err){
        console.log(err)
    }
} 
```

