<script setup>
import TicTacToe from './components/TicTacToe.vue'
import StarsBackground from './components/StarsBackground.vue'
</script>

<template>
  <StarsBackground class="app-bg">
    <main>
      <TicTacToe />
    </main>
  </StarsBackground>
</template>

<style scoped>
.app-bg {
  min-height: 100vh;
}

main {
  position: relative;
  display: flex;
  justify-content: center;
  padding: 2rem 0;
  z-index: 1;
}
</style>
