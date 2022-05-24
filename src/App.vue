<script setup>
import { computed, ref } from "vue";
import axios from "axios";

const URL = "http://localhost:5001/todos";

const input = ref("");
const todos = ref([]);

async function addTodo() {
  await axios({
    method: "post",
    url: URL,
    data: {
      id: Math.floor(Math.random() * (999 - 100 + 1)) + 100,
      text: input.value,
      completed: false,
    },
  })
    .catch((err) => {
      console.log(err);
    })
    .then((res) => {
      console.log(res);
    })
    .then(() => {
      input.value = "";
      getTodos();
    });
}

async function removeTodo(todo) {
  const id = todo.id;

  await axios({
    method: "delete",
    url: `${URL}/${id}`,
  })
    .catch((err) => {
      console.log(err);
    })
    .then((res) => {
      console.log(res);
    })
    .then(() => {
      getTodos();
    });
}

async function markComplete(todo) {
  todo.completed = !todo.completed;

  const id = todo.id;

  await axios({
    method: "put",
    url: `${URL}/${id}`,
    data: todo,
  })
    .catch((err) => {
      console.log(err);
    })
    .then((res) => {
      console.log(res);
    });
}

async function getTodos() {
  const res = await axios.get(URL);
  const list = JSON.parse(res.data);
  todos.value = list.todolist;
}
getTodos();
</script>

<template>
  <h1>A mini to do list application</h1>
  <input type="text" v-model="input" />
  <button :disabled="!input.length" @click="addTodo">ADD</button>

  <ul>
    <li v-for="todo in todos" :key="todo.id">
      <button @click="markComplete(todo)">DONE</button>
      <p class="todo" :class="{ complete: todo.completed }">
        {{ todo.text }}
      </p>

      <button class="removeButton" @click="removeTodo(todo)">X</button>
    </li>
  </ul>
</template>

<style>
body {
  font-family: "Nova Round", cursive;
  background-image: linear-gradient(45deg, #f8edeb, #fcd5ce, #ffb5a7);
  background-repeat: no-repeat;
  background-size: cover;
  padding-bottom: 100px;
}

input {
  margin-right: 10px;
  font-family: "Nova Round", cursive;
  padding: px;
}

ul {
  padding: 0;
  display: flex;
  flex-direction: column-reverse;
}

li {
  list-style-type: none;
  display: flex;
  align-items: center;
}

.todo {
  padding: 5px 10px;
  margin: 5px 10px;
}

.complete {
  background-color: #b8b8ff;
  text-decoration: line-through;
}
</style>
