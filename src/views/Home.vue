<template>
  <div class="d-flex justify-center align-center">
    <todo-list />
  </div>
</template>

<script>
import todoList from '../components/todoList.vue';
export default {
  name: 'Home',
  components: {
    todoList,
  },
};
</script>
