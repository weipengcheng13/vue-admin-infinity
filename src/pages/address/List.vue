<template>
    <div>
        <!--按钮-->
        <el-button type="primary" @click="toAddHandler">添加地址</el-button>
        <el-button type="danger">批量删除</el-button>

        <!--表格-->
        
        <el-table :data="addresss">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="province" label="所属省份"></el-table-column>
            <el-table-column prop="city" label="城市"></el-table-column>
            <el-table-column prop="area" label="所属区域"></el-table-column>
            <el-table-column prop="address" label="所属街道"></el-table-column>
            <el-table-column prop="telephone" label="电话"></el-table-column>
            <el-table-column  label="操作">
                <template v-slot="slot">
                    <a class="el-icon-delete" href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a class="el-icon-edit" href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--表格结束-->

        <!--分页开始-->
        <!-- <el-pagination
            small
            layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!--分页结束-->

        <!--模态框-->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            --{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="省份">
                    <el-input v-model="form.province"></el-input>
                </el-form-item>
                <el-form-item label="城市">
                    <el-input v-model="form.city"></el-input>
                </el-form-item>
                <el-form-item label="区域">
                    <el-input v-model="form.area"></el-input>
                </el-form-item>
                <el-form-item label="街道">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
                <el-form-item label="电话">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'    //导入一个系统库，用来查询字符串
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
             let url="http://localhost:6677/address/findAll"
             request.get(url).then((response)=>{
            //将匿名函数改为箭头函数指向外部函数的this
            //将查询结果设置到customers中
            this.addresss = response.data;
        })
        },
        submitHandler(){
            //通过request与后台逻辑进行交互，并且携带参数
            let url="http://localhost:6677/address/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束，模态框关闭
                this.closeModalHandler();
                this.$message({
                    type:"success",
                    message:response.message
                })
                this.loadData();
            })
        },
        toDeleteHandler(id){
            //先确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口完成删除操作
          let url="http://localhost:6677/address/deleteById?id="+id;
          request.get(url).then((response)=>{
              //刷新数据
                this.loadData();
              //提示结果
                this.$message({
                type: 'success',
                message:response.message
                });
          })
          
        })
        },
        toUpdateHandler(row){
            //模态框的表单中显示出当前行的信息
            this.form=row;
            this.title="修改地址信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.visible=true;
            this.form={
            }
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            form:{
            },
            visible:false,
            title:"录入地址信息",
            addresss:[]
        }
    },
    //生命周期,VUE实例完毕后要执行的操作
    created(){
       this.loadData();
    }
}
</script>

<style scoped>