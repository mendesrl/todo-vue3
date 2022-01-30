<template>
  <div>
    <h1>Todo List</h1>
    <input
      type="text"
      v-model="todoName"
      @keyup.enter="addTodo"
      aria-label="Adicine uma nova tarefa"
      placeholder="Adicine uma nova tarefa"
    />
    <ul>
      <li v-for="todo of todos" :key="todo.id">
        {{ todo.name }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

const baseURL = "http://localhost:3001/todos";

export default {
  name: "TodoList",
  data() {
    return {
      todos: [],
      todoName: "",
    };
  },
  async created() {
    try {
      const res = await axios.get(baseURL);

      this.todos = res.data;
    } catch (e) {
      console.error(e);
    }
  },
  methods: {
    async addTodo() {
      try {
        const res = await axios.post(baseURL, { name: this.todoName, done:false });
        this.todos = [...this.todos, res.data];
        this.todoName = "";
      } catch (e) {
        console.error(e);
      }
    },
  },
};
</script>

<style scoped>
h1 {
  text-decoration: underline;
}

li {
  color: white;
}

input {
  width: 100%;
  padding: 1rem;
  border-radius: 0.4rem;
  border: 1px solid #fd9644;
  margin-bottom: 2rem;
  font-size: 1.5rem;
}
</style>
