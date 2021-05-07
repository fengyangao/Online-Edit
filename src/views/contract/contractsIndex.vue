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
          :show-file-list="false"
          :file-list="fileList"
        >
          <el-button type="primary" plain>附件</el-button>
        </el-upload>
        <el-button type="primary" plain>打印预览</el-button>
        <input id="content" style="display: none" v-model="copyText" />
        <el-button
          type="primary"
          plain
          class="copybtn"
          v-clipboard:copy="copyText"
          v-clipboard:success="onCopy"
          v-clipboard:error="onError"
          @click="copy()"
          style="margin-left: 10px"
        >复制</el-button>
        <el-button type="primary">发起合同文本审批</el-button>
      </div>
    </div>
     <!-- iframe编辑 -->
    <contractS :iframeUrl="'/static/'+name+'.htm'"></contractS>
    <Loading class="loading" v-if="isLoading"></Loading>
  </div>
</template>
<script>
import axios from "axios";
import contractS from "../../components/contractS";
import Loading from '../../components/loading.vue';
export default {
  data() {
    return {
      contitle: {},
      name: "商铺租赁合同标准模板",      
      nowtime: "",
      numaa: "123",
      uploadlist: [],
      fileList: [],
      isLoading: true,
      copyText: "",
    }
  },
  filters: {
    nowtime(data) {
      return (
        data.getFullYear() +
        "年" +
        (data.getMonth() + 1) +
        "月" +
        data.getDate() +
        "日"
      );
    }
  },
  computed: {},
  methods: { 
    //上传成功
    uploasdfile(response, file, fileList) {
      this.isLoading = false;
      this.uploadlist.push(file.name);
    },
    beforeUpload() {
      this.isLoading = true;
    },
    onCopy(e) {
      alert("复制成功");
    },
    // 复制失败
    onError(e) {
      alert("失败");
    },
    copy() {
      // var _this=this;
      // axios.get('http://mockjs').then(res => {
      //   console.log(res.tableData.data)
      //   _this.copyText=res.tableData.data;
      // }, response => {
      //     console.log("error");
      // });
      this.copyText = 'https://fuss10.elemecdn.com/a/3f/3302e58f9a181d2509f3dc0fa68b0jpeg.jpeg';//要复制的内容
    },
  },
  mounted() {
    // console.log(this.$route.query);
    this.contitle = this.$route.query;
  },
  updated() {},
  components: {
    Loading,
    contractS
  }
}
</script>
<style scoped lang="scss">
@import "../../common/css/table.css";
.tac {
  padding: 20px 0 0 20px;
  position: relative;
  // overflow: hidden;
  width: 100%;
  margin-top: 122px;
  .titHeader {
    height: 100px;
    width: 80%;
    // min-width: 1200px;
    position: fixed;
    top: 200px;
    left: 240px;
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
      .upload-demo {
        display: inline-block;
        margin: 0 10px;
      }
    }
  }
}
</style>