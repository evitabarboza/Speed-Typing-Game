<template>
  <div class="container">
    <h1 style="color: antiquewhite;">Speed Typing Game</h1>
    <TypingGame />
  </div>
</template>

<script>
import TypingGame from './components/TypingGame.vue';

export default {
  components: {
    TypingGame
  }
};
</script>

<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: rgb(11, 16, 43); /* Applies the color to the whole screen */
}

.container {
  text-align: center;
  margin-top: 50px;
  font-family: Arial, sans-serif;
}

</style>