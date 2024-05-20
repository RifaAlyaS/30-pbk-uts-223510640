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
          <label>New ToDo </label>
          <input v-model="newTodo" name="newTodo" autocomplete="off" />
          <button>Add ToDo</button>
        </form>
  
        <div class="filter-buttons">
          <button
            class="filter-button"
            :class="{ active: filter === 'all' }"
            @click="filter = 'all'"
          >
            All
          </button>
          <button
            class="filter-button"
            :class="{ active: filter === 'done' }"
            @click="filter = 'done'"
          >
            Done
          </button>
          <button
            class="filter-button"
            :class="{ active: filter === 'undone' }"
            @click="filter = 'undone'"
          >
            Undone
          </button>
        </div>
  
        <ul>
          <li v-for="(todo, index) in filteredTodos" :key="index">
            <input
              type="checkbox"
              v-model="todo.done"
              :id="'todo_' + index"
            />
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
            <button @click="editTodo(index)">
              {{ todo.editing ? "Done" : "Edit" }}
            </button>
          </li>
        </ul>
        <h4 v-if="filteredTodos.length === 0">Empty list.</h4>
      </div>
    </main>
  </template>
  
  <script setup>
  import { ref, computed } from 'vue';
  
  const props = defineProps({
    todos: {
      type: Array,
      required: true
    }
  });
  
  const emit = defineEmits(['update-todos']);
  
  const newTodo = ref('');
  const filter = ref('all');
  const editingTodoListName = ref(false);
  const todoListName = ref('My Todo List');
  const editedTodoListName = ref(todoListName.value);
  
  function addTodo() {
    if (newTodo.value) {
      props.todos.push({
        done: false,
        content: newTodo.value,
        editing: false,
      });
      newTodo.value = '';
      saveData();
    }
  }
  
  function removeTodo(index) {
    props.todos.splice(index, 1);
    saveData();
  }
  
  function saveData() {
    emit('update-todos', props.todos);
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
    const todo = props.todos[index];
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
    if (filter.value === 'done') {
      return props.todos.filter((todo) => todo.done);
    } else if (filter.value === 'undone') {
      return props.todos.filter((todo) => !todo.done);
    } else {
      return props.todos;
    }
  });
  </script>