<template>
  <el-dialog
    :model-value="modelValue"
    :title="mode === 'add' ? '新增员工' : '编辑员工'"
    width="480px"
    @update:model-value="$emit('update:modelValue', $event)"
    @close="handleClose"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="80px"
    >
      <el-form-item label="姓名" prop="name">
        <el-input v-model="form.name" placeholder="请输入姓名" />
      </el-form-item>
      <el-form-item label="部门" prop="department">
        <el-select v-model="form.department" placeholder="请选择部门" style="width: 100%;">
          <el-option label="技术部" value="技术部" />
          <el-option label="产品部" value="产品部" />
          <el-option label="运营部" value="运营部" />
          <el-option label="人事部" value="人事部" />
          <el-option label="财务部" value="财务部" />
        </el-select>
      </el-form-item>
      <el-form-item label="绩效等级" prop="performance">
        <el-select v-model="form.performance" placeholder="请选择绩效等级" style="width: 100%;">
          <el-option label="A" value="A" />
          <el-option label="B" value="B" />
          <el-option label="C" value="C" />
        </el-select>
      </el-form-item>
    </el-form>

    <template #footer>
      <el-button @click="$emit('update:modelValue', false)">取消</el-button>
      <el-button type="primary" @click="handleSubmit">确定</el-button>
    </template>
  </el-dialog>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
import { ElMessage } from 'element-plus'

const props = defineProps({
  modelValue: Boolean,
  mode: { type: String, default: 'add' },
  editRow: { type: Object, default: null },
})
const emit = defineEmits(['update:modelValue', 'saved'])

const formRef = ref(null)
const form = reactive({ name: '', department: '', performance: '' })

const rules = {
  name: [{ required: true, message: '姓名不能为空', trigger: 'blur' }],
  department: [{ required: true, message: '请选择部门', trigger: 'change' }],
  performance: [{ required: true, message: '请选择绩效等级', trigger: 'change' }],
}

// 打开弹窗时，区分新增/编辑，做数据回填或重置
watch(
  () => props.modelValue,
  (val) => {
    if (val) {
      if (props.mode === 'edit' && props.editRow) {
        form.name = props.editRow.name
        form.department = props.editRow.department
        form.performance = props.editRow.performance
      } else {
        form.name = ''
        form.department = ''
        form.performance = ''
      }
      formRef.value?.clearValidate() // 清理上一次的红字报错
    }
  }
)

// 提交表单
const handleSubmit = async () => {
  try {
    await formRef.value.validate() // 校验通过才会往下走
    emit('saved', { name: form.name, department: form.department, performance: form.performance })
    emit('update:modelValue', false) // 关闭弹窗
  } catch {
    ElMessage.warning('请完善必填项')
  }
}

const handleClose = () => {
  formRef.value?.clearValidate()
}
</script>