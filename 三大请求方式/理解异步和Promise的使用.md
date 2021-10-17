# 理解异步和Promise的使用

### 1.什么是异步(asynchronization)

举个生活上的例子来说，晚餐时间到啦，我们先去煮饭，然后把饭入锅了，我们总不能一直在这里一直等到它熟了才去炒菜吧？这样多憨啊（现实中也没人会这样做吧），所以我们设置了电饭煲定时，当饭熟的时候电饭煲就会嘀嘀嘀嘀地响来通知我们。这样我们就不用在这里一直等饭熟了，煮了饭之后我们立马就去炒菜，然后菜煮好了，那边电饭煲也来通知我们饭熟了，然后我们就可以开锅了。这就是一个简单的异步流程，如果放到编程里面，就有同步任务和异步任务之分，Javascript是一个单线程的编程语言，所以会涉及到大量的异步函数，上面的例子，如果煮了饭就一直在这里等饭熟不去做其它事情，就造成了阻塞，我们没法去干其它事情了，直到饭熟了我们才能再去处理其它事情，解决阻塞的方法就是通过异步回调函数。我们煮饭事件就是一个异步事件，然后电饭煲定时通知我们就是一个回调函数，饭熟了我们就能得到通知，然后再回去处理煮饭这个事件，在饭还没熟的过程中，我们就能够去做其它事情了。这就是所谓的异步机制。

### 2.Javascript里的异步有哪些?

最简单的理解，就是需要花时间去等待的事件，只要它不造成阻塞，完成后可以通过回调函数处理，那么它就是异步事件，就比如说`setTimeout`这个函数，让我们看一段代码

```js
(function cook(){
    console.log('开始煮饭')
    //setTimeout里的箭头函数就相当于回调函数
    setTimeout(()=>{
        console.log('饭熟了')
    }，1000)
})()
console.log('开始煮菜')
```

我们用立即执行函数去执行`cook`这个函数，最终在控制台打印出来的结果是 `开始煮饭->开始煮菜->饭熟了` 在这里，我们假设把饭煮熟需要1秒钟的时间。所以`setTimeout`里的饭熟了就需要1秒钟后才能打印出来。 按照我们的正常思维，代码是从上到下执行的，如果`setTimeout`这里的1秒钟，需要我们等待才能去执行下面的开始煮菜，那么它就造成阻塞了,明显做事效率会大大降低。所以这里它需要异步处理，当1秒钟后，就再去执行`setTimeout`箭头函数里的`console.log`('饭熟了') 到这里，你应该对异步有了最基本的了解吧?

------

使用ajax举个例子

```js
console.log('开始请求数据')
$.ajax({
    //假设这是请求数据的接口地址
    url:'xxxx',
    //这里success里的function就是成功拿到数据之后的回调函数
    success: function(data){
        //当成功拿到数据之后才执行
        console.log(data)
    }
})
console.log('去做其他事情')
```

在这里,ajax请求数据也是一个异步方法，最后打印的结果是 `开始请求数据-> 去做其它事情 -> 成功拿到数据...`
异步事件，就是需要点时间去完成，就像下面这样

```js
console.log('开始做第一件事情啦')
setTimeout(()=>{
    console.log('开始做第二件事情啦')
},0)
console.log('开始做第三件事情啦')
```

在这里，打印出来的结果是:

开始做第一件事->开始做第三件事->开始做第二件事

为什么把`setTimeout`设置为0了也不顺序执行呢？（偷偷告诉你们，在浏览器的环境里，`setTimeout`设置为0，在执行的时候也会有4ms的延迟） 所以它还是一个异步事件。 至于为什么，那就要更深入到Js底层的事件循环机制了。到了这里，你有没有对异步事件有了一个比较清晰的理解？

### 3、回调地狱Callback hell

for exmple

```js
setTimeout(()=>{
    console.log('买菜啦')
    setTimeout(()=>{
        console.log('煮菜啦')
        setTimeout(()=>{
            console.log('吃饭啦')
            setTimeout(()=>{
                console.log('洗碗啦')
            },1000)
        },1000)
    },1000)
},1000)
```

