<script setup>
import { ref, onMounted, computed, watch } from 'vue'
//define constant variables to be used
const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref()


//add todo to list, check for invaild inputs and reset content and category state
const add_todo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}
//remove todo from list, doing it this way mutates the whole list, also removing item from localstorage
const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)

}

//arrange todos list by time created, the newest at the top
const todos_asc = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

//save todos to localStorage on change to save state between loads
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

//save name to localStorage on change to save state between loads
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
//pull name and todos value from localStorage
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here"
        v-model="name"/>
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      
      <form @submit.prevent="add_todo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text" 
          placeholder="e.g. make a video"
          v-model="input_content" />
        <h4>Pick a category</h4>

        <div class="options">

          <label>
              <input 
              type="radio" 
              name="category"
              value="business"
              v-model="input_category" />
              <span class="bubble business"></span>
              <div>Business</div>
          </label>

          <label>
              <input 
              type="radio" 
              name="category"
              value="personal"
              v-model="input_category" />
              <span class="bubble personal"></span>
              <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add todo" :style="
        `background-color: var(--${input_category});
        box-shadow: var(--${input_category}-glow);
        `">

      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">

          <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 
            'done'
          }`">
          
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`">
            </span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>

          </div>

      </div>
    </section>

  </main>
</template>

