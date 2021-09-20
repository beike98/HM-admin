<template>
  <el-container class="home-container">
    <!--侧边栏菜单-->
    <el-menu
      :default-active="activePath"
      class="home-menu"
      background-color="#2a3846"
      text-color="#9da8ba"
      active-text-color="#409efff"
      :collapse="isCollapse"
      unique-opened
      router>
      <div class="home-menu-title">
        <a href="/#/welcome">沐海后台管理系统</a>
      </div>
      <!--一级菜单-->
      <el-submenu :index="item.id.toString()" v-for="item in menuList" :key="item.id">
        <template slot="title">
          <i :class="iconsObj[item.id]"></i>
          <span>{{ item.authName }}</span>
        </template>
        <!--二级菜单-->
        <el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children" :key="subItem.id"
                      @click="savaNavState('/' + subItem.path)">
          <template slot="title">
            <i class="el-icon-menu"></i>
            <span>{{ subItem.authName }}</span>
          </template>
        </el-menu-item>
      </el-submenu>
    </el-menu>
    <el-container class="home-content">
      <!--头部-->
      <el-header class="home-header">
        <el-button type="primary" size="mini" icon="el-icon-s-fold" class="home-collapse-btn" @click="onCollapse"/>
        <span class="home-title">当前用户：{{ idInfo.username }}</span>
        <el-button type="text" @click="logout" class="home-logout">
          <i class="el-icon-refresh-left"></i>
          <span>退出登陆</span>
        </el-button>
      </el-header>
      <!--主体-->
      <el-main class="home-main">
        <router-view/>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      id: '',
      idInfo: '',
      menuList: [],
      isCollapse: false,
      activePath: '',
      iconsObj: {
        125: 'el-icon-s-custom',
        103: 'el-icon-key',
        101: 'el-icon-shopping-bag-1',
        102: 'el-icon-tickets',
        145: 'el-icon-s-data'
      }
    }
  },
  created () {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
    this.id = window.sessionStorage.getItem('id')
    this.getIdInfo()
  },
  methods: {
    // 退出登陆
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    // 侧边菜单开关
    onCollapse () {
      this.isCollapse = !this.isCollapse
    },
    // 保存菜单栏item项激活状态
    savaNavState (activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    },
    // 获取菜单列表
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) {
        this.$message.error(res.meta.msg)
      } else {
        this.menuList = res.data
      }
    },
    // 获取当前用户信息
    async getIdInfo () {
      const { data: res } = await this.$http.get('users/' + this.id)
      this.idInfo = res.data
    }
  }
}
</script>

<style scoped>
.home-container {
  height: 100%;
}

.home-menu {
  height: 100%;
}

.home-menu:not(.el-menu--collapse) {
  width: 260px;
}

.home-header {
  line-height: 60px;
  display: flex;
  justify-content: flex-end;
  font-size: 14px;
  position: relative;
  box-shadow: 0 2px 3px rgba(0, 0, 0, .1);
}

.home-content {
  background: #f1f1f1;
}

.home-collapse-btn {
  position: absolute;
  left: 15px;
  top: 50%;
  transform: translateY(-50%);
}

.home-title, .home-logout {
  padding-left: 15px;
  color: #888888;
}

.home-menu-title {
  height: 60px;
  color: #9da8ba;
  text-align: center;
  line-height: 60px;
}
</style>
