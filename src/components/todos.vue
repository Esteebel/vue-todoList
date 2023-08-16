<template>
  <div class="all">
    <h2 class="head">{{ name }}</h2>
    <input
      v-model="newTodo"
      type="text"
      class="todo-input"
      @keyup.enter="addTodo"
      placeholder="What needs to be done"
    />
    <div v-for="(todo, index) in todos" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed" />
        <div
          v-if="!todo.editing"
          @click="editTodo(todo)"
          class="todo-item-label"
          :class="{ completed: todo.completed }"
        >
          {{ todo.title }}
        </div>
        <input
          @blur="doneEdit(todo)"
          @keyup.enter="doneEdit(todo)"
          @keyup.esc="cancelEdit(todo)"
          v-focus
          v-else
          class="todo-item-edit"
          type="text"
          v-model="todo.title"
        />
      </div>
      <div class="remove-item" @click="removeTodo(index)">&times;</div>
    </div>

    <div class="another-container">
      <div>
        <label>
          <input
            type="checkbox"
            :checked="!noneRemaining"
            @change="checkAllTodos"
          />Check All
        </label>
      </div>
      <div>{{ remaining }} items left</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "todo-list",
  data() {
    return {
      name: "LIST OF THINGS TO DO",
      newTodo: "",
      todos: [],
    };
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    noneRemaining() {
      return this.remaining !== 0;
    },
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },
  methods: {
    async addTodo() {
      if (this.newTodo.trim().length === 0) {
        return;
      }
      const response = await axios.post("http://localhost:3000/todos", {
        title: this.newTodo,
        completed: false,
      });
      this.todos.push(response.data);
      this.newTodo = "";
    },
    async editTodo(todo) {
      todo.editing = true;
    },
    async doneEdit(todo) {
      if (todo.title.trim().length === 0) {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
      await axios.put(`http://localhost:3000/todos/${todo.id}`, todo);
    },
    async removeTodo(index) {
      const todo = this.todos[index];
      await axios.delete(`http://localhost:3000/todos/${todo.id}`);
      this.todos.splice(index, 1);
    },
    async checkAllTodos(event) {
      const checked = event.target.checked;
      await Promise.all(
        this.todos.map((todo) =>
          axios.put(`http://localhost:3000/todos/${todo.id}`, {
            ...todo,
            completed: checked,
          })
        )
      );
    },
    async fetchTodos() {
      const response = await axios.get("http://localhost:3000/todos");
      this.todos = response.data;
    },
  },
  created() {
    this.fetchTodos();
  },
};
</script>

<style lang="scss">
.head {
  text-decoration: underline;
  color: #2c3e50;
  margin-top: 70px;
}

.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 20px;
  margin-top: 50px;

  &:focus {
    outline: 0;
  }
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  padding: 10px;
  border: 1px solid #ccc;
  font-family: "avenir", Helvetica, sans-serif;

  &:focus {
    outline: none;
  }
}

.completed {
  text-decoration: line-through;
  color: grey;
}

.another-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-top: 14px;
}

button {
  font-size: 14px;
  background-color: white;
  appearance: none;

  &:focus {
    outline: none;
  }
}

.active {
  background-color: lightgreen;
}
</style>
