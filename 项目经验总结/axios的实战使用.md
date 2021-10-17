# axios的五种请求接口方式、封装请求、请求路径拼接

### 一、Axios介绍

axios是基于promise的http库，可以用在浏览器和nodejs中

可以提供以下服务：

1. 从浏览器中创建XMLHttpRequests
2. 从nodejs创建http请求
3. 支持PromiseApi
4. 拦截请求和响应
5. 转换请求数据和响应数据
6. 取消请求
7. 自动转换json数据
8. 客户端支持防御xspf

### 二、axios的请求方法：get、post、put、patch、delete

- get：获取数据（安全性低，所以**只**用于获取数据）
- post：提交数据（**所有**数据传输到后端）
- patch：更新数据（只将**更改的**数据推送到后端）
- delete：删除数据

#### axios的请求参数有：

- url：请求路径
- **params：请求参数**（请求时携带的参数，比如id
- method：请求方法
- data：传输到后端的数据

#### 1、get

> 不带参数

```js
//方式一 axios({ method: 'get', url: '/url'})

//方式二 axios({'/url'})

```

> 携带参数(**重点**)

```js
//way one

axios.get('/url/list',{ 
    params: {id:12}
    //请求的地址实际为 localhost:8080/url/list?id=12

}).then(res => {
    //.....do
})


```

**重点：携带参数的完整版**

```js
//在api文件下的一个manage.js
axios({
    method:'',
    url:'',
    params:{
        id:''
    }
}).then(res =>{
    
})
```

#### 2、post

data可以有两种格式: 

- form-data(图片上传,文件上传)
- applicition/json（一般使用这个）

> applicition/json

```vue
<template>
</template>
<script>
    data(){
        return{
            data:{},
            config:{}
        },
            //applicition/json格式的数据
        methods:{
            getPost(){
                axios({
                    methods:'post',
                    url:'/url',
                    data: this.data,
                    config: this.config
                })
            }
        }
    }
</script>
<style>
</style>
```

> form-data

```js
//省略。。。
let formData =new FormData()
let data = {id: 12}
for(let key in data){
    formData.append(key,data[key])//第一个参数为命名，第二个参数是传值
}
axios({
	method:'post',
    url:'/url',
    data:formData
})
```

该请求发出之后可以在浏览器中查看此次请求的request header里面`content-type: 为 form-data`形式

#### 3、put对数据全部进行更新

该请求和post类似，只是请求方法不同

#### 4、patch只对更改过的数据进行更新

该请求和post类似，只是请求方法不同

#### 5、delete删除请求(参数可以放在url上也可以和post一样放在请求体中)

axios.delete('/url', {params: {id: 12}})  参数在url，params很重要

axios.delete('/url', {data: {id: 12}}) 参数在请求体中，将params改为 data就行



### 三、重点）封装请求



1. 封装axios,  request.js

   ```js
   //request.js
   import Vue from 'vue'
   import axios from 'axios'
   import store from '@/store'
   import { VueAxios } from './axios'
   
   //全局api基础地址
   let apiBaseUrl = window._CONFIG['domianURL'] || "/jeecg-boot";
   
   // 创建 axios 实例
   const service = axios.create({
     //baseURL: '/jeecg-boot',
     baseURL: apiBaseUrl, // api base_url
     timeout: 90000 // 请求超时时间
   })
   
   
   // request interceptor
   service.interceptors.request.use(config => {
     const token = Vue.ls.get(ACCESS_TOKEN)
     if (token) {
       // 让每个请求携带自定义 token 请根据实际情况自行修改
       config.headers[ 'X-Access-Token' ] = token 
     }
     return config
   },(error) => {
     return Promise.reject(error)
   })
   
   // response interceptor
   service.interceptors.response.use((response) => {
       return response.data
     }, err)
   
   const installer = {
     vm: {},
     install (Vue, router = {}) {
       Vue.use(VueAxios, router, service)
     }
   }
   
   export {
     installer as VueAxios,
     service as axios
   }
   ```
   
   
   
2. 在api文件下建立一个js文件，储存封装的请求,manage.js

   ```js
   //manage.js
   import Vue from 'vue'
   import { axios } from '@/utils/request'
   
   //post**
   export function postAction(url,data,params){
       return axios({
           url:url,
           method: 'post',//请求方式
           data: data,//数据
           params:params//请求头携带参数，可用可不用
       })
   }
   
   //post method = {post | put}
   export function httpAction(url,parameter,data){
        return axios({
           url:url,
           method: method,
           data: data
       })
   }
   
   //put
   export function putAction(url,data){
       return axios({
           url:url,
           method: 'put',
           data: data
       })
   }
   
   //**get
   export function getAction(url,params){
       return axios({
           url: url,
           method: 'get',
           params: params //请求参数，非传输参数
       })
   }
   
   //deleteAction
   export function deleteAction(url,params) {
     return axios({
       url: url,
       method: 'delete',
       params: params  //请求参数，非传输参数
     })
   }
   ```

3. 全局api

   ```js
   import { getAction, deleteAction, putAction, postAction, httpAction } from '@/api/manage.js'
   import Vue from 'vue'                                            
   import { UI_CACHE_DB_DICT_DATA } from '@/store/mutation-types'   
   
   //举例，请求下面的接口多次，重复使用
   const queryExcel = params => getAction('/excel/queryExcel', params)                           
   const deleteCategory = params => getAction('/excel/deleteCategory', params)                   
   const deleteManagement = params => deleteAction('/templateManagement/deleteTemplate/',params) 
   
   const saveExcelData = params => postAction('/excel/saveData', params)  
   
   export{
   	queryExcel,
       deleteCategory,
       deleteManagement,
       saveExcelData
   }
   ```

   **注意：**

   - 因为get与delete不需要传输数据，但是一般请求时都需要携带请求参数，实际情况都是携带id什么的，所以get与delete里封装时，第二个参数是请求参数
   - post与put请求时一般不会在请求接口时携带请求参数，所以第二个参数是请求数据

### 四、请求路径的拼接

- 上述封装的函数携带请求参数，一般会出现这样的格式/url/?id=xxxx   这个时候id为我们传入的请求参数

```js
import { queryExcel,
    deleteCategory,
    deleteManagement,
    saveExcelData } from '@/src/api'
//id = 1233455
getQueryExcel(id){
    queryExcel({id}).then({
        //....
    })//请求路径变为/excel/queryExcel?id=1233455
}

getQueryExcel(typeId){
    queryExcel({typeId}).then({
        //....
    })//请求路径变为/excel/queryExcel?typeId=1233455
}

//注意变化，传入参数的命名会对请求路径有变化
```

- 有时候请求路径需要拼接，因为携带请求参数可能直接携带，不用`id=`的格式

  ![image-20210319142739978](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210319142739978.png)

```js
import { getAction, deleteAction, putAction, postAction, httpAction } from '@/api/manage.js'

//this.id = 555666
getQueryExcel(){
    getAction(`/excel/queryExcel${this.id}`)
}

/**拼接格式：
记住是``  反向的单引号，变量用${}包裹，不需要连接符，直接包裹之后加在路径后面
`url${ver}`
**/
```

注意是**反向单引号**