<template>
  <main class="app">
    <div>
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
          <thead>
            <tr>
              <th style="border: 2px solid white;">User</th>
              <th style="border: 2px solid white;">Title</th>
              <th style="border: 2px solid white;">Body</th>
            </tr>
          </thead>
          <tbody>
            <template v-for="user in users" :key="user.id">
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

<script setup>
import { ref, onMounted } from 'vue';

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

const fetchUsers = async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    users.value = await response.json();
    console.log('Fetched users:', users.value);
  } catch (error) {
    console.error('Error fetching users:', error);
  }
};

const getUser = (userId) => {
  return users.value.find(user => user.id === userId) || { name: 'Unknown' };
};

const userPostsByUser = (userId) => {
  return userPosts.value.filter(post => post.userId === userId);
};

onMounted(() => {
  fetchUsers();
  fetchUserPosts();
});
</script>


