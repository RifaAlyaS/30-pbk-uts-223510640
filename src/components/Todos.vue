<template>
  <main class="app">
    <div class="container">
      <form @submit.prevent="addTodo()">
        <center>
          <h2 @click="editTodoListName">
            <span v-if="!editingTodoListName">{{ todoListName }}</span>
            <input
              v-model="editedTodoListName"
              v-if="editingTodoListName"
              @keyup.enter="saveTodoListName()"
              @blur="saveTodoListName()"
              @keyup.escape="cancelEditingTodoListName"
              @keyup.shift.enter="cancelEditingTodoListName"
            />
          </h2>
        </center>
        <label>New ToDo</label>
        <input v-model="newTodo" name="newTodo" autocomplete="off" />
        <button>Add ToDo</button>
      </form>

      <div class="filter-buttons">
        <router-link class="filter-button" :to="{ path: '/todos', query: { filter: 'all' } }" :class="{ active: filter === 'all' }">All</router-link>
        <router-link class="filter-button" :to="{ path: '/todos', query: { filter: 'done' } }" :class="{ active: filter === 'done' }">Done</router-link>
        <router-link class="filter-button" :to="{ path: '/todos', query: { filter: 'undone' } }" :class="{ active: filter === 'undone' }">Undone</router-link>
      </div>

      <ul>
        <li v-for="(todo, index) in filteredTodos" :key="index">
          <input type="checkbox" v-model="todo.done" :id="'todo_' + index" />
          <label :for="'todo_' + index" :class="{ done: todo.done }">
            <span v-if="!todo.editing">{{ todo.content }}</span>
            <input
              v-else
              v-model="todo.content"
              @keyup.enter="saveEditedTodo()"
              @blur="saveEditedTodo()"
              @keyup.escape="cancelEditingTodo()"
              @keyup.shift.enter="cancelEditingTodo()"
            />
          </label>
          <button @click="removeTodo(index)">Remove</button>
          <button @click="editTodo(index)">{{ todo.editing ? "Done" : "Edit" }}</button>
        </li>
      </ul>
      <h4 v-if="filteredTodos.length === 0">Empty list.</h4>
    </div>
  </main>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useRoute } from 'vue-router';

const newTodo = ref('');
const editingTodoListName = ref(false);
const todoListName = ref('My Todo List');
const editedTodoListName = ref(todoListName.value);
const route = useRoute();

const todos = ref(JSON.parse(localStorage.getItem('todos')) || [
  { done: false, content: 'Write a blog post', editing: false }
]);

function updateTodos(updatedTodos) {
  todos.value = updatedTodos;
  localStorage.setItem('todos', JSON.stringify(todos.value));
}

function addTodo() {
  if (newTodo.value) {
    todos.value.push({
      done: false,
      content: newTodo.value,
      editing: false,
    });
    newTodo.value = '';
    saveData();
  }
}

function removeTodo(index) {
  todos.value.splice(index, 1);
  saveData();
}

function saveData() {
  updateTodos(todos.value);
}

function saveTodoListName() {
  todoListName.value = editedTodoListName.value;
  editingTodoListName.value = false;
  saveData();
}

function cancelEditingTodoListName() {
  editedTodoListName.value = todoListName.value;
  editingTodoListName.value = false;
}

function editTodo(index) {
  const todo = todos.value[index];
  todo.editing = !todo.editing;
  saveData();
}

function saveEditedTodo() {
  saveData();
}

function cancelEditingTodo() {
  saveData();
}

const filteredTodos = computed(() => {
  const filter = route.query.filter;
  if (filter === 'done') {
    return todos.value.filter(todo => todo.done);
  } else if (filter === 'undone') {
    return todos.value.filter(todo => !todo.done);
  } else {
    return todos.value;
  }
});
</script>