<template>
    <div class="min-h-screen bg-gradient-to-r from-purple-800 via-purple-600 to-blue-500 flex items-center justify-center">
      <div class="flex-col sm:w-10/12 lg:w-1/2 p-8 bg-black bg-opacity-50 rounded-lg shadow-lg">
        <h1 class="text-4xl font-bold text-center text-white tracking-widest mb-8">TODO</h1>
        <div class="flex mb-6">
          <input
            v-model="newTask"
            @keyup.enter="addTask"
            placeholder="Create a new todo..."
            class="flex-1 p-4 bg-gray-800 text-white placeholder-gray-500 rounded-l-lg focus:outline-none focus:ring-2 focus:ring-purple-400"
          />
          <button
            @click="addTask"
            class="bg-purple-500 text-white p-4 rounded-r-lg hover:bg-purple-600 transition"
          >
            +
          </button>
        </div>
  
        <ul class="space-y-3 text-white">
          <li
            v-for="(task, index) in filteredTasks"
            :key="index"
            class="flex justify-between items-center p-4 bg-gray-800 rounded-lg shadow-sm"
          >
            <div class="flex items-center space-x-3">
              <input
                type="checkbox"
                v-model="task.completed"
                @change="saveTasks"
                class="h-5 w-5 text-purple-500 border-gray-300 rounded focus:ring-purple-400 focus:outline-none"
              />
              <span
                :class="{
                  'line-through text-gray-500': task.completed,
                  'text-white': !task.completed,
                }"
                class="text-lg"
              >
                {{ task.text }}
              </span>
            </div>
            <div class="flex space-x-3">
              <button
                @click="editTask(index)"
                class="text-purple-500 hover:text-purple-700 transition"
              >
                ✎
              </button>
              <button
                @click="removeTask(index)"
                class="text-red-500 hover:text-red-700 transition"
              >
                🗑
              </button>
            </div>
          </li>
        </ul>
  
        <div class="flex justify-between items-center mt-6 text-sm text-gray-400">
          <span>{{ remainingTasks }} items left</span>
          <div class="hidden sm:flex space-x-4">
            <button
              @click="filterTasks('all')"
              class="hover:text-white"
              :class="{'text-white': filter === 'all'}"
            >
              All
            </button>
            <button
              @click="filterTasks('active')"
              class="hover:text-white"
              :class="{'text-white': filter === 'active'}"
            >
              Active
            </button>
            <button
              @click="filterTasks('completed')"
              class="hover:text-white"
              :class="{'text-white': filter === 'completed'}"
            >
              Completed
            </button>
          </div>
          <button @click="clearCompleted" class="hover:text-white">
            Clear Completed
          </button>
        </div>
        <Footer></Footer>

      </div>
    </div>

  </template>
  
  <script setup>
  import { ref, computed, onMounted, watch } from 'vue'
  import Footer from './FooterSection.vue'
  
  const tasks = ref([])
  const newTask = ref('')
  const editingIndex = ref(null)
  const filter = ref('all')
  
  onMounted(() => {
    const savedTasks = JSON.parse(localStorage.getItem('tasks'))
    if (savedTasks) {
      tasks.value = savedTasks
    }
  })
  
  watch(tasks, (newTasks) => {
    localStorage.setItem('tasks', JSON.stringify(newTasks))
  }, { deep: true })
  
  const addTask = () => {
    if (newTask.value.trim() === '') return
  
    if (editingIndex.value !== null) {
      tasks.value[editingIndex.value].text = newTask.value
      editingIndex.value = null
    } else {
      tasks.value.push({ text: newTask.value, completed: false })
    }
    
    newTask.value = ''
    saveTasks()
  }
  
  const removeTask = (index) => {
    tasks.value.splice(index, 1)
    saveTasks()
  }
  
  const editTask = (index) => {
    newTask.value = tasks.value[index].text
    editingIndex.value = index
  }
  
  const saveTasks = () => {
    localStorage.setItem('tasks', JSON.stringify(tasks.value))
  }
  
  const remainingTasks = computed(() => {
    return tasks.value.filter(task => !task.completed).length
  })
  
  const filteredTasks = computed(() => {
    if (filter.value === 'all') {
      return tasks.value
    } else if (filter.value === 'active') {
      return tasks.value.filter(task => !task.completed)
    } else if (filter.value === 'completed') {
      return tasks.value.filter(task => task.completed)
    }
    return tasks.value
  })
  
  const filterTasks = (status) => {
    filter.value = status
  }
  
  const clearCompleted = () => {
    tasks.value = tasks.value.filter(task => !task.completed)
    saveTasks()
  }
  </script>
  
  <style scoped>
  .line-through {
    text-decoration: line-through;
  }
  </style>
  