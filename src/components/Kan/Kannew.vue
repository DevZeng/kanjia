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

        <el-form-item label="商家：" prop="shop_id">
          <el-select v-model="shop_id" placeholder="请选择商家" filterable>
            <el-option v-for="item in shopArr" :label="item.name" :value="item.id" :key="item.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="商品分类：" prop="type_id">
          <el-select v-model="type_id" placeholder="请选择商品分类" filterable>
            <el-option v-for="item in typeArr" :label="item.title" :value="item.id" :key="item.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="商品名称：" prop="title">
          <el-input v-model="newgood.title" placeholder="请输入商品名称" style="width:600px;"></el-input>
        </el-form-item>

        <el-form-item label="商品介绍：" prop="intro">
          <div class="edit_container">
            <quill-editor v-model="newgood.intro" style="height:300px;" ref="myQuillEditor" :options="editorOption" class="editer" placeholder= '请输入商品介绍'></quill-editor>
            <el-upload class="avatar-uploader quill-img" :action="upurl" :data="uptoken" :on-success='quillImgSuccess' style="display: none" >
              <el-button size="small" type="primary" id="imgInput" element-loading-text="插入中,请稍候">点击上传</el-button>
            </el-upload>
          </div>
        </el-form-item>

        <el-form-item label="特别提示：" prop="readme">
          <div class="edit_container">
            <quill-editor v-model="newgood.readme" style="height:300px;" ref="myQuillEditor1" :options="editorOption1" class="editer" placeholder= '请输入特别提示'></quill-editor>
            <el-upload class="avatar-uploader quill-img1" :action="upurl" :data="uptoken" :on-success='quillImgSuccess1' style="display: none" >
              <el-button size="small" type="primary" id="imgInput1" element-loading-text="插入中,请稍候">点击上传</el-button>
            </el-upload>
          </div>
        </el-form-item>

        <el-form-item label="商品图片：">
          <el-upload class="upload-demo" :action="upurl" :data="uptoken" :on-success="handleSuccess" :file-list="newgood.pictures" list-type="picture-card" accept="image/*">
           <i class="el-icon-plus"></i>
           <div slot="tip" class="el-upload__tip">提示：可上传JPG/PNG文件，建议图片长宽比为16:9</div>
         </el-upload>
       </el-form-item>

       <el-form-item label="商品原价：" prop="origin_price">
        <el-input v-model="newgood.origin_price" type="number" min="0" placeholder="请输入商品原价" style="width:600px;"><template slot="prepend">￥</template></el-input>
      </el-form-item>

      <el-form-item label="商品底价：" prop="min_price">
        <el-input v-model="newgood.min_price" type="number" min="0" placeholder="请输入商品底价" style="width:600px;"><template slot="prepend">￥</template></el-input>
      </el-form-item>

      <el-form-item label="商品数量：" prop="number">
        <el-input v-model="newgood.number" type="number" min="1" placeholder="请输入商品数量" style="width:600px;"></el-input>
      </el-form-item>

      <el-form-item label="活动标题：" prop="description">
        <el-input v-model="newgood.description" placeholder="请输入活动标题" style="width:600px;"></el-input>
      </el-form-item>

<!--       <el-form-item label="砍价限时：" prop="time">
        <el-input v-model="newgood.time" type="number" min="1" placeholder="请输入砍价限时(整数)" style="width:600px;"><template slot="append">小时</template></el-input>
      </el-form-item> -->

      <el-form-item label="砍价次数：" prop="clickNum">
        <el-input v-model="newgood.clickNum" type="number" min="1" placeholder="请输入砍价次数" style="width:600px;"></el-input>
      </el-form-item>

      <el-form-item style=";margin-top: 20px;">
        <el-button type="primary" @click="save()" size="small">提交审核</el-button>
        <el-button @click="golist()" size="small">取 消</el-button>
      </el-form-item>
    </el-form>
  </el-col>



</el-row>
</template>



