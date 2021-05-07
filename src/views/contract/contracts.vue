<template>
<div>
    <header class="monheader">
      <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect" background-color="#f3f6f9"
      text-color="#000"
      active-text-color="#409EFF">
        <el-menu-item index="1">电子合同</el-menu-item>
        <el-menu-item index="2">合同模板库</el-menu-item>
      </el-menu>
		</header>
		
	    <section >			
					<el-col :span="24" >
              <router-view></router-view>				
					</el-col>			
			</section>
  
</div>
</template>
<script>
import axios from "axios";
export default {
	data() {
    return { 
      activeIndex:'1',
      itemID:{},
      dataNav: {
      
    },
    // defaultProps: {
    //       children: 'children',
    //       label: 'nodeName'
    //     }
	}
	},
	 methods: {
		Search() {
      // if (this.filters.object !== "所有项目") {      
      //   var searcharr = [];
      //   this.datalist.map(item => {
      //     console.log(item);
      //     console.log(this.filters.buildName);
      //     if (item.buildName == this.filters.buildName) {
      //       console.log(item);
      //       searcharr.push(item);
      //     }
      //   });   
      //   this.datalist = searcharr;
      //   return;
      // } else {  
      //   this.getUsers();
      // }
      if (this.filters.auditStatus) {
        var searchlist = [];
        this.datalist.map(item => {
          if (this.filters.auditStatus == item.auditStatus) {
            searchlist.push(item);
          }
        });
        this.datalist = searchlist;
      }
    } ,
      handleNodeClick(data) {
        console.log(data.nodeId);
        this.itemID=data
      },
       handleSelect(key, keyPath) {
        console.log(key, keyPath);
        if(key=='1'){
          this.$router.push("/contract/contractsAa")
        }else{
          this.$router.push("/contract/contractsBb")
        }
      },
      getNav(){
        let _this=this;
        axios.get('../../../static/nav.json').then(response => {               
                  // console.log(response.data)//对象
                  // console.log(JSON.stringify(response.data))
                  _this.dataNav=response.data
                  console.log(_this.dataNav)
               }, response => {
                    console.log("error");
                });
                // console.log(this.dataNav)//
      },
      getPath(){
    console.log(this.$route.path);
    if(this.$route.path=='/contractsAa'||this.$route.path=='/contractsIndex'){
      let num='1';
      this.activeIndex=num
    }else{
      let num='2';
      this.activeIndex=num
    }
  }
   },
   watch: {
    '$route':'getPath'
  },

  

    mounted(){
      this.getNav()
    },
  //  components:{
  //     'v-contractsItemA':contractsItemA,
  //     'v-contractsItemB':contractsItemB
  //  }
}
</script>
<style scoped lang="scss">
 .monheader{
        padding: 10px;
        margin-top: 6px;
        width:100%;
        height: 40px;  
        .el-menu-demo{
          border-bottom: 0;
        }    
      }
</style>