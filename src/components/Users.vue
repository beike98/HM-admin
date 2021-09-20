<template>
  <div>
    <!--面包屑-->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!--卡片区-->
    <el-card>
      <!--头部的查询、添加-->
      <div class="users-head">
        <el-input placeholder="请输入查询内容" class="users-head-input" v-model="queryInfo.query" clearable
                  @clear="getUserList">
          <el-button slot="append" icon="el-icon-search" @click="getUserList"/>
        </el-input>
        <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
      </div>
      <!--表格-->
      <el-table :data="userList" border stripe>
        <el-table-column label="id" prop="id" width="80px"/>
        <el-table-column label="姓名" prop="username"/>
        <el-table-column label="邮箱" prop="email"/>
        <el-table-column label="电话" prop="mobile"/>
        <el-table-column label="角色" prop="role_name"/>
        <el-table-column label="开启/禁用">
          <template slot-scope="scope">
            <!--开启/禁用操作-->
            <el-switch v-model="scope.row.mg_state" @change="userStateChanged(scope.row)"/>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <!--编辑用户-->
            <el-button type="primary" size="small" @click="showEditDialog(scope.row.id)">编辑</el-button>
            <!--删除用户-->
            <el-button type="danger" size="small" @click="removeUserById(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!--分页-->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
    </el-card>
    <!--添加用户对话框-->
    <el-dialog
      title="添加用户"
      :visible.sync="addDialogVisible"
      @close="addDialogClosed"
      :close-on-click-modal="false"
      width="30%">
      <el-form ref="addFormRef" :model="addForm" :rules="addFormRules" label-width="80px" status-icon>
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password" type="password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="联系方式" prop="mobile">
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="addDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="addUser">确 定</el-button>
  </span>
    </el-dialog>
    <!--编辑用户对话框-->
    <el-dialog
      title="编辑用户"
      :visible.sync="editDialogVisible"
      @close="editDialogClosed"
      :close-on-click-modal="false"
      width="30%">
      <el-form ref="editFormRef" :model="editForm" :rules="editFormRules" label-width="80px" status-icon>
        <el-form-item label="用户名">
          <el-input v-model="editForm.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="联系方式" prop="mobile">
          <el-input v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="editDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="editUserInfo">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Users',
  data () {
    const checkEmail = (rule, value, callback) => {
      const regEmail = /^[A-Za-z0-9]+([_.][A-Za-z0-9]+)*@([A-Za-z0-9-]+\.)+[A-Za-z]{2,6}$/
      if (regEmail.test(value)) {
        return callback()
      } else {
        callback(new Error('请输入合法的邮箱！'))
      }
    }
    const checkMobile = (rule, value, callback) => {
      const regMobile = /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/
      if (regMobile.test(value)) {
        return callback()
      } else {
        callback(new Error('请输入合法的联系方式！'))
      }
    }
    return {
      // getUserList参数
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 10
      },
      // 用户列表数据
      userList: [],
      // 用户列表总条数
      total: 0,
      // 控制添加用户对话框显示/隐藏
      addDialogVisible: false,
      // 控制编辑用户对话框显示/隐藏
      editDialogVisible: false,
      // 添加用户的表单数据
      addForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      // 查询用户的信息
      editForm: {},
      // 添加表单的验证规则
      addFormRules: {
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
            message: '长度应为3到10个字符',
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
        ],
        // 邮箱是否合法
        email: [
          {
            required: true,
            message: '请输入邮箱',
            trigger: 'blur'
          },
          {
            validator: checkEmail,
            trigger: 'blur'
          }
        ],
        // 手机是否合法
        mobile: [
          {
            required: true,
            message: '请输入联系方式',
            trigger: 'blur'
          },
          {
            validator: checkMobile,
            trigger: 'blur'
          }
        ]
      },
      editFormRules: {
        // 邮箱是否合法
        email: [
          {
            required: true,
            message: '请输入邮箱',
            trigger: 'blur'
          },
          {
            validator: checkEmail,
            trigger: 'blur'
          }
        ],
        // 手机是否合法
        mobile: [
          {
            required: true,
            message: '请输入联系方式',
            trigger: 'blur'
          },
          {
            validator: checkMobile,
            trigger: 'blur'
          }
        ]
      }
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    // 获取用户列表
    async getUserList () {
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        this.$message.error('获取用户列表失败！')
      } else {
        this.userList = res.data.users
        this.total = res.data.total
      }
    },
    // 监听页面page-size变化
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getUserList()
    },
    // 监听页面page-num变化
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
    },
    // 监听switch开关状态变化
    async userStateChanged (userinfo) {
      const { data: res } = await this.$http.put(`users/${userinfo.id}/state/${userinfo.mg_state}`)
      if (res.meta.status !== 200) {
        userinfo.mg_state = !userinfo.mg_state
        this.$message.error('更新用户状态失败！')
      } else {
        this.$message.success('更新用户状态成功！')
      }
    },
    // 监听添加用户表单关闭时重置表单
    addDialogClosed () {
      this.$refs.addFormRef.resetFields()
    },
    // // 监听编辑用户表单关闭时重置表单
    editDialogClosed () {
      this.$refs.editFormRef.resetFields()
    },
    // 添加新用户
    addUser () {
      this.$refs.addFormRef.validate(async valid => {
        if (valid) {
          const { data: res } = await this.$http.post('users', this.addForm)
          if (res.meta.status !== 201) {
            this.$message.error('添加用户失败！')
          } else {
            this.$message.success('添加用户成功！')
            // 隐藏对话框
            this.addDialogVisible = false
            // 页面重新刷新数据展示
            await this.getUserList()
          }
        }
      })
    },
    // 打开编辑用户对话框
    async showEditDialog (id) {
      const { data: res } = await this.$http.get('users/' + id)
      if (res.meta.status !== 200) {
        this.$message.error('查询用户信息失败！')
      } else {
        this.editForm = res.data
        this.editDialogVisible = true
      }
    },
    // 修改用户信息
    editUserInfo () {
      this.$refs.editFormRef.validate(async valid => {
        if (valid) {
          const { data: res } = await this.$http.put('users/' + this.editForm.id, {
            email: this.editForm.email,
            mobile: this.editForm.mobile
          })
          if (res.meta.status !== 200) {
            this.$message.error('更新用户信息失败！')
          } else {
            // 隐藏对话框
            this.editDialogVisible = false
            // 页面重新刷新数据展示
            await this.getUserList()
          }
        }
      })
    },
    // 删除对应ID的用户
    async removeUserById (id) {
      const confirmResult = await this.$confirm('此操作将永久删除此用户，是否继续？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      if (confirmResult === 'confirm') {
        const { data: res } = await this.$http.delete('users/' + id)
        if (res.meta.status !== 200) {
          this.$message.error('删除用户失败！')
        } else {
          this.$message.success('删除用户成功！')
          // 页面重新刷新数据展示
          await this.getUserList()
        }
      }
    }
  }
}
</script>

<style scoped>
.users-head {
  display: flex;
  margin-bottom: 20px;
}

.users-head-input {
  width: 500px;
  margin-right: 20px;
}
</style>