<script>
  import { myshopGet, KanoneGet } from '../../api/api';
  import { typeGet } from '../../api/api';
  import { KanshopPost } from '../../api/api';
  import { Message } from 'element-ui';
  import qiniu from '../../api/qiniu';

  import 'quill/dist/quill.core.css';
  import 'quill/dist/quill.snow.css';
  import 'quill/dist/quill.bubble.css';

  import { quillEditor } from 'vue-quill-editor' //调用编辑器

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
        shopArr:[],

        newgood:{
          pictures:[],
          shop_id: '',
          type_id: '',
          title:'',
          intro:'',
          readme:'',
          time:'',
          description:'',
          number:'',
          clickNum:'',
          origin_price:'',
          min_price:'',
        },
        kanid:'',
        type_id:'',
        shop_id:'',
        editorOption:{
          placeholder: '请输入详细内容（添加图片请点击上方第一个按钮）',
          theme: 'snow',
          modules: {
            toolbar: {
              container: [
              ['image'],
              ['bold', 'italic', 'underline', 'strike'],
              ['blockquote', 'code-block'],
              [{ 'direction': 'rtl' }],
              [{ 'size': ['small', false, 'large', 'huge'] }],
              [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
              [{ 'color': [] }, { 'background': [] }],
              [{ 'font': [] }],
              [{ 'align': [] }],
              ['clean']
              ],
              handlers: {
                'image': function (value) {
                  if (value) {
                    document.getElementById('imgInput').click()
                  } else {
                    this.quill.format('image', false);
                  }
                }
              }
            }
          }
        },

        editorOption1:{
          placeholder: '请输入详细内容（添加图片请点击上方第一个按钮）',
          theme: 'snow',
          modules: {
            toolbar: {
              container: [
              ['image'],
              ['bold', 'italic', 'underline', 'strike'],
              ['blockquote', 'code-block'],
              [{ 'direction': 'rtl' }],
              [{ 'size': ['small', false, 'large', 'huge'] }],
              [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
              [{ 'color': [] }, { 'background': [] }],
              [{ 'font': [] }],
              [{ 'align': [] }],
              ['clean']
              ],
              handlers: {
                'image': function (value) {
                  console.log(value)
                  if (value) {
                    document.getElementById('imgInput1').click()
                  } else {
                    this.quill.format('image', false);
                  }
                }
              }
            }
          }
        },

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
          readme: [
          {required: true, message: '请输入特别提示', trigger: 'blur'},
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

        allParams:null,
      };
    },

    components: {
      quillEditor,
    },

    computed: {
      editor() {
        return this.$refs.myQuillEditor.quill
      },

      editor1() {
        return this.$refs.myQuillEditor1.quill
      }
    },

    methods:{
      quillImgSuccess(res, file) {
        // console.log(res)
        let quill = this.$refs.myQuillEditor.quill
        if (res.key) {
          let length = quill.getSelection().index;
          quill.insertEmbed(length, 'image', qiniu.showurl+ res.key)
          quill.setSelection(length + 1)
        } else {
          this.$message.error('图片插入失败')
        }
      },

      checkid(){
        var id=sessionStorage.getItem('editid');
        this.kanid=id;
        if(id){
          var allParams = '?id='+ id;
          KanoneGet(allParams).then((res)=>{
            console.log(res)
            this.newgood.intro = res.data.intro;
            this.newgood.title = res.data.title;
            this.newgood.description = res.data.description;
            this.newgood.origin_price = res.data.origin_price;
            this.newgood.min_price = res.data.min_price;
            this.newgood.readme = res.data.readme;
            var images=[];
            for(var i=0;i<res.data.picture.length;i++){
              console.log(res.data.picture[i]);
              images.push({
                uid:i,
                url:res.data.picture[i].href,
                response:{
                  key:1
                }
              })
            }
            this.newgood.pictures=images;
            this.newgood.type_id = res.data.type_id;
            this.type_id = res.data.type_id;
            this.shop_id = res.data.store_id;
            this.newgood.shop_id = res.data.store_id;
            this.newgood.number = res.data.number;
            this.newgood.clickNum = res.data.clickNum;
          })
          // origin_price:this.newgood.origin_price,
          //     min_price:this.newgood.min_price,
          //     pictures:this.newgood.pictures,
          //     type_id:this.newgood.type_id,
          //     shop_id:this.newgood.shop_id,
          //     title:this.newgood.title,
          //     intro:this.newgood.intro,
          //     readme:this.newgood.readme,
          //     description:this.newgood.description,
          //     number:this.newgood.number,
          //     clickNum:this.newgood.clickNum,
          // console.log('kanid')
        //   newgood:{
        //   pictures:[],
        //   shop_id: '',
        //   type_id: '',
        //   title:'',
        //   intro:'',
        //   readme:'',
        //   time:'',
        //   description:'',
        //   number:'',
        //   clickNum:'',
        //   origin_price:'',
        //   min_price:'',
        // },
        }
      },

      quillImgSuccess1(res, file) {
        console.log(res)
        let quill = this.$refs.myQuillEditor1.quill
        if (res.key) {
          let length = quill.getSelection().index;
          quill.insertEmbed(length, 'image', qiniu.showurl+ res.key)
          quill.setSelection(length + 1)
        } else {
          this.$message.error('图片插入失败')
        }
        console.log(this.newgood.readme)
      },

      getshop(){
        var allParams = '?limit=1000';
        myshopGet(allParams).then((res) => {
          this.shopArr=res.data.data;
          this.newgood.shop_id=res.data.data[0].id
        });
      },

      gettype(){
        var allParams = '';
        typeGet(allParams).then((res) => {
          this.typeArr=res.data.data;
          this.newgood.type_id=res.data.data[0].id
        });
      },

      handleSuccess(res, file,fileList) {
        console.log(fileList)
    this.newgood.pictures=[]
    for(var i=0;i<fileList.length;i++){
      if(fileList[i].response){
        if(fileList[i].response.key !== 1){
          this.newgood.pictures.push(qiniu.showurl+ fileList[i].response.key)  
        }else {
          this.newgood.pictures.push(fileList[i].url)
        }
      }
    }

      },

    
     

      handleExceed(files, fileList) {
        this.$message.warning(`只能上传1张图片`);
      },

      save(){
        // console.log(this.newgood.pictures)
        if(this.newgood.pictures.length==0){
          this.$message({
            message: '请上传图片',
            type: 'error'
          });
          return
        }

        this.$refs.newgood.validate((valid) => {
          if (valid) {
            var allParams = {
              origin_price:this.newgood.origin_price,
              min_price:this.newgood.min_price,
              pictures:this.newgood.pictures,
              type_id:this.type_id,
              shop_id:this.shop_id,
              title:this.newgood.title,
              intro:this.newgood.intro,
              readme:this.newgood.readme,
              description:this.newgood.description,
              number:this.newgood.number,
              clickNum:this.newgood.clickNum,
              // time:this.newgood.time
            };
            if(this.kanid){
                allParams.id=this.kanid
              }
            console.log(allParams)

            KanshopPost(allParams).then((res) => {
              // console.log(res)
              if (res.msg === "ok") {
               this.$message({
                message: '提交成功',
                type: 'success'
              });
               this.$router.push({ path: '/Kan/Kangood' });
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
       this.$router.push({ path: '/Kan/Kangood' });
     },
   },


   mounted: function () {
    this.getshop()
    this.gettype()
    this.checkid()

  }
}
</script>



<style scope>

</style>
