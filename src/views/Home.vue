<template>
  <el-container class="home-container">
    <!--边栏-->
    <el-menu
      default-active="2"
      class="home-menu"
      background-color="#2a3846"
      text-color="#9da8ba"
      active-text-color="#ffd04b"
      :collapse="isCollapse"
      :unique-opened="true"
    >
      <!--一级菜单-->
      <el-submenu :index="item.id.toString()" v-for="item in menuList" :key="item.id">
        <template slot="title">
          <i :class="iconsObj[item.id]"></i>
          <span>{{ item.authName }}</span>
        </template>
        <!--二级菜单-->
        <el-menu-item :index="subItem.id.toString()" v-for="subItem in item.children" :key="subItem.id">
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
        <span class="home-title">欢迎来到沐海后台。当前用户：admin｜超级管理员</span>
        <span>&nbsp;&nbsp;&nbsp;&nbsp;</span>
        <el-button type="text" @click="logout" class="home-logout">
          <i class="el-icon-refresh-left"></i>
          <span>退出登陆</span>
        </el-button>
      </el-header>
      <!--主体-->
      <el-main class="home-main">
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      menuList: [],
      isCollapse: false,
      iconsObj: {
        125: 'el-icon-s-custom',
        103: 'el-icon-key',
        101: 'el-icon-shopping-bag-1',
        102: 'el-icon-tickets',
        145: 'el-icon-s-data'
      }
    }
  },
  mounted () {
    this.getMenuList()
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    onCollapse () {
      this.isCollapse = !this.isCollapse
    },
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) {
        this.$message.error(res.meta.msg)
      } else {
        this.menuList = res.data
      }
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
  width: 300px;
}

.home-header {
  line-height: 60px;
  display: flex;
  justify-content: flex-end;
  font-size: 15px;
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

.home-title,.home-logout {
  color: #888888;
}
</style>
