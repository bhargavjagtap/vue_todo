<script setup>
import { ref, computed, onMounted, watch } from 'vue'

// give each todo a unique id
let id = 0
const pElementRef = ref(null)

onMounted(() => {
  setTimeout(() => {
    pElementRef.value.textContent = 'mounted!'
  },2000)
})

const newTodo = ref('')
const hideCompleted = ref(false);
const todoId = ref(1)
const todoData = ref(null)

async function fetchData() {
  todoData.value = null
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  todoData.value = await res.json()
}

fetchData()

watch(todoId, fetchData)

const todos = ref([
  { id: id++, text: 'Learn HTML' },
  { id: id++, text: 'Learn JavaScript' },
  { id: id++, text: 'Learn Vue' }
])

const filteredTodos = computed(() => {
  return hideCompleted.value
    ? todos.value.filter((t) => !t.done)
    : todos.value
})

function addTodo() {
  todos.value.push({ id: id++, text: newTodo.value })
  newTodo.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}
</script>

<template>
  <div class="todo">
    <form @submit.prevent="addTodo">
      <input v-model="newTodo">
      <button>Add Todo</button>    
    </form>
    <ul>
      <li v-for="todo in filteredTodos" :key="todo.id">
        <input type="checkbox" v-model="todo.done">
        <span :class="{done : todo.done}">
          {{ todo.text }}
        </span>
        <button @click="removeTodo(todo)">X</button>
      </li>
    </ul>
    <button @click="hideCompleted = !hideCompleted">
      {{ hideCompleted ? 'Show all' : "Hide completed" }}
    </button>
    <p ref="pElementRef">Hello</p>
  </div>
  <div class="todo">
    <button @click="todoId++">Fetch next todo</button>
    <p>Todo id: {{ todoId }}</p>
    <p v-if="!todoData">Loading...</p>
    <pre v-else>{{ todoData }}</pre>
  </div>
</template>

<style>
.done{
  text-decoration: line-through;
}
@media (min-width: 1024px) {
  .todo {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>