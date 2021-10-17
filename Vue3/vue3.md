# Vue3



## 一、vue3项目工程与vue2的差异

### 1.1.createApp

*引入的不再是vue的工厂函数，引入的是createApp工厂函数*

```js
//main.js
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

vue3不再支持vue2当中的写法

vue2的写法：

```js
new Vue({
    render:h => h(App)
}).$mount('#app')
```

### 1.2.组件差异

vue3的组件当中的组件的模板结构**可以没有**根标签

```vue
<template>
<!--注意这里没有div包裹了-->
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>
<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>
```

Chrome应用商店安装vue.js.devtools（vue开发者工具）

## 二、常用Composition API（组合式API）



### 2.1.setup

1. vue3的一个新的配置项（配置项是date、methods...），是一个函数

2. setup是所有composition aip的“表演舞台”

3. 组件中所有用到的数据、方法、计算属性等等，均要配置在setup当中

   ```vue
   <script>
   export default {
     name: "App",
     components: {
       // HelloWorld
     },
     setup() {
       //data
       let name = "doris";
       let age = 19;
   
       //methods
       function sayHello() {
         alert(`my name is ${name},my age is ${age}`);
       }
     },
   };
   </script>
   ```

4. setup有两种返回值：

   1. 若返回一个对象，则对象中的属性、方法，在模块中均可以直接使用（**重点！！**）

      ```js
      //记住这里有返回值！！这样才能在template当中使用
      //返回一个对象
      return {
            name,
            age,
            sayHello,
      };
      ```

      

   2. 若返回一个渲染函数，则可以自定义渲染内容（了解）

      ```js
      import { h } from "vue";
      //返回一个渲染函数
      return () => h("h1", "test");
      ```

      

5. 注意点：

   目前vue3当中可以按照vue2配置（直接data、methods。。等）来写

   

   1. 尽量不要与vue2.x配置混用

      - vue2当中的配置（data、methods、computed...）**可以访问**setup中的属性、方法...
      - 但是setup当中**不能访问**到vue2的配置项
      - 如果有重名，**setup优先**

      

      

   2. setup不能是一个async函数，因为返回值不再是return的对象，而是promise，模板看不到return对象中的属性、方法

   6.setup处理时间在beforeCreate之前
   
   

### 2.2.ref函数

ref函数不是ref属性，ref函数是vue3新增的一个函数

#### 2.2.1.定义

![image-20210811094617123](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210811094617123.png)

#### 2.2.2.使用

在vue3当中，修改数据不能支撑vue2当中的修改方式，比如下方修改方式：

```js
let name = "doris";
let age = 19;

function changeInfo() {
      name = "jake";
      age = 23;
      console.log("test change");
}
```

函数会调用，但是**这样的修改方式没有办法修改数据并更新到页面**

因此我们就要借助ref函数来进行数据的修改

引用对象：想实现响应式，将数据给ref，加工生成引用对象

```js
import {ref} from 'vue'	
//data
    let name = ref("doris");
    let age = ref(19);
	let job = ref({
        type:'前端工程师',
        salary:18880
    })
    //methods
    function changeInfo() {
      //获取值的方法要加value
      name.value = "jake";
      age.value = 23;
      //对象类型的写法
      job.value.type = 'java工程师'
    }
```

经过实际代码实现，在**模板template当中**（看解释是模板自动解析为组和对象的value），不需要加value来读取和修改数据，直接一般操作就可以，但是在setup当中修改还是value访问

![image-20210811094148312](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210811094148312.png)

ref处理基本数据类型，是加工为RefImpl（引用对象），非基本类型不会继续加工为引用对象（比如对象类型，是使用Proxy对象{封装在reactive当中}）

### 2.3.reactive函数

#### 2.3.1.定义

定义一个**对象类型**的响应式数据，只能处理对象类型，基本数据类型不支持

![image-20210811100705793](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210811100705793.png)

深层次指不论对象有多少层，都能处理

#### 2.3.2.使用

##### 1、处理对象

```js
import { ref, reactive } from "vue";
	let job = reactive({
      type: "fore",
      salary: 19000,
    });
	function changeInfo() {
      job.type = 'java'
    }
