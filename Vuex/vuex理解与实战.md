# 基于vuex的知识点和列子

官方vuex的操作顺序图

<img src="C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210801093025022.png" alt="image-20210801093025022" style="zoom:80%;" />

**1、组件如果dispatch事件非常明确，可以直接到Mutations**

<img src="C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210801092800193.png" alt="image-20210801092800193" style="zoom:80%;" />

**2、store管理三大部分**

<img src="C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210801093233498.png" alt="image-20210801093233498" style="zoom: 80%;" />

## 1.vuex的构成：

（1）**State**:状态（vuex存放的数据的地方）

​		state本质是一个object对象

（2）Getter：获取器（vuex通过现有数据计算出新的数据）

**（3）Mutation**：方法库（vuex的方法库，用于改变数据），修改加工

（4）**Action**：动作（提交Mutation，可以包含异步操作）

（5）Mudule：模块（将vuex进行分模块）

### 1.1.简述：

vuex不属于任何组件，是一种全局共享状态，不同组件可以修改，修改后的vuex状态会影响到不同的组件，vuex是vue的一个插件

![image-20210801093524816](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210801093524816.png)

### 1.2.使用场景：

1. 多个组件依赖于同一状态
2. 来自不同组件的行为需要变更同一状态（数据）

## 2.vuex内部执行顺序：

1. 组件通过dispatch触发actions里面的动作

   dispatch（"事件名",传入参数1，。。。）

   ```js
   dispatch("count",20)
   ```

2. actions调用`commit`提交到Mutations

   actions:{

   事件名：function{}

   }

   actions调用commit：commit（"事件名",参数）

   ```js
   actions:{
       count(){
           //xxxx
           commit("count",2)
       }
   }
   ```

3. Mutations注册事件，通过这些事件来改变state（数据）的状态

   ```js
   mutations:{
       count:function(){
           state.sum += 2
       }
   }
   ```

   Mutations可以拿到state

   

4. 最后State状态改变完后渲染到组件上

## 3.vuex的核心

### 1、State：

用于储存一些源数据，在组件中的**计算属性**中通过$store.state获取数据，也可以直接使用mapState

> 所有需要共享的数据都放在state中

```js
//在store中
new Vuex.store({
    state:{
        token:'',
        todos:[
            {id:1,name:'',done:''},
            {id:2,name:'',done:''},
        ]
    }
})

```

```js
//在vue组件中

//$store.state
computed:{
    count(){
        return this.$store.state.token;
    }
}

```

### 2、Actions：

触发mutations事件

改变state状态的事件注册完了，那要如何触发呢？那就是Action啦

> 通过调用 mutation 方法异步的改变 state 状态（我认为可能这就是为什么Mutations里的方法不能为异步）

```js

new Vuex.store({
    state:{
        count:22
    },
    actions:{
        /*
        第一个参数是context，第二个参数是传入的值
        context类比与迷你store，有store部分功能但是并不全，这里的context给actions提供了commit
        */
        add(context){
            context.commit('add')
        }
    },
    mutations:{
        add:state=>state.count++
    },    
})


```

```vue
//组件中使用
<script>
    import { mapActions } from 'vuex'
	//....
	methods:{
    	//直接在组件中通过this.$store.dispatch('add')触发action
    	add(){
        	return this.$store.dispatch('add')
    	}
	}
</script>

```

### 3、Mutations：

用来注册一些事件，然后通过这些事件来改变state的状态（类似methods）

**Mutations必须是同步函数**

> 在vuex中只能通过提交Mutations来修改store的状态并且是一个同步的过程

```js
new Vuex.store({
    state:{
        count:22
    },
    mutations:{
        //state为第一参数,第二个参数是传入的值
        add(state){
            state.count++;
        }
    }
})
```

```js
import { mapMutations } from 'vuex'
//...
methods:{
    ...mapMutations({
        add:'add'
    })
    ...mapMutations([
        'add'
    ])
}
```

### 4、Getter：

用来获取State里的源数据,在组件上通过$store.getters来获取或者mapGetter

> 可以对state进行过滤和加工

```js
new Vuex.Store({
    state:{
        msg:'welcom learn vuex'
    },
    //获取State里的源数据
    getter:{
        msg:function(state){
            return state.msg
        }
    }
})
```

​		Getter还可以用来对数据进行操作后再返回

```js
//例如过滤数据
new Vuex.Store({
    getter:{
        //state作为第一个参数
        doneTodos:state=>{
            return state.todos.filter(todo=>todo.done)
        },
        //getters可作为第二个参数
        doneTodesCount:(state,getters)=>{
            return getters.doneTodos.length
        }
    }
})
```

`Getter可以看做是vuex的计算属性，getter的返回值会根据他的依赖被缓存起来，且只有当它的的依赖值发生改变才会被重新计算。同State一样，也可以用mapGetters来获取`

```js
import { mapGetters } from 'vuex'//使用mapXXX都需要从vuex中导入

//....
computed:{    
    //$store.getter
    doneCount(){
        return $store.getter.doneTodosCount
    }
}
```



## 4.实战

#### 4.1.配置Vuex和store

src/store/index.js

