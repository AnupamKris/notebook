<template>
  <div class="sidebar">
    <div class="control-buttons">
      <button @click="toggleTheme" class="first">
        <span class="material-symbols-outlined">
          {{ theme === "light" ? "dark_mode" : "light_mode" }}
        </span>
      </button>
      <button @click="closeProject">
        <span class="material-symbols-rounded"> home </span>
      </button>
      <button @click="createProject">
        <span>
          <span class="material-symbols-outlined"> add </span>
        </span>
      </button>
    </div>
    <div class="projects">
      <div
        class="project"
        v-for="project in projects"
        :key="project.id"
        @click="selectProject(project.id)"
      >
        <div class="title">
          <p>
            {{ project.title }}
          </p>
          <p class="time">
            {{ project.lastUpdated.toLocaleString() }}
          </p>
        </div>
        <span
          class="material-symbols-outlined"
          :style="project.favorite ? filled : outlined"
          @click="addToFavorite(project)"
        >
          favorite
        </span>
      </div>
    </div>
    <div class="pop-out">
      <button @click="turnOnPopOut">
        <span class="material-symbols-outlined"> open_in_new </span>
      </button>
    </div>
  </div>
</template>

<script setup>
const props = defineProps([
  "theme",
  "toggleTheme",
  "projects",
  "selectProject",
  "createProject",
  "addToFavorite",
  "closeProject",
  "turnOnPopOut",
]);
const filled =
  "font-variation-settings:'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24";
const outlined =
  "font-variation-settings:'FILL' 0, 'wght' 400, 'GRAD' 0, 'opsz' 24";
</script>

<style lang="scss" scoped>
.material-symbols-rounded,
.material-symbols-outlined {
  font-variation-settings: "FILL" 0, "wght" 300, "GRAD" 0, "opsz" 40;
}

.sidebar {
  min-width: 300px;
  height: 100%;
  overflow-y: auto;
  background-color: var(--color-background-tertiary);
  color: var(--color-text);
  // padding-top: 60px;
  border-right: 1px solid var(--color-border);
  transition: 0.3s;
  position: relative;

  .pop-out {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 10px;

    height: 40px;
    width: 40px;

    position: absolute;
    bottom: 10px;
    right: 10px;
    border-radius: 50%;

    background-color: var(--color-background-secondary);

    button {
      cursor: pointer;
    }
  }

  .control-buttons {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border-bottom: 1px solid var(--color-border);
    height: 60px;

    button {
      cursor: pointer;
    }

    .first {
      margin-right: auto;
    }
  }

  .projects {
    padding: 10px;
    overflow-y: auto;
    height: calc(100% - 60px);
    position: sticky;
    // top: 60px;

    .project {
      padding: 10px;
      cursor: pointer;
      transition: 0.3s;
      display: flex;
      justify-content: space-between;
      align-items: center;

      .title {
        p {
          margin: 0;
        }

        .time {
          font-size: 8px;
          color: var(--color-text-secondary);
        }
      }

      &:hover {
        background-color: var(--color-background-secondary);
      }
    }
  }
}
</style>
