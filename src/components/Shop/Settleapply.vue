<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>商家管理</el-breadcrumb-item>
        <el-breadcrumb-item>代理申请</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">

      <el-table :data="list" border stripe size="small">
        <el-table-column prop="store_name" label="商家名称" min-width="100" align="center">
        </el-table-column>
        <el-table-column prop="phone" label="申请人电话" min-width="150" align="center">
        </el-table-column>
        <el-table-column prop="address" label="地址" min-width="180" align="center">
        </el-table-column>
        <el-table-column prop="state" label="申请状态"  min-width="150" align="center">
          <template slot-scope="scope">
            <el-button size="mini" type="info" v-if="scope.row.state==1" @click="check(scope.row)">未处理</el-button>
            <el-button size="mini" type="success" v-if="scope.row.state==2">已处理</el-button>
          </template>
        </el-table-column>
        
      </el-table>

      <el-pagination style="float:left;margin-top:20px;" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="limit" @current-change="handleCurrentChange" @size-change="handleSizeChange" layout="total,sizes, prev, pager, next, jumper" :total="count" prev-text="上一页" next-text="下一页">
      </el-pagination>
    </el-col>


    <el-col>
      <el-dialog title="拒绝不可恢复，是否确定拒绝？" :visible.sync="dialogDelVisible" width="30%">
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="submitdel()">确 定</el-button>
          <el-button @click="dialogDelVisible = false">取 消</el-button>
        </div>
      </el-dialog>
    </el-col>

  </el-row>
</template>



<script>

  import { sapplies } from '../../api/api';
  import { sapplyPost } from '../../api/api';
  import { applyCheck }from '../../api/api';


  export default {
    data() {
      return {
        list:[],
        currentPage: 1,
        count:0,
        limit:10,
        dialogDelVisible:false,
        delId:'',

        checkper1:false,
      };
    },

    methods:{
      checkPer(){
        var per = sessionStorage.getItem('permissions');

        if(per.indexOf('settleCheck')>-1){
          this.checkper1=true;
        }
      },



      getlist(){
        var allParams = '?page='+ this.currentPage + '&limit=' + this.limit;
        sapplies(allParams).then((res) => {
          this.list=res.data.data;
          this.count=res.data.count;
        });
      },
      check(row){
        // console.log('check');
        var allParams = '?id='+ row.id+'&state=2';
        applyCheck(allParams).then((res) => {
         this.getlist();
       });
      },

      handleEdit(index, row){
        var allParams={
          id:row.check_id,
          state:1
        }
        sapplyPost(allParams).then((res) => {
          console.log(res)
          if (res.msg === "ok") {
           this.$message({
            message: '审核成功',
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

      handleDelete(index, row) {
        this.dialogDelVisible = true;
        this.delId = row.check_id;
      },

      submitdel(){
        this.dialogDelVisible = false;
        var allParams={
          id:this.delId,
          state:2
        }
        sapplyPost(allParams).then((res) => {
          console.log(res)
          if (res.msg === "ok") {
           this.$message({
            message: '审核成功',
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
      this.checkPer();
    }
  }
</script>


<style>
.logo{
  max-width: 300px;
  margin-left: 20px;
}
</style>
