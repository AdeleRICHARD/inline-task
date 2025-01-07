<template>
  <div class="app-container">
    <h1>Day Completion</h1>

    <!-- Saisie et bouton d'ajout -->
    <div class="add-task-container">
      <input v-model="newTaskTitle" @keyup.enter="addTask" placeholder="Add new task" />
      <button @click="addTask">Add</button>
    </div>

    <!-- Barres de progression (ou "orbes") -->
    <div class="progress-orbs">
      <span v-for="task in tasks" :key="task.id" class="orb" :class="{ completed: task.completed }"
        @click="toggleTask(task)">
      </span>
    </div>

    <h2>
      Intel Of The Day
    </h2>
    <ul class="checklist">
      <li v-for="task in tasks" :key="task.id">
        <input type="checkbox" v-model="task.completed" @change="onTaskChange" />
        {{ task.title }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// Référence vers notre liste de tâches
const tasks = ref([])

// Saisie pour la nouvelle tâche
const newTaskTitle = ref('')

// Chargement depuis localStorage au montage
onMounted(() => {
  const saved = localStorage.getItem('my-tasks')
  if (saved) {
    tasks.value = JSON.parse(saved)
  }
})

// Inverser l'état de completed
function toggleTask(task) {
  task.completed = !task.completed
  saveTasks()
}

// Enregistrer dans localStorage
function saveTasks() {
  localStorage.setItem('my-tasks', JSON.stringify(tasks.value))
}

// Quand on change la checkbox
function onTaskChange() {
  saveTasks()
}

// Ajouter une nouvelle tâche
function addTask() {
  // On ignore si newTaskTitle est vide
  if (!newTaskTitle.value.trim()) return

  // Générer un id unique très simple
  const newId = tasks.value.length
    ? tasks.value[tasks.value.length - 1].id + 1
    : 1

  // Pousser la nouvelle tâche dans le tableau
  tasks.value.push({
    id: newId,
    title: newTaskTitle.value,
    completed: false
  })

  // Reset du champ
  newTaskTitle.value = ''

  // Enregistrer
  saveTasks()
}
</script>

<style scoped>
.app-container {
  padding: 1rem;
  margin: 0 auto;
  text-align: center;
}

/* Title */
h1 {
  margin-bottom: 1rem;
  font-size: 1.5rem;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

h2 {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

/* Add task */
.add-task-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 1rem;
  gap: .5rem;
}

.add-task-container input[type="text"] {
  padding: 0.5rem;
  font-size: 1rem;
  flex: 1;
  max-width: 200px;
}

.add-task-container button {
  border: none;  
  outline: none;
  background: linear-gradient(to right, #4facfe, #2f8adf);
  color: #fff;  
  padding: 0.5rem 1rem;
  border-radius: 5px;
  font-size: 1rem;
  cursor: pointer;
  transition: transform 0.1s ease-in-out, background-color 0.2s ease-in-out;
}

.add-task-container button:hover {
  transform: translateY(-2px);
  background: linear-gradient(to right, #3e8ddb, #00c9d8);
}

.add-task-container button:active {
  transform: translateY(0);
  background: linear-gradient(to right, #3490d1, #00b8bd);
}

/* Orbs */
.progress-orbs {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  gap: .5rem;
  justify-content: left;
  margin-bottom: 2rem;
}

.orb {
  width: 25px;
  height: 25px;
  border-radius: 50%;
  border: 3px solid gold;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  user-select: none;
}

.orb.completed {
  background-color: gold;
  color: white;
}

.checklist {
  list-style: none;
  padding: 0;
  text-align: left;
  margin: 0 auto;
  max-width: 300px;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

li {
  margin-bottom: 0.5rem;
}

input[type="checkbox"] {
  margin-right: 0.5rem;
}

@media (min-width: 768px) {
  .app-container {
    max-width: 500px;
  }

  h1 {
    font-size: 2rem;
  }

  .progress-orbs {
    justify-content: center;
  }
}
</style>