```js
import Vuex from "vuex"
import Vue from "vue"
Vue.use(Vuex)

export default new Vuex.Store({
    state:{
        
    },
    actions:{
        
    },
    mutations:{
        
    },
})
```

src/main.js

```js
import Vue from "vue"
import store from "./store"

new Vue({
  store,
  render: (h) => h(App),
}).$mount("#app");
```

#### 4.2.组件

src/component/Count.vue

```vue
<template>
  <div>
    <h4>当前求和：{{ $store.state.sum }}</h4>
    <select v-model.number="number">
      <option value="10">10</option>
      <option value="11">11</option>
      <option value="20">20</option>
    </select>
    <button @click="add">添加</button>
    <button @click="reduce">删除</button>
    <button @click="addOdd">奇数不能加</button>
  </div>
</template>

<script>
export default {
  name: "count",
  data() {
    return {
      number: 1,
    };
  },
  methods: {
    add() {
      this.$store.dispatch("add", this.number);
    },
    reduce() {
      this.$store.dispatch("reduce", this.number);
    },
    addOdd() {
      this.$store.dispatch("addOdd", this.number);
    },
  },
  created() {},
  computed: {},
};
</script>

<style scoped>
button {
  margin-left: 10px;
}
</style>

```

#### 4.3.写处理

##### 一般处理过程:

src/store/index.js

```js
import Vue from "vue";
// 引入vuex
import Vuex from "vuex";

Vue.use(Vuex);

// 创建并导出store
export default new Vuex.Store({
  state: {
    sum: 0,
  },  
  actions: {
    // context是迷你的store，有commit
    add(context, value) {
      console.log("actions触发了add");
      context.commit("ADD", value);
    },
    reduce(context, value) {
      context.commit("reduce", value);
    },
    addOdd(context, value) {
      if (context.state.sum % 2) {
        context.commit("addOdd", value);
      }
    },
  },
  mutations: {
    ADD(state, value) {
      console.log("mutatios调用了ADD");
      state.sum += value;
    },
    reduce(state, value) {
      state.sum -= value;
    },
    addOdd(state, value) {
      state.sum += value;
    },
  },
  modules: {},
  getters: {
    getSum(state) {
      return state.sum * 10;
    },
  },  
});
```

##### 特殊情况:

**component直接到mutations：**

src/component/Count.vue

```vue
<template>
  <div>
    <!--直接使用store获取-->
    <h4>当前求和：{{ $store.state.sum }}</h4>
    <h4>和的10倍：{{ $store.getters.getSum }}</h4> 
    <select v-model.number="number">
      <option value="10">10</option>
      <option value="11">11</option>
      <option value="20">20</option>
    </select>
    <button @click="add">添加</button>
    <button @click="reduce">删除</button>
    <button @click="addOdd">奇数不能加</button>
  </div>
</template>

<script>
export default {
  name: "count",
  data() {
    return {
      number: 1,
    };
  },
  methods: {
    add() {
        //直接commit到mutations
      this.$store.commit("add", this.number);
    },
    reduce() {
      this.$store.commit("reduce", this.number);
    },
    addOdd() {
      this.$store.dispatch("addOdd", this.number);
    },
  },
  created() {},
  computed: {},
};
</script>

<style scoped>
button {
  margin-left: 10px;
}
</style>

```

src/store/index.js

```js
import Vue from "vue";
// 引入vuex
import Vuex from "vuex";

Vue.use(Vuex);

// 创建并导出store
export default new Vuex.Store({
  state: {
    sum: 0,
  },  
  actions: {
    // context是迷你的store，有commit    
    addOdd(context, value) {
      if (context.state.sum % 2) {
        context.commit("addOdd", value);
      }
    },
  },
  mutations: {
    add(state, value) {
      state.sum += value;
    },
    reduce(state, value) {
      state.sum -= value;
    },
    addOdd(state, value) {
      state.sum += value;
    },
  },
  modules: {},
});
```

## 5.mapState与mapGetter

### 5.1.mapState

```js
//mapState
import { mapState } from  'vuex'
computed:{
    //借助mapState生成计算属性，从state当中读取数据
    //1.对象写法（可以进行重命名）
    ...mapState({
        sum:state=>state.sum
    })
    //2.数组写法（命名与state的数据一致）
    ...mapState([
        'sum',
        'title'
    ])
}
```

组件中可以直接使用，不再需要this.$store.state.xxx

```html
<h4>当前求和：{{ sum }}</h4>
```

### 5.2.mapGetter

```js
import { mapGetters } from 'vuex'
//使用mapXXX都需要从vuex中导入

//借助mapGetters生成计算属性，从state当中读取数据
computed:{
    //1、数组写法
    ...mapGetters([
        'doneTodos',
        'doneTodosCount',
        'getSum'
    ])
    //2、对象重命名写法
    ...mapGetters({
        doneCount:'doneTodosCount'
        //.....
    })
}
```

组件当中使用从this.$store.getters.xxx变为xxx

```html
<h4>和的10倍：{{ getSum }}</h4>
```

### 5.3.mapActions

