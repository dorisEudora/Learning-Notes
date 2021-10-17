# fetch请求操作

请求接口模拟，[地址为](http://jsonplaceholder.typicode.com/)

## 1.todos请求

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>fetch</title>
    <link href="../style.css" type="text/css" />
    <!-- 引入vue-->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
</head>

<body>
    <h1>fetch</h1>
    <div id="fetch-app">
        <div v-for="item in todos">
            <h4>{{item.title}}</h4>
            <p v-if="item.completed">{{item.completed}}</p>
        </div>
    </div>
</body>
<script src="app.js" type="text/javaScript"></script>
</html>
```

app.js  [请求地址](http://jsonplaceholder.typicode.com/todos)

```js
new Vue({
    el:"#fetch-app",
    data(){
        return{
            todos:[]
        }
    },
    mounted(){
        // fetch api请求
        this.getFetch()
    },
    methods:{
        getFetch(){ fetch('http://jsonplaceholder.typicode.com/todos').then(res =>{
                // console.log(res)
            
            //对返回的数据进行处理并返回，固定写法
                return res.json()
            }).then(todos => {
                // console.log(todos)
                this.todos = todos
            })
        }
    }
})
```

## 2.post请求

```js
fetch("http://jsonplaceholder.typicode.com/todos", {
    method: "POST",
    //fetch的body数据都要为字符串
    body: JSON.stringify(this.todo),
    headers: {
        "Content-type": "application/json"
    }
}).then(res => {
    return res.json()
}).then(todo => {
    console.log(todo)
    //this.todos.unshift(todo)
})
```

## 3.get请求

```js
/*
第一种
*/
fetch("http://jsonplaceholder.typicode.com/posts/1").then(res => {
    return res.json()
}).then(todo => {
    this.todos.unshift(todo)
})

/*
第二种
*/
fetch(`http://jsonplaceholder.typicode.com/comments?postId=${postId}`).then(res => {
    return res.json()
}).then(todo => {
    console.log(todo)
})
```

