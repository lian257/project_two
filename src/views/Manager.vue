<template>
  <div>
    <el-container>
      <!--    侧边栏  -->
      <el-aside :width="asideWidth" style="min-height: 100vh; background-color: #001529">
        <div style="height: 60px; color: white; display: flex; align-items: center; justify-content: center">
          <img src="@/assets/logo1.png" alt="" style="width: 40px; height: 40px">
          <span class="logo-title" v-show="!isCollapse">honey2024</span>
        </div>

        <el-menu :collapse="isCollapse" :collapse-transition="false" router background-color="#001529" text-color="rgba(255, 255, 255, 0.65)" active-text-color="#fff" style="border: none" :default-active="$route.path">
          <el-menu-item index="/">
            <i class="el-icon-menu"></i>
            <span slot="title">系统首页</span>
          </el-menu-item>
          <el-menu-item index="/1">
            <i class="el-icon-house"></i>
            <span slot="title">系统首页</span>
          </el-menu-item>
          <el-menu-item index="/2">
            <i class="el-icon-house"></i>
            <span slot="title">系统首页</span>
          </el-menu-item>
          <el-submenu index="3">
            <template slot="title">
              <i class="el-icon-menu"></i>
              <span>信息管理</span>
            </template>
            <el-menu-item>用户信息</el-menu-item>
            <el-menu-item>管理员信息</el-menu-item>
            <el-menu-item index="/">系统首页</el-menu-item>
          </el-submenu>
        </el-menu>

      </el-aside>

      <el-container>
        <!--        头部区域-->
        <el-header>

          <i :class="collapseIcon" style="font-size: 26px" @click="handleCollapse"></i>
          <el-breadcrumb separator-class="el-icon-arrow-right" style="margin-left: 20px">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
          </el-breadcrumb>

          <div style="flex: 1; width: 0; display: flex; align-items: center; justify-content: flex-end">
            <i class="el-icon-quanping" style="font-size: 19px" @click="handleFull"></i>
            <el-dropdown placement="bottom">
              <div style="display: flex; align-items: center; cursor: default">
                <img src="@/assets/logo1.png" alt="" style="width: 40px; height: 40px; margin: 0 5px">
                <span>管理员</span>
              </div>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item>个人信息</el-dropdown-item>
                <el-dropdown-item>修改密码</el-dropdown-item>
                <el-dropdown-item @click.native = "logout">退出登录</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>

        </el-header>

        <!--        主体区域-->
        <el-main>
          <div style="box-shadow: 0 0 10px rgba(0,0,0,.1); padding: 10px 20px;
                border-radius: 5px; margin-bottom: 10px">
            早安，骚年，祝你开心每一天！
          </div>

          <div style="display: flex">
            <el-card style="width: 30% ; margin-top: 10px" >
              <div slot="header" class="clearfix">
                <span>哥哥带你做毕设2024</span>
              </div>
              <div>
                2024毕设正式开始了！哥哥带你手把手敲出来！
                <div style="margin-top: 20px">
                  <div style="margin: 10px 0"><strong>主题色</strong></div>
                  <el-button type="primary">按钮</el-button>
                  <el-button type="success">按钮</el-button>
                  <el-button type="warning">按钮</el-button>
                  <el-button type="danger">按钮</el-button>
                  <el-button type="info">按钮</el-button>
                </div>
              </div>
            </el-card>

            <el-card style="width: 70%">
              <div slot="header" class="clearfix">
                <span>用户数据</span>
              </div>
              <div>
                <el-table :data="users">
                  <el-table-column label="Id" prop="id"></el-table-column>
                  <el-table-column label="用户名" prop="username"></el-table-column>
                  <el-table-column label="姓名" prop="name"></el-table-column>
                  <el-table-column label="地址" prop="address"></el-table-column>
                  <el-table-column label="文件上传">
                    <template v-slot="scope">
                      <el-upload
                          action="http://localhost:9090/file/upload"
                          :headers="{token:user.token}"
                          :show-file-list="false"
                          :on-success="(row,file,fileList) => handleTableFileUpload(scope.row,file,fileList)"
                      >
                        <el-button size="mini" type="primary">点击上传</el-button>
                      </el-upload>
                    </template>
                  </el-table-column>

                  <el-table-column label="文件预览">
                    <template v-slot="scope">
                      <el-image v-if ="scope.row.avatar" :src="scope.row.avatar"
                                style="width: 50px;height: 50px">
                      </el-image>
                    </template>
                  </el-table-column>

                </el-table>
              </div>
            </el-card>
          </div>
          <div style="display: flex; margin: 10px 0">
            <el-card style="width: 50% ; margin-left: 10px ">
              <div slot="header" class="clearfix">
                <span>文件上传测试</span>
              </div>
              <div>

              </div>
            </el-card>
          </div>
        </el-main>

      </el-container>


    </el-container>
  </div>
