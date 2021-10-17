# method踩坑：

事因：在method当中写了一个递归函数,遍历一个**树形结构的数组**，但是在函数内部使用的时候，函数不能调用，系统提醒是无意义函数

```js
readColumns(nodes=[]){
    for(let item of nodes){
        if(!item.children){
            item.editRender = this.editRender
        }else if(item.children && item.children.length){
            readColumns(item.children)//系统提示无意义函数
        }
    }
    return nodes
}
```

**原因：**因为在Vue的method当中，调用一个method当中的函数，必须要加上this，否则无法找到这个函数到底是哪儿的

正确写法：

```js
eadColumns(nodes=[]){
    for(let item of nodes){
        if(!item.children){
            item.editRender = this.editRender
        }else if(item.children && item.children.length){
            this.readColumns(item.children)//加this
        }
    }
    return nodes
}
```

