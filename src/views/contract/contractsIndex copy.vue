<template>
  <div class="tac">
    <div class="titHeader">
      <div style="min-width:600px;float:left">
        <el-breadcrumb separator="/">
          <el-breadcrumb-item class="item_inner">
            合同编号
            <span>{{contitle.number}}</span>
          </el-breadcrumb-item>
          <el-breadcrumb-item class="item_inner">
            租户名称
            <span>{{contitle.users}}</span>
          </el-breadcrumb-item>
          <el-breadcrumb-item class="item_inner">
            合同类型
            <span>{{contitle.type}}</span>
          </el-breadcrumb-item>
        </el-breadcrumb>
        <br />
        <el-breadcrumb separator="/">
          <el-breadcrumb-item class="item_inner">
            状态
            <span>{{contitle.state}}</span>
          </el-breadcrumb-item>
          <el-breadcrumb-item class="item_inner">
            创建时间
            <span>{{new Date()|nowtime}}</span>
          </el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <div class="btn-grounp">
        <el-button type="primary" plain>保存</el-button>
        <el-upload
          class="upload-demo"
          action="https://jsonplaceholder.typicode.com/posts/"
          :on-success="uploasdfile"
          :on-progress="beforeUpload"
          multiple
          :limit="1"
          :show-file-list='false'
          :file-list="fileList"
          >
          <el-button type="primary" plain>附件</el-button>
        </el-upload>
        <el-button type="primary" plain>打印预览</el-button>
        <input id="content" style="display: none" v-model='copyText'/>
        <el-button type="primary" 
        plain class="copybtn" 
        v-clipboard:copy="copyText"
        v-clipboard:success="onCopy"
        v-clipboard:error="onError"
        @click="copy()"
        style="margin-left: 10px"
        >复制打印地址</el-button>
        <el-button type="primary">发起合同文本审批</el-button>
      </div>
    </div>
    <div class="secontent">
      <div class="con-tit">
        <div class="left-right">
          <div class="left-1">
            <span>附件管理</span>
            <i class="el-icon-link"></i>
          </div>
          <div class="left-2">
            <div v-for="(item,index) in uploadlist" :key="index" class="left-list">
              <span class="abbreviation">{{item}}</span>&nbsp;
              <span style="color:#f00">{{new Date()|nowtime}}</span>&nbsp;
              <el-button type="text" @click="handleRemove(index)">删除</el-button>
            </div>
          </div>
        </div>
        <div class="left-right">
          <div class="right-1">
            合同台帐
          </div>
          <div class="right-2">
            <p>
              <span style="margin-right:100px;">租赁审批表</span>
              <router-link  to="" style="color:#409EFF">{{numaa}}</router-link>
            </p>
            <p >
              <span>租赁合同审批表</span>
              <span></span>
            </p>
          </div>
        </div>
      </div>
      <!-- <div v-html="message">{{message}}</div>     -->
      <el-row :gutter="24" >
        <el-col :span="18">
          <iframe
        id="con-iframe"
        name="con-iframe"
        :src="'/static/'+name+'.htm'"
        style="border: 0; width: 100%;height:100vh;position: relative;top:0"
      ></iframe>
        </el-col>
        <el-col :span="6">
            <el-tabs v-model="activeName2" type="card" @tab-click="handleClick">
              <el-tab-pane label="填空列表" name="first" >
                <div v-for="(item,index) in tableData" :key="index" class="conspan" @click="itemi(index)">
                  <span class="con-span">&nbsp;</span>
                  <el-button type="text" @click="updata(index)" style="float:right">编辑</el-button>
                </div>
              </el-tab-pane>
              <el-tab-pane label="修改记录" name="second">修改记录暂空...</el-tab-pane>
            </el-tabs>                     
        </el-col>
      </el-row>     
    </div>
    <el-dialog title="编辑" width="40%" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="请输入内容" >
          <el-input v-model="form.name" ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="close()">确 定</el-button>
      </div>
    </el-dialog>
    <Loading class="loading" v-if="isLoading"></Loading>
  </div>
