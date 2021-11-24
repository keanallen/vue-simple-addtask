<template>
  <div v-for="task in tasks" :key="task.objectId">
    <task-item
      @toggle-reminder="$emit('toggle-reminder', task.objectId)"
      @delete-task="$emit('delete-task', task.objectId)"
      :task="task"
    />
  </div>
</template>

<script>
import TaskItem from "./TaskItem.vue";
export default {
  name: "Tasks",
  components: { TaskItem },
  props: {
    tasks: Array,
  },
  emits: ["delete-task", "toggle-reminder"],
};
</script>