我们平时吃饭就是先买菜然后才能煮饭，饭熟了吃了饭之后才能洗碗，也就是说，下一个事件得等上一个事件完成后才能开始。

这段代码会依次打印出来:`买菜啦->煮菜啦->吃饭啦->洗碗啦`
但是看这段代码有没有感觉特别丑？形成了4层的嵌套，如果再多嵌套一点，就会形成了这个符号>一个尖三角
让我们来再看另一个ajax请求数据的例子

```js
  $.ajax({
    url: "xxxxx/api/xxx",
    success: function(data1) {
      console.log('请求到了第一个数据', data1)
      $.ajax({
        url: "xxxxx/api/xxx",
        success: function(data2) {
          console.log('请求到了第二个数据', data2)
          $.ajax({
            url: "xxxxx/api/xxx",
            success: function(data3) {
              console.log('请求到了第三个数据', data3)
            }
          });
        }
      });
    }
  });

```

你可能会问为什么要这样嵌套？因为请求下一个数据需要在上一个数据的基础上做点处理才能继续请求。也就是说，第三个数据的请求依赖于第二个数据，第二个数据的请求依赖于第一个数据，如果也层层嵌套下去，那也形成了回调地狱。按我的理解，一直嵌套下去到了18层就会到达地狱`（嘿嘿嘿，恭喜你到达了地狱，因为每一个嵌套里都可能会有大量的代码，你的代码你以后估计也很难看懂了）` 地狱空荡荡，恶魔在人间，你写了这样的代码别人看了估计也会想打死你这个恶魔（为什么要伤害我们？）

### 4、解决方案（Promise

先说说什么是`Promise`吧。首先说，它是ES6里面的东西，可以优美的解决上面的回调地狱问题。Promise是一个对象，从中文上说，这是承诺的意思，承诺它过一段时间会给你一个结果`（我答应过你的事情我就一定要去做，君子一言，驷马难追，我不会咕咕咕你的啦)`。promise有三种状态：`pending(等待态)，resolved(成功态)，rejected(失败态)`。状态一旦改变，就不会再变。

我们直接上代码，来看看如何去使用Promise吧，首先我们需要先对异步函数来一个Promise封装`（记住了哈: 异步执行的方法都可以封装成Promise)`，拿回上面的例子来说，

```js
/*把setTimeout来封装成一个晚餐(dinner)的函数，
传入事件参数(event)*/
  function dinner(event) {
  /*这里创建一个promise对象，
  成功执行就用resolve返回一个结果，
  如果执行失败则用reject返回结果*/
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(console.log(event))
      }, 1000)
    })
  }

 /* 我们再来看看如何使用这个函数,
   下面有两种调用方式*/

 //第一种调用方式
  dinner('买菜啦')
  .then(dinner('煮饭啦'))
  .then(dinner('吃饭啦'))
  .then(dinner('洗碗啦'))

 //第二种调用方式
  let res1 = dinner('买菜啦')
  let res2 = res1.then(dinner('煮饭啦'))
  let res3 = res2.then(dinner('吃饭啦'))
  let res4 = res3.then(dinner('洗碗啦'))
```

打印结果跟上面的嵌套调用一样`买菜啦->煮饭啦->吃饭啦->洗碗啦`
对比上面的层层嵌套，有没有感觉明了了一点？

我们再拿ajax的例子来做一下Promise封装

```js
  //进行封装
  function myAjax(url) {
    return new Promise((resolve, reject) => {
      $.ajax({
        url: url,
        success: function(data) {
          //成功则返回data数据
          resolve(data)
        },
        error: function(err) {
          //失败则返回错误信息err
          reject(err)
        }
      });
    })
  }

  /*来来来，看好了，
  封装好ajax之后我们就可以去进行请求数据了*/
  let res1 = myAjax('xxxx/api/xxxx');
  let res2 = res1.then(myAjax('xxxx/api/xxxx'));
  let res3 = res2.then(myAjax('xxxx/api/xxxxx'));
```

`res1接收的是第一次请求接口返回的数据，res2依赖于第一次请求的数据，所以用res1.then去请求第二个接口的数据，res3的请求依赖于第二次请求的数据所以用res2.then去请求第三个接口的数据`
假如发生错误的话，就需要捕捉错误，就可以用.catch去捕捉错误信息，类似于这样

