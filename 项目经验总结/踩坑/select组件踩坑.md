# select组件踩坑



事情是这样的，使用了a-select组件，一开始应该是绑定（:key）的item.value,但是不小心改成了item.id，value修改后触发的事件触发以后，以为传回的value是item.value，结果是item.id，一直无法进行匹配

### 踩坑事件：

:**key绑定的内容**  就是 **value修改后触发的事件触发后回调的参数**  ，回调参数并不是根据value变化，而是根据:key绑定的内容！！！

```vue
<template>
<a-form-item label="模板类别">
                <a-select
                  v-decorator="[
                  'typeId',
                  {
                    rules: [{ required: true, message: '请选择表的模板类别' }],
                  },
                ]"
                  placeholder="请选择表的模板类别"
                  @change="changeType"
                >
                  <a-select-option v-for="item in list" :key="item.typeName">
                    {{ item.typeName }}
                  </a-select-option>
                </a-select>
              </a-form-item>
</template>
<script>
    export default{
        data(){
        	return{
            
        	}
    	},
        methods:{
            //回调函数的参数value是:key绑定的
            changeType(value) {
        	this.newExcel.typeName = value
        	let ts = this
        	for (let i in ts.list) {
          		if (ts.list[i].typeName === value) {
            	ts.newExcel.typeid = ts.list[i].typeid
          }
        }
      },
        }
    }   
    
</script>
```

