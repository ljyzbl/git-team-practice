<template>
  <div>
    <el-button type="primary" @click="openDialog">新增用户</el-button>

    <el-dialog v-model="dialogVisible" title="新增用户" width="500px" destroy-on-close>

      <el-form ref="formRef" :model="form" :rules="rules" label-width="80px">
        <!-- ① 姓名：必填 -->
         <el-form-item label="姓名" prop="name">
          <el-input v-model="form.name" placeholder="请输入姓名" />
        </el-form-item>

        <el-form-item label="性别" prop="gender">
            <el-radio-group v-model="form.gender">
                <el-radio value="1">女</el-radio>
                <el-radio value="0">男</el-radio>
            </el-radio-group>
        </el-form-item>

        <el-form-item label="手机号" prop="phone">
          <el-input v-model="form.phone" placeholder="请输入手机号" />
        </el-form-item>

        <el-form-item label="邮箱" prop="email">
          <el-input v-model="form.email" placeholder="请输入邮箱" />
        </el-form-item>

        <!-- ④ 状态：el-select，启用/禁用，默认启用 -->
         <el-form-item label="状态" prop="status">
             <el-select v-model="form.status" style="width: 100%">
                <el-option label="启用" value="1" />
                <el-option label="禁用" value="0" />
             </el-select>
         </el-form-item>
        <!-- ⑤ 备注：el-input type="textarea" -->
         <el-form-item label="备注" prop="remark">
             <el-input v-model="form.remark" type="textarea" :rows="3" />
         </el-form-item>
      </el-form>

      <template #footer>
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit">确定</el-button>
      </template>

    </el-dialog>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'

const dialogVisible = ref(false) //弹窗开关
const formRef = ref(null)
const form = reactive({
  name: '',
  gender:'',
  phone: '',
  email: '',
  status: '1',   // 默认启用
  remark: ''
})

const rules = {
  // 在这里写校验规则
  name: [
    { required: true, message: '请输入姓名', trigger: 'blur' }
  ],
  gender:[
    {required: true, message: '请选择性别', trigger: 'change'}
  ],
  phone: [
    { required: true, message: '请输入手机号', trigger: 'blur' },
    { pattern: /^1[3-9]\d{9}$/, message: '手机号格式不正确', trigger: 'blur' }
  ],
  email: [
    { type: 'email', message: '邮箱格式不正确', trigger: 'blur' }
  ],
  status: [
    { required: true, message: '请选择状态', trigger: 'change' }
  ]
}

const openDialog = () => {
  dialogVisible.value = true
}

const handleSubmit = () => {
  // 校验通过：console.log 表单数据 + 关闭弹窗
  formRef.value.validate((valid) => {
    if (valid) {
      console.log('新增用户：', { ...form })
      dialogVisible.value = false
      formRef.value?.resetFields() // 关闭时重置
    }
  })
}
</script>
