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
/* Conteneur principal */
.app-container {
  /* Sur mobile, on occupe toute la largeur avec un padding */
  padding: 1rem;
  margin: 0 auto;
  /* permet de centrer quand on est en desktop */
  text-align: center;
}

/* Titre */
h1 {
  margin-bottom: 1rem;
  font-size: 1.5rem;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

h2 {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

/* Section d'ajout de tâche */
.add-task-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 1rem;
}

.add-task-container input[type="text"] {
  padding: 0.5rem;
  font-size: 1rem;
  flex: 1;
  max-width: 200px;
  /* Sur mobile, ça prendra la largeur, sur grand écran, on limite un peu */
}

.add-task-container button {
  padding: 0.5rem 1rem;
  margin-left: 0.5rem;
  font-size: 1rem;
}

/* La rangée d’orbes (mobile-first) */
.progress-orbs {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  /* Permet de passer à la ligne si l'écran est vraiment étroit */
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

/* 
  Media Query pour écrans plus larges (>= 768px) 
  -> On limite la largeur du conteneur pour un meilleur centrage
*/
@media (min-width: 768px) {
  .app-container {
    max-width: 500px;
    /* Ajustable : tu peux viser 600-700px ou plus */
  }

  h1 {
    font-size: 2rem;
  }
}
</style>
