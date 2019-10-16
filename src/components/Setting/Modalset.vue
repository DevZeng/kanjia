<template>
 <el-row class="warp" style="padding:20px 0 0 20px;">
  <el-col :span="24" class="warp-breadcrum">
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
      <el-breadcrumb-item>系统设置</el-breadcrumb-item>
      <el-breadcrumb-item>首页弹窗</el-breadcrumb-item>
    </el-breadcrumb>
  </el-col>


  <el-col :span="24" class="warp-main">
    <el-row>
      <el-form ref="modaldata" :model="modaldata" label-width="100px" class="form" status-icon size="small" style="width:600px;" :rules="rules">

        <el-form-item label="弹窗开关：">
          <el-radio-group @change="jifenkg" v-model="modaldata.state">
            <el-radio-button label="1">关</el-radio-button>
            <el-radio-button label="2">开</el-radio-button>
          </el-radio-group>
        </el-form-item>

        <el-form-item v-if="modaldata.state=='2'" label="标题" prop="title">
          <el-input placeholder="请输入标题" v-model="modaldata.title">
          </el-input>
        </el-form-item>

        <el-form-item v-if="modaldata.state=='2'" label="内容" prop="content">
          <el-input placeholder="请输入内容" v-model="modaldata.content">
          </el-input>
        </el-form-item>

        <el-form-item v-if="modaldata.state=='2'"> 
          <el-button size="small" type="primary" @click="confirm">提交</el-button> 
        </el-form-item>
      </el-form>


    </el-row>
  </el-col>


</el-row>
</template>

<script>

  import { modalPost } from '../../api/api';
  import { modalGet } from '../../api/api';

  import { Message } from 'element-ui';


  export default {
    data() {
      return {

        modaldata:{
          state:'1',
          title:'',
          content:'',
        },

        rules:{
          title: [
          {required: true, message: '请输入标题', trigger: 'blur'},
          ],
          content: [
          {required: true, message: '请输入内容', trigger: 'blur'},
          ],

        },

      };
    },

    methods:{


      getmodel(){
        var allParams = '';
        modalGet(allParams).then((res) => {
          this.modaldata=res.data
        });
      },

      
      jifenkg(val){
        console.log(val=='1')
        if(val=='1'){
          var allParams={
            state:'1',
          }
          modalPost(allParams).then((res) => {
            console.log(res)
            if (res.msg === "ok") {
             this.$message({
              message: '提交成功',
              type: 'success'
            });
             this.getconfig()
           } else {
             this.$message({
              message: res.msg,
              type: 'error'
            });
           }
         });
        }else{
          this.modaldata.state=val
        }
      },

      confirm(){
        this.$refs.modaldata.validate((valid) => {
          if (valid) {
            var allParams={
              state:this.modaldata.state,
              title:this.modaldata.title,
              content:this.modaldata.content,
            }
            console.log(allParams)
            modalPost(allParams).then((res) => {
              console.log(res)
              if (res.msg === "ok") {
               this.$message({
                message: '提交成功',
                type: 'success'
              });
               this.getconfig()
             } else {
               this.$message({
                message: res.msg,
                type: 'error'
              });
             }
           });

          }else{
            return false;
          }
        })
      },


    },

    mounted: function(){
      this.getmodel();

    }
  }
</script>

<style scoped>
  .grey{
    color: #aaa;
    float: left;
  }
</style>