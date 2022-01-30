<template>
  <div class="component">
    <h1 class="component__title">Lista de Tarefas</h1>

    <div class="component__task--add">
      <div :class="{ error: invalidField }">
        <input
          type="text"
          aria-label="Adicione uma nova tarefa"
          placeholder="Adicione uma nova tarefa"
          v-model="taskName"
          @keyup.enter="addTask"
          @click="invalidField = false"
        />
        <span v-show="invalidField">O campo está vazio!</span>
      </div>

      <button @click="addTask">+</button>
    </div>

    <div class="component__task--list">
      <ul>
        <li v-for="todo of todos" :key="todo.id">
          <div>
            <span :class="{ done: todo.done }" @click="doneTask(todo.id)">
              {{ todo.name }}
            </span>
            <div @click="deleteTodo(todo.id)">x</div>
          </div>
        </li>
      </ul>
    </div>
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
      invalidField: false,
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
    async addTask() {
      if (!this.validTask()) {
        this.invalidField = true;
      }
      if (this.validTask()) {
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
      }
    },
    async doneTask(id) {
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
    validTask() {
      if (this.taskName != "") {
        return true;
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
.component__task--add div {
  width: 70%;
  margin: 16px 8px;
}

.component__task--add div input {
  border-radius: 0.4rem;
  padding: 8px;
  border: 1px solid #6b6b6a;
}

.component__task--add div input:focus {
  outline: none;
  border: 1px solid #fd9644;
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
  cursor: pointer;
}
.component__task--list {
  margin: 8px;
}
.component__task--list ul {
  list-style: none;
  max-width: 250px;
}

.component__task--list li {
  width: 100%;
  height: 32px;
}

.component__task--list li div {
  display: flex;
}
.component__task--list li div span {
  width: 90%;
  word-break: break-all;
}
.error input {
  border: 1px solid #fd4444;
}
.error span {
  color: #fd4444;
  font-size: 12px;
  display: block;
}
.done {
  text-decoration: line-through;
}
</style>
