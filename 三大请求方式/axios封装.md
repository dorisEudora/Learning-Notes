# 封装axios

封装优点：

1. 方便后期代码的维护
2. 减少代码量，减少不必要的重复请求响应
3. 通过配合async await，让代码更加优雅

## 1、创建axios实例，设置baseURL、请求头

```js
import axios from 'axios'
import vue from 'vue'

const service = axios.create({
    baseURL:'https://192.0801.563',//请求地址前缀
    //请求头，一般请求头不会改变，初始配置就可以设置
    headers:{
        get:{
            'Content-Type':'application/x-www-form-urlencoded;charset=utf-8',
            //若设置单点登录or其他功能也可以一起配置
        },
        post:{
            'Content-Type':'application/json; charset=utf-8',
             //若设置单点登录or其他功能也可以一起配置
        }
    }
})
```

## 2.跨域、超时、响应码处理

1. 是否允许跨域属性：withCredentials
2. 配置超时时间属性：timeout
3. 定义http状态是resolve或者reject promise：validateStatus(){}

```js
const service = axios.create({
    withCredentials:true,
    timeout:3000,
    //方便async..await模式处理reject
    validateStatus:function(){
        //使用async-await，处理reject情况较为繁琐，所以全部返回resolve，在业务代码中处理异常
        return true
    }
})
```

正常情况http响应码是2or304就是resolve状态，其余为reject状态，然后我们在业务代码里reject是使用try。catch来捕获和处理异常

但是在async..await当中（很多时候都会使用），捕获catch异常非常麻烦，这个时候就借助validateStatus，**将所有的状态都抛给resolve，统一在then里处理**

## 3.请求、响应处理

1. **transformRequest**：允许在服务器发出请求前，修改请求数据
2. **transformResponse**：允许在then/catch之前，修改响应数据

```js
const service = axios.create({
    transformRequest:[function(data){
        //处理请求数据
        data = JSON.stringify(data)//such as改为json格式数据
        return data
    }],
    transformResponse:[function(data){
        //处理响应数据
        if(typeof data == 'string' && data.startsWith('{'))				{//将接送字符串改为JSON
            data = JSON.parse(data)
        }
            
        return data
    }]
})
```

## 4.拦截器

1. 请求拦截器、响应拦截器
2. 在then、catch处理之前做业务处理

之前使用validateStatus，将所有的状态都给了resolve，导致**在请求出错的时，数字协议当中的msg字段（消息）没有**，所有我们需要**手动添加msg**

### 4.1请求拦截器

**很少需要**在请求拦截器中做额外处理的需求，所以，请求拦截器的 resolve 状态，只需直接返回就可以了。

**但是一般情况业务处理，请求拦截器会设置请求头携带token**

headers.Authorization

```js
service.interceptors.request.use((config)=>{
     // confing.url可拿到当前页面路径，出去登陆页面，其它的页面携带token
    if(config.url !== '\login'){
        let token = localStorage.getItem('token')
        config.headers.Authorization = token
    }
    return config
},(err)=>{
    //错误抛到业务代码
    err.data = {}
    err.data.msg = '服务器异常，请联系管理员！'
    return Promise.resolve(err)
})
```

**注意！**：这里error是抛给了resolve，是**针对async-await形式**的使用，一般then、catch操作时抛给reject处理（所有的error都应该如此，如下也是这样的原则）

### 4.2.响应拦截器

- 手动判断状态码来设置msg（消息）字段

```js
// 根据不同的状态码，生成不同的提示信息
const showStatus = (status) => {
    let message = ''
    // 这一坨代码可以使用策略模式进行优化
    switch (status) {
        case 400:
            message = '请求错误(400)'
            break
        case 401:
            message = '未授权，请重新登录(401)'
            break
        case 403:
            message = '拒绝访问(403)'
            break
        case 404:
            message = '请求出错(404)'
            break
        case 408:
            message = '请求超时(408)'
            break
        case 500:
            message = '服务器错误(500)'
            break
        case 501:
            message = '服务未实现(501)'
            break
        case 502:
            message = '网络错误(502)'
            break
        case 503:
            message = '服务不可用(503)'
            break
        case 504:
            message = '网络超时(504)'
            break
        case 505:
            message = 'HTTP版本不受支持(505)'
            break
        default:
            message = `连接出错(${status})!`
    }
    return `${message}，请检查网络或联系管理员！`
}
```

- 响应拦截器，http错误处理

```js
service.interceptors.response.use((res)=>{
    let status = res.status
    let msg = ''
    if(status < 200 || status >= 300){
        // 处理http错误，抛到业务代码
        msg = showStatus(status)
        if(typeof res.data == 'string'){
            res.data = {msg}
        }else{
            res.data.msg = msg
        }
    }
},(err)=>{
    // 错误抛到业务代码
    error.data = {}
    error.data.msg = '请求超时或服务器异常，请检查网络或联系管理员！'
    return Promise.resolve(err)
})
```

## 5.支持TypeScript

由于 axios 本身有 TypeScript 相关的支持，所以只需要把**对应的类型导入，然后赋值即可**。

## 6.完整代码

```js
// http.ts
import axios, { AxiosRequestConfig, AxiosResponse } from 'axios'

const showStatus = (status: number) => {
  let message = ''
  switch (status) {
    case 400:
      message = '请求错误(400)'
      break
    case 401:
      message = '未授权，请重新登录(401)'
      break
    case 403:
      message = '拒绝访问(403)'
      break
    case 404:
      message = '请求出错(404)'
      break
    case 408:
      message = '请求超时(408)'
      break
    case 500:
      message = '服务器错误(500)'
      break
    case 501:
      message = '服务未实现(501)'
      break
    case 502:
      message = '网络错误(502)'
      break
    case 503:
      message = '服务不可用(503)'
      break
    case 504:
      message = '网络超时(504)'
      break
    case 505:
      message = 'HTTP版本不受支持(505)'
      break
    default:
      message = `连接出错(${status})!`
  }
  return `${message}，请检查网络或联系管理员！`
}

const service = axios.create({
  // 联调
  baseURL: process.env.NODE_ENV === 'production' ? `/` : '/apis',
  headers: {
    get: {
      'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
    },
    post: {
      'Content-Type': 'application/json;charset=utf-8'
    }
  },
  // 是否跨站点访问控制请求
  withCredentials: true,
  timeout: 30000,
  transformRequest: [(data) => {
    data = JSON.stringify(data)
    return data
  }],
  validateStatus () {
    // 使用async-await，处理reject情况较为繁琐，所以全部返回resolve，在业务代码中处理异常
    return true
  },
  transformResponse: [(data) => {
    if (typeof data === 'string' && data.startsWith('{')) {
      data = JSON.parse(data)
    }
    return data
  }]
})

// 请求拦截器
service.interceptors.request.use((config: AxiosRequestConfig) => {
    return config
}, (error) => {
    // 错误抛到业务代码
    error.data = {}
    error.data.msg = '服务器异常，请联系管理员！'
    return Promise.resolve(error)
})

// 响应拦截器
service.interceptors.response.use((response: AxiosResponse) => {
    const status = response.status
    let msg = ''
    if (status < 200 || status >= 300) {
        // 处理http错误，抛到业务代码
        msg = showStatus(status)
        if (typeof response.data === 'string') {
            response.data = {msg}
        } else {
            response.data.msg = msg
        }
    }
    return response
}, (error) => {
    // 错误抛到业务代码
    error.data = {}
    error.data.msg = '请求超时或服务器异常，请检查网络或联系管理员！'
    return Promise.resolve(error)
})

export default service
```

