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
    <button @click="addTodo">Add</button>
    <ul>
      <li
        v-for="todo of todos"
        :key="todo.id"
        :class="{ done: todo.done }"
        style="display: flex; position: relative"
      >
        <span @click="doneTodo(todo.id)">{{ todo.name }}</span>
        <div
          @click="deleteTodo(todo.id)"
          style="
            position: absolute;
            right: 10px;
            font-size: 18px;
            border: 1px solid;
            padding: 8px;
            bottom: 20px;
          "
        >
          x
        </div>
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
  created() {
    this.loadData();
  },
  methods: {
    async loadData() {
      try {
        const res = await axios.get(baseURL);

        this.todos = res.data;
      } catch (e) {
        console.error(e);
      }
    },
    async addTodo() {
      try {
        const res = await axios.post(baseURL, {
          name: this.todoName,
          done: false,
        });
        this.todos = [...this.todos, res.data];
        this.todoName = "";
      } catch (e) {
        console.error(e);
      }
    },
    async doneTodo(id) {
      try {
        await axios.patch(`${baseURL}/${id}`, { done: true });

        this.todos = this.todos.map((todo) => {
          if (todo.id === id) {
            todo.done = true;
          }
          return todo;
        });
      } catch (e) {
        console.error(e);
      }
    },
    async deleteTodo(id) {
      try {
        await axios.delete(`${baseURL}/${id}`);

        this.loadData();
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

.done {
  text-decoration: line-through;
}
</style>
