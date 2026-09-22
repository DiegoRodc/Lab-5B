<template>
  <div class="home">
    <v-card class="mx-auto" max-width="900" v-if="fetched">
      <NewTaskForm
        :form="form"
        :addTask="createTask"
      />

      <v-divider></v-divider>

      <v-list subheader two-line flat>
        <v-list-subheader>
          Tasks:
        </v-list-subheader>

        <TaskList
          :tasks="tasks"
          :updateTask="updateTask"
          :deleteTask="deleteTask"
        />
      </v-list>
    </v-card>
  </div>
</template>

<script>
import { getCurrentDate, formatDate } from '@/util'
import TaskList from '@/components/TaskList.vue'
import NewTaskForm from '@/components/NewTaskForm.vue'

export default {
  name: 'HomeView',
  components: {
    TaskList,
    NewTaskForm,
  },
  props: {
    user: {
      type: Object,
      required: true,
    },
  },
  data () {
    return {
      fetched: false,
      tasks: [],
      form: {
        Text: '',
        Date: getCurrentDate(),
        Done: false,
      },
    }
  },
  mounted () {
    this.readTasks()
  },
methods: {
  createTask (form) {
    const payload = {
      text: form.Text,
      date: form.Date,
    }

    console.log('Creating task with payload:', payload)

    fetch(`${process.env.VUE_APP_API_ORIGIN}/api/v1/tasks`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify(payload),
    })
      .then(response => {
        if (!response.ok) {
          console.error('Create task failed, status:', response.status)
          return
        }
        this.readTasks()
        this.form.Text = ''
        this.form.Date = getCurrentDate()
        this.form.Done = false
      })
      .catch(err => {
        console.error('Network error creating task:', err)
      })
  },

  readTasks () {
    fetch(`${process.env.VUE_APP_API_ORIGIN}/api/v1/tasks`, {
      credentials: 'include',
    })
      .then(response => {
        if (!response.ok) {
          console.error('Read tasks failed, status:', response.status)
          return
        }
        return response.json()
      })
      .then(data => {
        if (data) {
          this.tasks = data
          this.fetched = true
        }
      })
      .catch(err => {
        console.error('Network error reading tasks:', err)
      })
  },

  updateTask (task) {
    const payload = {
      done: !task.done,       // 🔥 backend uses "done"
    }

    fetch(`${process.env.VUE_APP_API_ORIGIN}/api/v1/tasks/${task._id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify(payload),
    })
      .then(response => {
        if (!response.ok) {
          console.error('Update task failed, status:', response.status)
          return
        }
        this.readTasks()
      })
      .catch(err => {
        console.error('Network error updating task:', err)
      })
  },

  deleteTask (task) {
    fetch(`${process.env.VUE_APP_API_ORIGIN}/api/v1/tasks/${task._id}`, {
      method: 'DELETE',
      credentials: 'include',
    })
      .then(response => {
        if (!response.ok) {
          console.error('Delete task failed, status:', response.status)
          return
        }
        this.readTasks()
      })
      .catch(err => {
        console.error('Network error deleting task:', err)
      })
  },
}
}
</script>

<style scoped>
.form {
  padding: 0 1rem;
}
</style>