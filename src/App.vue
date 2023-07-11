<script setup>
import { ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todo_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
})

</script>

<template>

  <main class ="app">

    <section class="greeting">
      <h2 class = "greeting__title">
        Hello, <input v-model="name" class="greeting__input" placeholder="Your name">
      </h2>
    </section>
  </main>
</template>
