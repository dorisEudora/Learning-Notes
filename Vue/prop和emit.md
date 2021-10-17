# prop与emit

1. prop用于父组件传值给子组件
2. **父组件是调用子组件的组件**，prop常常用于全局组件or公共组件

## 1.prop使用示例：

在组件app.vue当中调用了users.vue这个组件，构成父子关系，app.vue是父组件，users.vue是子组件

父组件app.vue

```vue
<template>
  <div id="app">
    <!-- myuser是自己给子组件定义的属性，users是父组件传给子组件的数据 -->
      <!-- 还有需要注意的点是，这里是用v-bind绑定的，否则无法生效 -->
    <users :myuser="users"></users>
  </div>
</template>
<script>
import users from "./components/Users.vue";
export default {
  name: "app",
  components: {
    users,
  },
  data() {
    return {
      users: ["小猪佩奇", "小羊肖恩", "小兔桃乐西", "小猫臭屁"],
    };
  },
};
</script>
<style lang="scss" scoped>
</style>

```

子组件users.vue

```vue
<template>
  <div class="users">
    <h1>Users</h1>
    <ul>
        <!-- 遍历 -->
      <li v-for="(item, index) in myuser" :key="index">{{ item }}</li>
    </ul>
  </div>
</template>
<script>
export default {
  name: "Users",
  // 从父组件传来的值，但是命名是自定义属性名
  //写法一
  props: ["myuser"],
  //写法二
  props:{
      users:{
          type:Array,
          required:true
      }
  }
  data() {
    return {};
  },
};
</script>

<style scoped>
</style>
```

## 2.prop属性传值两种写法

假设传值names（对象）、users（数组）、title（字符串）

1. 数组型写法

   ```js
   props:["names","users","title"]
   ```

2. 对象型写法

   ```js
   props:{
       names:{
           type:Object,
           required:true    
       },
       users:{
           type:Array,
           required:true    
       },
       title:{
           type:String,
           required:true    
       },
   }
   ```

   

## 3.prop传引用使用

若要操作父组件传来的值，在子组件中写操作

users.vue

```js
export default {
  name: "Users",
  // 从父组件传来的
  props: ["myuser"],
  data() {
    return {};
  },
  methods: {
    delteItem() {
      console.log("pop");
      //注意命名
      this.myuser.pop();
    },
  },
};
```

1. 同一个子组件，在同一个父级当中使用，某一个子组件修改了父级一个属性值，同样会修改到另外一个同样组件的相关值
2. 不同的子组件，操作同一个父组件的一个属性值，不会互相影响

**子组件按理是不应该直接在子组件当中写方法操作父组件的属性值**

## 4.emit注册事件，子组件合法修改父组件内容

注册事件 参数1：参数名称  参数2。。：值

this.$emit("参数1","....")

子组件Header.vue

```vue
<template>
  <div class="header" @click="changeTitle">
    <h1>{{ title }}</h1>
  </div>
</template>
<script>
export default {
  props: {
    title: {
      type: String,
    },
  },
  data() {
    return {
    };
  },
  methods: {
    changeTitle() {
      // 注册事件 参数1：参数名称  参数2。。：值
      //1个参数
      this.$emit("titleChange", "doris change title by emit");
        
      //多个参数
      this.$emit("titleChange", "doris change title by emit","test");  
      this.$emit("titleChange", "doris change title by emit","test","...");  
    },
  },
};
</script>
```

父组件app.vue

```vue
<template>
  <div id="app">
      <!-- 注意这里绑定的事件是注册的事件名，触发的是父组件里的方法-->
    <Header :title="title" @titleChange="updataTitle"></Header>
  </div>
</template>
<script>
import Header from "./components/Header.vue";
export default {
  name: "app",
  components: {
    users,
    Header,
    Footer,
  },
  data() {
    return {
      title: "vue Component",
    };
  },
  methods: {
    //data是从第二个参数开始的值，每个参数有一个对应
    //一个参数
    updataTitle(data) {
      console.log(data);
      this.title = data;
    },
    //多个参数
    updataTitle(data,data2) {
      console.log(data,data2);
    },
    updataTitle(data,data2,..) {
      console.log(data,data2);
    },
  },
};
</script>

```

