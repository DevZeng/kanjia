<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>商家管理</el-breadcrumb-item>
        <el-breadcrumb-item>交易统计</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">
      <el-form :inline="true">

        <el-form-item label="商店地区：" prop="cityone">
          <el-select v-model="cityone" placeholder="请选择市" filterable @change="getcitytwo">
            <el-option v-for="item in levelarr1" :label="item.name" :value="item.id" :key="item.id"></el-option>
          </el-select>
          <el-select v-model="filter.city_id" placeholder="请选择区" filterable>
            <el-option v-for="item in levelarr2" :label="item.name" :value="item.id" :key="item.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item>
          <div class="block">
            <span class="demonstration">请选择时间范围：</span>
            <el-date-picker v-model="date" type="daterange" range-separator="/" @change="getSTime" start-placeholder="开始日期" end-placeholder="结束日期" format="yyyy-MM-dd" value-format="yyyy-MM-dd">
            </el-date-picker>
          </div>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" size="small" @click="getlist">搜索</el-button>
          <el-button size="small" @click="clear">清空</el-button>
        </el-form-item>

      </el-form>


      <el-table :data="list" border stripe size="small" style="width: 1001px">
        <el-table-column prop="manager" label="店主" min-width="200" align="center">
        </el-table-column>

        <el-table-column prop="name" label="商家名称" min-width="300" align="center">
        </el-table-column>

        <el-table-column prop="amount" label="交易总额" min-width="300" align="center">
        </el-table-column>


<!--         <el-table-column label="操作" width="300" align="center">
          <template slot-scope="scope">
            <el-button type="primary" size="small" @click="handleEdit(scope.$index, scope.row)" v-show="checkper1">编辑</el-button>
            <el-button type="danger" size="small" @click="handleDelete(scope.$index, scope.row)" v-show="checkper2">删除</el-button>
          </template>
        </el-table-column> -->
      </el-table>

    </el-col>



  </el-row>
</template>



<script>
  import { shopaddGet } from '../../api/api';
  import { shopcount } from '../../api/api';



  export default {
    data() {
      return {
        currentPage: 1,
        list:[],
        loading: false,
        count:0,
        limit:10,


        filter:{
          city_id:'',
          start:'',
          end:''
        },
        date:'',

        cityone:'',
        levelarr1:[],
        levelarr2:[],




      };
    },

    methods:{

      getadd(){
        var allParams = '?page=1&limit=10000&base=1';
        shopaddGet(allParams).then((res) => {
          this.levelarr1=res.data.data;
          this.cityone=res.data.data[0].id
          this.getcitytwo()
        });
      },

      getcitytwo(){
        var allParams = '?parent_id='+this.cityone;
        shopaddGet(allParams).then((res) => {
          this.filter.city_id=res.data.data[0].id
          this.levelarr2=res.data.data;
        });
      },


      getSTime(val){
        this.filter.start=val[0];
        this.filter.end=val[1];
      },


      getlist(){
        var allParams = '?city_id=' + this.filter.city_id    + '&start=' + this.filter.start   + '&end=' + this.filter.end;
        shopcount(allParams).then((res) => {
          this.list=res.data;
        });
      },

      clear(){
        this.cityone=''
        this.filter={
          city_id:'',
          start:'',
          end:''
        }
        this.getlist();
      },


    },

    mounted: function () {
      this.getadd()

      var that =this
      setTimeout(function(){
        that.getlist();
      },1000)
      // this.checkPer();
    }
  }
</script>


<style>

</style>
