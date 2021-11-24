<template>
  <button
    @click="$emit('btn-click')"
    :style="{ background: color }"
    class="btn"
  >
    {{ text }}
  </button>
</template>

<script>
export default {
  name: "AddTaskButton",
  props: {
    text: String,
    color: String,
  },
};
</script>