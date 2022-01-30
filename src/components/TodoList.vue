<template>
  <div class="component">
    <h1 class="component__title">Lista de Tarefas</h1>
    <div class="component__task--add">
      <input
        type="text"
        aria-label="Adicione uma nova tarefa"
        placeholder="Adicione uma nova tarefa"
        v-model="taskName"
        @keyup.enter="addTodo"
      />
      <button @click="addTodo">+</button>
    </div>

    <!-- <ul>
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
    </ul> -->
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
      taskName: "",
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
          name: this.taskName,
          done: false,
        });
        this.todos = [...this.todos, res.data];
        this.taskName = "";
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
.component {
  margin: 32px;
}
.component__title {
  color: #fd9644;
  margin: 16px 8px;
}
.component__task--add {
    display: flex;
    align-items: center;
}
.component__task--add input {
    width: 70%;
    border-radius: 0.4rem;
    padding: 8px;
    border: 1px solid #6b6b6a;
    margin: 16px 8px;
}
.component__task--add input:focus {
    outline-color: #fd9644;
    
}
.component__task--add button { 
    width: 30%;
    background-color: #fd9644;
    border: 1px solid #fd9644;
    color: #ffff;
    font-weight: bold;
    border-radius: 50px;
    width: 30px;
    height: 30px;
    font-size: 24px;
}
li {
  color: white;
}



.done {
  text-decoration: line-through;
}
</style>
