<template>
  <main :class="theme">
    <SideBar
      :toggleTheme
      :theme
      :projects
      :createProject
      :selectProject
      :addToFavorite
      :closeProject
      :turnOnPopOut
      v-if="!popout"
    />
    <div class="container" v-if="currentProject">
      <!-- <input type="text" v-model="username" />
      <input type="text" v-model="roomId" />
      <button @click="joinRoom">Join</button> -->
      <input
        class="title"
        type="text"
        v-model="currentProject.title"
        @focus="checkEmpty"
      />
      <TipTap
        v-model="currentProject.content"
        :title="currentProject.title"
        :exportToPdf
      />
    </div>
    <div class="container c-center" v-else>
      <h3>Favorites</h3>
      <div
        class="recent-projects"
        v-if="projects.filter((p) => p.favorite).length"
      >
        <div
          class="project"
          v-for="project in projects.filter((p) => p.favorite)"
          :key="project.id"
          @click="selectProject(project.id)"
        >
          <p>{{ project.title }}</p>
          <p class="content" v-html="project.content"></p>
        </div>
      </div>

      <p v-else class="none">No favorites yet...</p>

      <h3>Recents</h3>
      <div class="recent-projects" v-if="projects.slice(0, 4).length">
        <div
          class="project"
          v-for="project in projects.slice(0, 4)"
          :key="project.id"
          @click="selectProject(project.id)"
        >
          <p>{{ project.title }}</p>
          <p class="content" v-html="project.content"></p>
        </div>
      </div>
      <p v-else class="none">No recent projects...</p>

      <button @click="createProject" class="create">
        <span class="material-symbols-outlined"> add </span>
      </button>
    </div>
  </main>
</template>

<script setup>
import { Artico } from "@rtco/client";
import { v4 } from "uuid";
import html2pdf from "html2pdf.js";

// import { fabric } from "fabric";

const popout = ref(false);
const projects = ref([]);
const currentProject = ref(null);
const roomId = ref("123");
const theme = ref("dark");

// random username
const username = ref("user" + Math.floor(Math.random() * 1000).toString());
const memberDetails = ref({});

const addToFavorite = (project) => {
  project.favorite = !project.favorite;
};

const turnOnPopOut = () => {
  // popout.value = true;
};

const checkEmpty = (e) => {
  if (currentProject.value.title === "Untitled Project") {
    e.target.select();
  }
};

const createProject = () => {
  const project = {
    title: "Untitled Project",
    content: "",
    favorite: false,
    id: v4(),
    lastUpdated: new Date(),
  };
  projects.value.push(project);
  currentProject.value = project;
};

const selectProject = (projectId) => {
  currentProject.value = null;
  const project = projects.value.find((p) => p.id === projectId);

  currentProject.value = project;
  currentProject.value.lastUpdated = new Date();
};

const closeProject = () => {
  currentProject.value = null;
};

const exportToPdf = () => {
  const element = document.getElementById("tiptap");
  html2pdf(element, {
    margin: 1,
    filename: currentProject.value.title,
    html2canvas: {
      scale: 2,
    },
    jsPDF: {
      unit: "in",
      format: "letter",
      orientation: "portrait",
    },
  });
};

let room;
let timeout;

const rtco = new Artico();

const title = ref("Title");

const toggleTheme = () => {
  theme.value = theme.value === "light" ? "dark" : "light";
};

const joinRoom = () => {
  room = rtco.join(roomId.value, { username: "John Doe" });
  room.on("open", () => {
    console.log("Room opened");
    room.send(
      JSON.stringify({
        type: "init",
        username: username.value,
      })
    );
  });

  room.on("join", (peer) => {
    console.log("Peer joined", peer);
    room.send(
      JSON.stringify({
        type: "init",
        username: username.value,
      })
    );
  });

  room.on("leave", (peer) => {
    console.log("Peer left", peer);
  });

  room.on("message", (message) => {
    let data = JSON.parse(message);
    console.log("Message received", data);
    if (data.type === "init") {
      memberDetails.value[data.username] = data;
    } else {
      console.log("Unknown message type", data);
    }
  });
};

rtco.on("open", () => {
  console.log("Connected to RTCO server", rtco.id);
});

