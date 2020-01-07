<template>
  <div>
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="所有订单" name="first">
        <!-- 表格 -->
        <el-table :data="orders.list">
          <el-table-column prop="id" label="订单编号"></el-table-column>
          <el-table-column prop="orderTime" label="下单时间"></el-table-column>
          <el-table-column prop="total" label="总价"></el-table-column>
          <el-table-column prop="status" label="状态"></el-table-column>
          <el-table-column prop="customerId" label="顾客id"></el-table-column>
          <el-table-column prop="waiterId" label="员工id"></el-table-column>
          <el-table-column prop="addressId" label="地址id"></el-table-column>
          <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
              <a href @click.prevent="getOrderLinesByOrderId">详情</a>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <!-- /表格 -->
      <!-- 分页--> 
        <el-pagination
            layout="prev, pager, next"
            :total="orders.total" @current-change="pageChangeHandler">
         </el-pagination>
        <!-- /分页--> 
      <el-tab-pane label="待支付" name="second"></el-tab-pane>
      <el-tab-pane label="待派单" name="third"></el-tab-pane>
      <el-tab-pane label="待接单" name="fourth"></el-tab-pane>
      <el-tab-pane label="待服务" name="fifth"></el-tab-pane>
      <el-tab-pane label="待确认" name="sixth"></el-tab-pane>
      <el-tab-pane label="已完成" name="seventh"></el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
import request from "@/utils/request";
import querystring from "querystring";
export default {
  methods: {
    pageChangeHandler(page){
        this.params.page = page-1;
        this.loadData();
    },
    sendOrder() {},
    getOrderLinesByOrderId() {},
    handleClick(tab, event) {
      console.log(tab, event);
    },
    loadData() {
      let url = "http://localhost:6677/order/queryPage"
      request({
        url,
        method:"post",
        headers:{
            "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.params)
      }).then((response)=>{
          this.orders = response.data;
      });
    },
    
  },
  data() {
    return {
      activeName: "first",
      visible: false,
      orders: {},
      form: {
          type:"order"
      },
      params:{
          page:0,
          pageSize:10
      }
    };
  },
  created() {
    //在页面加载出来时加载数据
    this.loadData();
  }
};
</script>
<style lang="stylus" scoped></style>