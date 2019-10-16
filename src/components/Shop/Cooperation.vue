<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>商家管理</el-breadcrumb-item>
        <el-breadcrumb-item>合作申请</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">


      <el-table :data="list" border stripe size="small" style="width: 60%">


        <el-table-column prop="store_name" label="店铺名称" min-width="200" align="center">
        </el-table-column>

        <el-table-column prop="phone" label="电话" min-width="100" align="center">
        </el-table-column>

        <el-table-column prop="updated_at" label="提交时间" min-width="100" align="center">
        </el-table-column>


      </el-table>

      <el-pagination style="float:left;margin-top:20px;" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="limit" @current-change="handleCurrentChange" @size-change="handleSizeChange" layout="total,sizes, prev, pager, next, jumper" :total="count" prev-text="上一页" next-text="下一页">
      </el-pagination>
    </el-col>




  </el-row>
</template>



<script>
  import { sapplies } from '../../api/api';


  export default {
    data() {
      return {
        list:[],
        currentPage: 1,
        count:0,
        limit:10,
      };
    },

    methods:{
      getlist(){
        var allParams = '?page='+ this.currentPage + '&limit=' + this.limit;
        sapplies(allParams).then((res) => {
          this.list=res.data.data;
          this.count=res.data.count;
        });
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
