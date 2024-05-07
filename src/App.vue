<template>
  <div id="app">
    <chat-component></chat-component>
  </div>
</template>

<script>
import ChatComponent from "./components/ChatComponent.vue";

export default {
  name: "App",
  components: {
    ChatComponent,
  },
};
</script>

<style>
@import "../style.css";
</style>
