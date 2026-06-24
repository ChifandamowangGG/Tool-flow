<script setup>
defineProps({
  currentFilter: { type: String, default: 'all' },
  currentCategory: { type: String, default: 'all' }
})
const emit = defineEmits(['update:currentFilter', 'update:currentCategory'])

const filters = [
  { value: 'all', label: '全部' },
  { value: 'active', label: '待办' },
  { value: 'overdue', label: '已逾期' },
  { value: 'completed', label: '已完成' }
]

const categories = [
  { value: 'all', label: '全部分类', color: null },
  { value: 'work', label: '工作', color: '#3498db' },
  { value: 'study', label: '学习', color: '#9b59b6' },
  { value: 'life', label: '生活', color: '#e67e22' }
]
</script>

<template>
  <div class="card">
    <div class="filter-bar">
      <button
        v-for="f in filters"
        :key="f.value"
        class="filter-btn"
        :class="{ active: currentFilter === f.value }"
        @click="emit('update:currentFilter', f.value)"
      >
        {{ f.label }}
      </button>
    </div>
    <div class="filter-bar" style="margin-bottom:0">
      <button
        v-for="c in categories"
        :key="c.value"
        class="filter-btn"
        :class="{ active: currentCategory === c.value }"
        :style="currentCategory === c.value && c.color ? { background: c.color, borderColor: c.color, color: '#fff' } : {}"
        @click="emit('update:currentCategory', c.value)"
      >
        {{ c.label }}
      </button>
    </div>
  </div>
</template>
