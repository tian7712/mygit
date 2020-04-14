<template>
  <el-container class="home_container">
      <!-- 头部区域 -->
      <el-header>
        <div>
          <img src="../assets/xiao.jpg" alt="" width="70px" height="70px">
          <span>电商后台管理系统</span>
        </div>
        <el-button type="info" @click="logout">退出</el-button>
      </el-header>
      <!-- 页面主体区域 -->
      <el-container>
        <!-- 侧边栏 -->
        <el-aside :width="isCollaps ? '64px' : '200px'">
          <div class="toggle-button" @click="toggleCollaps">|||</div>
          <!-- unique-opened  menu 属性，只保持一个子菜单的展开   collapse-transition 折叠是否动画效果默认打开  default-active 当前激活菜单的 index 也就是当前路径-->
          <!-- router 开启路由模式，对应的二级菜单可以通过index属性的值作为路劲跳转 -->
          <el-menu background-color="#333744" text-color="#fff" active-text-color="#409EFF" unique-opened :collapse="isCollaps" :collapse-transition="false" router :default-active="activePath">
            <!-- 一级菜单 -->
            <!-- 在index属性上加：，动态绑定，设置成唯一值，这样一级菜单点开的时候只会显示自己的二级菜单，index属性需要字符串类型的值，所以可以拼接空字符串的方式转换 -->
           <el-submenu :index="item.id + ''" :key="item.id" v-for="item in menulist">
             <!-- 一级菜单的模板 -->
              <!-- 图标 -->
              <template slot="title">
              <i :class="iconObj[item.id]"></i>
              <span>{{item.authName}}</span>
              </template>
            <!-- 二级菜单 -->
            <el-menu-item :index="'/' + subItem.path + ''" v-for="subItem in item.children" :key="subItem.id" @click="saveNavStatus('/' + subItem.path)">
            <!-- 点击了二级菜单，需要将当前的路径保存起来，存储到sessionStorage ，然后在赋值给:default-active 属性，这样的话，只要不关闭浏览器或者不点击别的二级菜单之前的状态都会保存下来 -->
              <template slot="title">
              <!-- 图标 -->
              <i class="el-icon-menu"></i>
              <span>{{subItem.authName}}</span>
            </template>
            </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <!-- 右侧主体区域 -->
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      menulist:[],
      iconObj:{
        '125':'iconfont icon-user',
        '103':'iconfont icon-tijikongjian',
        '101':'iconfont icon-shangpin',
        '102':'iconfont icon-danju',
        '145':'iconfont icon-baobiao'
      },
      isCollaps:false,
      // 被激活的链接地址
      activePath:''
    }
  },
  // 实例创建完成之后
  created() {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    logout () {
      // 清空token
      window.sessionStorage.clear()
      // 重定向页面到登录
      this.$router.push('/login')
    },
    // 获取菜单列表
    async getMenuList () {
      const { data : res } = await this.$http('/menus')
      // console.log(res)
      if (res.meta.status !== 200) return this.$message.error(res.meta.message)
      this.menulist = res.data
    },
    // 点击折叠菜单
    toggleCollaps () {
      this.isCollaps = !this.isCollaps
    },
    // 保存链接的激活状态
    saveNavStatus(activePath){
      window.sessionStorage.setItem('activePath' , activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style lang="less" scoped>
  .home_container {
    height: 100%;
  }
  .el-header {
    background-color: #373d41;
    display: flex;
    justify-content: space-between;
    padding-left: 0;
    align-items: center;
    color: #fff;
    font-size: 20px;
    div {
      display: flex;
      align-items: center;
    span {
        margin-left: 15px;
      }
    }
  }
  .el-aside {
    background-color: #333744;
    .el-menu {
      border-right: 0;
    }
  }
  .el-main {
    background-color: #eaedf1;
  }
  .iconfont{
    margin-right: 10px;
  }
  .toggle-button {
    background-color: #4a5064;
    font-size: 10px;
    color: #fff;
    line-height: 24px;
    text-align: center;
    letter-spacing: 0.2em;
    cursor: pointer;
  }

</style>
