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
      <div v-for="task in tasks" :key="task.id" class="orb" :class="{ completed: task.completed }"
        @click="toggleTask(task)">
        {{ task.id }}
      </div>
    </div>

    <!-- Liste de tâches (avec checkboxes) -->
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
const tasks = ref([
  { id: 1, title: 'Faire du sport', completed: false },
  { id: 2, title: 'Lire un chapitre', completed: false },
  { id: 3, title: 'Prendre des nouvelles d\'un proche', completed: false },
  { id: 4, title: 'Coder un peu', completed: false },
])

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
  margin: 2rem;
  text-align: center;
  font-family: sans-serif;
}

.add-task-container {
  margin-bottom: 1rem;
}

input[type="text"] {
  padding: 0.5rem;
}

button {
  margin-left: 0.5rem;
  padding: 0.5rem;
}

.progress-orbs {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-bottom: 2rem;
}

.orb {
  width: 50px;
  height: 50px;
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
}
</style>
