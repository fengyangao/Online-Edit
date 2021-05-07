<template>
  <div class="home-container">
    <div class="home-header">
      <div :class="isCollapse?'logo-collapse-width':'logo-width'" @click="tohome()">
        <img src="../assets/images/Logo-header.png" />
      </div>
      <el-radio-group v-model="isCollapse" class="tools">
        <el-radio-button :label="false">展开</el-radio-button>
        <el-radio-button :label="true">收起</el-radio-button>
      </el-radio-group>
      <div class="userinfo">
        <el-dropdown trigger="hover" @command="handleCommand">
          <div class="userinfo-inner">
            <span>{{sysUserName}}</span>
            <img src="../assets/images/portrait.jpg" />
          </div>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="1">密码重置</el-dropdown-item>
            <el-dropdown-item divided command="2">退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </div>
    <div class="main">
      <el-menu
        :default-active="$route.path"
        class="el-menu-vertical-demo"
        @open="handleOpen"
        @close="handleClose"
        :collapse="isCollapse" 
      >
      <!-- background-color="#545c64"
        text-color="#fff"
        active-text-color="#ffd04b" -->
        <el-submenu
          :index="index+''"
          v-for="(item,index) in routers"
          :key="index"
          v-if="!item.hidden"
        >
          <template slot="title">
            <i :class="item.iconTu"></i>
            <span slot="title" v-if="!item.hidden">{{item.name}}</span>
          </template>
          <el-menu-item-group v-for="ich in item.children" :key="ich.path">
            <el-menu-item :index="ich.path" @click="$router.push(ich.path)">{{ich.name}}</el-menu-item>
          </el-menu-item-group>
        </el-submenu>
      </el-menu>
      <div :class="isCollapse?'content-container':'content-containers'">
        <div class="breadcrumb-container">
          <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb-inner">
            <el-breadcrumb-item
              v-for="(item,index) in $route.matched"
              :key="index"
              :to="{ path:item.path}"
            >{{ item.name }}</el-breadcrumb-item>
          </el-breadcrumb>
        </div>
        <div>
          <transition name="fade" mode="out-in">
            <router-view></router-view>
          </transition>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      sysUserName: "",
      sysUserAvatar: "",
      form: {
        name: "",
        region: "",
        date1: "",
        date2: "",
        delivery: false,
        type: [],
        resource: "",
        desc: ""
      },
      isCollapse: false,
      routers: []
    };
  },
  methods: {
    handleOpen(key, keyPath) {
      console.log(key, keyPath);
    },
    handleClose(key, keyPath) {
      console.log(key, keyPath);
    },
    onSubmit() {
      console.log("submit!");
    },
    handleopen() {
      //console.log('handleopen');
    },
    handleclose() {
      //console.log('handleclose');
    },
    handleselect(a, b) {},
    //退出登录
    handleCommand(val) {
      // this.$message('click on item ' + val);
      if (val == "2") {
        this.$router.push("/login");
        // sessionStorage.removeItem('user');
      }
    },
    //折叠导航栏
    collapse() {
      this.collapsed = !this.collapsed;
      var uiwidth = document.getElementById("lastclass");
      if (uiwidth.offsetWidth === 0) {
        uiwidth.style.width = "230px";
      }
    },
    showMenu(i, status) {
      this.$refs.menuCollapsed.getElementsByClassName(
        "submenu-hook-" + i
      )[0].style.display = status ? "block" : "none";
    },
    tohome() {
      this.$router.push("/homePage/page");
    }
  },
  mounted() {
    var user = sessionStorage.getItem("user");
    if (user) {
      user = JSON.parse(user);
      this.sysUserName = user.name || "admin";
      this.sysUserAvatar = user.avatar;
    }
    console.log(this.$router.options.routes);
    this.routers = [];
    if (this.$router.options.routes) {
      this.$router.options.routes.map(item => {
        if (!item.hidden) {
          this.routers.push(item);
        } else {
          return;
        }
      });
    }
  }
};
</script>

<style scoped lang="scss">
.home-container {
  width: 100%;
  height: 100%;
  font-size: 16px;
  overflow: hidden;
  .home-header {
    display: flex;
    flex-direction: row;
    height: 70px;
    width: 100%;
    line-height: 70px;
    background-color: rgb(84, 92, 100);
    .userinfo {
      text-align: right;
      padding-right: 50px;
      flex: 1;
      .userinfo-inner {
        cursor: pointer;
        color: #fff;
        span {
          line-height: 72px;
        }
        img {
          width: 50px;
          height: 50px;
          border-radius: 50%;
          margin: 10px 0px 10px 10px;
          float: right;
        }
      }
    }
    .logo-width {
      width: 200px;
      text-align: center;
      background-position: center;
      cursor: pointer;
      z-index: 999;
      // border-right:1px solid #fff;
      padding-top: 13px;
      img {
        height: 39px;
      }
    }
    .logo-collapse-width {
      width: 64px;
      text-align: center;
      background: #545c64;
      background-position: center;
      cursor: pointer;
      z-index: 999;
      // border-right:1px solid #fff;
      padding-top: 10px;
    }
    .tools {
      padding: 16px 0 0 20px;
      cursor: pointer;
    }
  }
  .main {
    display: flex;
    height: 95%;
    // overflow: hidden;
    .content-container {
      padding: 20px;
      // width: 98%;
      // overflow: hidden;
      // overflow-y: scroll;
      .breadcrumb-container {
		height: 30px;
        // margin-bottom: 15px;

        .title {
          width: 200px;
          float: left;
          color: #475669;
        }
        .breadcrumb-inner {
          float: left;
        }
      }
      .content-wrapper {
        background-color: #fff;
        // box-sizing: border-box;
        height: 100%;
      }
    }
    .content-containers {
      min-width: 83%;
      max-width: 100%;
      padding: 20px;
      // height: 100%;
      // overflow: hidden;
      // overflow-y: scroll;
      .breadcrumb-container {
        // margin-bottom: 15px;
		height: 30px;
        .title {
          width: 200px;
          float: left;
          color: #475669;
        }
        .breadcrumb-inner {
          float: left;
        }
      }
      .content-wrapper {
        background-color: #fff;
        // box-sizing: border-box;
      }
    }
  }
}
</style>
<style >
.el-table .cell {
  padding-left: 10px;
  padding-right: 10px;
}
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  /* transition: width 0.9s; */
}
.el-menu-vertical-demo:not(.el-menu--collapse) .el-submenu {
  width: 200px; 
}
.el-menu--collapse .el-submenu {
  widows: 64px;
}
.el-menu-vertical-demo{
 
}
</style>