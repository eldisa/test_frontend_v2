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
          <tr
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
import { ref, reactive } from 'vue'
import ETextField from '~/components/ETextField.vue'
import EBtn from '~/components/EBtn.vue'

interface User {
  id: number
  name: string
  age: number
}

const userName = ref('')
const userAge = ref<number | string>('')
const nameError = ref('')
const ageError = ref('')

// --- New state for handling CRUD operations ---
const users = ref<User[]>([
  { id: 1, name: '林建智', age: 16 },
  { id: 2, name: '吳政貞', age: 19 },
  { id: 3, name: '楊柏宏', age: 18 },
  { id: 4, name: '李建宇', age: 20 },
  { id: 5, name: '蘇名正', age: 15 },
])
const editingUserId = ref<number | null>(null)

// --- Validation Logic (re-organized) ---
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

// --- CRUD Operations ---
const handleAdd = () => {
  if (!validateForm()) return

  const newUser: User = {
    id: users.value.length + 1,
    name: userName.value,
    age: Number(userAge.value),
  }
  users.value.push(newUser)
  resetForm()
  alert(`提交成功！新增使用者: ${userName.value}`)
}

const handleUpdate = () => {
  if (!validateForm()) return
  if (editingUserId.value === null) return

  const userIndex = users.value.findIndex((user) => user.id === editingUserId.value)
  if (userIndex !== -1) {
    users.value[userIndex].name = userName.value
    users.value[userIndex].age = Number(userAge.value)
  }
  resetForm()
  alert(`提交成功！更新使用者: ${userName.value}`)
}

const handleDelete = (userToDelete: User) => {
  if (confirm(`確定要刪除使用者 ${userToDelete.name} 嗎？`)) {
    users.value = users.value.filter((user) => user.id !== userToDelete.id)
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
  handleAdd()
}
</script>

<style scoped>
#app {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
}
</style>
