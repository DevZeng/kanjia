<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>商家管理</el-breadcrumb-item>
        <el-breadcrumb-item>商家列表</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">

     <el-form :inline="true">
      <el-form-item>
        <el-button type="primary" size="small" @click="newone">新增店铺</el-button>
      </el-form-item>
    </el-form>


    <el-table :data="list" border stripe size="small" style="width: 80%">
      <el-table-column prop="id" label="编号" min-width="100" align="center">
      </el-table-column>

      <el-table-column prop="name" label="店铺名称" min-width="200" align="center">
      </el-table-column>

      <el-table-column prop="manager" label="掌柜名称" min-width="200" align="center">
      </el-table-column>

      <el-table-column prop="address" label="地址" min-width="200" align="center">
      </el-table-column>

      <el-table-column prop="created_at" label="创建时间" min-width="200" align="center">
      </el-table-column>

      <el-table-column label="操作" min-width="200" align="center">
       <template slot-scope="scope">
        <el-button type="primary" size="mini" @click="handleChange(scope.row)">修改</el-button>
        <!-- <el-button type="primary" size="mini" @click="handleDel(scope.row)">删除</el-button> -->
      </template>
    </el-table-column>

  </el-table>

  <el-pagination style="float:left;margin-top:20px;" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="limit" @current-change="handleCurrentChange" @size-change="handleSizeChange" layout="total,sizes, prev, pager, next, jumper" :total="count" prev-text="上一页" next-text="下一页">
  </el-pagination>
</el-col>




</el-row>
</template>



<script>
  import { shopGet } from '../../api/api';


  export default {
    data() {
      return {
        filter:{
          name:''
        },
        list:[],
        currentPage: 1,
        count:0,
        limit:10,
        dialogInfoVisible:false,
        shopinfo:{
          id:1
        },
      };
    },

    methods:{
      getlist(){
        var allParams = '?page='+ this.currentPage + '&limit=' + this.limit;
        shopGet(allParams).then((res) => {
          this.list=res.data.data;
          this.count=res.data.count;
        });
      },

      detail(){
        this.dialogDelVisible=true
      },

      save(){
        this.dialogInfoVisible=false
      },

      search(){
        this.getlist()
      },

      clear(){
        this.filter={
          name:''
        }
      },

      newone(){
        this.$router.push({ path: '/Shop/Newshop' });
      },

      handleChange(row){
        sessionStorage.setItem('shopid',row.id);
        this.$router.push({ path: '/Shop/Newshop' });
      },

      handleCurrentChange(val) {
        this.currentPage = val;
        this.getlist();
      },

      handleSizeChange(val){
        this.limit = val;
        this.getlist();
      },
    },

    mounted: function () {
      this.getlist();
    }
  }
</script>


<style>
.logo{
  max-width: 300px;
  margin-left: 20px;
}
</style>
