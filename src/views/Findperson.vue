<template>
  <div>
    <el-input
      v-model="keyword"
      placeholder="请输入姓名"
      clearable
    />

    <el-button @click="handleSearch">查询</el-button>
    <el-button @click="handleReset">重置</el-button>

    <!-- <p>当前页：{{ pageData.map(user => user.name).join('、') }}</p> -->

    <el-table :data="pageData" row-key="id" style="width: 100%">
      <!-- 在这里补姓名列和状态列 -->
       <el-table-column label="姓名" prop="name"></el-table-column>

       <el-table-column label="状态"  prop="status"></el-table-column>

       <el-table-column label="操作">
            <template #default="{ row }"> <!-- row表示拿到的这行的数据 -->

                <el-button type="danger" link @click="handleDelete(row.id)">删除</el-button>

                <el-button type="primary" link @click="opendialog(row)">编辑</el-button>

                <el-dialog v-model="editDialogVisible" title="编辑用户" width="500px">  
                    <!-- el-dialog 的显示由 v-model 控制，表单则通过 :model 关联数据对象 -->
                    <el-form :model="editForm" label-width="80px">

                        <el-form-item label="姓名">
                        <el-input v-model="editForm.name" />
                        </el-form-item>

                        <el-form-item label="状态">
                        <el-select v-model="editForm.status">
                            <el-option label="启用" value="启用" />
                            <el-option label="禁用" value="禁用" />
                        </el-select>
                        </el-form-item>

                    </el-form>

                    <template #footer> 
                        <!-- 用 #footer 插槽放自定义按钮 -->
                        <el-button @click="editDialogVisible = false">取消</el-button>
                        <el-button type="primary" @click="handleSave">确定</el-button>
                    </template>

                </el-dialog>
            </template>
            
        </el-table-column>

    </el-table>

    <el-pagination 
    v-model:current-page="currentPage"
    v-model:page-size="pageSize"
    :total="tableData.length" 
     layout="prev, pager, next, sizes, total">
    </el-pagination>
  </div>
</template>

<script setup>
import { computed,ref, watch,reactive  } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'

const keyword = ref('')
const users = ref([
  { id: 1, name: '张三', status: '启用' },
  { id: 2, name: '李四', status: '禁用' },
  { id: 3, name: '王小明', status: '启用' },
  { id: 4, name: '王小明', status: '启用' },
  { id: 5, name: '王小明', status: '启用' },
  { id: 6, name: '王小明', status: '启用' }
])
const tableData = ref([...users.value])

const currentPage = ref(1) // 当前页码
const pageSize = ref(2)    // 每页显示几条
const pageData = computed(() => {  //一页的数据
const start = (currentPage.value - 1) * pageSize.value
const end = start + pageSize.value
return tableData.value.slice(start, end)
})
watch(tableData, () => {
  const maxPage = Math.max(1, Math.ceil(tableData.value.length / pageSize.value))
  if (currentPage.value > maxPage) currentPage.value = maxPage
})

const editDialogVisible=ref(false)
const editForm=reactive({
    id: null,
    name: '',
    status: ''
    })




function handleSearch() {
  // 在这里按姓名筛选 users，并更新 tableData
//   console.log('关键词：', JSON.stringify(keyword.value))
   const text = keyword.value.trim()
    if (!text) {
    tableData.value = users.value
    currentPage.value = 1
    return
  }
   tableData.value = users.value.filter(user =>user.name.includes(text))
//    console.log('筛选结果：', tableData.value)
//    console.log('当前页姓名：', pageData.value.map(user => user.name))
    currentPage.value = 1  
}

function handleReset() {
  // 在这里清空 keyword，并恢复所有用户
  keyword.value = ''
  tableData.value = users.value
   currentPage.value = 1
}

function handleDelete(id){
    ElMessageBox.confirm(
    '确定删除这条用户记录吗？',
    '删除确认',
    {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
    }
    ).then(()=>{
        users.value = users.value.filter(user=>user.id != id)
        // 根据当前搜索词重新生成表格数据
        const text = keyword.value.trim()
        tableData.value = text? users.value.filter(user => user.name.includes(text)): users.value
        ElMessage.success('删除成功')
        }).catch(()=>{

        }) 
}

function opendialog(row){ 
Object.assign(editForm, row) 
// 把这一行复制到 editForm
editDialogVisible.value = true
}

function handleSave(){
    // 更新完整用户数据中 id 对应的那一项
  users.value = users.value.map(user => //map 会遍历用户列表：如果某行的 id 和正在编辑的 editForm.id 一样，就用表单里的姓名和状态生成更新后的用户；其他用户保持原样。
    user.id === editForm.id
      ? {
          ...user,
          name: editForm.name,
          status: editForm.status
        }
      : user
  )

  // 按当前搜索词重新筛选表格数据
  const text = keyword.value.trim()
  tableData.value = text? users.value.filter(user => user.name.includes(text)): users.value

  // 关闭弹窗
  editDialogVisible.value = false
}

//一个模拟接口,暂时用 users 模拟服务端保存的完整数据
// function mockFetchUsers({ page, pageSize, keyword }) {
//   return new Promise(resolve => {
//     setTimeout(() => {
//       // 1. 按 keyword 筛选 users.value
//       const text = keyword.value.trim()
//       const filteredUsers = text? users.value.filter(user => user.name.includes(text)): users.value
//       // 2. 算出当前页在筛选结果里的起止位置
//        const start = (page - 1) * pageSize
//         const end = start + pageSize
//       // 3. resolve({ list: 当前页数据, total: 筛选后的总数 })
//       resolve({
//         list:filteredUsers.slice(start, end),
//         total: filteredUsers.length
//       })

//     }, 500)
//   })
// }
</script>