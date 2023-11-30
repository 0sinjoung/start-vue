<!--
An example of creating a reusable grid component and using it with external data.
-->

<script setup>
import { ref, computed } from 'vue'
const filterButtons = ['All', 'Completed', 'Active' ];
// (1) state
const todos = ref([])
const currentViewType = ref('All')

const filterByButtons = {
  All: (todos) => todos,
  Completed: (todos) => todos.filter((item) => item.completed),
  Active: (todos) => todos.filter((item) => !item.completed),
}

// (2) driven
const filteredTodos = computed(() => filterByButtons[currentViewType.value](todos.value));


// (3) handle
const addTodo = e => {
  const todoTitle = e.target.value.trim()

  if (todoTitle) {
    todos.value.push({
      id: Date.now(),
      title: todoTitle,
      completed: false
    })

    e.target.value = ''
  }
}

const removeTodo = (id) => {
  todos.value = todos.value.filter(item => item.id !== id)
}

</script>

<template>
  <h1>Todos</h1>  
  <div>
    <button v-for="button of filterButtons" @click="currentViewType = button" :key="button">{{ button }}</button>
  </div>
  <input type="text" name="addTodo" id="addTodo" autofocus placeholder="Add todos..." @keyup.enter="addTodo">
  <div>
    <h2>lists</h2>
    <ul>
      <li v-for="todo of filteredTodos" :key="todo.id">
        <input type="checkbox" name="title" id="id" v-model="todo.completed">
        <p :class="{completed: todo.completed}">{{ todo.title }}</p>
        <button @click="removeTodo(todo.id)">X</button>
      </li>
    </ul>
  </div>
</template>


<style>
.completed {
  text-decoration: line-through;
}
</style>