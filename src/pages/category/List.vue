<template>
    <div>

    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->

    <!-- 表格 -->
    <el-table :data="categorys">
        <el-table-column type="selection"></el-table-column>
      <el-table-column width="100"  prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="栏目名称"></el-table-column>
      <el-table-column prop="num" label="序号"></el-table-column>
      <el-table-column prop="parentId" label="父栏目"></el-table-column>
      <el-table-column fixed="right" label="操作">
              <template v-slot="slot"><!--获取操作数据-->
              <a class="el-icon-delete" href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
              <a class="el-icon-edit" href="" @click.prevent="toUpdataHandler(slot.row)">编辑</a>
              </template>
            </el-table-column>
        </el-table>
    <!-- /表格结束 -->

    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
    <!-- /分页结束 -->

    <!--模态框-->
            <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">
      {{form}}
      <span>
         <el-form :model="form" label-width="80px">
          <el-form-item label="栏目名称">
            <el-input v-model="form.name"><!--v-model双向数据绑定-->
            </el-input>
          </el-form-item>
          <el-form-item label="序号">
            <el-input v-model="form.num"><!--v-model双向数据绑定-->
            </el-input>
          </el-form-item>
          <el-form-item label="父栏目">
            <el-select  v-model="form.parentId" placeholder="请选择">
             <el-option
             v-for="item in options"
             :key="item.id"
             :label="item.parentId"
            :value="item.id">
             </el-option>
            </el-select>
          </el-form-item>      
        </el-form>
      </span>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!--/模态框-->

    </div>
</template>

<script>
import request from "@/utils/request"//@=src目录,第三方库需要加路径
import querystring from "querystring"//系统库，不用加路径
export default {
    methods:{
      loadCategoty(){
        let url="http://localhost:6677/category/findAll";
        request.get(url).then((response)=>{
            // 将查询结果设置到customers中，this只想外部函数的this
            this.options = response.data;
        })
    },
          //提交方法
        submitHandler(){
          //前端向后台发送请求，完成数据的保存操作
    let url="http://localhost:6677/category/saveOrUpdate"
    request({
      url,
      method:"POST",
      headers:{
        "Content-Type":"application/x-www-form-urlencoded"
      },
      data:querystring.stringify(this.form)
       }).then((response)=>{
      //关闭模态框
      this.closeModalHandler();
      //刷新
      this.loadData();
      //提示消息
      this.$message({
        type:"success",
        message:request.message
      })
      })     
        },

        //重载数据
        loadData(){
//vue实例创建完毕执行操作
let url="http://localhost:6677/category/findAll"
request.get(url).then((response)=>{
  //过滤后的结果
  this.categorys = response.data  //把查询结果放置到product中
})
    },

        toAddHandler(){//添加
            this.visible=true;
            this.title = "录入栏目信息"
            this.loadCategoty();
        },

        closeModalHandler(){//取消方法
            this.visible = false;
        },

        toDeleteHandler(id){
           //确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口,完成删除操作
          let url = "http://localhost:6677/category/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示结果
             this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
          })
         
        })
        },
      
        toUpdataHandler(row){
          this.title="修改栏目信息",
            this.visible=true;
            this.form = row;
        },
    },
    data(){
        return{
        title:"录入栏目信息",
        visible:false,
        categorys:[],
        options:[],
        form:{
      }
        }
    },
    created(){
        //在页面加载出来的时候显示数据
    this.loadData();
  }
}
</script>
<style scoped>

</style>