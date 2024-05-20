<template>
  <div>
    <nav>
      <button @click="selectedComponent = 'todo'">Show Todos</button>
      <button @click="selectedComponent = 'post'">Show Posts</button>
    </nav>
    <template v-if="selectedComponent === 'todo'">
      <Todos :todos="todos" @update-todos="updateTodos" />
    </template>
    <template v-else-if="selectedComponent === 'post'">
      <Posts />
    </template>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Todos from './components/Todos.vue';
import Posts from './components/Posts.vue';

const selectedComponent = ref('todo');
const todos = ref(JSON.parse(localStorage.getItem('todos')) || [
  { done: false, content: 'Write a blog post', editing: false }
]);

function updateTodos(updatedTodos) {
  todos.value = updatedTodos;
  localStorage.setItem('todos', JSON.stringify(todos.value));
}
</script>
