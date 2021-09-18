<template>
  <div class="login_container">
    <div class="login-box">
      <div class="login-title">
        沐海后台管理系统
      </div>
      <!--登陆表单-->
      <el-form>
        <!--用户名-->
        <el-form-item>
          <el-input prefix-icon="iconfont icon-ic_uesr_n" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <!--密码-->
        <el-form-item>
          <el-input prefix-icon="iconfont icon-wodemima" placeholder="请输入密码"></el-input>
        </el-form-item>
        <!--登陆按钮-->
        <el-form-item>
          <el-button type="primary" class="login-btn">登陆</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login'
}
</script>

<style scoped>
.login-title {
  text-align: center;
  padding: 15px;
  color: #5d6062;
}

.login_container {
  height: 100%;
  background-image: linear-gradient(45deg, #93a5cf 0%, #e4efe9 100%);
  display: flex;
  justify-content: center;
  align-items: center;
}

.login-box {
  border-radius: 4px;
  padding: 25px 15px;
  background: #ffffff;
  width: 360px;
}

.login-btn {
  width: 100%;
}

</style>
