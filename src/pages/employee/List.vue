<template>
  <div>
      员工管理
   <!-- 按钮 -->
   <el-button type="warning" @click="toAddHandler">添加</el-button>
   <!-- 表格 -->
   <el-table :data="employees">
     <el-table-column label="编号" prop="id"></el-table-column>
     <el-table-column label="姓名" prop="username"></el-table-column>
     <el-table-column label="真实姓名" prop="realname"></el-table-column>
     <el-table-column label="联系方式" prop="telephone"></el-table-column>
     <el-table-column label="密码" prop="password"></el-table-column>
     <el-table-column label="身份证号" prop="idCard" width="200px"></el-table-column>
     <el-table-column label="银行账号" prop="bankCard" withd="200px"></el-table-column>
     <el-table-column label="操作" fixed="right">
     <!-- slot获取当前行数据 -->
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
   <el-form-item label="性别">
    <el-radio-group v-model="form.gender">
    <el-radio label="男">男</el-radio>
    <el-radio label="女">女</el-radio>
    </el-radio-group>
   </el-form-item>
   <el-form-item label="密码">
     <el-input v-model="form.password" type="password"></el-input>
   </el-form-item>
   <el-form-item label="真实姓名">
     <el-input v-model="form.realname"></el-input>
   </el-form-item>
   <el-form-item label="身份证号">
     <el-input v-model="form.idCard"></el-input>
   </el-form-item>
   <el-form-item label="银行账号">
     <el-input v-model="form.bankCard"></el-input>
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
 methods:{
   //重载员工数据
   loadData(){
     let url="http://localhost:6677/waiter/findAll"
     //箭头函数中的this指向外部函数的this
     request.get(url).then((response)=>{
       this.employees=response.data;
     })
   },
    submitHandler(){
      this.visible=false;
      let url="http://localhost:6677/waiter/saveOrUpdate"
      request({
            url,
            method:"POST",
            headers:{
              //告诉后台要传输什么类型字符串
              "Content-Type":"application/x-www-form-urlencoded"
            },
            //将数据打包成要传输的类型字符串
            data:querystring.stringify(this.form)}).then((response)=>{
              this.closeModalHandler();
              this.loadData();
            this.$message({
            type: 'success',
            message: response.message
          });
            })
    },
      toAddHandler(){
          this.visible=true;
          this.title="录入员工信息";
          this.form={};
          },
        closeModalHandler(){
       this.visible=false;
        },
        toUppdataHandler(row){
           this.visible=true; 
           this.title="修改员工信息";
           this.form=row; 
        },
          toDeleteHandler(id){
            //确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
         //调用后台接口，完成删除操作
          let url="http://localhost:6677//waiter/deleteById?id="+id;
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
 data(){
       return{
       visible:false,
       title:"录入信息",
       form:{},
       employees:[]
        }
    },
 created(){
   this.loadData();
 }
}
</script>

<style scoped>

</style>