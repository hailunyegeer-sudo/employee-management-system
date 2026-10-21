<template>
  <div style="max-width: 360px; margin: 120px auto;">
    <h2>员工管理系统登录</h2>
    <el-form :model="form" label-width="70px">
      <el-form-item label="账号">
        <el-input v-model="form.username" placeholder="任意账号" />
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="form.password" type="password" placeholder="任意密码" />
      </el-form-item>
      <el-form-item label="角色">
        <el-radio-group v-model="form.role">
          <el-radio value="管理员">管理员</el-radio>
          <el-radio value="普通员工">普通员工</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleLogin" style="width: 100%;">
          登录
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script setup>
import { reactive } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'

const router = useRouter()
const form = reactive({ username: '', password: '', role: '管理员' })

const handleLogin = () => {
  if (!form.username || !form.password) {
    ElMessage.warning('请输入账号和密码')
    return
  }
  localStorage.setItem('token', 'fake-token-' + Date.now())
  localStorage.setItem('role', form.role)
  ElMessage.success('登录成功')
  router.push('/employees')
}
</script>