</template>

<script>

import request from "@/utils/request";

export default {
  name: 'HomeView',
  data() {
    return {
      isCollapse: false,  // 不收缩
      asideWidth: '200px',
      collapseIcon: 'el-icon-s-fold',
      users: [],
      user: JSON.parse(localStorage.getItem('honey-user') || '{}'),
      url:''
    }
  },
  mounted() {
    /**
     页面加载后刷新
     axios.get('http://localhost:8080/user/selectAll').then(res =>{
       console.log(res)
       this.users = res.data.data
     })
     */
    request.get('user/selectAll').then(res => {
      this.users = res.data
    })
  },
  methods: {
    handleTableFileUpload(row,file,fileList){
      console.log(row,file,fileList)
      row.avatar = file.response.data
      console.log(row)

       request.put('/user/update',row).then(res =>{
         if(res.code === '200'){
           this.$message.success('上传成功')
         }else{
           this.$message.error(res.msg)
         }
       })
    },
    handleFull() {
      document.documentElement.requestFullscreen()
    },
    handleCollapse() {
      this.isCollapse = !this.isCollapse
      this.asideWidth = this.isCollapse ? '64px' : '200px'
      this.collapseIcon = this.isCollapse ? 'el-icon-s-unfold' : 'el-icon-s-fold'
    },
    logout(){
      localStorage.removeItem('honey-user')
      this.$router.push('/login')
    },
    handleFileUpload(response, file, fileList) {
      console.log(response, file, fileList)
    },
  }
}
</script>

<style>
.el-menu--inline {
  background-color: #000c17 !important;
}
.el-menu--inline .el-menu-item {
  background-color: #000c17 !important;
  padding-left: 49px !important;
}
.el-menu-item:hover, .el-submenu__title:hover {
  color: #fff !important;
}
.el-submenu__title:hover i {
  color: #fff !important;
}
.el-menu-item:hover i {
  color: #fff !important;
}
.el-menu-item.is-active {
  background-color: #1890ff !important;
  border-radius: 5px !important;
  width: calc(100% - 8px);
  margin-left: 4px;
}
.el-menu-item.is-active i, .el-menu-item.is-active .el-tooltip{
  margin-left: -4px;
}
.el-menu-item {
  height: 40px !important;
  line-height: 40px !important;
}
.el-submenu__title {
  height: 40px !important;
  line-height: 40px !important;
}
.el-submenu .el-menu-item {
  min-width: 0 !important;
}
.el-menu--inline .el-menu-item.is-active {
  padding-left: 45px !important;
}
/*.el-submenu__icon-arrow {*/
/*  margin-top: -5px;*/
/*}*/

.el-aside {
  transition: width .3s;
  box-shadow: 2px 0 6px rgba(0,21,41,.35);
}
.logo-title {
  margin-left: 5px;
  font-size: 20px;
  transition: all .3s;   /* 0.3s */
}
.el-header {
  box-shadow: 2px 0 6px rgba(0,21,41,.35);
  display: flex;
  align-items: center;
}
</style>