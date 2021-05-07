<template>
  <el-row class="tac">
    <el-col :span="6">
      <el-tree :data="Nav" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
    </el-col>
    <el-col :span="18" style="padding-left:20px">
      <header>
        <el-form :inline="true">
          <el-form-item>
            <el-input suffix-icon="el-icon-search" v-model="filters" placeholder="请输入品牌名" clearable></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="Search('1')">搜索</el-button>
          </el-form-item>
        </el-form>
      </header>
      <!--列表-->
      <section>
        <el-table
          :data="datalist"
          highlight-current-row
          v-loading="loading"
          @row-dblclick="dbclick"
          style="width: 100%;"
          height="72vh"
        >
          <el-table-column type="index" width="60" label="序号"></el-table-column>
          <el-table-column prop="number" label="合同编号"></el-table-column>
          <el-table-column prop="name" label="项目名称">
            <!-- <template slot-scope="scope">
					<span class="abbreviation">{{scope.row.abbreviation}}</span>
            </template>-->
          </el-table-column>
          <el-table-column prop="users" label="租户"></el-table-column>
          <el-table-column prop="brand" label="品牌"></el-table-column>
          <el-table-column prop="type" label="合同类型"></el-table-column>
          <el-table-column prop="standard" label="是否标准"></el-table-column>
          <el-table-column prop="admin" label="创建人"></el-table-column>
          <el-table-column prop="dataTime" label="更新"></el-table-column>
          <el-table-column prop="state" label="状态"></el-table-column>
        </el-table>
      </section>
    </el-col>
  </el-row>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      filters: "",
      contype: "",
      loading: false,
      datalist: [
        {
          number:"202061500001745",
          name:"乐坊虹井",
          users:"张三",
          brand:"KFC",
          type:'标准合同',
          standard:'是',
          admin:'李四',
          dataTime:'2020-6-15',
          state:'待审核'
        }
      ],
      dataarr: [],
      Nav: [
        {
          label: "租赁合同"
        },
        {
          label: "企划合同",
          children: [
            {
              label: "多经租赁合同"
            },
            {
              label: "广告租赁合同"
            },
            {
              label: "仓库租赁合同"
            }
          ]
        }
      ],
      defaultProps: {
        children: "children",
        label: "label"
      }
    };
  },
  methods: {
    dbclick(v) {
      console.log(v);
      this.$router.push({ path: "/contract/contractsIndex", query: v });
    },
    handleNodeClick(data) {
      this.filters = "";
      this.datalist = this.dataarr;
      console.log(data.label, 1);
      if (data.label == "企划合同") {
        return;
      } else {
        this.filters = data.label;
        this.Search();
      }
    },

    //获取用户列表
    getUser() {
      // let para = {
      // 	name: this.filters.name
      // };
      // this.loading = true;
      // //NProgress.start();
      // getUserList(para).then((res) => {
      // 	this.users = res.data.users;
      // 	this.loading = false;
      // 	//NProgress.done();
      // });
      var _this = this;
      axios.get("/getcon").then(res => {
        console.log(res);
        _this.datalist = res.data;
        _this.dataarr = res.data;
      });
    },
    Search() {
      //搜索
      let searchlist = [];
      if (this.filters) {
        this.datalist.map(item => {
          if (this.filters == item.type) {
            searchlist.push(item);
          }
          if (this.filters == item.users) {
            searchlist.push(item);
          }
          if (this.filters == item.number) {
            searchlist.push(item);
          }
        });
        this.datalist = searchlist;
      }
    }
  },
  components: {},
  mounted() {
    this.getUser();
  }
};
</script>

<style scoped lang="scss">
@import "../../common/css/table.css";
.tac {
  padding-top: 20px;

  .header {
    padding: 10px;
    margin: 10px 0px;
    height: 60px;
  }
  .el-tree {
    height: 100vh;
    border-right: solid 1px #ddd;
    /deep/ .el-tree-node {
      height: 26px !important;
      line-height: 26px !important;
    }
  }
}
</style>