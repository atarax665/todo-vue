<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const category = ref([])
const input_content = ref('')
const type = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(category, (newVal) => {
  localStorage.setItem('category', JSON.stringify(newVal))
}, {
  deep: true
})

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const add_business_category = () => {
  if (input_category.value.trim() === '' || type.value === null) {
		return
	}
    category.value.push({
    category: input_category.value,
    type: type.value,
  })
}

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
  category.value = JSON.parse(localStorage.getItem('category')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

    <section class="create-todo">
      <h3>ADD CATEGORIES</h3>
      <form id="new-todo-form" @submit.prevent="add_business_category">
        <input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. Workout split"
					v-model="input_category" />

        <div class="options">
          <label>
            <input type="radio" name="type" value="business" v-model="type" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="type" value="personal" v-model="type" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add category" />
      </form>
    </section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. Learn List rendering"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options" v-for="category in category">
					<label>
            <input type="radio" name="category" :value="category.type" v-model="input_category" />
            <span :class="`bubble ${
              category.type == 'business' 
                ? 'business' 
                : 'personal'
            }`"></span>
            <div>{{ category.category }}</div>
          </label>
				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
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