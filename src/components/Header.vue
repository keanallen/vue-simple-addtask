<template>
  <header>
    <h1>{{ title }}</h1>
    <AddTaskButton
      @btn-click="$emit('toggle-task-form')"
      :text="showAddTask ? 'Close' : 'Add Task'"
      :color="showAddTask ? 'red' : 'green'"
    />
  </header>
</template>

<script>
import AddTaskButton from "./AddTaskButton.vue";
export default {
  name: "Header",
  props: {
    title: String,
    showAddTask: Boolean,
  },
  components: { AddTaskButton },
};
</script>



<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
</style>