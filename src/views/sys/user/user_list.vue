<template>
  <init :default-clear="false">
    <auth-table
        ref="search"
        :optionData.sync="option"
        @loadData="load">
    </auth-table>

    <el-dialog title="添加/修改用户信息" v-model="option.showDialog">
      <User_edit ref="user_ref" @load="$refs.search.queryData()"></User_edit>
    </el-dialog>

  </init>
</template>

<script>
import User_edit from "@/views/sys/user/user_edit";
export default {
  name: "user_list",
  components: {User_edit},
  data(){
    return{

      option: {
        //弹窗关闭（showDialog，名称自定义，可以多个）
        showDialog:false,
        //搜索
        searchList: {
          list:
              {
                userName:{placeholder:'请输入用户名',type:"input",title:'用户名',value:''},
                phone:{placeholder:'请输入手机号',type:"input",title:'手机号',value:''},
              },

          func:[
              {
                title:"添加",//按钮名称
                funName:this.add,//事件
                auth:"",//按钮权限（可写可不写）
                icon:'',//图片（可写可不写）
              }
              ],
          //按钮布局是否跟在查询款后面
          butNewlineLayout:false,
        },
        //是否首次加
        load:true,
        //开启分页
        openPageLoad:true,
        //页大小默认10
        page:{
          currentPage:1,
          pageSize:10,
          total:0,
        },
        table:[
          {
            prop:'userName',
            label:'用户名',
          },
          {
            prop:'phone',
            label:'手机号',
          },
          {
            prop:'remark',
            label:'备注',
            width:120,
          },
          {
            prop:'createTime',
            label:'创建时间',
          },
        ],
        //操作栏宽度
        authButWidth:160,
        //操作
        authBut:[
          {name:"修改",func:this.rowEdit},
          {name:"删除",func:this.rowDel},
        ],
        //列表数据
        data: [],
      },
    }
  },
  methods: {

    //查询（查询条件直接带入{key:1,key:2}）
    load(data) {
      this.$api.userApi.getUserPageList(data).then(res => {
        if (res.data.status == 200) {
          this.option.data = res.data.data.list;
          this.option.page.total = res.data.data.total;
        } else {
          this.$message.error(res.data.message)
        }
      })
    },
    //新增
    add(){
      this.option.showDialog=true;
      this.$nextTick(() => {
        this.$refs.user_ref.editRow({});
      })
    },
    //修改
    rowEdit(row){
      this.option.showDialog=true;
      this.$nextTick(() => {
        this.$refs.user_ref.editRow(JSON.parse(JSON.stringify(row)));
      })
    },
    //删除
    rowDel(row){
      this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$api.userApi.delUser(row).then(res => {
          this.$message.error(res)
        })
      }).catch(()=>{})
    },

  }
}
</script>

<style scoped>

</style>