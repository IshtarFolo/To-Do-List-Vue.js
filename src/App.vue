<script setup>
// Import Vue
import { ref, resolveTransitionHooks } from 'vue'
// Styles
import './assets/main.css'

/*===============
**  VARIABLES  **
================*/

// Tableau des tâches --> À conserver dans le localStorage
var tasks = ref([])

// Ici on crée une tâche
let message = ref('')

// On détermine le nombre de tâches avec la méthode length
var nbrtasks = ref(0)

/*=============
** FONCTIONS **
==============*/
// Quand on charge la page, on vérifie si le localStorage contient des tâches
// Si oui, on les récupère et on les affiche
// Sinon, on initialise le tableau tasks à vide
if(localStorage.getItem('tasks') != null)
{
  getTasks()
}
else
{
  tasks.value = []
}

// On ajoute une tâche à la liste en cliquant sur le bouton "OK"
function addTask(event)
{
  if(message.value != '')
  {
    // On ajoute les tâches au tableau "tasks". On en fait des Objets pour pouvoir les manipuler plus facilement.
    // Ici text = la variable message enregistrée dans le champ input
    // completed = false par défaut et on l'inversera si la case est cochée
    tasks.value.push({ text:message.value, completed: false })
    message.value = ''
    countTasks()
    saveTasks()
  }
  else
  {
    alert('Veuillez entrer une tâche')
  }
}

// On compte le nombre de tâches
function countTasks(event) 
{
  if(tasks.value.length > 0)
  {
    nbrtasks.value = tasks.value.length
  } 
  else 
  {
    nbrtasks.value = 0
  }
}

// On ajoute une classe au li si la case est cochée
function tacheFinie(index, event)
{
  tasks.value[index].completed = event.target.checked
}

// On récupère les tâches du localStorage
function getTasks()
{
  if (localStorage.getItem('tasks') != null) 
  {
    tasks.value = JSON.parse(localStorage.getItem('tasks'))
  }
  else 
  {
    tasks.value = []
  }
  countTasks()
}

// On sauvegarde les tâches dans le localStorage
function saveTasks()
{
  if (tasks.value.length > 0) 
  {
    localStorage.setItem('tasks', JSON.stringify(tasks.value))
  }
  else
  {
    localStorage.removeItem('tasks')
  }
}

// On supprime une tâche de la liste et du localStorage
function rmTask(index)
{
  // On supprime la tâche du tableau tasks
  tasks.value.splice(index, 1)
  // On met à jour le nombre de tâches
  saveTasks()
  countTasks()
}

</script>

<!-- **************** App **************** -->
<template>
  <header>
    <h1>Challenge #1 : To-Do list</h1>
  </header>

  <div class="container">
    <div class="main">
      <div class="task-creator">
        <p>Ajouter une tâche : &nbsp;</p>
        <input class="task-add" v-model="message" />

        <!-- Bouton pour ajouter la tache a la liste des taches -->
        <button class="btn-add" @click="addTask">OK</button>
      </div>

    <!-- La liste des tâches apparaît ici et on peut les supprimer avec un click du bouton -->
      <div class="liste-taches">
        <h2>Liste des tâches</h2>

        <ol>
          <!-- On récupère les tâches crées dans le localstorage -->
          <li v-for="(task, index) in tasks" :key="index" :class="{ fini: task.completed }">
              <div>
                <!-- Checkbox -->
                <input type="checkbox" class="checkbox" @change="tacheFinie(index, $event)"/>
                <p>{{ task.text }}</p>
                <!-- On supprime la tâche avec le bouton "X" -->
                <button class="btn-suppr" @click="rmTask(index)">X</button>
              </div>
          </li>
        </ol>

      </div>
    </div>
  </div>

  <footer>
    <h2>Nombre de tâches</h2>
      <p>Nombre de tâches : {{ nbrtasks }}</p>
  </footer>

</template>