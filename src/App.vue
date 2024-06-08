<script setup>

import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const selectedCategory = ref('all')

const filteredTodos = computed(() => {
  if(selectedCategory.value === 'all'){
    return todos.value
  }
  return todos.value.filter(todo=> todo.category === selectedCategory.value)
})

const todos_asc = computed(() => {
  return [...filteredTodos.value].sort((a, b) => a.createdAt - b.createdAt)
})

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
}



const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, { deep: true })

watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

const setFilterCategory = (category) => {
  selectedCategory.value = category
}

const resetFilter = () => {
  selectedCategory.value = 'all'
}

</script>


<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Bonjour, <input type="text" placeholder="Name" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a todo</h3>

      <form @submit.prevent="addTodo">
        <h4>On fait le point sur ta todo ?</h4>
        <input v-model="input_content" type="text" placeholder="Rentre une tache à faire" />

        <h4>Choisi une catégorie</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business">business</span>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">
            <span class="bubble personal">personal</span>
          </label>
        </div>
        <input type="submit" value="Ajouter une tache"><br>
        
          
      </form>
    </section>

    <section class="todo-list">
      <h3>Todo list</h3>
      <div class="filters">
        <button @click="setFilterCategory('all')">All</button>
        <button @click="setFilterCategory('business')">Business</button>
        <button @click="setFilterCategory('personal')">Personal</button>
      </div>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.name" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input v-model="todo.content" type="text" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
    
  </main>
</template>
