<template>
 <div>
   顾客管理
   <!-- 按钮 -->
   <el-button type="warning" @click="toAddHandler">添加</el-button>
   <!-- 表格 -->
   <el-table :data="customers">
     <el-table-column label="编号" prop="id"></el-table-column>
     <el-table-column label="姓名" prop="username"></el-table-column>
     <el-table-column label="真实姓名" prop="realname"></el-table-column>
     <el-table-column label="联系方式" prop="telephone"></el-table-column>
     <el-table-column label="操作">
         <template v-slot="slot">
           <!-- 操作 -->
       <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
       <a href="" @click.prevent="toUppdataHandler(slot.row)">修改</a>
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
 <el-form :model="form" label-width="80px">
   <el-form-item label="用户名">
     <el-input v-model="form.username"></el-input>
   </el-form-item>
   <el-form-item label="密码">
     <el-input v-model="form.password" type="password"></el-input>
   </el-form-item>
   <el-form-item label="真实姓名">
     <el-input v-model="form.realname"></el-input>
   </el-form-item>
   <el-form-item label="电话">
     <el-input v-model="form.telephone"></el-input>
   </el-form-item>
 </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler">取 消</el-button>
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
      let url="http://localhost:6677/customer/findAll"
      request.get(url).then((response)=>{
        //将查询结果赋值给customer
          this.customers=response.data;
      })
      },
        toAddHandler(){
        this.visible=true;
        this.title="录入顾客信息";
        //将form变为初始值
        this.form={
        }
           },
        closeModalHandler(){
        this.visible=false;
        },
        toUppdataHandler(row){
        this.visible=true;
        this.title="修改顾客信息";
        this.form=row; 
        },
        //通过request与后台数据进行交互,携带参数
        submitHandler(){  
          let url="http://localhost:6677/customer/saveOrUpdate";
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
          alert(id);
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口，完成删除操作
          let url="http://localhost:6677/customer/deleteById?id="+id;
          request.get(url).then((response)=>{
                    this.loadData();
                    this.$message({
                    type: 'success',
                    message: response.message
                  });
           })
         
        });
        }
           },
    //用于存放要向网页中显示的数据
    data(){
      return{
       visible:false,
       title:"title",
       form:{},
       customers:[]
      }
    },
    created(){
     this.loadData()
    }
}
</script>
<style scoped>

</style>