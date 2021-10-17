# 导出excel文件

首先明确这时的请求头的content-type不应该是一般标准格式or json格式了

这时的content-type是application/vnd.ms-excel

**manage.js**

```js
/**
 * 导出文件 用于文件导出
 * @param url
 * @param fileName 
 * @param that
 * @returns {*}
 */
export function toExport(url, fileName, that) {
  that.$message.success('文件开始下载')
  if (!fileName || typeof fileName != 'string') {
    fileName = '导出文件'
  }
  downFile(url).then((data) => {
    if (!data) {
      that.$message.warning('文件下载失败')
      return
    }
    if (typeof window.navigator.msSaveBlob !== 'undefined') {
      window.navigator.msSaveBlob(
        new Blob([data], {
          //请求头
          type: 'application/vnd.ms-excel',
        }),
        fileName + '.xlsx'
      )
    } else {
      let url = window.URL.createObjectURL(
        new Blob([data], {
          type: 'application/vnd.ms-excel',
        })
      )
      let link = document.createElement('a')
      link.style.display = 'none'
      link.href = url
      //设置下载后文件的格式和名称
      link.setAttribute('download', fileName + '.xlsx')
      document.body.appendChild(link)
      link.click()
      that.$message.success('文件下载完成')
      document.body.removeChild(link) //下载完成移除元素
      window.URL.revokeObjectURL(url) //释放掉blob对象          
    }
  })
}
```

editManageItem.vue

```vue
<template>
<div>
    <button @click="inputData" type="primary">
        导入
    </button>
</div>
</template>
<script>
    import { toExport } from '@/api/manage'
    export default{
        data(){
            
        },
        methods:{
            inputData() {// 下载模板
                let isInput = confirm('是否使用导入数据进行填报？(先下载模板填报后再上传导入)')
                if (isInput) {
                    // 请求地址
                    let url = `/templateManagement/downloadImportTemplate/${this.templateId}`
                    let that = this
                    let tempName = this.tempName
                    toExport(url, tempName, that)
                    this.dataModule = true
                }
            },
        }
    }
</script>
<style>
</style>
```

