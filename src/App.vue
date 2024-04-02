<template>
  <main :class="theme">
    <SideBar @toggleTheme="toggleTheme" />
    <div class="container">
      <!-- <input type="text" v-model="username" />
      <input type="text" v-model="roomId" />
      <button @click="joinRoom">Join</button> -->
      <input class="title" type="text" v-model="title" />
      <TipTap />
    </div>
  </main>
</template>

<script setup>
import { Artico } from "@rtco/client";
import { fabric } from "fabric";

const roomId = ref("123");
const theme = ref("dark");

// random username
const username = ref("user" + Math.floor(Math.random() * 1000).toString());
const memberDetails = ref({});

let room;

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

onMounted(() => {});
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

    .title {
      font-size: 2rem;
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
}
</style>
