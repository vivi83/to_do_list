<script setup>
/* eslint-disable */
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">Bienvenue sur votre liste des tâches à faire</h2>
    </section>

    <section class="create-todo">
      <h3>Créer une tâche</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <input
          type="text"
          name="content"
          id="content"
          placeholder="Quelle est votre tâche?"
          v-model="input_content"
        />

        <h4>Choisir une catégorie</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Travail</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personnel</div>
          </label>
        </div>

        <input type="submit" value="Ajouter tâche" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Liste des tâches à faire</h3>
      <div class="list" id="todo-list">
        <!--  les classes par ordre ascendant -->
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
          :key="todo.id"
        >
          <label>
            <!-- donne la couleur pour connaitre la catégorie -->
            <input type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${
                todo.category == 'business' ? 'business' : 'personal'
              }`"
            ></span>
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