```vue
//组件中使用
<tempalte>
    <div>
        <!--value就是要传的值，因为mapActions只能注册事件，不能直接传值-->
        <button @click="add(value)">添加</button>
    	<button @click="reduce(value)">删除</button>
    </div>
</tempalte>
<script>
    import { mapActions } from 'vuex'
	//....
	methods:{
    //使用mapActions生成对应的方法，方法当中会触发dispatch
    	//1、数组写法
    	...mapActions([
        	'add'
    	])
        //2、对象写法
        ...mapActions({
            add:'add',
            reduce:'reduce'
        })
	}  
    
</script>

```

### 5.4.mapMutations

```vue
//组件中使用
<tempalte>
    <div>
        <!--value就是要传的值，因为mapMutations只能注册事件，不能直接传值-->
        <button @click="add(value)">添加</button>
    	<button @click="reduce(value)">删除</button>
    </div>
</tempalte>
<script>
    import { mapMutations } from 'vuex'
	//....
	methods:{
    //使用mapMutations生成对应的方法，方法当中会触发commit
    	//1、数组写法
    	...mapMutations([
        	'add'
    	])
        //2、对象写法
        ...mapMutations({
            add:'add',
            reduce:'reduce'
        })
	}  
    
</script>
```

## 6.模块化module

目的：让代码更好维护，多种数据进行分类

1. 一般store里是actions、mutations、state、Getters
2. 模块化之后就变成：模块1、模块2、模块3 --->>
3. 模块下再包括actions、mutations、state、Getters

src/store/index.js

```js
import Vue from "vue";
// 引入vuex
import Vuex from "vuex";

Vue.use(Vuex);

/*每个模块都有actions、mutations、。。。*/
const countModlues = {
  actions: {
    // context是迷你的store，有commit
    addOdd(context, value) {
      if (context.state.sum % 2) {
        context.commit("addOdd", value);
      }
    },
  },
  mutations: {
    add(state, value) {
      console.log("mutatios调用了ADD");
      state.sum += value;
    },
    reduce(state, value) {
      state.sum -= value;
    },
    addOdd(state, value) {
      state.sum += value;
    },
  },
  state: { sum: 0,title:'test'},
  getters: {
    getSum(state) {
      return state.sum * 20;
    },
  },
};
const personModlues = {
  actions: {},
  mutations: {
    addPerson(state, value) {
      state.personList.push(value);
    },
  },
  state: { personList: [{ id: "001", name: "doris" }] },
  getters: {},
};
// 创建并导出store
export default new Vuex.Store({
  modules: {
    //简写
    countModlues,
    personModlues,
    //重写
    a:countModlues,
    //...
  },
});

```

使用模块以后，读取数据不是this.$store.state.xxx,以此类推

### 6.1.一般读取方式：

```js
this.$store.state.模块.数据
//this.$store.getter['模块/事件']
this.$store.getter['person/getSum']

//this.$store.commit['模块/注册事件',事件]
this.$store.commit("person/addPerson", perons);

//this.$store.dispatch['模块/注册事件',事件]
this.$store.dispatch("person/addPerson", perons);
```

### 6.2.mapXXX读取

这里用mapState，mapGetter举例，其他同理可得

```js
...mapState(['countModlues'])
...mapGetter(['countModlues'])

```

使用：

```html
<!--state当中的sum-->
<div>{{countModlues.sum}}</div>
<!--getters当中的getSum-->
<div>{{countModlues.getSum}}</div>
```

### 6.3.命名空间namespaced

src/store/index.js

```js
import Vue from "vue";
// 引入vuex
import Vuex from "vuex";

Vue.use(Vuex);

const countModlues = {
  //namespaced默认为false
  namespaced: true,
  actions: {
    // context是迷你的store，有commit
    addOdd(context, value) {
      if (context.state.sum % 2) {
        context.commit("addOdd", value);
      }
    },
  },
  mutations: {
    add(state, value) {
      console.log("mutatios调用了ADD");
      state.sum += value;
    },
    reduce(state, value) {
      state.sum -= value;
    },
    addOdd(state, value) {
      state.sum += value;
    },
  },
  state: { sum: 0 },
  getters: {
    getSum(state) {
      return state.sum * 20;
    },
  },
};
const personModlues = {
  namespaced: true,
  actions: {},
  mutations: {
    addPerson(state, value) {
      state.personList.push(value);
    },
  },
  state: { personList: [{ id: "001", name: "doris" }] },
  getters: {},
};
// 创建并导出store
export default new Vuex.Store({
  modules: {
    count: countModlues,
    person: personModlues,
  },
});
```

namespaced默认为false，需要设置为true，可以实现在组件当中使用mapState的另一种方法，使用时更简便

- 所有mapXX都可以这样使用，对象写法同理可的

```js
...mapState('count',['sum','title'])
...mapActions("count", ["addOdd"]),
...mapMutations("count", ["add", "reduce"]),
...mapGetters("count", ["getSum"]),
```

- 使用：

```html
<div>{{sum}}</div>
```

## 7.actions里做请求

```js
actions:{
    getUsers(context){
        axios.get('url').then(res => {
            context.commit("getUsers",res)
        },err => {
            console.log(err.message)
        })
    }
}
```

