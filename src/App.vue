<template>
  <div class="container">
    <div class="header">
      <h1>Menu</h1>
      <button @click="selectedMenu = 'All'" :class="{ active: selectedMenu === 'All' }">Todos</button>
      <button @click="selectedMenu = 'Post'" :class="{ active: selectedMenu === 'Post' }">Post</button>
    </div>
    <div class="content">
      <div class="todo-app" v-if="selectedMenu === 'Todos'">
        <h1>Activity</h1>
        <input v-model="newTask" @keyup.enter="addTask" placeholder="New Assignment" />
        <ul>
          <li v-for="task in todos" :key="task.id">
            <input type="checkbox" v-model="task.completed" @change="toggleTask(task)" />
            <span :class="{ completed: task.completed }">{{ task.title }}</span>
            <button @click="editTask(task)">Edit</button>
            <button @click="removeTask(task)">Delete</button>
          </li>
        </ul>
      </div>
      <div class="post-app" v-else-if="selectedMenu === 'Post'">
        <h1>User Posts</h1>
        <div class="filters">
          <label for="userSelect">Select User:</label>
          <select v-model="selectedUser" @change="loadPosts">
            <option v-for="user in users" :value="user.id">{{ user.name }}</option>
          </select>
        </div>
        <div v-if="posts.length > 0">
          <ul>
            <li v-for="post in posts" :key="post.id">
              <h3>{{ post.title }}</h3>
              <p>{{ post.body }}</p>
            </li>
          </ul>
        </div>
        <div v-else>
          <p>There Are No Posts For The Selected User.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const selectedMenu = ref('Todos');
const selectedUser = ref('');
const users = ref([]);
const posts = ref([]);
const todos = ref([]);
const newTask = ref('');

function addTask() {
  if (newTask.value.trim() === '') return;
  todos.value.push({ id: Date.now(), title: newTask.value, completed: false });
  newTask.value = '';
  saveData();
}

function toggleTask(task) {
  task.completed = !task.completed;
  saveData();
}

function removeTask(task) {
  todos.value = todos.value.filter(t => t.id !== task.id);
  saveData();
}

function editTask(task) {
  const newTitle = prompt('Edit task title', task.title);
  if (newTitle !== null) {
    task.title = newTitle;
    saveData();
  }
}

function saveData() {
  localStorage.setItem('todos', JSON.stringify(todos.value));
}

function loadData() {
  const savedTodos = localStorage.getItem('todos');
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos);
  }
}

function loadUsers() {
  fetch('https://jsonplaceholder.typicode.com/users')
    .then(response => response.json())
    .then(data => {
      users.value = data;
      if (data.length > 0) {
        selectedUser.value = data[0].id; // Memilih pengguna pertama sebagai default
        loadPosts();
      }
    })
    .catch(error => console.error('Error fetching users:', error));
}

function loadPosts() {
  if (!selectedUser.value) return;
  fetch(`https://jsonplaceholder.typicode.com/posts?userId=${selectedUser.value}`)
    .then(response => response.json())
    .then(data => {
      posts.value = data;
    })
    .catch(error => console.error('Error fetching posts:', error));
}

onMounted(() => {
  loadData();
  loadUsers();
});
</script>

<style scoped>
.container {
  background: rgb(61, 61, 61);
  width: 100%;
  min-height: 100vh;
  padding: 20px; /* Mengubah padding menjadi 20px */
  margin-top: -20px; /* Mengubah margin menjadi -20px */
  margin-left: -20px; /* Mengubah margin menjadi -20px */
  margin-bottom: -20px; /* Mengubah margin menjadi -20px */
}

.header {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 40px; /* Mengubah margin menjadi 40px */
}

.header h1 {
  margin-right: 40px; /* Mengubah margin menjadi 40px */
  color: #ff0000; /* Mengubah warna menjadi merah */
  font-size: 24px; /* Mengubah ukuran menjadi 24px */
}

.header button {
  border: none;
  background: transparent;
  color: #00ff00; /* Mengubah warna menjadi hijau */
  font-size: 20px; /* Mengubah ukuran menjadi 20px */
  cursor: pointer;
  margin-right: 20px; /* Mengubah margin menjadi 20px */
}

.header button.active {
  font-weight: bold;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.todo-app {
  width: 100%;
  max-width: 600px; /* Mengubah max-width menjadi 600px */
  background: #f0f0f0; /* Mengubah warna menjadi abu-abu */
  padding: 30px; /* Mengubah padding menjadi 30px */
  border-radius: 12px; /* Mengubah border-radius menjadi 12px */
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.2); /* Mengubah box-shadow */
}

.todo-app input {
  width: 100%;
  padding: 15px; /* Mengubah padding menjadi 15px */
  margin-bottom: 30px; /* Mengubah margin menjadi 30px */
  border: 1px solid #ccc;
  border-radius: 6px; /* Mengubah border-radius menjadi 6px */
}

.todo-app ul {
  list-style: none;
  padding: 0;
}

.todo-app li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 15px 0; /* Mengubah padding menjadi 15px 0 */
  border-bottom: 1px solid #ddd; /* Mengubah warna menjadi abu-abu */
}

.todo-app li span.completed {
  text-decoration: line-through;
  color: #999; /* Mengubah warna menjadi abu-abu */
}

.todo-app li button {
  margin-left: 15px; /* Mengubah margin menjadi 15px */
  border: none;
  background: #ff0000; /* Mengubah warna menjadi merah */
  color: #fff; /* Mengubah warna menjadi putih */
  cursor: pointer;
  padding: 8px 15px; /* Mengubah padding menjadi 8px 15px */
  border-radius: 4px;
}

.post-app {
  width: 100%;
  max-width: 600px; /* Mengubah max-width menjadi 600px */
  background: #f0f0f0; /* Mengubah warna menjadi abu-abu */
  padding: 30px; /* Mengubah padding menjadi 30px */
  border-radius: 12px; /* Mengubah border-radius menjadi 12px */
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.2); /* Mengubah box-shadow */
}

.post-app .filters {
  margin-bottom: 30px; /* Mengubah margin menjadi 30px */
}

.post-app select {
  width: 100%;
  padding: 15px; /* Mengubah padding menjadi 15px */
  border: 1px solid #ccc;
  border-radius: 6px; /* Mengubah border-radius menjadi 6px */
}

.post-app ul {
  list-style: none;
  padding: 0;
}

.post-app li {
  padding: 15px 0; /* Mengubah padding menjadi 15px 0 */
  border-bottom: 1px solid #ddd; /* Mengubah warna menjadi abu-abu */
}

.post-app li h3 {
  margin: 0 0 15px 0; /* Mengubah margin menjadi 0 0 15px 0 */
}

.post-app li p {
  margin: 0;
}

</style>