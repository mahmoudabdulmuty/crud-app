<template>
  <h1>Todo App</h1>
  <header class="header">
    <input
      class="search-query"
      v-if="state.todos.length"
      type="search"
      v-model="searchQuery"
      placeholder="search todos"
    />

    <button @click="getChecked">{{ checkedValue }} todos</button>

    <input type="text" v-model="state.newTodo" placeholder="add a new todo" />
    <button @click="addNewTodo">add</button>
    <button @click="sortTodos">sort</button>
  </header>

  <main class="todos-wrapper">
    <ul v-if="filteredTodos.length">
      <li
        :class="{ done: todo.done }"
        v-for="todo in filteredTodos"
        :key="todo.id"
        @click="toggleDone(todo)"
      >
        <span>{{ todo.title }}</span>
        <button class="delete" @click="deleteTodo(todo.id)">Delete Todo</button>
      </li>
    </ul>
  </main>
</template>

<script setup>
import { computed, reactive, ref } from "vue";

const state = reactive({
  newTodo: "",
  todos: [],
});

const searchQuery = ref("");
const checked = ref(false);

const addNewTodo = () => {
  if (state.newTodo) {
    state.todos.push({
      title: state.newTodo,
      done: false,
      id: Math.random(),
    });
  } else {
    alert("you must type something");
  }
  state.newTodo = "";
};

const toggleDone = (todo) => {
  todo.done = !todo.done;
};

const deleteTodo = (id) => {
  state.todos = state.todos.filter((todo) => todo.id !== id);
};

const filteredTodos = computed(() => {
  if (searchQuery.value) {
    return state.todos.filter((todo) =>
      todo.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    );
  } else {
    return state.todos;
  }
});

const getChecked = () => {
  if (checked.value) {
    state.todos = state.todos.filter((todo) => todo);
  } else {
    state.todos = state.todos.filter((todo) => todo.done);
  }
  checked.value = !checked.value;
};

const checkedValue = computed(() => {
  return checked.value ? "all" : "checked";
});

const sortTodos = () => {
  state.todos.sort(ascendingSort);
};

function ascendingSort(a, b) {
  if (a.title < b.title) {
    return -1;
  }
  if (b.title > a.title) {
    return 1;
  }
  return 0;
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  width: 100%;
  height: 100vh;
  overflow: hidden;
  font-family: "Poppins", sans-serif;
  background: linear-gradient(135deg, #4ab1ff, #2d5cfe);
}

h1 {
  text-align: center;
  margin-bottom: 10px;
}

.header {
  display: flex;
  gap: 20px;
  justify-content: center;
  align-items: center;
}
.header input {
  height: 40px;
  width: 50%;
  padding-left: 10px;
  border: none;
}

.header button {
  padding: 10px;
  cursor: pointer;
  color: #555;
  background: #d9d9d9;
  transition: 0.3;
  border: none;
  width: 12%;
  border-radius: 6px;
}
.header button:hover {
  background-color: #bbb;
}
main {
  margin-top: 20px;
}
ul li {
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 8px 12px 40px;
  background: #eee;
  font-size: 18px;
  transition: 0.2s;
}

ul li:nth-child(odd) {
  background: #f9f9f9;
}
ul li:hover {
  background: #ddd;
}

.delete.delete {
  padding: 12px 16px 12px 16px;
}

.done span {
  text-decoration: line-through;
}
</style>