```

对比ref与reactive的用法

##### 2、处理数组

```js
import { ref, reactive } from "vue";
	let array = reactive(['doris','jake','herry'])
	function changeInfo() {
      //reactive可以实现这样的修改，ref不支持
      array[0] = 'tomy'
    }
```

##### 3、处理基本数据（假性处理）

将所有的数据都放在一个对象里，这样reactive就处理所有的数据

```js
import {ref,reactive} from 'vue'	
//data
let person = {
    name:'doris',
    age:19,
    job:{
        type:'前端工程师',
        salary:18990
    },
    arrayList:['doris','jake','herry']
}
    let p = reactive(person)
    //methods
    function changeInfo() {
      p.name = "jake";
      p.age = 23;
      //对象类型的写法
      p.job.type = 'java工程师',
      p.array[0] = 'tomy'
    }
```

#### 4、模仿vue2的数据操作

```js
import {ref,reactive} from 'vue'	
//data
let data = reactive({
    person:{
    	name:'doris',
    	age:19,
    	job:{
        	type:'前端工程师',
        	salary:18990
    	},
    	arrayList:['doris','jake','herry']
	},
    student:{}
})
    //methods
    function changeInfo() {
      data.p.name = "jake";
      data.p.age = 23;
      //对象类型的写法
      data.p.job.type = 'java工程师',
      data.p.array[0] = 'tomy'
    }
```



### 2.4.Vue3.0x中的响应式原理

#### 1.vue2.0的响应式原理

![image-20210811101553732](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210811101553732.png)

直接修改属性界面不会更新，需要借助vue当中的方法进行响应式数据，比如，

- 新增需要vue.set()或者this.$set()
- 删除需要this.$delete() 或 vue.delete

因为vue2当中的响应式：

![image-20210813094508124](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210813094508124.png)

#### 2.vue3.0的响应原理

vue3不存在vue2当中的页面不更新的问题，使用proxy与reflect

**定义：**

![image-20210813101048164](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210813101048164.png)

##### 2.4.2.1响应原理—proxy

对对象的操作，proxy都能捕捉到，并且proxy提供了除get、set外还有delete操作，并且所有的操作都是循环操作，所有的属性都适用

- get（目标对象,目标属性）
- set（目标对象,目标属性,修改值）
- delete（目标对象,目标属性）

![image-20210813095638491](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210813095638491.png)



##### 2.4.2.2响应原理—Reflect

reflect是window对象的一个属性，反射

同样有get、set操作，参数与proxy一致

![image-20210813100423174](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210813100423174.png)

上面的操作，分别打印true和false，如果是使用ObjectProperty，上面的操作会报错，表示不能重复定义同一个属性，但是reflect不会报错，但是会返回操作成功与否，就是会减少try..catch

##### 2.4.2.3vue3.0响应原理

![image-20210813100829554](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210813100829554.png)

### 2.5.vue3当中的computed

与vue2.0当中的computed配置一致

#### 1.引入computed

import {computed} from ‘vue’

vue3当中的computed变成了一个函数computed(()=>{

})

#### 2.使用computed

1. ##### 计算属性--简写（不考虑计算属性是动态修改的情况）

   computed(()=>{

   })

   ```js
   import { reactive, computed } from "vue";
   export default {
       /*vue2当中的写法
     computed: {
       fullName() {
         let name = this.person.firstName + this.person.lastName;
         return name;
       },
     },*/
     setup() {
       //   data
       let person = reactive({
         firstName: "doris",
         age: 19,
         lastName: "Eduora",
       });
       // computed Vue3
       let fullName = computed(() => {
         let name = this.person.firstName + this.person.lastName;
         return name;
       });
       return {
         person,
         fullName,
       };
     },
   };
   ```

   这种computed的值不可以修改，如果修改会导致下面的报错

    computed value is readonly

2. ##### 计算属性--完整（考虑动态修改）

   get获取数据，set设置数据（当值修改时触发）

   computed({

   ​	set(){}

   ​	get(){}

   })

   ```js
   let fullName = computed({
         set(value) {
           let nameArr = value.split("-");
           person.firstName = nameArr[0];
           person.lastName = nameArr[1];
         },
         get() {
           return person.firstName + "-" + person.lastName;
         },
       });
   ```

### 2.6.watch

vue2当中的watch写法：适用vue3但不建议

```js
watch: {
      sum: {
          //注意oldValue是第一个参数，newValue是第二个参数
        handler(oldValue, newValue) {
          console.log("sum change:", oldValue, newValue);
        },
      },
    },
