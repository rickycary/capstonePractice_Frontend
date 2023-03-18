<!-- Javascript -->
<script setup>
import { ref, onBeforeMount } from 'vue';
import Form from "./components/Form.vue"

const url = "https://capstonepractice-backend.onrender.com/todo"
const todos = ref([])
const showForm = ref(false)
const reminder = ref("")
const completed = ref(false)
const _id = ref(null)
const action = ref("create")

const getTodos = async () => {
  const response = await fetch(url)
  const data = await response.json()
  todos.value = data
}

const toggleForm = () => {
  showForm.value = !showForm.value
}

function resetForm(){
  _id.value = ""
  reminder.value = ""
  completed.value = false
  action.value = "create"
}

const createTodo = async (newTodo) => {
  await fetch(url, {
    method: "post",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(newTodo)
  })
  getTodos()
  toggleForm()
  resetForm()
}

const updateTodo = async (todo) => {
  await fetch(url + `/${todo._id}`, {
    method: "put",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(todo)
  })

  getTodos()
  toggleForm()
  resetForm()
}

const deleteTodo = async (todo) => {
  await fetch(url + `/${todo._id}`, {
    method: "delete"
  })
  getTodos()
}

const selectTodoToEdit = (todo) => {
  _id.value = todo._id
  reminder.value = todo.reminder
  completed.value = todo.completed
  action.value = "update"
  toggleForm()
}

onBeforeMount(() => {getTodos()})
</script>


<!-- HTML -->
<template>
  <div>
    <h1>Ricky's Todo App</h1>
    <button @click="toggleForm">Create a Todo</button>
    <ul>
      <li v-for="todo of todos" v-bind:key="todo._id">
        <h1 @click="selectTodoToEdit(todo)">{{ todo.reminder }} - {{ todo.completed }}</h1>
        <button @click="deleteTodo(todo)">Delete</button>
      </li>
    </ul>
    <Form v-if="showForm" v-bind:reminder="reminder" v-bind:completed="completed" v-bind:_id="_id" v-bind:action="action" v-bind:createTodo="createTodo" v-bind:updateTodo="updateTodo" />
  </div>
</template>



<style scoped>
</style>
