<template>
  <div>  
 
 
     <template v-if="selectedComponent === 'todo'">
         <main class="app">
                 <div class="container">
                   <button @click="selectedComponent = 'todo'">Show Todos</button>
                   <button @click="selectedComponent = 'post'">Show Posts</button>
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
                           @keyup.enter="saveEditedTodo(index)"
                           @blur="saveEditedTodo(index)"
                           @keyup.escape="cancelEditingTodo(index)"
                           @keyup.shift.enter="cancelEditingTodo(index)"
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
 
     <template v-else-if="selectedComponent === 'post'">
       <main class="app">
         <div>
           <button @click="selectedComponent = 'todo'">Show Todos</button>
           <button @click="selectedComponent = 'post'">Show Posts</button>
           <h2>Posts</h2>
           <label for="userSelect">Select User: </label>
           <select id="userSelect" v-model="selectedUser" @change="fetchUserPosts">
             <option value="">All User</option>
             <option v-for="user in users" :value="user.id" :key="user.id">{{ user.name }}</option>
           </select>
 
           <template v-if="selectedUser">
             <h3 style="color: orange;">User: {{ getUser(selectedUser).name }}</h3>
             <ol style="text-align: left;">
               <li v-for="(post, index) in userPosts" :key="post.id">
                 <h3><strong>Title: {{ post.title }}</strong></h3>
                 <p>Body: {{ post.body }}</p>
               </li>
             </ol>
           </template>
 
           <template v-else>
             <table style="border: 2px solid white;">
               <thead >
                 <tr>
                   <th style="border: 2px solid white;">User</th>
                   <th style="border: 2px solid white;">Title</th>
                   <th style="border: 2px solid white;">Body</th>
                 </tr>
               </thead>
               <tbody>
                 <template v-for="user in users">
                   <tr>
                     <td style="border: 2px solid white;">{{ user.name }}</td>
                     <td colspan="2" style="text-align: center; border: 2px solid white;">--- Posts by {{ user.name }} ---</td>
                   </tr>
                   <tr v-for="post in userPostsByUser(user.id)" :key="post.id">
                     <td></td>
                     <td style="border: 2px solid white;">{{ post.title }}</td>
                     <td style="border: 2px solid white;">{{ post.body }}</td>
                   </tr>
                 </template>
               </tbody>
             </table>
           </template>
         </div>
       </main>
     </template>
 
   </div>
 </template>
 
 <script setup>
 import { ref, computed, onMounted } from "vue";
 
 const newTodo = ref("");
 const defaultData = [
   {
     done: false,
     content: "Write a blog post",
     editing: false,
   },
 ];
 const todosData = JSON.parse(localStorage.getItem("todos")) || defaultData;
 const todos = ref(todosData);
 const filter = ref("all");
 const editingTodoListName = ref(false);
 const todoListName = ref("My Todo List");
 const editedTodoListName = ref(todoListName.value);
 
 function addTodo() {
   if (newTodo.value) {
     todos.value.push({
       done: false,
       content: newTodo.value,
       editing: false,
     });
     newTodo.value = "";
     saveData();
   }
 }
 
 function removeTodo(index) {
   todos.value.splice(index, 1);
   saveData();
 }
 
 function saveData() {
   const storageData = JSON.stringify(todos.value);
   localStorage.setItem("todos", storageData);
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
 
 function cancelEditingTodo(index) {
   todos.value[index].editing = false;
 }
 
 const filteredTodos = computed(() => {
   if (filter.value === "done") {
     return todos.value.filter((todo) => todo.done);
   } else if (filter.value === "undone") {
     return todos.value.filter((todo) => !todo.done);
   } else {
     return todos.value;
   }
 });
 
 const selectedComponent = ref('todo'); 
 const selectedUser = ref(null);
 const userPosts = ref([]);
 const users = ref([]);
 
 const fetchUserPosts = async () => {
   try {
     let url = 'https://jsonplaceholder.typicode.com/posts';
     if (selectedUser.value) {
       url += `?userId=${selectedUser.value}`;
     }
     const response = await fetch(url);
     const fetchedPosts = await response.json();
     userPosts.value = fetchedPosts.map(post => ({
       userId: post.userId,
       id: post.id,
       title: post.title,
       body: post.body
     }));
     console.log('User posts:', userPosts.value);
   } catch (error) {
     console.error('Error fetching user posts:', error);
   }
 };
 
 onMounted(() => {
   todos.value = JSON.parse(localStorage.getItem("todos")) || [];
 });
 
 const fetchUsers = async () => {
   try {
     const response = await fetch('https://jsonplaceholder.typicode.com/users');
     users.value = await response.json();
     console.log('Fetched users:', users.value);
   } catch (error) {
     console.error('Error fetching users:', error);
   }
 };
 
 const getUser = userId => {
   return users.value.find(user => user.id === userId) || { name: 'Unknown' };
 };
 
 const userPostsByUser = userId => {
   return userPosts.value.filter(post => post.userId === userId);
 };
 
 onMounted(() => {
   fetchUsers();
 });
 
 </script>