# vue3的Compiler原理

## 1.优化

相比于vue2，compiler优化方案上：使js处理更加快速，对于一些静态资源和不变化的数据，不会重复加载，提高性能；按需加载

vue3中文文档：https://v3.cn.vuejs.org/

### 1.1.优化案例1：

1. 静态Node不再做更新处理（hoistStatic -》SSR优化）
2. 静态绑定的class，id不再作更新处理
3. 结合打包标记的PatchFlag，进行更新分析（动态绑定）

### 1.2.优化2：

1. 事件监听Cache缓存处理（cacheHandlers），这样不会多次实例化一些事件，从而提高处理速度

2. hoistStatic自动针对多静态节点进行优化，输出字符串

   

## 2.新增功能

#### 2.1.Fragment：不受根节点限制，渲染函数可接收Array

#### 2.2.Teleport：类似Portal，随用随取

比如弹窗，actions

#### 2.3.Suspense：嵌套的异步依赖

比如：async `setup（）`

## 3.按需加载（配合vite）&组合API

按需加载：根据需求进行加载使处理更加快速

### 3.1.组合API

#### 1、setup函数：

所有响应式数据创建和声明的地方，以前是data里

```vue
<tempalte>
    <div>
        <HelloWorld></HelloWorld>
    </div>
</tempalte>
<script setup>
 /*
 组件导入,无需再如vue2当中进行components定义，直接可以使用了*/
import HelloWorld from 'comps/HelloWorld.vue'
    
import { defineProps, reactive } from 'vue'

defineProps({
  msg: String
})

const state = reactive({ count: 0 })
</script>
```



## 4.vue2与vue3处理进行对比

### 4.1.vue2

#### 1、vue2 的options API 逻辑：

![image-20210731092856370](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210731092856370.png)

#### 2、Vue2逻辑复用方式

![image-20210731093108438](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210731093108438.png)

### 4.2.vue3

vue3使用函数式编程，不是options API 是Composition API

![image-20210731093302520](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210731093302520.png)

#### 1、Composition Api的特点

为vue应用提供更好的逻辑复用和代码组织

1. 复杂组件逻辑进行分离
2. **组件间逻辑共享**

#### 2、Vue3的变化

1. 组合式API+函数式编程
2. **组件间逻辑共享**