setup(){
    let sum = ref(0)
    return{
        sum
    }
}
```

#### 1.引入watch

import { watch } from "vue";

watch写了以后不用放在setup的return里面

#### 2.使用watch监视ref定义的数据

1. 监视ref定义的一个基本类型的响应式数据

   ```js
   //注意newValue是第一个参数，oldValue是第二个参数
   watch(sum, (newValue, oldValue) => {
         console.log("sum change:", oldValue, newValue);
       });
   ```

   

2. 监视ref定义的多个基本类型的响应数据（数组形式）

   ```js
   //第一个参数就是监视对象，写成数组形式
   watch([sum,msg], (newValue, oldValue) => {
         console.log("change:", oldValue, newValue);
       });
   ```

   打印结果：

   回调函数的参数返回值变为了数组，下标与传入监视对象一致

   ![image-20210817103830370](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210817103830370.png)

   

   监视基本类型的ref对象，不能加value，如果加了value监视的就是基本类型的值，watch不能监视值

   

3. 第三个参数是watch的配置项

   ```js
   watch(
         [sum, msg],
         (newValue, oldValue) => {
           console.log("change:", oldValue, newValue);
         },
         {
           immediate: true,
         }
       );
   ```

   4.监视ref定义的对象

   ref包的对象是借助了reactive生成的proxy（代理对象），不能像基本类型不加value

   ```js
   watch(person.value, (newValue, oldValue) => {
         console.log("change:", oldValue, newValue);
    });
   ```

   第二种写法:

   ```js
   watch(person, (newValue, oldValue) => {
         console.log("change:", oldValue, newValue);
    },{deep:true});
   ```

   

#### 3.使用watch监视reactive定义的数据

reactive的是引用类型   ，而且这里**强制开启了deep:true 意思就是deep的配置是无效的**

1. 监视某个对象

   ```js
    watch(info,(oldvalue,newvalue) => {
           console.log(oldvalue,newvalue)
        	console.log(newValue.name, oldValue.name);
           },{immediate:true})
   ```

   ![image-20210819085612282](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210819085612282.png)

   打印出的是reactive的对象，而且oldValue与newValue都是一样的，说明一件事，在监视对象里

   - 此处无法正确获取oldValue，所以一般使用没有oldValue这个参数
   - 强制开启了深度监视（deep配置无效）

   

2. 监视对象的某个对象的属性值

   ```js
   //第一个参数是函数形式的写法，返回监测的属性值
   watch(() => person.name,
         (newValue, oldValue) => {
           console.log("person.name:", newValue, oldValue);
         }
       );
   ```

   ![image-20210819092633925](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210819092633925.png)

3. 监视对象的某几个对象的属性值

   ```js
   //类比ref监测多个，使用数组写法
   watch(
   [() => person.name,()=> person.age]
         (newValue, oldValue) => {
           console.log("person.name:", newValue, oldValue);
         }
       );
   ```

   注意！！！：

   如果监测一个reactive**对象**是无法关闭deep（深度监视），但是只监视reactive对象的**属性**，可以修改deep属性

   



### 2.7.watchEffect

可以监视回调函数当中所有使用的数据

```js
watchEffect(() => {
    //监测reactive对象
      let name = person.name;
    //监测ref的基本数据类型
      let getSum = sum.value;
      let getMsg = msg.value;
      console.log("watchEffect");
    });
