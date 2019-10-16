<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>限时砍价</el-breadcrumb-item>
        <el-breadcrumb-item>活动列表</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">

      <el-button type="primary" size="small" @click="newone" style="margin-bottom: 20px;" v-show="checkper1">发布活动</el-button>

      <el-table :data="list" border stripe style="width:95%" size="small">

        <el-table-column prop="title" label="商品名称" min-width="200" align="center">
        </el-table-column>


        <el-table-column prop="number" label="数量" width="100" align="center">
        </el-table-column>

        <el-table-column prop="origin_price" label="原价" width="100" align="center">
        </el-table-column>

        <el-table-column prop="min_price" label="底价" width="100" align="center">
        </el-table-column>

        <el-table-column prop="description" label="活动标题" min-width="200" align="center">
        </el-table-column>

<!--         <el-table-column prop="time" label="砍价限时" min-width="100" align="center">
          <template slot-scope="scope"><span>{{scope.row.time}}小时</span></template>
        </el-table-column> -->

        <el-table-column prop="clickNum" label="砍价次数" width="100" align="center">
        </el-table-column>

        <el-table-column prop="read_num" label="浏览人数" width="100" align="center">
        </el-table-column>

        <el-table-column prop="bargain_num" label="总砍价次数" width="100" align="center">
        </el-table-column>

        <el-table-column prop="updated_at" label="活动开始时间" width="180" align="center">
        </el-table-column>

        <el-table-column label="操作" width="100" align="center">
         <template slot-scope="scope">
          <el-button type="danger" size="small" @click="handleDelete(scope.$index, scope.row)" v-show="checkper2">删除</el-button>
        </template>
      </el-table-column>

    </el-table>

    <el-pagination style="float:left;margin-top:20px;" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="limit" @current-change="handleCurrentChange" @size-change="handleSizeChange" layout="total,sizes, prev, pager, next, jumper" :total="count" prev-text="上一页" next-text="下一页">
    </el-pagination>


  </el-col>



  <el-col>
    <el-dialog title="删除不可恢复，是否确定删除？" :visible.sync="dialogDelVisible" width="30%">
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitdel()">确 定</el-button>
        <el-button @click="dialogDelVisible = false">取 消</el-button>
      </div>
    </el-dialog>
  </el-col>

</el-row>
</template>



<script>
  import { KancheckGet } from '../../api/api';
  import { Kandelete } from '../../api/api';

  import qiniu from '../../api/qiniu';

  import { Message } from 'element-ui';

  export default {
    data() {
      return {

        currentPage: 1,
        list:[],
        count:0,
        limit:10,
        dialogNewVisible:false,
        dialogDelVisible:false,
        filter:{

        },
        editId:'',
        delId:'',

        checkper1:false,
        checkper2:false,

      };
    },

    methods:{
      checkPer(){
        var per = sessionStorage.getItem('permissions');
        if(per.indexOf('addBargainPromotion')>-1){
          this.checkper1=true;
        }

        if(per.indexOf('delBargainPromotion')>-1){
          this.checkper2=true;
        }
      },

      getlist(){
        var allParams = '?page='+ this.currentPage + '&limit=' + this.limit + '&state=2';
        KancheckGet(allParams).then((res) => {
          this.list=res.data.data;
          this.count=res.data.count
        });
      },

      handleDelete(index, row) {
        this.dialogDelVisible = true;
        this.delId = row.id;
      },

      submitdel(){
        this.dialogDelVisible = false;
        var allParams='?id='+this.delId
        Kandelete(allParams).then((res) => {

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

      handleCurrentChange(val) {
        this.currentPage = val;
        this.getlist();
      },

      handleSizeChange(val){
        this.limit = val;
        this.getlist();
      },

      newone(){
        this.$router.push({ path: '/Kan/Kannew' });
      }
    },

    mounted: function () {
      this.getlist();
      this.checkPer();
      sessionStorage.removeItem('editid');
    }
  }
</script>


<style scope>
.icon{
  width: 20px;
  height: 20px;
  margin: 2px;
}
</style>
