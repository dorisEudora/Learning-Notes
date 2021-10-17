# 导入Excel并发送到后端

### 思路

#### 一、view

1. 导入excel
2. table动态回显数据，区分图片格式，对boolean类型的数据回显成中文（是/否）

#### 二、js

1. 获取上传的excel文件，使用js的file对象

2. 判断根据file.name和文件大小来判断文件是否能上传

3. return false来阻止上传以后就提交

4. 使用formData将file存储下来

5. 触发提交按钮进行请求提交

   **重点：file对象和FormData的使用**

#### 三、踩过的坑

1. 前端解析excel--->xlsx工具库

   存在问题：**不能解析excel中的图片**。
   xlsx提供的方法：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621230942495.png)

2. java解析excel的两种方式

   - 传统的POI（强大，**支持解析图片**）——博主最终是采用这种方式实现的
   - 阿里巴巴的EasyExcel（**不支持解析图片**，目前一直有新版本迭代，简易新颖

### 1、view

#### （1）、导入Excel

代码中增加了回显sheet工作表的名称，目的在于与table中的数据连带

```vue
<el-row :gutter="20" type="flex" justify="sapce-between">
    <el-col :span="6">
        <el-button type="danger" v-for="(item,index) in sheetNameArr" :key="index" @click.native="changeTable(index)">
            {{item}}
        </el-button>
    </el-col>
    <el-col :span="1" :offset="12">
        <el-upload class='upload-demo' ref="upload" action="doUpload" :limit="1" :before-upload="beforeUpload">
            <el-button slot="trigger" szie="middle" type="primary" style="margin-right:20px">
            </el-button>
        </el-upload>
    </el-col>
</el-row>
```

## (2)、动态回显

```vue
 <el-table class="listTable_ele" :data="tableData" stripe height="650" style="width:100%" border >
            <el-table-column v-for="(item,index) in tableHead[0]" :key="index" :prop="index" :label="item">
                <template  slot-scope="scope">
                    <img v-if="index == 'imgs'" v-for="itm in scope.row.imgs" :src="itm" width="40" height="40">
                    <img v-if="index === 'packPics'" :src="scope.row.packPics"  width="scope.row.packPics!=''?50px:0" height="50px" >
                    <template v-else> {{ scope.row[scope.column.property]===true?"是":(scope.row[scope.column.property]===false?"否":scope.row[scope.column.property])}} </template>
                </template>
            </el-table-column>                               
            <el-table-column prop="operate" label="操作">
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="text" size="small" slot="reference" >编辑</el-button>
                    
                </template>
            </el-table-column>
</el-table>

```

### 2、js（重点

```vue
<script>
    data(){
        return{
            files:{},
            fileName:'',
            sheetNameArr:[],
            tableData1:[],
            tableData:[]，
            id:'',
            description:''
        },
        methods:{         
            beforeUpload(file){   
                console.log(file,'文件')
                this.files = file
                console.log(this.files)
                const extension = file.name.split('.')[1] === 'xls'
                const extension2 = file.name.split('.')[1] === 'xlsx'
                const extension3 = file.name.split('.')[1] === 'csv'
                const isLt5M = file.size  1024 / 1024 < 5
                if(!extension && !extension2 && !extension3){
                    this.$message.warning('上传模板只能是xls、xlsx、cvs格式！')
                    return
                }
                if(!isLt5M){
                    this.$message.warning('上传模板大小不能超过5MB！')
                    return
                }
                this.fileName = file.name
                setTimeout(() => {
                    this.submitUpload()
                },500)
                return false	// **返回false不会自动上传
            },
                //上传excel
            submitUpload(){
                let ts = this
                console.log('上传'+this.files.name)
                if(this.fileName == ""){
                    this.$message.warning('请选择要上传的文件')
                    return false
                }
                //文件传输过程中，file不能直接传输，必须转为FormData
                let fileFormData = new FormData()
                //除了文件的其他信息
                fileFormData.append("id", this.id)
                fileFormData.append("description", "excel上传测试")
                //filename是键，file是值，就是要上传的文件、test是要传的文件名
                fileFormData.append("files",this.files,this.fileName)
                //接口请求头
                let requestConfig = {
                    headers:{
                        'Conten-Type':'multipart/form-data'
                    }
                }
                //执行上传excelList
                //请求路径
                this.url = '/local/ExcelRead/upload'
                this.$axios.post(this.url,fileFormData).then(res => {
                    let excelDate = res.data.data;
                    let i=0;
                    for(var sheetName in excelDate){
                        let sheetData = excelDate[sheetName];
                        ts.sheetNameArr.push(sheetName) ;//存储sheet名称
                        if(i==0){
                            ts.tableData1 = sheetData;
                            ts.tableData = ts.tableData1;
                          
                        }else{
                            ts.tableData2 = sheetData
                        
                        }
                        i++;
                    }
                }).catch(err => {
                    this.$message.warning(err.message)
                    this.$message.error("excel上传失败，请重新上传！")
                }).finally(() => {
                    
                })
            }    
        },
            
    }
</script>

```

