<template>
  <v-container>
    <v-row class="d-flex justify-center align-center">
      <v-col cols="8">
        <v-alert v-if="error" color="red lighten-2" dark>
          {{ error }}
        </v-alert>
        <v-alert v-if="success" type="success">
          {{ success }}
        </v-alert>
      </v-col>
    </v-row>
    <!--Input Field-->
    <v-row class="d-flex justify-center align-center mt-3 mb-3">
      <v-col cols="8">
        <v-card class="d-flex justify-space-between">
          <v-col cols="11">
            <v-card-title>
              <v-text-field
                v-model="textValue"
                label="Add a new todo"
                single-line
                hide-details
              >
              </v-text-field>
            </v-card-title>
          </v-col>
          <v-col cols="1" class="d-flex justify-end align-end">
            <v-btn class="mx-2" fab dark color="indigo" @click="postNewTodo">
              <v-icon dark>
                mdi-plus
              </v-icon>
            </v-btn>
          </v-col>
        </v-card>
      </v-col>
    </v-row>
    <!--Dropdown-->
    <v-row>
      <v-col cols="10" class="d-flex justify-end align-end mb-8">
        <v-btn color="primary" dark @click="sortTodos">
          Sort Todos by Name
        </v-btn>
      </v-col>
    </v-row>
    <!-- Cards-->
    <v-row class="d-flex justify-center align-center">
      <v-col cols="7" v-if="todos.length > 0">
        <todo-card
          v-for="(todo, index) in filteredTodos"
          :key="todo.id"
          :todoData="todo"
          :todoIndex="index"
          @removeTodo="deleteTodo"
          @editTodo="updateTodo"
        />
      </v-col>
      <h2 v-else>Woohoo, nothing left todo!</h2>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';
import todoCard from './todoCard.vue';

export default {
  data: () => ({
    textValue: '',
    todos: [],
    error: '',
    success: '',
  }),
  components: {
    todoCard,
  },
  computed: {
    filteredTodos() {
      return this.todos.filter((el) => {
        if (el.title.includes(this.textValue)) {
          return el;
        }
      });
    },
  },
  methods: {
    async getTodos() {
      try {
        const response = await axios.get(
          'https://jsonplaceholder.typicode.com/todos?_page=1&_limit=5'
        );
        this.setTodos(response.data);
      } catch (error) {
        if (error)
          this.setErrorMessage('Something went wrong. Please try again later');
      }
    },
    async postNewTodo() {
      if (this.textValue.length === 0) {
        this.setErrorMessage('Please enter a valid todo');
        return;
      }
      fetch('https://jsonplaceholder.typicode.com/posts', {
        method: 'POST',
        body: JSON.stringify({
          title: this.textValue,
          userId: 1,
        }),
        headers: {
          'Content-type': 'application/json; charset=UTF-8',
        },
      })
        .then((response) => response.json())
        .then((json) => this.addNewTodo(json))
        .catch((error) => {
          if (error)
            this.setErrorMessage(
              'Something went wrong. Please try again later'
            );
        });
    },
    async updateTodo(value) {
      const { index, title, id, completed } = value;
      fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
        method: 'PATCH',
        body: JSON.stringify({
          title,
          completed,
        }),
        headers: {
          'Content-type': 'application/json; charset=UTF-8',
        },
      })
        .then((response) => response.json())
        .then((json) => this.updatingTodo(json, index));
    },
    async deleteTodo(index, id) {
      try {
        await axios.delete(`https://jsonplaceholder.typicode.com/posts/${id}`);
        this.todos.splice(index, 1);
      } catch (error) {
        if (error)
          this.setErrorMessage('Something went wrong. Please try again later');
      }
    },
    setTodos(data) {
      this.todos = data;
    },
    addNewTodo(todo) {
      this.setSuccessMessage('New todo was successfully added');
      const { id, title, userId } = todo;

      const newTodo = {
        completed: false,
        id,
        title,
        userId,
      };

      this.todos.unshift(newTodo);
      this.resetInputField();
    },
    updatingTodo(todo, index) {
      this.todos[index].title = todo.title;
      this.todos[index].completed = todo.completed;
    },
    resetInputField() {
      this.textValue = '';
    },
    setSuccessMessage(text) {
      this.success = text;
      setTimeout(() => (this.success = ''), 3000);
    },
    setErrorMessage(text) {
      this.error = text;
      setTimeout(() => (this.error = ''), 3000);
    },
    sortTodos() {
      const sortedTodos = this.todos.sort((a, b) =>
        a.title.localeCompare(b.title)
      );
      this.todos = sortedTodos;
    },
  },
  created() {
    this.getTodos();
  },
};
</script>
