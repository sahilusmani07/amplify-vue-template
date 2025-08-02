<script setup lang="ts">
import '@/assets/main.css';
import { onMounted, ref } from 'vue';
import type { Schema } from '../../amplify/data/resource';
import { generateClient } from 'aws-amplify/data';

const client = generateClient<Schema>();
const todos = ref<Array<Schema['Todo']["type"]>>([]);

function deleteTodo(id: string) {
  client.models.Todo.delete({ id });
}

function listTodos() {
  client.models.Todo.observeQuery().subscribe({
    next: ({ items }) => {
      todos.value = items;
    },
  });
}

function createTodo() {
  const content = window.prompt("Todo content");
  if (content) {
    client.models.Todo.create({ content }).then(() => {
      listTodos();
    });
  }
}

onMounted(() => {
  listTodos();
});
</script>

<template>
  <main>
    <h1>My todos</h1>
    <button @click="createTodo">+ new</button>
    <ul>
      <li 
        v-for="todo in todos" 
        :key="todo.id"
        @click="deleteTodo(todo.id)"
      >
        {{ todo.content }}
      </li>
    </ul>
    <div>
      🥳 App successfully hosted. Try creating a new todo.
      <br />
      <a href="https://docs.amplify.aws/gen2/start/quickstart/nextjs-pages-router/">
        Review next steps of this tutorial.
      </a>
    </div>
  </main>
</template>
