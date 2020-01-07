<template>
  <div>
<!-- 添加按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>

<!-- 添加表格 -->
    <el-table :data="comments">
      <el-table-column prop="id" label="编号" fixed="left"></el-table-column>
      <el-table-column prop="content" label="内容" ></el-table-column>
      <el-table-column prop="commentTime" label="评论时间" width="200"></el-table-column>
      <el-table-column label="操作" fixed="right">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>

<!-- 添加分页 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>

<!-- 模态框 -->
    <el-dialog :title="title" :visible.sync="visible" width="60%" >
      <el-form :model="form" label-width="80px">
        <el-form-item label="内容">
          <el-input v-model="form.content"></el-input>
        </el-form-item>
        <el-form-item label="评论时间">
          <el-input v-model="form.commentTime"></el-input>
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
import request from '@/utils/request' // @ = src目录
import querystring from 'querystring'
export default {
  //用于存放要向网页中存放的数据
  data(){
    return{
      form:{
        type:"comment"
      },
      title:"添加评论",
      visible:false,
      comments:[]
    }
  },
  //生命周期,vue实例创建完毕
  created(){
    this.loadData();
  },
  //方法,存放网页中需要调用的方法
  methods:{
    loadData(){
      let url = "http://localhost:6677/comment/findAll"
      request.get(url).then((response)=>{ 
      //将匿名函数改为箭头函数  function（response）{}
      //将查询结果设置到customer
      this.comments = response.data;
    })
    },
    submitHandler(){
      //this.form(对象) ---字符串--> 后台 {type:'customer',age:12}
      //通过request与后台进行交互，并且携带参数
      let url = "http://localhost:6677/comment/saveOrUpdate"
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        //模态框关闭
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
          //调用后台接口，完成删除操作
        let url = "http://localhost:6677/comment/deleteById?id="+id;
        request.get(url).then((response)=>{
          //刷新数据，提示结果
        this.loadData();
        this.$message({
          type: 'success',
          message:response.message
        });
        })
        })
    },
    toUpdateHandler(row){
      this.title = "修改评论";
      this.visible = true;
      this.form = row;
    },
    closeModalHandler(){
      this.visible=false;
    },
    toAddHandler(){
      this.title = "添加评论";
      this.visible = true;
      this.form = {
        type:"comment"
      }
    }
  }
}
</script>


<style scoped>

</style>
