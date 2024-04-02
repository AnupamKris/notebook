<template>
  <div class="sidebar">
    <button @click="toggleTheme">Dark</button>
  </div>
</template>

<script setup>
const emit = defineEmits(["toggleTheme"]);

const toggleTheme = () => {
  emit("toggleTheme");
};
</script>

<style lang="scss" scoped>
.sidebar {
  min-width: 300px;
  height: 100%;
  background-color: var(--color-background-tertiary);
  //   position: fixed;
  //   top: 0;
  //   left: 0;
  //   z-index: 100;
  padding-top: 60px;
  border-right: 1px solid var(--color-border);
  transition: 0.3s;
}
</style>
