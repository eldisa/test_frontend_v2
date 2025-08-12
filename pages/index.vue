<template>
  <div
    id="app"
    class="flex flex-col items-center justify-center min-h-screen bg-gray-900 p-8 text-white"
  >
    <div class="w-full max-w-[600px] p-6 bg-gray-800 rounded-xl shadow-lg">
      <h1 class="text-2xl font-bold mb-6 text-center text-white">操作</h1>

      <form @submit.prevent="handleSubmit" class="space-y-6">
        <!-- 姓名輸入框 -->
        <ETextField
          v-model:value="userName"
          label="名字"
          placeholder="請輸入您的姓名"
          :errorMessage="nameError"
        />

        <!-- 年齡輸入框 -->
        <ETextField
          v-model:value="userAge"
          label="年齡"
          placeholder="請輸入您的年齡"
          type="number"
          :errorMessage="ageError"
        />

        <div class="flex justify-end space-x-4">
          <EBtn type="button" color="success" v-if="editingUserId !== null" @click="handleUpdate">
            修改
          </EBtn>
          <EBtn type="submit" color="warn" v-else> 新增 </EBtn>
          <EBtn type="button" color="error" v-if="editingUserId !== null" @click="cancelEdit">
            取消
          </EBtn>
        </div>
      </form>
    </div>

    <!-- User List Table -->
    <div class="w-full max-w-[600px] mt-8 p-6 bg-gray-800 rounded-xl shadow-lg">
      <h2 class="text-xl font-bold mb-4 text-center">使用者資料</h2>
      <table class="w-full text-center table-auto">
        <thead>
          <tr class="text-gray-400 border-b border-gray-700">
            <th class="py-2 px-4 w-1/12">#</th>
            <th class="py-2 px-4 w-5/12">名字</th>
            <th class="py-2 px-4 w-2/12">年齡</th>
            <th class="py-2 px-4 w-4/12">操作</th>
          </tr>
        </thead>
        <tbody>
          <!-- 顯示載入狀態 -->
          <tr v-if="isLoading">
            <td colspan="4" class="py-4 text-center text-gray-500">載入中...</td>
          </tr>
          <!-- 顯示使用者清單 -->
          <tr
            v-else
            v-for="(user, index) in users"
            :key="user.id"
            class="border-b border-gray-700 last:border-b-0"
          >
            <td class="py-3 px-4 w-2/12">{{ index + 1 }}</td>
            <td class="py-3 px-4 w-4/12">{{ user.name }}</td>
            <td class="py-3 px-4 w-2/12">{{ user.age }}</td>
            <td class="py-3 px-4 flex justify-end space-x-2">
              <EBtn color="success" @click="handleEdit(user)"> 修改 </EBtn>
              <EBtn color="error" @click="handleDelete(user)"> 刪除 </EBtn>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import ETextField from '~/components/ETextField.vue'
import EBtn from '~/components/EBtn.vue'

interface User {
  id: number
  name: string
  age: number
}

const API_URL = 'https://3559.wu.elitepro.ltd/api/user'

const userName = ref('')
const userAge = ref<number | string>('')
const nameError = ref('')
const ageError = ref('')

// 處理載入狀態
const isLoading = ref(true)

// 存放使用者清單
const users = ref<User[]>([])
const editingUserId = ref<number | null>(null)

// --- Validation Logic ---
const validateForm = () => {
  nameError.value = ''
  ageError.value = ''
  let isValid = true

  if (!userName.value) {
    nameError.value = '姓名是必填的！'
    isValid = false
  }
  if (!userAge.value) {
    ageError.value = '年齡是必填的！'
    isValid = false
  } else if (isNaN(Number(userAge.value))) {
    ageError.value = '年齡必須為數字！'
    isValid = false
  }
  return isValid
}

// --- API Calls (Axios) ---
// --- CRUD Operations ---

const fetchUsers = async () => {
  isLoading.value = true
  try {
    const response = await axios.get(API_URL)
    users.value = response.data.data || []
  } catch (error) {
    console.error('取得使用者清單失敗:', error)
  } finally {
    isLoading.value = false
  }
}

const handleAdd = async () => {
  if (!validateForm()) return

  try {
    const response = await axios.post(API_URL, {
      name: userName.value,
      age: Number(userAge.value),
    })
    // 直接更新本地資料，避免重新載入整個表格
    if (response.data.code === 200) {
      users.value.push({
        id: users.value.length + 1,
        name: userName.value,
        age: Number(userAge.value),
      })
    }

    resetForm()
    alert(`新增使用者成功！`)
  } catch (error) {
    console.error('新增使用者失敗:', error)
    alert('新增使用者失敗！')
  }
}

const handleUpdate = async () => {
  if (!validateForm()) return
  if (editingUserId.value === null) return

  try {
    const response = await axios.put(API_URL, {
      id: editingUserId.value,
      name: userName.value,
      age: Number(userAge.value),
    })
    // 直接更新本地資料，避免重新載入整個表格
    if (response.data.code === 200) {
      const index = users.value.findIndex((user) => user.id === editingUserId.value)
      if (index !== -1) {
        users.value[index] = {
          ...users.value[index],
          name: userName.value,
          age: Number(userAge.value),
        }
      }
    }

    alert(`更新使用者成功！`)
  } catch (error) {
    console.error('更新使用者失敗:', error)
    alert('更新使用者失敗！')
  }
}

const handleDelete = async (userToDelete: User) => {
  const isConfirmed = window.confirm(`確定要刪除使用者 ${userToDelete.name} 嗎？`)
  if (!isConfirmed) return

  try {
    const response = await axios.delete(API_URL, {
      data: { id: userToDelete.id },
    })
    // 直接更新本地資料，避免重新載入整個表格
    if (response.data.code === 200) {
      users.value = users.value.filter((user) => user.id !== userToDelete.id)
    }

    alert(`刪除使用者成功！`)
  } catch (error) {
    console.error('刪除使用者失敗:', error)
    alert('刪除使用者失敗！')
  }
}

const handleEdit = (user: User) => {
  editingUserId.value = user.id
  userName.value = user.name
  userAge.value = user.age
}

const cancelEdit = () => {
  resetForm()
}

const resetForm = () => {
  userName.value = ''
  userAge.value = ''
  nameError.value = ''
  ageError.value = ''
  editingUserId.value = null
}

const handleSubmit = () => {
  if (editingUserId.value === null) {
    handleAdd()
  } else {
    handleUpdate()
  }
}

onMounted(() => {
  fetchUsers()
})
</script>

<style scoped>
#app {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
}
</style>
