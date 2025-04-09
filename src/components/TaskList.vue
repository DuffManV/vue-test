<template>
  <div class="task-list">
    <h1>Список задач</h1>
    <div v-if="loading">Загрузка...</div>
    <div v-else>
      <div v-for="task in tasks" :key="task.id" class="task-item">
        <input
          type="checkbox"
          :checked="task.done"
          @change="toggleTask(task.id)"
        />
        <span :class="{ 'task-done': task.done }">{{ task.title }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TaskList',
  data() {
    return {
      tasks: [],
      loading: true
    }
  },
  methods: {
    toggleTask(id) {
      this.tasks = this.tasks.map(task => 
        task.id === id ? { ...task, done: !task.done } : task
      )
      this.saveTasks()
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    async loadTasks() {
      try {
        const savedTasks = localStorage.getItem('tasks')
        if (savedTasks) {
          this.tasks = JSON.parse(savedTasks)
        } else {
          const response = await fetch('/tasks.json')
          this.tasks = await response.json()
        }
      } catch (error) {
        console.error('Ошибка загрузки задач:', error)
        this.tasks = []
      } finally {
        this.loading = false
      }
    }
  },
  created() {
    this.loadTasks()
  }
}
</script>

<style>
.task-list {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.task-item {
  display: flex;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #eee;
}

.task-item input {
  margin-right: 10px;
}

.task-done {
  text-decoration: line-through;
  color: #888;
}

h1 {
  color: #333;
}
</style>