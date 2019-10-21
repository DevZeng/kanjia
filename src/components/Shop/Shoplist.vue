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
        <el-button type="danger" size="mini" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
      </template>
    </el-table-column>

  </el-table>


  <el-pagination style="float:left;margin-top:20px;" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="limit" @current-change="handleCurrentChange" @size-change="handleSizeChange" layout="total,sizes, prev, pager, next, jumper" :total="count" prev-text="上一页" next-text="下一页">
  </el-pagination>
</el-col>


<el-col>
  <el-dialog title="删除不可恢复，是否确定删除？" :visible.sync="dialogDelVisible" width="30%">
    <div slot="footer" class="dialog-footer">
      <el-button type="primary" @click="submitdel()" size="small">确 定</el-button>
      <el-button @click="dialogDelVisible = false" size="small">取 消</el-button>
    </div>
  </el-dialog>
</el-col>

</el-row>

</template>



<script>
  import { shopGet, shopaddDel , delStore} from '../../api/api';


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
        delId:'',
        dialogDelVisible:false,
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
      handleDelete(index, row) {
        console.log(row);
    this.dialogDelVisible = true;
    this.delId = row.id;
  },

  submitdel(){
    this.dialogDelVisible = false;
    var allParams='?id='+this.delId
    delStore(allParams).then((res) => {
        // console.log(res)
        if (res.msg === "ok") {
         this.$message({
          message: '删除成功',
          type: 'success'
        });
         this.getlist();
         this.dialogDelVisible = false;
       } else {
         this.$message({
          message: res.msg,
          type: 'error'
        });
       }
     });
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
