<!--********** 文本合同编辑组件********** -->
<template>
  <div class="Isecontent">
    <div class="Iframes" :style="{'height':clientHeight+'px !important'}">
      <div class="iframe-con">
        <img src="../assets/images/revise.png" class="iframe-revise iframe-img" @click="irevise" />
        <img src="../assets/images/plus.png" class="iframe-plus iframe-img" @click="iplus" />
        <iframe id="con-iframe" name="con-iframe" :src="iframeUrl" class="in-iframe"></iframe>
      </div>
      <div class="right-ul" v-if="isshows">
        <el-tabs v-model="activeName2" type="card" @tab-click="handleClick">
          <el-tab-pane label="填空列表" name="first">
            <div class="conspan" :id="item.id" v-for="(item,index) in tableData" :key="index">
              <span class="con-tent">{{item.name}}</span>
              <el-button type="text" @click="itemi(item.id)" class="con-btn">编辑</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="修订记录" name="second">
            <div class="conspan" :id="item.id" v-for="(item,index) in revisionArr" :key="index">
              <span class="con-tent">{{item.name}}</span>
              <el-button type="text" @click="withdraw(index)" class="con-btn">撤回</el-button>
            </div>
            <p v-if="revisionArr==0 || !revisionArr" style="text-align:center">暂无修订记录</p>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>
    <el-dialog title="编辑" width="40%" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="请输入内容">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="close()">保 存</el-button>
      </div>
    </el-dialog>
    <el-dialog title="修订" :visible.sync="iseditors" width="40%" @close="closedia">
      <quill-editor
        v-model="conval"
        ref="myQuillEditor"
        style="height: 220px;margin-bottom:20px"
        :options="editorOption"
        @change="onEditorChange($event)"
      ></quill-editor>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="saveHtml">保 存</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import { quillEditor } from "vue-quill-editor";
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";
import $ from "jquery";
// editor工具栏配置
const toolbarOptions = [
  ["bold"], // 加粗 斜体 下划线 删除线 -----['bold', 'italic', 'underline', 'strike']
  // ['blockquote', 'code-block'], // 引用  代码块-----['blockquote', 'code-block']
  // [{ header: 1 }, { header: 2 }], // 1、2 级标题-----[{ header: 1 }, { header: 2 }]
  // [{ list: 'ordered' }, { list: 'bullet' }], // 有序、无序列表-----[{ list: 'ordered' }, { list: 'bullet' }]
  // [{ script: 'sub' }, { script: 'super' }], // 上标/下标-----[{ script: 'sub' }, { script: 'super' }]
  // [{ indent: '-1' }, { indent: '+1' }], // 缩进-----[{ indent: '-1' }, { indent: '+1' }]
  // [{'direction': 'rtl'}], // 文本方向-----[{'direction': 'rtl'}]
  // [{ size: ['small', false, 'large', 'huge'] }], // 字体大小-----[{ size: ['small', false, 'large', 'huge'] }]
  // [{ header: [1, 2, 3, 4, 5, 6, false] }], // 标题-----[{ header: [1, 2, 3, 4, 5, 6, false] }]
  // [{ color: [] }, { background: [] }], // 字体颜色、字体背景颜色-----[{ color: [] }, { background: [] }]
  // [{ font: [] }], // 字体种类-----[{ font: [] }]
  // [{ align: [] }], // 对齐方式-----[{ align: [] }]
  // ['clean'], // 清除文本格式-----['clean']
  ["image"] // 链接、图片、视频-----['link', 'image', 'video']
];
export default {
  data() {
    return {
      tableData: [], //编辑列表
      dialogFormVisible: false,
      form: {
        name: ""
      },
      activeName2: "first",
      clientHeight: null,
      isshows: true,
      conval: null,
      editorOption: {
        placeholder: "请输入内容",
        theme: "snow", // or 'bubble'
        modules: {
          toolbar: {
            container: toolbarOptions
          }
        }
      },
      iseditors: false,
      inputId: null,
      newHtml: "",
      revisionArr: [], //修订记录数组
      oldRecord: "",
      selectedHtml: "",
      settext: false
    };
  },
  props: {
    iframeUrl: {
      type: String,
      default: ""
    }
  },
  computed: {},
  methods: {
    // editor值改变事件
    onEditorChange(e) {
      console.log(e.html, e.text);
      
      if (e.html.indexOf("<strong") > -1) {
        console.log('1')
        // 文本加粗
        this.settext = true;
        this.selectedHtml = e.html;
        this.selectedval = e.text;
      } else if (e.html.indexOf("<img") > -1) {
        // 图片上传
        this.settext = true;
      }else{
        console.log('2')
        this.settext = true;
        this.selectedHtml = e.html;
        this.selectedval = e.text;
      }
    },
    //editor修订保存
    saveHtml() {
      if (this.settext) {
        console.log('3')
        var container = $("#con-iframe").contents()[0];
        var documentFragment = null;
        if (container.getSelection) {
          console.log('4')
          this.oldRecord = container.getSelection().toString();
          var range = container.getSelection().getRangeAt(0);
          documentFragment = container
            .getSelection()
            .getRangeAt(0)
            .cloneContents();
          //   documentFragment = container.selection.createRange().HtmlText;
          // var range = container.selection.createRange();
          if (container.getSelection().rangeCount == 0) {
            console.log('5')
            container.getSelection().addRange(fullSelectedRange);
          }
        }
        var ins = container.createElement("span");       
        
        for (var i = 0; i < documentFragment.childNodes.length; i++) {
          var childNode = documentFragment.childNodes[i];
          if (childNode.nodeType == 3) {
            // Text 节点
            // this.selectedHtml += childNode.nodeValue;
            console.log('6')
          } else {
            console.log('7')
            var nodeHtml = childNode.outerHTML;
            this.selectedHtml += nodeHtml;
          }
        }
        var originalSource = null;
        if (this.selectedHtml.indexOf("<span") > -1) {
          console.log('8')
          ins.setAttribute(
            "originalSource",
            $(this.selectedHtml).attr("originalSource")
          );
          ins.setAttribute("id", $(this.selectedHtml).attr("id"));
          console.log('9')
        } else {
          console.log('10')
          ins.setAttribute("originalSource", this.selectedval);
          var newId = new Date().getTime() + "" + parseInt(Math.random() * 100);
          ins.setAttribute("id", newId);
          ins.innerHTML = this.selectedHtml;
          ins.style = "background:#F9B500;display:inline-block;";
          this.revisionArr.push({
            name: this.selectedval,
            oldtext: this.oldRecord,
            id: newId
          });
        }

       
        range.deleteContents();
        range.insertNode(ins);
        this.iseditors = false;
        // this.conval = null;
      } else {
        this.$message({
          message: "文本选中后编辑",
          type: "none"
        });
      }
    },
    //编辑弹窗保存
    close() {
      if (this.form.name) {
        var container = $("#con-iframe").contents()[0];
        $(".conspan[id=" + this.inputId + "] .con-tent").html(this.form.name);
        $(container)
          .find(".input-comm[id=" + this.inputId + "]")
          .html(this.form.name);
        this.dialogFormVisible = false;
        setTimeout(function() {
          $(container)
            .find("[input-edit]")
            .css("background-color", "rgb(203, 203, 203)");
          $(".conspan").removeClass("active");
        }, 5000);
      }
    },
    // 生成右侧编辑列表
    getspan() {
      // iframe 初始化
      var container = $("#con-iframe").contents()[0]; // iframe container
      // 初始化 iframe中 input-edit 默认样式
      $(container)
        .find("[input-edit]")
        .css({ "min-height": "16px" });
      // 刷新 填空列表
      let that = this;
      that.tableData = [];
      $(container)
        .find("[input-edit]")
        .each(function(index, value) {
          var id = $(this).attr("id");
          that.tableData.push({
            id: id,
            name: $(this).html()
          });
        });
    },
    // 右侧菜单切换
    handleClick(tab, event) {
      console.log(tab, event);
    },
    // 合同编辑 ******iframe右侧编辑******
    itemi(id) {
      this.form.name = "";
      this.inputId = id;
      // console.log(Ytop, id);
      var container = $("#con-iframe").contents()[0];
      var Ytop = parseInt(
        $(container)
          .find(".input-comm[id=" + id + "]")
          .offset().top
      );
      $(container)
        .find("[input-edit]")
        .css("background-color", "rgb(203, 203, 203)");
      $(container)
        .find(".input-comm")
        .removeClass("active");
      $(".conspan").removeClass("active");
      $(".conspan[id=" + id + "]").addClass("active");
      $(container)
        .find("[input-edit]")
        .css("background-color", "rgb(203, 203, 203)");
      $(container)
        .find(".input-comm[id=" + id + "]")
        .css("background-color", "#18b0e2");
      frames["con-iframe"].document.documentElement.scrollTop = Ytop - 20;
      let val = $(container)
        .find(".input-comm[id=" + id + "]")
        .html();
      if (val) {
        this.form.name = val;
      }
      this.dialogFormVisible = true;
    },
    //修订撤回
    withdraw(i) {
      var container = $("#con-iframe").contents()[0];
      let lid = this.revisionArr[i].id;
      $(container)
        .find("span[id=" + lid + "]")
        .html(this.revisionArr[i].oldtext);
      $(container)
        .find("span[id=" + lid + "]")
        .css({ "background-color": "#fff", "font-weight": "100" });
      this.revisionArr.splice(i, 1);
    },
    //修订弹窗
    irevise() {
      var word = document
        .getElementById("con-iframe")
        .contentWindow.getSelection()
        .toString();
      // window.getSelection?window.getSelection():document.selection.createRange().text;
      // window.getSelection ? window.getSelection().removeAllRanges() : document.selection.empty();
      if (word) {
        this.conval = word;
        this.iseditors = true;
      } else {
        this.$message({
          message: "鼠标未选中内容！",
          type: "none"
        });
      }
    },
    //修订弹窗关闭
    closedia() {
      this.conval = null;
    },
    //iframe放大
    iplus() {
      this.isshows = !this.isshows;
    }
  },
  created(){
    window.itemi = this.itemi
  },
  mounted() {
    this.contitle = this.$route.query;
    let self = this;
    setTimeout(() => {
      self.getspan();
    }, 1500);
    this.clientHeight = document.body.clientHeight - 470;
  },
  components: {
    quillEditor
  }
};
//******绑定 iframe 中 input-edit 点击事件*******
$(function() {
  var container = $("#con-iframe").contents()[0];
  // 单击事件
  $(container).on("click", "[input-edit]", function(e) {
    e.stopPropagation();
    e.preventDefault();
    if(!$(this).html()){
    $(container)
      .find("[input-edit]")
      .css("background-color", "rgb(203, 203, 203)");
    $(this).css("background-color", "#18b0e2");
    var id = $(this).attr("id");
    $(".conspan").removeClass("active");
    $(".conspan[id=" + id + "]").addClass("active");
    }
  });
  // 双击事件
  $(container).on("dblclick", "[input-edit]", function(e) {
    e.stopPropagation();
    e.preventDefault();    
    if(!$(this).html()){
    $(container)
      .find("[input-edit]")
      .css("background-color", "rgb(203, 203, 203)");
    $(this).css("background-color", "#18b0e2");
    var id = $(this).attr("id");
    $(".conspan").removeClass("active");
    $(".conspan[id=" + id + "]").addClass("active");
    // $(".el-dialog__wrapper").css({"z-index":"2003","display":"block"})
    parent.itemi(id)
    }
  });
  
});
</script>
<style scoped lang="scss">
.Isecontent {
  .Iframes {
    display: flex;
    overflow: hidden;
    .iframe-con {
      position: relative;
      flex: 1;
      height: 100%;
      .iframe-img {
        position: absolute;
        top: 20px;
        width: 40px;
        z-index: 999;
      }
      .iframe-revise {
        right: 100px;
        top: 24px;
        height: 36px;
      }
      .iframe-plus {
        right: 40px;
        height: 40px;
      }
      .in-iframe {
        border: 0;
        width: 100%;
        height: 100vh;
        position: absolute;
        top: 0;
        overflow-y: scroll;
      }
    }
    .right-ul {
      width: 500px;
      .conspan {
        display: flex;
        border-bottom: 1px solid #e4e5ea;
        height: 30px;
        line-height: 30px;
        padding: 10px;
        .con-tent {
          flex: 1;
          font-size: 16px;
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
        }
        .con-btn {
          cursor: pointer;
          color: #18b0e2;
          width: 80px;
        }
      }
      .active {
        border-left: 4px solid #18b0e2;
      }
      .el-tab-pane {
        overflow-y: scroll;
        height: 100vh;
      }
    }
  }
  .el-dialog {
    display: none;
  }
}
</style>