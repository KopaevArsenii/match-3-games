<template>
  <div id="app">
    <game-board/>
  </div>
</template>
<script>
import GameBoard from './components/GameBoard.vue';
export default {
  name: 'App',
  components: {
    GameBoard
  }
};
</script>
<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
}
</style>