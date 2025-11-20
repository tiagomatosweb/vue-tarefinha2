<template>
    <div class="input-group mb-3">
      <input 
        v-model="task"
        type="text" 
        placeholder="Adicionar uma nova tarefa..." 
        class="form-control"
        @keyup.enter="addTask"
      >
      <button 
        class="btn btn-success"
        @click="addTask"
      >Adicionar</button>
    </div>
</template>

<script setup>
import {ref} from 'vue'

const emit = defineEmits(['add-task'])
const task = ref('')

const addTask = () => {
    if (!task.value) return
    emit('add-task', task.value)
    task.value = ''
}
</script>