watch(
  [currentProject],
  () => {
    console.log("Project changed", currentProject.value);

    // sorrt projects by date and then by favorite
    projects.value.sort((a, b) => {
      return b.favorite - a.favorite || b.lastUpdated - a.lastUpdated;
    });

    localStorage.setItem("projects", JSON.stringify(projects.value));
  },
  { deep: true }
);

onMounted(() => {
  let savedProjects = localStorage.getItem("projects");
  if (savedProjects) {
    projects.value = JSON.parse(savedProjects);
  }
});
</script>

<style lang="scss">
.light {
  --color-primary: #3f51b5;
  --color-text: #333;
  --color-background: #f4f4f4;
  --color-background-secondary: #adadad;
  --color-background-tertiary: #e0e0e0;

  --color-background-hover: #d4d4d4;

  --color-border: #c9c9c9;
}

.dark {
  --color-primary: #3f51b5;
  --color-text: #f4f4f4;
  --color-background: #1f1f1f;
  --color-background-secondary: #292929;
  --color-background-tertiary: #1a1a1a;

  --color-background-hover: #1f1f1f;

  --color-border: #292929;
}

.code-block {
  background: var(--color-background-tertiary);
  padding: 10px;
  border-radius: 5px;
  margin: 10px 0;
}

.code {
  background: var(--color-background-tertiary);
  padding: 5px;
  // border-radius: 5px;
  margin: 10px 0;
  font-family: Consolas;
}

main {
  display: flex;
  height: 100vh;

  .container {
    padding: 20px;

    height: 100%;
    width: 100%;

    background: var(--color-background);
    transition: 0.3s;

    position: relative;

    overflow-y: auto;

    &::-webkit-scrollbar {
      display: none;
    }

    /* Hide scrollbar for IE, Edge and Firefox */

    -ms-overflow-style: none;
    /* IE and Edge */
    scrollbar-width: none;
    /* Firefox */

    .title {
      font-size: 24px;
      font-weight: bold;
      margin-bottom: 20px;
      border: none;
      width: 100%;
      outline: none;
      border-radius: 0px;

      background: transparent;

      color: var(--color-text);
      border-bottom: 1px solid var(--color-border);
      transition: 0.3s;
    }
  }

  .c-center {
    display: flex;
    // justify-content: center;
    align-items: center;
    flex-direction: column;
    position: relative;

    color: var(--color-text);
    overflow-y: auto;

    &::-webkit-scrollbar {
      display: none;
    }

    /* Hide scrollbar for IE, Edge and Firefox */

    -ms-overflow-style: none;
    /* IE and Edge */
    scrollbar-width: none;
    /* Firefox */

    h3 {
      width: 100%;
      // margin-top: 20px;
    }

    .recent-projects {
      width: 100%;
      min-height: 250px;
      display: flex;
      gap: 10px;
      margin-top: 20px;

      overflow-x: auto;

      &::-webkit-scrollbar {
        display: none;
      }

      /* Hide scrollbar for IE, Edge and Firefox */

      -ms-overflow-style: none;
      /* IE and Edge */
      scrollbar-width: none;
      /* Firefox */

      .project {
        width: 170px;
        min-width: 170px;
        height: 220px;
        border-radius: 10px;
        cursor: pointer;
        transition: 0.3s;
        display: flex;
        // justify-content: space-between;
        flex-direction: column;
        align-items: center;
        background: var(--color-background-tertiary);
        position: relative;

        overflow: hidden;
        padding-bottom: 10px;

        &:before {
          content: "";
          width: 100%;
          height: 100%;
          position: absolute;
          left: 0;
          top: 0;
          // background: linear-gradient(transparent 150px, var(--color-background-tertiary));
          background: rgb(74, 135, 255);
          background: linear-gradient(
            180deg,
            transparent 0%,
            transparent 150px,
            var(--color-background-tertiary) 210px,
            var(--color-background-tertiary) 220px
          );
        }

        p {
          padding: 10px;
          width: 100%;
        }

        .content {
          padding: 0;
          font-size: 8px;

          p {
            padding: 0 10px;
          }
        }

        &:hover {
          background-color: var(--color-background-secondary);
        }
      }
    }

    .none {
      width: 100%;
    }

    .create {
      position: fixed;
      bottom: 20px;
      right: 20px;

      border-radius: 50%;

      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      background: var(--color-background-secondary);

      color: var(--color-text);
    }
  }
}
</style>
