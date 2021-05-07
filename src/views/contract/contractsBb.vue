<template>
  <el-row class="tac">
    <el-col :span="6">
      <el-tree :data="Nav" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
    </el-col>
    <el-col :span="18">
      <p class="messageTit">{{isshow?'当前选择：'+name:'当前选择：你还没有选择任何模板!'}}</p>
      <iframe :src="'/static/'+name+'.htm'" style="border: 0; width: 100%;height:64vh" v-if='isshow'></iframe>
      <p v-if='!isshow' class="messageCon">请在左侧选择您要查看的标准合同模板!</p>
    </el-col>			
  </el-row>
</template>
<script>
	import axios from "axios";
	export default {
    
		data() {    
			return {
        name: '',	
        isshow:false,			
         Nav: [
          {
            label: '上海区域公司',
            children:[
              {
                label: '乐虹坊',
                children:[
                  {
                    label: '租赁合同',
                    children:[
                      {
                        label:'商铺租赁合同标准模板'
                      },
                      {
                        label:'多经租赁合同标准模板'
                      },
                      {
                        label:'广告租赁合同标准模板'
                      },
                      {
                        label:'仓库租赁合同标准模板'
                      }
                    ]
                  }
                ]
            },
            {
                label: '虹桥晶座',
            }
            ]  
          },
        ],
        defaultProps: {
          children: 'children',
          label: 'label'
        }
			}
		},
		methods: {
			handleNodeClick(data) {
        console.log(data);
        if(data.children==undefined){
          this.isshow=true
          this.name=data.label
        }else{
          this.isshow=false
        }
       
      },

		},
		
		mounted() {
			
		}
	};

</script>

<style scoped lang="scss">
@import '../../common/css/table.css';
.tac{
  padding:20px 0 0 20px;
	.messageTit{
    height: 64px;
    line-height: 64px;
    background-color: #fdeadf;
    padding-left: 60px;
    margin-top: 60px;
  }
  .messageCon{
    font-size: 22px;
    margin: 100px auto;
  }
}
</style>