<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>代理管理</el-breadcrumb-item>
        <el-breadcrumb-item>提现管理</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">

      <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
        <el-form :inline="true" :model="form">
         <!-- <el-form-item lable>代理昵称：</el-form-item> -->
         <el-form-item>
          <el-input v-model="form.name" placeholder="请输入代理名称" style="min-width: 200px;" ></el-input>
        </el-form-item>

<!--         <el-form-item>
          <div class="block">
            <span class="demonstration">月份：</span>
            <el-date-picker v-model="form.month" type="month" placeholder="请选择日期" :editable='editable'  @change="getSTime">
            </el-date-picker>
          </div>
        </el-form-item> -->

        <el-form-item>
          <el-button type="primary" @click="getlist()">搜索</el-button>
          <el-button @click="clearss">清空</el-button>
        </el-form-item>
      </el-form>
    </el-col>

    <el-table :data="list" border stripe size="small" style="width: 95%;">
      <el-table-column prop="id" label="提现编号" min-width="100" align="center">
      </el-table-column>
      <el-table-column prop="name" label="代理名称" min-width="200" align="center">
      </el-table-column>
<!--       <el-table-column prop="code" label="联系电话" min-width="200" align="center">
      </el-table-column>
      <el-table-column prop="code" label="申请提现月份" min-width="200" align="center">
      </el-table-column> -->
      <el-table-column prop="price" label="提现金额" min-width="200" align="center">
      </el-table-column>
      <el-table-column prop="bank" label="开户银行" min-width="200" align="center">
      </el-table-column>
      <el-table-column prop="account" label="银行卡号" min-width="200" align="center">
      </el-table-column>


      <el-table-column label="操作" min-width="200" align="center" v-show="checkper1">
       <template slot-scope="scope">
        <el-tag size="mini" type="success" v-show="scope.row.state==2 ? true : false">已发放</el-tag>
        <el-tag size="mini" type="danger" v-show="scope.row.state==3 ? true : false">已拒绝</el-tag>
        <el-button type="primary" v-show="scope.row.state==1 ? true : false" size="mini" @click="handleEdit(scope.row)">确认提现</el-button>
        <el-button type="danger" v-show="scope.row.state==1 ? true : false" size="mini" @click="handleDel(scope.row)">拒绝</el-button>
      </template>
    </el-table-column>
  </el-table>

  <el-pagination style="float:left;margin-top:20px;" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="limit" @current-change="handleCurrentChange" @size-change="handleSizeChange" layout="total,sizes, prev, pager, next, jumper" :total="count" prev-text="上一页" next-text="下一页">
  </el-pagination>
</el-col>

<el-col>
  <el-dialog title="此操作不可恢复，是否确定拒绝？" :visible.sync="dialogDelVisible" width="30%">
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogDelVisible  = false">取 消</el-button>
      <el-button type="primary" @click="submitdel()">确 定</el-button>
    </div>
  </el-dialog>
</el-col>
</el-row>
</template>



<script>


  import { withdrawGet } from '../../api/api';
  import { withdrawPass } from '../../api/api';
  import { withdrawReject } from '../../api/api';


  import { Message } from 'element-ui';


  export default {
    data() {
      return {
        currentPage: 1,
        count:0,
        limit:10,
        list:[],
        dialogDelVisible:false,
        form:{
          name:''
        },
        currentid:'',
        checkper1:false,
      };
    },

    methods:{
      checkPer(){
        var per = sessionStorage.getItem('permissions');

        if(per.indexOf('withdrawCheck')>-1){
          this.checkper1=true;
        }

      },


      getlist(){
        var allParams = '?page='+ this.currentPage + '&limit=' + this.limit + '&name=' + this.form.name;
        withdrawGet(allParams).then((res) => {
          this.list=res.data.data;
          this.count=res.data.count;
        });
      },

      clearss(){
        this.form={
          name:''
        }
        this.getlist();
      },

      handleEdit(row){
        var allParams = '?id='+ row.id;
        withdrawPass(allParams).then((res) => {
          console.log(res)
          Message({
            message: "审核成功",
            type: 'success'
          });
          this.getlist();
        });

      },

      handleDel(row){
        this.currentid= row.id
        this.dialogDelVisible=true
      },

      submitdel(){

        var allParams = '?id='+ this.currentid;
        withdrawReject(allParams).then((res) => {
          console.log(res)
          this.dialogDelVisible=false
          Message({
            message: "审核成功",
            type: 'success'
          });
          this.getlist();
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

</style>
