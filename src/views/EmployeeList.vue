<template>
  <div style="padding: 20px;">
    <!-- 顶部工具栏 -->
    <div style="display: flex; gap: 12px; margin-bottom: 16px; align-items: center;">
      <el-input
        v-model="searchKeyword"
        placeholder="按姓名搜索"
        clearable
        style="width: 240px;"
        @keyup.enter="handleSearch"
      />
      <el-button type="primary" @click="handleSearch">搜索</el-button>
      <el-button @click="resetSearch">重置</el-button>
      
      <!-- 靠右对齐 -->
      <div style="flex: 1;"></div>
      
      <el-button type="success" @click="openDialog('add')">新增员工</el-button>
      <el-button @click="handleLogout">退出登录</el-button>
    </div>

    <!-- 核心表格 -->
    <el-table :data="pagedData" border stripe style="width: 100%;">
      <el-table-column prop="id" label="ID" width="70" />
      <el-table-column prop="name" label="姓名" />
      <el-table-column prop="department" label="部门" />
      <el-table-column prop="performance" label="绩效等级" width="110">
        <template #default="{ row }">
          <el-tag :type="tagType(row.performance)">{{ row.performance }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="200">
        <template #default="{ row }">
          <el-button size="small" @click="openDialog('edit', row)">编辑</el-button>
          <!-- 权限控制：普通员工看不到删除按钮 -->
          <el-button
            v-if="currentRole !== '普通员工'"
            size="small"
            type="danger"
            @click="handleDelete(row.id)"
          >
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination
      v-model:current-page="currentPage"
      v-model:page-size="pageSize"
      :total="filteredData.length"
      :page-sizes="[3, 5, 10]"
      layout="total, sizes, prev, pager, next"
      style="margin-top: 16px; justify-content: flex-end;"
    />
  </div>

  <!-- 引入刚才写的弹窗组件 -->
  <EmployeeDialog
    v-model="dialogVisible"
    :mode="dialogMode"
    :edit-row="editRow"
    @saved="handleSaved"
  />
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage, ElMessageBox } from 'element-plus'
import EmployeeDialog from '../components/EmployeeDialog.vue'

const router = useRouter()
// 获取当前角色（加分项用到）
const currentRole = localStorage.getItem('role') || '管理员'

// ---------- 模拟数据 ----------
const employees = ref([
  { id: 1, name: '张三', department: '技术部', performance: 'A' },
  { id: 2, name: '李四', department: '产品部', performance: 'B' },
  { id: 3, name: '王五', department: '技术部', performance: 'C' },
  { id: 4, name: '赵六', department: '运营部', performance: 'A' },
  { id: 5, name: '孙七', department: '人事部', performance: 'B' },
  { id: 6, name: '周八', department: '财务部', performance: 'A' },
])

// ---------- 搜索逻辑 ----------
const searchKeyword = ref('')
const filteredData = computed(() => {
  if (!searchKeyword.value.trim()) return employees.value
  return employees.value.filter(e => e.name.includes(searchKeyword.value.trim()))
})
const handleSearch = () => { currentPage.value = 1 }
const resetSearch = () => { searchKeyword.value = ''; currentPage.value = 1 }

// ---------- 分页逻辑 ----------
const currentPage = ref(1)
const pageSize = ref(5)
const pagedData = computed(() => {
  const start = (currentPage.value - 1) * pageSize.value
  return filteredData.value.slice(start, start + pageSize.value)
})

// ---------- 删除逻辑 ----------
const handleDelete = (id) => {
  ElMessageBox.confirm('确定删除该员工吗？', '提示', { type: 'warning' })
    .then(() => {
      employees.value = employees.value.filter(e => e.id !== id)
      ElMessage.success('已删除')
      // 如果删除后当前页没数据了，自动回退一页
      const maxPage = Math.ceil(filteredData.value.length / pageSize.value) || 1
      if (currentPage.value > maxPage) currentPage.value = maxPage
    })
    .catch(() => {})
}

// ---------- 弹窗逻辑 ----------
const dialogVisible = ref(false)
const dialogMode = ref('add')
const editRow = ref(null)

const openDialog = (mode, row = null) => {
  dialogMode.value = mode
  editRow.value = row
  dialogVisible.value = true
}

const handleSaved = (payload) => {
  if (dialogMode.value === 'add') {
    const newId = Math.max(...employees.value.map(e => e.id), 0) + 1
    employees.value.push({ ...payload, id: newId })
    ElMessage.success('新增成功')
  } else {
    const idx = employees.value.findIndex(e => e.id === editRow.value.id)
    if (idx > -1) employees.value[idx] = { ...editRow.value, ...payload }
    ElMessage.success('修改成功')
  }
  dialogVisible.value = false
}

// ---------- 标签颜色 ----------
const tagType = (p) => ({ A: 'success', B: 'warning', C: 'danger' }[p] || 'info')

// ---------- 退出 ----------
const handleLogout = () => {
  localStorage.removeItem('token')
  localStorage.removeItem('role')
  router.push('/login')
}
</script>