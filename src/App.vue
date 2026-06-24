<script setup>
import { ref, computed, watch } from 'vue'
import TodoInput from './components/TodoInput.vue'
import TodoItem from './components/TodoItem.vue'
import TodoFilter from './components/TodoFilter.vue'
import TodoStats from './components/TodoStats.vue'

// ---- State ----
const todos = ref(loadTodos())
const currentFilter = ref('all')
const currentCategory = ref('all')
const isDark = ref(localStorage.getItem('todo-theme') === 'dark')
const searchQuery = ref('')

// ---- Persistence ----
function loadTodos() {
  try {
    const data = localStorage.getItem('todo-list')
    return data ? JSON.parse(data) : []
  } catch { return [] }
}

watch(todos, (val) => {
  localStorage.setItem('todo-list', JSON.stringify(val))
}, { deep: true })

watch(isDark, (val) => {
  localStorage.setItem('todo-theme', val ? 'dark' : 'light')
  document.documentElement.classList.toggle('dark', val)
}, { immediate: true })

// ---- Actions ----
let nextId = computed(() => Math.max(0, ...todos.value.map(t => t.id)) + 1)

function addTodo({ text, category,dueDate}) {
  todos.value.push({
    id: nextId.value,
    text,
    category,
    dueDate,
    done: false,
    createdAt: Date.now()
  })
}

function toggleTodo(id) {
  const todo = todos.value.find(t => t.id === id)
  if (todo) todo.done = !todo.done
}

function deleteTodo(id) {
  todos.value = todos.value.filter(t => t.id !== id)
}

// ---- 拖拽排序 ----
function onReorder({ fromId, toId }) {
  const list = [...todos.value]
  const fromIndex = list.findIndex(t => t.id === fromId)
  const toIndex = list.findIndex(t => t.id === toId)
  if (fromIndex === -1 || toIndex === -1) return

  const [moved] = list.splice(fromIndex, 1)
  list.splice(toIndex, 0, moved)
  todos.value = list
}

// ---- Computed ----
const filteredTodos = computed(() => {
  let list = todos.value

  if (currentCategory.value !== 'all') {
    list = list.filter(t => t.category === currentCategory.value)
  }
  if (currentFilter.value === 'active') {
    list = list.filter(t => !t.done)
  } else if (currentFilter.value === 'completed') {
    list = list.filter(t => t.done)
  } else if (currentFilter.value === 'overdue') {
    const today = new Date(new Date().toDateString())
    list = list.filter(t => !t.done && t.dueDate && new Date(t.dueDate) < today)
  }
  if (searchQuery.value.trim()) {
    list = list.filter(t => t.text.includes(searchQuery.value.trim()))
  }
  return list
})
</script>

<template>
  <button class="theme-toggle" @click="isDark = !isDark">
    {{ isDark ? '☀️' : '🌙' }}
  </button>

  <div class="app-header">
    <h1>📋 Todo 任务管理</h1>
    <p>管理你的工作、学习和生活</p>
  </div>

  <TodoInput @add="addTodo" />
  <TodoFilter
    v-model:currentFilter="currentFilter"
    v-model:currentCategory="currentCategory"
  />

  <div class="card" v-if="todos.length > 0">
    <input
      v-model="searchQuery"
      type="text"
      placeholder="🔍 搜索任务..."
      class="search-input"
    />
  </div>

  <TodoStats :todos="todos" />

  <div class="card">
    <ul class="todo-list">
      <li v-if="filteredTodos.length === 0" class="empty-tip">
        {{ todos.length === 0 ? '还没有任务，输入一个开始吧 🚀' : '没有匹配的任务' }}
      </li>
      <TodoItem
        v-for="todo in filteredTodos"
        :key="todo.id"
        :todo="todo"
        @toggle="toggleTodo"
        @delete="deleteTodo"
        @reorder="onReorder"
      />
    </ul>
  </div>
</template>