```js
  myAjax('xxxx/api/xxxx')
    .then(res => {
    //请求成功执行这里。打印成功返回的数据
      console.log(res)
    })
    .catch(err => {
    //请求失败执行这里。打印错误信息
      console.log(err)
    })
```

到了这里，你理解Promise的基本使用了吗？

然后我们再去说说Promise的另外两个方法`Promise.all`和`Promise.race`
**Promise.all:** 顾名思义，就是一次性请求所有Promise方法
来看看基本使用

```js
 //先进行Promise封装
  function dinner(event) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(event)
      }, 1000)
    })
  }
 
  //用变量去引用Promise方法
  let p1 = dinner('买菜啦')
  let p2 = dinner('煮饭啦')
  let p3 = dinner('吃饭啦')
  let p4 = dinner('洗碗啦')

  //在这里就会一次性执行上面的4个Promise方法
  Promise.all([p1, p2, p3, p4])
    .then(res => {
      console.log(res)
    })
```

我们来看看最后的结果，可以看出来它最后返回的是一个数组。`Promise.all`的方式就是一次性请求，比上面的一个一个链式请求的方式要快一些，如果有发生错误，可以在.then()后面加一个`.catch`来捕捉错误信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200515190225187.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1MDQ4MA==,size_16,color_FFFFFF,t_70)

**Promise.race:** 顾名思义哈，它就是竞赛的意思，哪个异步方法先执行那么就返回它的结果，最后只返回一个结果，而不是一个数组。我们再看一个例子

```js
  function buy(event) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(event)
      }, 2000)
    })
  }


  function eat(event) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(event)
      }, 1000)
    })
  }

  function wash(event) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(event)
      }, 3000)
    })
  }

  let p1 = buy('买菜啦')
  let p2 = eat('吃饭啦')
  let p3 = wash('洗碗啦')

  Promise.race([p1, p2,p3])
    .then(res => {
      console.log(res)
    })
```

在这里，我们假设买菜花了2秒，吃饭花了1秒，洗碗花了3秒，最后结果理所当然是打印出来`吃饭啦`，因为吃饭吃得最快嘛。Promise的最基本使用方式，到这里你理解了吗？

##### 你以为到这里就结束了？别急，还有好东西！继续往下看👇

### 5、最好使用的方式async await

在ES8中，加入了async await的语法，让Promise的调用更加像同步的方式，它比.then这样的方式更加好用，直接上代码，先看看如何使用它吧。 `这段代码可以直接复制到你们编译器去测试！！！`

```js
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>

</body>
<script>
  /*在这里，我们直接用了最外层嵌套立即执行函数，
    并且在函数开头写上async */
  (async function() {
    function buy(event) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(event)
          resolve(event)
        }, 2000)
      })
    }

    function eat(event) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(event)
          resolve(event)
        }, 1000)
      })
    }

    function wash(event) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(event)
          resolve(event)
        }, 3000)
      })
    }

    /*使用await 之前一定要在它最外层的函数前面写上async
    	否则会报错
    */
    let p1 = await buy('买菜啦')
    let p2 = await eat('吃饭啦')
    let p3 = await wash('洗碗啦')
  })()
</script>

</html>


//其实也可以先创建一个Promise函数然后在async函数里面使用await
//such as
const p = function buy(event) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(event)
          resolve(event)
        }, 2000)
      })
}
async function(){
    const p1 = await p('买菜了')
}
```

首先说，买菜花了2秒钟，吃饭花了1秒钟，洗碗花了3秒钟，假如按正常的调用方式`(把里面的 await 都给删掉)`，那最后打印结果就是`吃饭啦->买菜啦->洗碗啦` 因为哪个花的时间少肯定哪个先打印嘛。

但是哈， 一旦你加了 `await`在Promise方法的前面，那么下一个Promise方法的执行就要去`等待上一个Promise方法完成之后`才会执行，就像在这里，最后打印的结果就是`买菜啦->吃饭啦->洗碗啦`，从而达到了我们最初说的，下一个结果的执行依赖于上一个结果。在这里，有没有明显感觉比上面的几种方式都要简洁了许多，而且它更加趋于同步的写法。