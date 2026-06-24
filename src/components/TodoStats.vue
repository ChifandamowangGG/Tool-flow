<script setup>
import { computed } from 'vue'

const props = defineProps({
  todos: { type: Array, required: true }
})

const total = computed(() => props.todos.length)
const active = computed(() => props.todos.filter(t => !t.done).length)
const overdue = computed(() =>
  props.todos.filter(t => !t.done && t.dueDate && new Date(t.dueDate) < new Date(new Date().toDateString())).length
)
const completed = computed(() => props.todos.filter(t => t.done).length)
const completion = computed(() => total.value ? Math.round((completed.value / total.value) * 100) : 0)
</script>

<template>
  <div class="card" v-if="todos.length > 0">
    <div class="stats-grid">
      <div class="stat-item">
        <div class="stat-number">{{ total }}</div>
        <div class="stat-label">总任务</div>
      </div>
      <div class="stat-item">
        <div class="stat-number">{{ active }}</div>
        <div class="stat-label">待完成</div>
      </div>
      <div class="stat-item">
        <div class="stat-number">{{ completed }}</div>
        <div class="stat-label">已完成</div>
      </div>
      <div class="stat-item">
  <div class="stat-number" style="color:var(--danger)">{{ overdue }}</div>
  <div class="stat-label">已逾期</div>
</div>
      <div class="stat-item">
        <div class="stat-number">{{ completion }}%</div>
        <div class="stat-label">完成率</div>
      </div>
    </div>
    <div class="progress-bar">
      <div class="progress-fill" :style="{ width: completion + '%' }"></div>
    </div>
  </div>
</template>
