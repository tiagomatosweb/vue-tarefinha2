<template>
  <div class="container" style="max-width: 800px;">
    <h1 class="text-center my-4">Tarefinha 2</h1>
    
    <!-- Stats -->
    <TaskStats :tasks="tasks" />
    
    <!-- Add new task -->
    <TaskInput @add-task="addTask" />

    <!-- Filters -->
    <TaskFilters 
      v-if="tasks.length"
      @search="onSearch"
      @status="onStatus"
    />

    <!-- Tasks -->
    <TaskList 
      :tasks="filteredTasks" 
      @delete="deleteTask"
    />

    <!-- Empty state -->
     <TaskEmptyState 
      v-if="!filteredTasks.length"
      :message="emptyStateMessage" 
    />
  </div>
</template>

<script setup>
import {ref, computed} from 'vue'
import TaskStats from './components/TaskStats.vue'
import TaskInput from './components/TaskInput.vue'
import TaskFilters from './components/TaskFilters.vue'
import TaskList from './components/TaskList.vue'
import TaskEmptyState from './components/TaskEmptyState.vue'

const tasks = ref([])
const filterSearch = ref('')
const filterStatus = ref('')
const filteredTasks = computed(() => {
  let output = tasks.value
  if (filterSearch.value) {
    const search = filterSearch.value.toLowerCase()

    output = output.filter(o => o.name.toLowerCase().includes(search))
  }

  if (filterStatus.value === 'pending') {
    return output.filter(o => o.completed === false)
  } else if (filterStatus.value === 'completed')  {
    return output.filter(o => o.completed === true)
  }

  return output;
});

const onSearch = (search) => {
  filterSearch.value = search
}
const onStatus = (status) => {
  filterStatus.value = status
}

const addTask = (task) => {
  if (!task) { return }

  tasks.value.push({
    id: Date.now(),
    name: task,
    completed: false,
    state: 'show' // edit, delete
  })
}

const deleteTask = (task) => {
  const index = tasks.value.findIndex(o => o.id === task.id)
  if (index !== -1) {
    tasks.value.splice(index, 1)
  }
}

const emptyStateMessage = computed(() => {
  let output = 'Nenhuma tarefa cadastrada.'

  if (filterSearch.value || filterStatus.value) {
    return 'Nenhum resultado para este filtro.'
  }

  return output;
})
</script>
