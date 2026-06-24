<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add'])
const text = ref('')
const category = ref('work')
const dueDate =ref('')

function handleAdd() {
  const trimmed = text.value.trim()
  if (!trimmed) return
  emit('add', { text: text.value, category: category.value, dueDate: dueDate.value })
  text.value = ''
}
</script>

<template>
  <div class="card">
    <div class="todo-input-row">
      <input
        v-model="text"
        type="text"
        placeholder="输入新任务，按回车添加..."
        @keyup.enter="handleAdd"
      />
      <select v-model="category" class="category-select">
        <option value="work">💼 工作</option>
        <option value="study">📚 学习</option>
        <option value="life">🏠 生活</option>
      </select>
      <input type="date" v-model="dueDate" class="category-select">
      <button class="btn btn-primary" @click="handleAdd">添加</button>
    </div>
  </div>
</template>
