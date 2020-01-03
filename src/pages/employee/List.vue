<template>
  <div>
    <!-- 按钮 -->
    <div>
    <el-button type="primary" size="small"  @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    </div>

    <!-- 表格 -->
    <el-table :data="employees">
      <el-table-column prop="id" label="编号" fixed="left"></el-table-column>
      <el-table-column prop="realname" label="姓名" fixed="left"></el-table-column>
      <el-table-column prop="gender" label="性别"></el-table-column>
      <el-table-column prop="telephone" label="联系方式" width="120"></el-table-column>
      <el-table-column prop="idCard" label="身份证号" width="200"></el-table-column>
      <el-table-column prop="bankCard" label="银行卡号" width="200"></el-table-column>
      <el-table-column label="操作" fixed="right">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>

    <!-- 模态框 -->
    <el-dialog :title="title" :visible.sync="visible" width="60%" >
       <el-form :model="form" label-width="80px">
        <el-form-item label="真实姓名">
          <el-input v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="性别">
          <el-radio-group v-model="form.gender">
            <el-radio label="男">男</el-radio>
            <el-radio label="女">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone"></el-input>
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input v-model="form.idCard"></el-input>
        </el-form-item> 
        <el-form-item label="银行卡号">
          <el-input v-model="form.bankCard"></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="closeModalHandler" size="small">取 消</el-button>
        <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import request from '@/utils/request' 
import querystring from 'querystring'
export default {
  //用于存放要向网页中存放的数据
  data(){
    return{
      form:{
        type:"waiter"
      },
      title:"录入员工信息",
      visible:false,
      employees:[]
    }
  },
  created(){
    this.loadData();
  },
  //方法,存放网页中需要调用的方法
  methods:{
    loadData(){
      let url = "http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        this.employees = response.data;
      })
    },
    submitHandler(){
       let url = "http://localhost:6677/waiter/saveOrUpdate"
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        this.closeModalHandler();
        this.$message({
          type:"success",
          message:response.message
        });
        this.loadData();
      })
    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
          this.loadData();
        })
    },
    toUpdateHandler(id){
      this.title="修改员工信息";
      this.visible=true;
    },
    closeModalHandler(){
      this.visible=false;
    },
    toAddHandler(){
      this.title="录入员工信息";
      this.visible=true;
    }
  }
}
</script>

<style scoped>

</style>
