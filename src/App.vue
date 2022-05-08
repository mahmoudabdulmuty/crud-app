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

    <button ref="checkedButton" @click="toggleChecked">
      {{ checkedValue }} todos
    </button>

    <input
      ref="newTodo"
      type="text"
      v-model="state.newTodo"
      placeholder="add a new todo"
    />
    <button ref="addBtn" @click="addNewTodo">add</button>
    <button @click="sortTodos">sort</button>
  </header>

  <main>
    <ul v-if="filteredTodos.length && !checked">
      <li v-for="todo in filteredTodos" :key="todo.id">
        <span :class="{ done: todo.done }" @click="toggleDone(todo)">{{
          todo.title
        }}</span>
        <div class="btns-wrapper">
          <span ref="timer" class="timer">00:00:00</span>
          <button ref="start" @click="timerFunction('start')">start</button>
          <button disabled ref="stop" @click="timerFunction('stop')">
            stop
          </button>
          <button disabled @click="timerFunction('pause')" ref="pause">
            pause
          </button>
          <button class="delete" @click="deleteTodo(todo.id)">
            Delete Todo
          </button>
          <button class="update" @click="updateTodo(todo.id)">
            Update Todo
          </button>
        </div>
      </li>
    </ul>

    <ul v-if="checked">
      <li v-for="todo in checkedTodos" :key="todo.id">
        <span :class="{ done: todo.done }" @click="toggleDone(todo)">{{
          todo.title
        }}</span>
        <div class="btns-wrapper">
          <button class="delete" @click="deleteTodo(todo.id)">
            Delete Todo
          </button>
          <button class="update" @click="updateTodo(todo.id)">
            Update Todo
          </button>
        </div>
      </li>
    </ul>
  </main>
</template>

<script setup>
import { computed, onMounted, reactive, ref } from "vue";

onMounted(() => {
  newTodo.value.focus();
});

const state = reactive({
  newTodo: "",
  todos: [],
});

const searchQuery = ref("");
const checked = ref(false);
const addBtn = ref("add");
const newTodo = ref("");
const targetId = ref(null);
const time = ref(0);
const timer = ref("");
const start = ref(null);
const stop = ref(null);
const pause = ref(null);
const interval = ref(null);

const addNewTodo = () => {
  if (state.newTodo && addBtn.value.innerText === "add") {
    state.todos.push({
      title: state.newTodo,
      done: false,
      id: Math.random(),
    });
    state.newTodo = "";
  } else if (!state.newTodo) {
    alert("you must type something");
  } else if (addBtn.value.innerText === "update") {
    state.todos.forEach((todo) => {
      if (todo.id === targetId.value) {
        todo.title = newTodo.value.value;
        state.newTodo = "";
        addBtn.value.innerText = "add";
      }
    });
  }
};

const toggleDone = (todo) => {
  todo.done = !todo.done;
};

const deleteTodo = (id) => {
  state.todos = state.todos.filter((todo) => todo.id !== id);
};

const updateTodo = (id) => {
  const targetTodo = state.todos.find((todo) => todo.id === id);
  state.newTodo = targetTodo.title;
  targetTodo.value = targetTodo.title;
  targetId.value = id;
  addBtn.value.innerText = "update";
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

const toggleChecked = () => {
  checked.value = !checked.value;
};

const checkedTodos = computed(() => {
  if (checked.value) {
    return state.todos.filter((todo) => todo.done);
  } else {
    return state.todos;
  }
});

const checkedValue = computed(() => {
  return checked.value ? "all" : "checked";
});

const sortTodos = () => {
  state.todos.sort(ascendingSort);
};

function ascendingSort(a, b) {
  if (a.title.toLowerCase() < b.title.toLowerCase()) {
    return -1;
  }
  if (b.title.toLowerCase() > a.title.toLowerCase()) {
    return 1;
  }
  return 0;
}

function startTimer() {
  interval.value = setInterval(() => {
    time.value++;
    let mins = Math.floor(time.value / 10 / 60) % 60;
    let secs = Math.floor(time.value / 10) % 60;
    let tenths = time.value % 10;
    if (mins < 10) {
      mins = "0" + mins;
    }
    if (secs < 10) {
      secs = "0" + secs;
    }
    timer.value[0].innerText = mins + ":" + secs + ":" + "0" + tenths;
  }, 100);
}

const timerFunction = (action) => {
  if (action === "start") {
    startTimer();
    start.value[0].disabled = true;
    stop.value[0].disabled = false;
    pause.value[0].disabled = false;
  } else if (action === "pause") {
    clearInterval(interval.value);
    stop.value[0].disabled = false;
    start.value[0].disabled = false;
  } else if (action === "stop") {
    clearInterval(interval.value);
    stop.value[0].disabled = true;
    pause.value[0].disabled = true;
    start.value[0].disabled = false;
    timer.value[0].innerText = `00:00:00`
    time.value = 0;
  }
};
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
  transition: 0.3s;
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

.btns-wrapper {
  display: flex;
  align-items: center;
  gap: 5px;
}

.delete,
.update {
  padding: 12px 16px 12px 16px;
}

.done {
  text-decoration: line-through;
}
</style>
