<template>
  <div class="container">
    <Header title="Task Tracker" @button-clicked="toggleAddTaskForm"/>
    <AddTask v-if="showForm" @form-submit="addTask"/>
    <Tasks @delete-task="deleteTask" @toggle-reminder="toggleReminder" v-bind:tasks="tasks" />
  </div>
</template>

<script>
import Header from './components/Header'
import AddTask from './components/AddTask'
import Tasks from './components/Tasks'

export default {
  name: 'App',
  components: {
    Header,
    AddTask,
    Tasks,
  },
  data() {
    return {
      tasks: [],
      showForm: false
    }
  },
  methods: {
    async fetchTasks() {
      const res = await fetch('api/tasks')
      return res.json()
    },
    async addTask(task) {
      const res = await fetch('api/tasks', {
        method: "POST",
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(task)
      })
      if (res.status == '201') {
        const data = await res.json()
        this.tasks = [...this.tasks, data]
        this.showForm = false
      } else {
        console.log('Something went wrong while adding a task')
      }
    },
    async deleteTask(id) {
      const res = await fetch(`api/tasks/${id}`, {
        method: "DELETE",
        headers: {
          'Content-Type': 'application/json'
        }
      })
      if (res.status == '200') {
        this.tasks = this.tasks.filter((task) => task.id != id)
      } else {
        console.log('Something went wrong while deleting a task')
      }
    },
    async toggleReminder(id) {
      const task = await (await fetch(`api/tasks/${id}`)).json()
      console.log(task)
      const res = await fetch(`api/tasks/${id}`, {
        method: "PUT",
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({...task, reminder: !task.reminder})
      })

      this.tasks = this.tasks.map((task) => {
        if (task.id === id) {
          return {...task, reminder: !task.reminder}
        } else {
          return task
        }
      });
    },
    toggleAddTaskForm() {
      this.showForm = !this.showForm
    }
  },
  async created() {
    this.tasks = await this.fetchTasks()
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Poppins', sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