```

watchEffect类似computed

![image-20210819095036715](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210819095036715.png)



### 2.8.vue3.0生命周期

![image-20210819095644976](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210819095644976.png)

左vue2右vue3

- vue3.0继承vue2.0的生命周期：

<img src="C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210819100724631.png" alt="image-20210819100724631" style="zoom:50%;" />

<img src="C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210819100816511.png" alt="image-20210819100816511" style="zoom:50%;" />

- vue3提供的composition api形式的生命周期

  **beforeCreate -> use setup()**

  **created -> use setup()**

  beforeMount -> onBeforeMount

  mounted -> onMounted

  beforeUpdate -> onBeforeUpdate

  updated -> onUpdated

  beforeUnmount -> onBeforeUnmount

  unmounted -> onUnmounted

  errorCaptured -> onErrorCaptured

  renderTracked -> onRenderTracked

  renderTriggered -> onRenderTriggered

  activated -> onActivated

  deactivated -> onDeactivated

## 三、自定义hook函数

#### 3.1.定义：

![image-20210820082033298](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210820082033298.png)

3.2.使用

- 创建一个hooks文件夹，里面放hook函数文件
- 引入函数，函数需要return返回值，因为要定义在setup里

## 四、toRef和toRefs

### 4.1、定义

![image-20210822101537108](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822101537108.png)

### 4.2、使用

#### 1.一般使用toRef

![image-20210822103915612](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822103915612.png)

- toRef的使用，这样可以在模板里使用reactive对象的时候，更加简便，而且不会使数据分家

- 如果使用ref的话，会使数据分家，修改数据不会跟源数据一致
- toRef相当于引用，ref相当于复制

#### 2.对象使用toRefs（常用）

...**toRefs**(person)

这样可以解析第一层属性值，访问数据可省略目标对象名

访问方式：

```vue
<h2 v-show="name">name:{{ name }}</h2>
<h2>age: {{ age }}</h2>
```

## 五、不常用Composition API（组合式API）

### 5.1.shallowReactive与shallowRef

![image-20210822150722199](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822150722199.png)

- shallowReactive使用方法和作用效果与reactive一致，但是只解析第一层，不会deep（深层次）的作用
- shallow:浅层次，就指只能浅层次的解析数据为响应式数据
- shallowRef如果处理对象，作用效果就不会和ref一致，不会形成响应式数据，想要修改就要替换对象

### 5.2.readonly与shallowReadonly

![image-20210822151555587](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822151555587.png)

- readonly：

![image-20210822151813678](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822151813678.png)

不能修改响应式数据了

- shallowReadonly

作用效果与readonly一致，不过只对第一层数据有作用效果



### 5.3.toRow与markRow

![image-20210822152456643](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822152456643.png)

### 5.4.customRef

![image-20210822152610792](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822152610792.png)

```js
function myRef(value) {
      return customRef((track, trigger) => {
        return {
          get() {
            console.log("有人从myRef当中获取数据");
            track();//通知vue追踪value的变化
            return value;
          },
          set(newValue) {
            value = newValue;
            console.log("有人从myRef当中修改数据");
            trigger();//通知vue去重新解析模板
          },
        };
      });
    }
    let keyword = myRef(5);
```

### 5.5.provide与inject

![image-20210822154144734](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822154144734.png)

#### 1.祖先组件传入

```js
import {provide,reactive} from 'vue'
setup{
    let house = reactive({
        name:'',
        money:''
    })
    let h = provide(house,'house')
}
```

provide作用域：子孙后代都能使用

![image-20210822154729070](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822154729070.png)

#### 2.后代组件传出

```js
import {inject} from 'vue'
setup{
    let house = inject('house')
}
```



### 5.6.响应式数据检查

![image-20210822155407059](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822155407059.png)

![image-20210822155539491](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822155539491.png)



## 六、新的组件

### 6.1.Fragment

![image-20210822160344883](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822160344883.png)

### 6.2.Teleport

![image-20210822160439194](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822160439194.png)

teleport可以将目标结构传送到指定位置，to属性指明传送的指定位置

teleport一般在封装弹窗组件dialog当中使用



6.3.异步组件

![image-20210822161606799](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210822161606799.png)

