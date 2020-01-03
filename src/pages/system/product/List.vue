<template>
 <div>
   顾客管理
   <!-- 按钮 -->
   <el-button type="warning" @click="toAddHandler">添加</el-button>
   <!-- 表格 -->
   <el-table :data="products">
     <el-table-column label="编号" prop="id"></el-table-column>
     <el-table-column label="产品名称" prop="name"></el-table-column>
     <el-table-column label="价格" prop="price"></el-table-column>
     ><el-table-column label="描述" prop="description"></el-table-column>
     <el-table-column label="所属产品" prop="categoryId"></el-table-column>
    <el-table-column label="操作">
    <template v-slot="slot">
           <!-- 操作 -->
       <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
       <a href="" @click.prevent="toUppdataHandler(slot.row)">修改</a>
       <a href="">详情</a>
         </template>
         </el-table-column>
   </el-table>
   <!-- 分页 -->
   <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!-- 点击添加按钮，弹窗 -->
<el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%"
  >
  {{form}}
 <el-form :model="form" label-width="80px">
   <el-form-item label="产品名称">
     <el-input v-model="form.name"></el-input>
   </el-form-item>
   <el-form-item label="价格">
     <el-input v-model="form.price"></el-input>
   </el-form-item>
   <el-form-item label="描述" >
    <el-input type="textarea" v-model="form.description"></el-input>
  </el-form-item>
 </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler" type="reset">取 消</el-button>
    <el-button type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
 </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法   
    methods:{
      loadData(){
 //vue实例创建完毕，要执行的操作
      let url="http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        //将查询结果赋值给customer
          this.products=response.data;
      })
      },
        toAddHandler(){
        this.visible=true;
        this.title="添加产品信息"
           },
        closeModalHandler(){
        this.visible=false;
        },
        toUppdataHandler(){
        this.visible=true; 
        this.title="修改产品信息"
        },
        //通过request与后台数据进行交互,携带参数
        submitHandler(){  
          let url="http://localhost:6677/product/saveOrUpdate";
          request({
            url,
            method:"POST",
            headers:{
              //告诉后台要传输什么类型字符串
              "Content-Type":"application/x-www-form-urlencoded"
            },
            //将数据打包成要传输的类型字符串
            data:querystring.stringify(this.form)}).then((response)=>{
            //请求结束，模态框关闭
            this.closeModalHandler();
            this.loadData();
            this.$message({
              type:"success",
              message:response.message
            })                   
            })

        },
        toDeleteHandler(id){
            //确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
          type: 'success',
          message: '删除成功!'
          });
        });
        }
           },
    //用于存放要向网页中显示的数据
    data(){
      return{
       title:"title",
       visible:false,
       form:{},
       products:[]
      }
    },
    created(){
     this.loadData()
    }
}
</script>
<style scoped>

</style>