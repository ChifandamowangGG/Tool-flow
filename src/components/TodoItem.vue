<script setup>
const props = defineProps({
  todo: { type: Object, required: true }
})
const emit = defineEmits(['toggle', 'delete', 'reorder'])

function onDragStart(event) {
  event.dataTransfer.setData('text/plain', props.todo.id)
  event.target.classList.add('dragging')
  sessionStorage.setItem('drag-todo-id', props.todo.id)
}

function onDragEnd(event) {
  event.target.classList.remove('dragging')
}

function onDrop(event) {
  const dragId = Number(sessionStorage.getItem('drag-todo-id'))
  if (dragId && dragId !== props.todo.id) {
    emit('reorder', { fromId: dragId, toId: props.todo.id })
  }
}

function getDateInfo(dateStr) {
  if (!dateStr) return { text: '', overdue: false }
  const now = new Date()
  const due = new Date(dateStr)
  const today = new Date(now.getFullYear(), now.getMonth(), now.getDate())
  const dueDay = new Date(due.getFullYear(), due.getMonth(), due.getDate())
  const diffTime = dueDay.getTime() - today.getTime()
  const diffDays = Math.round(diffTime / (1000 * 60 * 60 * 24))

  let countdownText = ''
  let isOverdue = false
  if (diffDays > 0) {
    countdownText = `剩余 ${diffDays} 天`
  } else if (diffDays === 0) {
    countdownText = "今天截止！"
  } else {
    countdownText = `已过期 ${Math.abs(diffDays)} 天`
    isOverdue = true
  }
  return {
    text: `${due.getMonth() + 1}月${due.getDate()}日 · ${countdownText}`,
    overdue: isOverdue
  }
}

const categoryMap = { work: "工作", study: "学习", life: "生活" }
</script>

<template>
  <li
    class="todo-item"
    :class="{ completed: todo.done }"
    draggable="true"
    @dragstart="onDragStart"
    @dragover.prevent
    @drop="onDrop"
    @dragend="onDragEnd"
  >
    <input
      type="checkbox"
      :checked="todo.done"
      class="todo-checkbox"
      @change="emit('toggle', todo.id)"
    />
    <div style="flex:1">
      <span class="todo-text">{{ todo.text }}</span>
      <span
        v-if="todo.dueDate"
        class="todo-date"
        :class="{ overdue: getDateInfo(todo.dueDate).overdue && !todo.done }"
      >
        📅 {{ getDateInfo(todo.dueDate).text }}
      </span>
    </div>
    <span class="todo-category" :class="todo.category">
      {{ categoryMap[todo.category] || todo.category }}
    </span>
    <button class="todo-delete" @click="emit('delete', todo.id)" title="删除">✕</button>
  </li>
</template>
