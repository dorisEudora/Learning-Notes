# 缓存机制keep-alive

keep-alive包裹组件，可以在当前页面包裹的组件内缓存已请求到的数据和一些输入的数据，

```vue
<keep-alive>
    <!--动态组件-->
    <component :is="comp"></component>
</keep-alive>
<button @click="comp = 'form-one'" 
    show form one
</button>
<button @click="comp = 'form-two'" 
    show form two
</button>
<script>
    import formOne from "./component/formOne.vue"
    import formTwo from "./component/formTwo.vue"
    export default{
        data(){
            return{
                comp:"form-one"
            }
        },
        methods:{
            
        },
        components:{
            "form-one":formOne,
            "form-two":formTwo
        }
    }
</script>
```

