<template>
  <el-row class="warp" style="padding:20px 0 0 20px;">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>限时砍价</el-breadcrumb-item>
        <el-breadcrumb-item>发布活动</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">

      <el-form label-width="120px" width="900px" center style="width: 1000px" :rules="rules" ref="newgood" :model="newgood">

<!--         <el-form-item label="商品分类：" prop="type_id">
          <el-select v-model="newgood.type_id" placeholder="请选择商品分类" filterable>
            <el-option v-for="item in typeArr" :label="item.title" :value="item.id" :key="item.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="商品名称：" prop="title">
          <el-input v-model="newgood.title" placeholder="请输入商品名称" style="width:600px;"></el-input>
        </el-form-item>

        <el-form-item label="商品介绍：" prop="intro">
          <el-input v-model="newgood.intro" placeholder="请输入商品介绍" style="width:600px;"></el-input>
        </el-form-item>

        <el-form-item label="商品原价：" prop="origin_price">
          <el-input v-model="newgood.origin_price" type="number" min="0" placeholder="请输入商品原价" style="width:600px;"><template slot="prepend">￥</template></el-input>
        </el-form-item>

        <el-form-item label="商品底价：" prop="price">
          <el-input v-model="newgood.price" type="number" min="0" placeholder="请输入商品底价" style="width:600px;"><template slot="prepend">￥</template></el-input>
        </el-form-item> -->

        <el-form-item label="商品数量：" prop="number">
          <el-input v-model="newgood.number" type="number" min="1" placeholder="请输入商品数量" style="width:600px;"></el-input>
        </el-form-item>

        <el-form-item label="活动标题：" prop="description">
          <el-input v-model="newgood.description" placeholder="请输入活动标题（20字以内）" maxlength="20" style="width:600px;"></el-input>
        </el-form-item>

<!--         <el-form-item label="砍价限时：" prop="time">
          <el-input v-model="newgood.time" type="number" min="1" placeholder="请输入砍价限时(整数)" style="width:600px;"><template slot="append">小时</template></el-input>
        </el-form-item> -->

        <el-form-item label="砍价次数：" prop="clickNum">
          <el-input v-model="newgood.clickNum" type="number" min="1" placeholder="请输入砍价次数" style="width:600px;"></el-input>
        </el-form-item>

        <el-form-item style=";margin-top: 20px;">
          <el-button type="primary" @click="save()" size="small">提交</el-button>
          <el-button @click="golist()" size="small">取 消</el-button>
        </el-form-item>
      </el-form>
    </el-col>


  </el-row>
</template>

<script>

  import { typeGet } from '../../api/api';
  import { KanoneGet } from '../../api/api';
  import { KanonePut } from '../../api/api';
  import { Message } from 'element-ui';

  import qiniu from '../../api/qiniu';

  export default {
    data() {
      var checkvalue = (rule, value, callback) => {
        if (!value) {
          return callback(new Error('此项不能为空'));
        }
        setTimeout(() => {
          if (Math.sign(value) == 1) {
            if(value%1 === 0){
             callback();
           }else{
            callback(new Error('请输入整数'));
          }
        } else if(Math.sign(value) == 0) {
          callback(new Error('不能为0'));
        } else if(Math.sign(value) == -1) {
          callback(new Error('请输入正数'));
        }else{
          callback(new Error('请输入数字'));
        }
      }, 100);
      };

      return {
        uptoken:{
          token:qiniu.token,
        },
        upurl:qiniu.upurl,

        typeArr:[],

        newgood:{          
          pictures:[],
          type_id: 1,
          title:'',
          intro:'',
          // time:'',
          description:'',
          number:'',
          clickNum:'',
        },



        selectgood:true,

        rules:{
          origin_price: [
          {required: true, message: '请输入商品原价', trigger: 'blur'},
          ],
          min_price: [
          {required: true, message: '请输入商品底价', trigger: 'blur'},
          ],
          title: [
          {required: true, message: '请输入商品名称', trigger: 'blur'},
          ],
          intro: [
          {required: true, message: '请输入商品介绍', trigger: 'blur'},
          ],
          description: [
          {required: true, message: '请输入活动标题', trigger: 'blur'},
          ],
          number: [
          {required: true, validator: checkvalue, trigger: 'blur'},
          ],
          clickNum: [
          {required: true, validator: checkvalue, trigger: 'blur'},
          ],
          // time: [
          // {required: true, validator: checkvalue, trigger: 'blur'},
          // ],
        },

      };
    },


    methods:{
      gettype(){
        var allParams = '';
        typeGet(allParams).then((res) => {
          this.typeArr=res.data.data;
        });
      },

      getonekan(){
       var id = sessionStorage.getItem('kancheckid');
       this.actid= id
       var allParams = '?id='+id;
       KanoneGet(allParams).then((res) => {
        this.newgood=res.data
      });
     },

     save(){

      if(this.newgood.stock_id==''){
        this.$message.error(`请选择商品`);
        return
      }


      this.$refs.newgood.validate((valid) => {
        if (valid) {
            // console.log(stocks)
            var allParams = '?id='+this.actid
            +'&description='+this.newgood.description
            +'&number='+this.newgood.number
            +'&clickNum='+this.newgood.clickNum
            // +'&time='+this.newgood.time
            // +'&type_id='+this.newgood.type_id
            // +'&title='+this.newgood.title
            // +'&pictures='+this.newgood.pictures
            // +'&intro='+this.newgood.intro;


            // var allParams = {
            //   id:this.actid,
            //   stock_id:this.newgood.stock_id,
            //   description:this.newgood.description,
            //   number:this.newgood.number,
            //   clickNum:this.newgood.clickNum,
            //   start:this.newgood.start,
            //   end:this.newgood.end,
            //   price:this.newgood.price,
            //   origin_price:this.newgood.origin_price,
            // };

            console.log(allParams)
            KanonePut(allParams).then((res) => {
              console.log(res)
              if (res.msg === "ok") {
               this.$message({
                message: '提交成功',
                type: 'success'
              });

               this.$router.push({ path: '/Kan/Kancheck' });

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

    golist(){
     this.$router.push({ path: '/Kan/Kancheck' });
   },

 },

 mounted: function () {
  this.getonekan()
  this.gettype()
}
}
</script>



<style scope>

</style>
