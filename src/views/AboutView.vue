<template>
  <el-form :model="form" inline>
    <!-- 在这里补输入框 -->
     <el-form-item label="姓名">
       <el-input  v-model="form.name" placeholder="请输入姓名"></el-input>
     </el-form-item>
    <!-- 在这里补下拉框 -->
     <el-form-item label="状态">
      <el-select  v-model="form.status" placeholder="请选择状态">
      <el-option label="启用" value="1">启用</el-option>
      <el-option label="禁用" value="0">禁用</el-option>
      <el-option el-option label="全部" value="">全部</el-option>
     </el-select>
     </el-form-item>
    
    
    <!-- 在这里补查询和重置按钮 -->
     <el-form-item>
      <el-button id="cx" @click="handleQuery">查询</el-button>
     <el-button id="cz" @click="handleReset">重置</el-button>
     </el-form-item>

  </el-form>
</template>

<script setup>
import { reactive } from 'vue'

const form = reactive({
  name: '',
  status: ''
})

// 在这里写查询方法
const handleQuery = () => {
  console.log('查询条件：', { ...form })
}

// 在这里写重置方法
const handleReset = () => {
  form.name = ''
  form.status = ''
}

</script>