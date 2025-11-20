<template>
  <div class="container" style="max-width: 800px;">
    <h1 class="text-center my-4">Tarefinha</h1>
    
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
    <ul class="list-group">
      <li 
        v-for="task in filteredTasks"
        :key="task.id"
        class="list-group-item d-flex align-items-center gap-2"
      >
        <template v-if="task.state === 'show'">
          <input v-model="task.completed" type="checkbox" class="form-check-input">
          <span 
            class="flex-grow-1"
            :class="task.completed ? 'text-decoration-line-through text-muted' : null"
          >{{ task.name }}</span>
          <button class="btn btn-primary btn-sm" @click="editTask(task)">Editar</button>
          <button class="btn btn-danger btn-sm" @click="task.state = 'delete'">Excluir</button>
        </template>

        <template v-else-if="task.state === 'edit'">
          <input v-model="task._completed" type="checkbox" class="form-check-input">
          <input v-model="task._name" type="text" class="form-control form-control-sm">
          <button class="btn btn-success btn-sm" @click="commitTask(task)">Salvar</button>
          <button class="btn btn-secondary btn-sm" @click="task.state = 'show'">Cancelar</button>
        </template>

        <template v-else-if="task.state === 'delete'">
          <span class="flex-grow-1">
            <div class="fw-semibold">{{ task.name }}</div>
            <div class="text-muted small">Tem certeza que deseja remover?</div>
          </span>
          <button class="btn btn-danger btn-sm" @click="deleteTask(task)">Sim, excluir</button>
          <button class="btn btn-outline-secondary btn-sm" @click="task.state = 'show'">Cancelar</button>
        </template>
      </li>
    </ul>

    <!-- Empty state -->
    <div 
      v-if="!filteredTasks.length"
      class="card bg-light"
    >
      <div class="card-body text-center py-5">
        <p class="text-muted mb-0">
          {{ emptyStateMessage }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import {ref, computed} from 'vue'
import TaskStats from './components/TaskStats.vue'
import TaskInput from './components/TaskInput.vue'
import TaskFilters from './components/TaskFilters.vue'

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

const clearFilters = () => {
  filterSearch.value = ''
  filterStatus.value = ''
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

const editTask = (task) => {
  if (!Object.hasOwn(task, '_name')) {
    task._name = task.name
  }

  if (!Object.hasOwn(task, '_completed')) {
    task._completed = task.completed
  }
  
  task.state = 'edit'
}

const commitTask = (task) => {
  if (Object.hasOwn(task, '_name')) {
    task.name = task._name
  }

  if (Object.hasOwn(task, '_completed')) {
    task.completed = task._completed
  }

  task.state = 'show'
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
