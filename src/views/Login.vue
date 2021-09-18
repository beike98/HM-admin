<template>
  <div class="login-container">
    <div class="login-box">
      <div class="login-title">
        沐海后台管理系统
      </div>
      <!--登陆表单-->
      <el-form ref="loginFormRef" :model="loginForm" :rules="loginFormRules">
        <!--用户名-->
        <el-form-item prop="username">
          <el-input prefix-icon="iconfont icon-ic_uesr_n" placeholder="请输入用户名" v-model="loginForm.username"></el-input>
        </el-form-item>
        <!--密码-->
        <el-form-item prop="password">
          <el-input type="password" prefix-icon="iconfont icon-wodemima" placeholder="请输入密码"
                    v-model="loginForm.password"></el-input>
        </el-form-item>
        <!--登陆按钮-->
        <el-form-item>
          <el-button type="primary" class="login-btn" @click="login">登陆</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data () {
    return {
      // 表单数据绑定
      loginForm: {
        username: '',
        password: ''
      },
      // 表单数据验证
      loginFormRules: {
        // 用户名是否合法
        username: [
          {
            required: true,
            message: '请输入用户名',
            trigger: 'blur'
          },
          {
            min: 3,
            max: 10,
            message: '长度应为3到15个字符',
            trigger: 'blur'
          }
        ],
        // 密码是否合法
        password: [
          {
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          },
          {
            min: 6,
            max: 15,
            message: '长度应为6到15个字符',
            trigger: 'blur'
          }
        ]
      }
    }
  },
  methods: {
    // 表单提交预验证
    login () {
      this.$refs.loginFormRef.validate(async (valid) => {
        if (!valid) return
        const { data: res } = await this.$http.post('login', this.loginForm)
        if (res.meta.status === 200) {
          this.$message.success('登陆成功！')
          window.sessionStorage.setItem('token', res.data.token)
          await this.$router.push('/home')
        } else {
          this.$message.error('登陆失败！')
        }
      })
    }
  }
}
</script>

<style scoped>
.login-title {
  text-align: center;
  padding: 15px;
  color: #5d6062;
}

.login-container {
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
