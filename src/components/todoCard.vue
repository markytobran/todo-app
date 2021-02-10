<template>
  <v-card class="d-flex mb-5">
    <v-row>
      <v-col cols="2" class="d-flex justify-start align-center">
        <v-btn
          x-small
          rounded
          color="success"
          dark
          class="ml-2"
          v-if="todoData.completed === true"
          @click="editTodo(false)"
        >
          Completed
        </v-btn>
        <v-btn
          x-small
          rounded
          color="error"
          dark
          class="ml-2"
          v-else
          @click="editTodo(true)"
        >
          Not completed
        </v-btn>
      </v-col>
      <v-col cols="7" class="d-flex justify-start align-center ml-2">
        <v-card-title v-if="!isEdit">
          {{ todoData.title }}
        </v-card-title>
        <v-text-field single-line hide-details v-model="todoTitle" v-else>
        </v-text-field>
      </v-col>
      <v-col cols="1" class="d-flex justify-end align-center">
        <v-btn fab dark small color="cyan" @click="showInput" v-if="!isEdit">
          <v-icon dark>
            mdi-pencil
          </v-icon>
        </v-btn>
        <v-btn rounded color="primary" dark v-else @click="editTodo">
          Save
        </v-btn>
      </v-col>
      <v-col cols="1">
        <v-btn
          :loading="loading"
          class="ma-5"
          color="error"
          plain
          @click="deleteTodo"
        >
          Delete
        </v-btn>
      </v-col>
    </v-row>
  </v-card>
</template>

<script>
export default {
  props: ['todoData', 'todoIndex'],
  data() {
    return {
      loading: false,
      isEdit: false,
      todoTitle: null,
    };
  },
  methods: {
    deleteTodo() {
      this.loading = true;
      setTimeout(
        () => this.$emit('removeTodo', this.todoIndex, this.todoData.id),
        2000
      );
    },
    showInput() {
      this.isEdit = true;
    },
    editTodo(isCompleted) {
      this.$emit('editTodo', {
        index: this.todoIndex,
        title: this.todoTitle,
        id: this.todoData.id,
        completed: isCompleted,
      });
      this.isEdit = false;
    },
  },
  mounted() {
    this.todoTitle = this.todoData.title;
  },
};
</script>
