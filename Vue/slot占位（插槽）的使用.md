# slot占位（插槽）的使用

## 1.基本作用与用法：

在子组件当中写slot，可以使父组件调用子组件时传入标签给子组件

父组件app.vue

```vue
<template>
  <div id="app">
    <Form>
      <h2>this app's information</h2>
      <p>test slot</p>
    </Form>
  </div>
</template>
<script>
import Form from "./components/form.vue";
export default {
  name: "app",
  components: {
    Form,
  },
};
</script>

```

子组件form.vue

```vue
<template>
  <div>
    <h3>this is form</h3>
      <!--留位给传入的标签-->
    <slot></slot>
  </div>
</template>
<script>
export default {
  data() {
    return {};
  },
};
</script>

```

## 2.高级使用，指定位置

父组件app.vue

```vue
<template>
  <div id="app">
    <Form>
      <h2 slot="title">this app's information</h2>
      <p slot="text">test slot</p>
    </Form>
  </div>
</template>
<script>
import Form from "./components/form.vue";
export default {
  name: "app",
  components: {
    Form,
  },
};
</script>

```

子组件form.vue

```vue
<template>
  <div>
      <slot name="text"></slot>
    <h3>this is form</h3>
      <!--留位给传入的标签-->
    <slot name="title"></slot>
  </div>
</template>
<script>
export default {
  data() {
    return {};
  },
};
</script>

```

## 3.传入的标签样式：

无论是在传入or传出的页面写标签的样式都可以实现，一般在父组件里写

## 4.slot传入的标签里的值的设定

父组件app.vue(传入方)

```vue
<template>
  <div id="app">
    <Form>
      <h2 slot="title">{{title}}</h2>
      <p slot="text">test slot</p>
    </Form>
  </div>
</template>
<script>
import Form from "./components/form.vue";
export default {
  name: "app",
  components: {
    Form,
  },
  data(){
      return{
          //在传入方（父组件)当中写
          title:"this app's information"
      }
  }
};
</script>
```

在传入方（父组件)当中写，不能在子组件当中写