<script setup>
import Score from './components/Score.vue'
</script>

<template>
  <div class="app">
    <Score class="app__score"/>
  </div>
</template>

<style scoped>
.app {
  background-color: #121212;
  color: #fff;
  padding: 2em;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.app__score {
  width: 100%;
  max-width: 600px;
}
</style>
