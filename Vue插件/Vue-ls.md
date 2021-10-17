# Vue-ls的使用

## 1、安装

npm:   `npm install vue-ls --save`

yarn:   `yarn add vue-ls`

## 2、使用

```js
import Storage from 'vue-ls'
const options = {
    namespace:'vuejs__',	//key键前缀
    name:'ls',	//命名Vue变量.[ls]或this.[$ls],
    storage:'local'	// 存储名称: session, local, memory
}
Vue.use(Storage,options);
//or   Vue.use(Storage)

new Vue({
    el:'app',
    mounted:function(){
        Vue.ls.set('foo','boo')
        // 设置有效期
        Vue.ls.set('foo','boo',60*60*1000)//有效1小时
        Vue.ls.get('foo');
        Vue.ls.get('boo', 10); // 如果没有设置boo返回默认值10 
        
        let callback = (val, oldVal, uri) => {
          console.log('localStorage change', val);
        } 
        
        Vue.ls.on('foo', callback) //侦查改变foo键并触发回调 
        Vue.ls.off('foo', callback) //不侦查
        
        Vue.ls.remove('foo'); // 移除
    }
})
```

### 2.1、Global(全局)   ：`Vue.ls`

### 2.2、Context（上下文）：`this.$ls`

## 3、 API说明

### 1. Vue.ls.get(name, def)

**返回storage中 name值。在返回之前，内部解析JSON中的值**
def: 默认null, 如果为设置则返回 name .

### 2.Vue.ls.set(name, value, expire)

**在storage设置 name 的 value .并将 value 转化为JSON**
expire: 默认为 null , name 有效时间以**毫秒**为单位

### 3. Vue.ls.remove(name)

**从storage中移除 name . 成功移除 true, 否则返回false.**

### 4. Vue.ls.on(name, callback)

**持续监听 name 在其他标签上的更改，更改时触发 callback , 传递以下参数：**

newValue: 当前storage中 name , 从持久化的JSON中解析
oldValue: 旧的storage中 name , 从持久化的JSON中解析
url: 修改来自选项卡的URL

```
Vue.ls.on(name,(newValue,oldValue)=>{

})
```

### 5.Vue.ls.off(name, callback)

**删除以前的侦听器 Vue.ls.on(name, callback)**

### 6.Vue.ls.clear()

**清除storage.**