</template>
<script>
import axios from "axios";
import Loading from '../../components/loading.vue';
export default { 
  data() {
    return {
      contitle: {},
      name: "商铺租赁合同标准模板",
      tableData: [],
      nowtime: "",
      numaa: "123",
      message: "这里可以包含html标签",
      dialogFormVisible:false,
      form:{
        name:'',
        index:''
      },
      inputval:'',
      activeName2: 'first',
      uploadfile:[],
      spanval:[],
      classlist:[],
      spanindex:'',
      uploadlist:[],
      uploadTime:'',
      fileList:[],
      isLoading:false,
      copyText:''
    };
  },
 filters:{       
    nowtime(data){
        return data.getFullYear()+'年'+(data.getMonth()+1)+'月'+data.getDate()+'日';
    }
  },
  computed:{
   
  },
  methods: {
    // 合同编辑
    updata(val){
      this.form.name=''
      this.dialogFormVisible=true;
      this.form.index=val
      console.log(val)
    },
    //弹窗确定
    close(){
      if(this.form.name){   
        this.iframejs()
        this.classlist[this.form.index].innerHTML=this.form.name
        this.spanval[this.form.index].innerHTML=this.form.name       
        this.dialogFormVisible=false;

      }      
    },
    // 生成右侧编辑列表
    getspan() {
      this.iframejs()
      this.tableData=this.classlist
    } ,
    handleClick(tab, event) {
        console.log(tab, event);
    },
    itemi(index){
      this.spanindex=index;
      // var iframecon=frames["con-iframe"].document.documentElement.scrollTop;
      var x=document.getElementById("con-iframe").contentWindow.document.getElementsByClassName('input-comm')[index].offsetTop;
      document.getElementById("con-iframe").contentWindow.document.getElementsByClassName('input-comm')[index].classList.add("active-color");
      console.log(x)
      frames["con-iframe"].document.documentElement.scrollTop=x
      setTimeout(function(){ document.getElementById("con-iframe").contentWindow.document.getElementsByClassName('input-comm')[index].classList.remove("active-color") }, 3000);   
    },   
    //获取合同js
    iframejs(){
      let Con=document.getElementById("con-iframe").contentDocument;
      var classlist = Con.getElementsByClassName('input-comm') //iframe的列表
      var spanval = document.getElementsByClassName('con-span') //右侧编辑
      this.classlist=classlist
      this.spanval=spanval
    },
      //上传移除
      handleRemove(index) {
        this.$alert("确定删除","提示",{
          confirmButtonText:"确定",     
        });
        this.uploadlist.splice(index,1)
      },
      //上传成功
      uploasdfile(response, file, fileList){
        this.isLoading=false;
        this.uploadlist.push(file.name)      
      },
      beforeUpload(){
        this.isLoading=true;
      },
      onCopy(e){
       alert("复制成功");
      },
      // 复制失败
      onError(e){
        alert("失败");
      },
      copy(){
        // var _this=this;
        // axios.get('http://mockjs').then(res => {               
        //   console.log(res.tableData.data)
        //   _this.copyText=res.tableData.data;
        // }, response => {
        //     console.log("error");
        // });
        this.copyText="www.baidu.com";
      }
  },
  mounted() {
    console.log(this.$route.query);
    this.contitle = this.$route.query;
    // for(var i =1; i<this.tableData.length;i++){
    //     this.tableData[i].onclick()
    //   } 
     let self = this;
      setTimeout(() => {
      self.getspan();
      }, 2000);
},
components:{
		Loading
	}
  
}
</script>
<style scoped lang="scss">
@import "../../common/css/table.css";
.tac {
  padding: 20px 0 0 20px;
  position: relative;
  overflow-y: scroll;
  width:100%;
  height:90vh;
  overflow: auto;
  .titHeader {
    height: 100px;
    width: 80%;
    // min-width: 1200px;
    position: fixed;
    top: 200px;
    left:240px;
    background: rgba(255, 255, 255, 0.8);
    padding: 20px;
    z-index: 200;
    .item_inner {
      font-size: 19px !important;
      span {
        color: #409eff;
      }
    }
    .btn-grounp {
      float: right;
      margin-top: 30px;
      .upload-demo{
        display:inline-block;
        margin: 0 10px;
      }
    }
  }

  .secontent {
    margin-top: 122px;
    height:100%;
    .con-tit {
      height: 100px;
      width: 98.6%;
      display: flex;
      border-top: 8px solid #e4e5ea;
      border-bottom: 1px solid #e4e5ea;
      background: rgba(255, 255, 255, 0.8);
      margin-bottom: 20px;
      .left-right{
        display:flex;
        width:50%; 
        border-right:1px solid #e4e5ea;  
        .left-1{
          width:120px;
          line-height: 100px;
          border-left: 1px solid #e4e5ea;
          padding:0  10px;
          text-align: center;
        }
        .left-2{
          border-left: 1px solid #e4e5ea;
          padding: 0 20px;
          font-size: 18px;
          .left-list{
            height: 39px;
            line-height: 39px;
          }
        }
        .right-1{
          width:120px;
          line-height: 100px;
          padding:0  10px;
          text-align: center;
        }
        .right-2{
          width:97%;
          border-left: 1px solid #e4e5ea;
          p{
            width: 97%;
            line-height:34px;
            border-bottom: 1px solid #e4e5ea;
            padding-left: 20px;
          }
        }
      }
    }
    .el-row{
      width:100%;
      height: 100vh;
      :nth-child(2).el-col{
        .conspan{
          border-bottom:1px solid #e4e5ea;
          line-height:30px;
          padding:10px;
        }
        .el-tab-pane{
          overflow-y: scroll;  
          height: 100vh;      
        }
        
      }
    }
  }
}
</style>