<script setup>
  import { ref, onMounted, computed, watch } from "vue";
  const todos = ref([]);
  const name = ref("");
  const inputName = ref("");
  const inputCat = ref(null);

  const sortTodos = computed(() =>
    todos.value.sort((a, b) => {
      return a.createdAt - b.createdAt;
    })
  );
  const addTodo = () => {
    if (inputName.value.trim() === "" || inputCat.value === null) {
      return;
    }
    todos.value.push({
      content: inputName.value,
      cat: inputCat.value,
      done: false,
      createdAt: new Date().getDate(),
    });
  };
  const removeTodo = (todo) => {
    todos.value = todos.value.filter((e) => e !== todo);
  };
  watch(
    todos,
    (newval) => {
      localStorage.setItem("todos", JSON.stringify(newval));
    },
    { deep: true }
  );
  watch(name, (newval) => {
    localStorage.setItem("name", newval);
  });
  onMounted(() => {
    name.value = localStorage.getItem("name") || "";
    todos.value = JSON.parse(localStorage.getItem("todos")) || [];
  });
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's Up
        <input type="text" placeholder="Your Name Hear" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's On Your Todo List?</h4>
        <input type="text" placeholder="Make A List...." v-model="inputName" />
        <h4>Category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="Cat"
              value="businees"
              v-model="inputCat"
            />
            <span class="bubble businees"></span>
            <div>Businees</div>
          </label>
          <label>
            <input
              type="radio"
              name="Cat"
              value="personal"
              v-model="inputCat"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="todo in sortTodos"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.cat}`"></span>
          </label>
          <div class="toto-content">
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
