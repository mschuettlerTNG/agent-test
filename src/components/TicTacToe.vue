<script setup>
import { ref, computed, nextTick } from 'vue'
import confetti from 'canvas-confetti'
import GlowBorder from './GlowBorder.vue'

const board = ref(Array(9).fill(null))
const currentPlayer = ref('X')
const winner = ref(null)
const confettiFired = ref(false)

const winLines = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8],
  [0, 3, 6], [1, 4, 7], [2, 5, 8],
  [0, 4, 8], [2, 4, 6],
]

const winningLine = computed(() => {
  for (const line of winLines) {
    const [a, b, c] = line
    if (board.value[a] && board.value[a] === board.value[b] && board.value[a] === board.value[c]) {
      return line
    }
  }
  return null
})

const isDraw = computed(() => !winner.value && board.value.every(cell => cell !== null))

const status = computed(() => {
  if (winner.value) return `Player ${winner.value} wins!`
  if (isDraw.value) return "It's a draw!"
  return `Player ${currentPlayer.value}'s turn`
})

function fireConfetti() {
  const duration = 3000
  const end = Date.now() + duration
  const colors = ['#42b883', '#e5534b', '#ffd700', '#00bfff', '#ff69b4']

  ;(function frame() {
    confetti({
      particleCount: 3,
      angle: 60,
      spread: 55,
      origin: { x: 0, y: 0.7 },
      colors,
    })
    confetti({
      particleCount: 3,
      angle: 120,
      spread: 55,
      origin: { x: 1, y: 0.7 },
      colors,
    })

    if (Date.now() < end) {
      requestAnimationFrame(frame)
    }
  })()
}

function makeMove(index) {
  if (board.value[index] || winner.value) return
  board.value[index] = currentPlayer.value

  const line = winningLine.value
  if (line) {
    winner.value = currentPlayer.value
    if (!confettiFired.value) {
      confettiFired.value = true
      nextTick(() => fireConfetti())
    }
  } else {
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
  }
}

function resetGame() {
  board.value = Array(9).fill(null)
  currentPlayer.value = 'X'
  winner.value = null
  confettiFired.value = false
}
</script>

<template>
  <div class="game">
    <h2>Tic Tac Toe</h2>
    <p class="status" :class="{ 'status-win': winner, 'status-draw': isDraw }">{{ status }}</p>
    <div class="board">
      <button
        v-for="(cell, index) in board"
        :key="index"
        class="cell"
        :class="{
          x: cell === 'X',
          o: cell === 'O',
          winning: winningLine && winningLine.includes(index),
        }"
        :disabled="!!cell || !!winner"
        @click="makeMove(index)"
      >
        <GlowBorder
          v-if="!cell && !winner"
          :color="['#42b883', '#35495e', '#42b883']"
          :border-radius="8"
          :border-width="2"
          :duration="4"
        />
        <span class="cell-content">{{ cell }}</span>
      </button>
    </div>
    <button class="reset" @click="resetGame">New Game</button>
  </div>
</template>

<style scoped>
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.2rem;
}

h2 {
  font-size: 1.8rem;
  color: var(--color-heading);
}

.status {
  font-size: 1.2rem;
  font-weight: 600;
  min-height: 1.6rem;
}

.status-win {
  color: #42b883;
}

.status-draw {
  color: #e5534b;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 96px);
  grid-template-rows: repeat(3, 96px);
  gap: 6px;
}

.cell {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 96px;
  height: 96px;
  font-size: 2.5rem;
  font-weight: 700;
  border: 2px solid var(--color-border);
  border-radius: 8px;
  background: var(--color-background-soft);
  color: var(--color-text);
  cursor: pointer;
  transition: background 0.15s, border-color 0.15s;
  overflow: hidden;
}

.cell:hover:not(:disabled) {
  background: var(--color-background-mute);
  border-color: var(--color-border-hover);
}

.cell:disabled {
  cursor: default;
}

.cell.x {
  color: #42b883;
}

.cell.o {
  color: #e5534b;
}

.cell.winning {
  background: hsla(160, 100%, 37%, 0.15);
  border-color: hsla(160, 100%, 37%, 0.5);
}

.cell-content {
  position: relative;
  z-index: 1;
}

.reset {
  margin-top: 0.5rem;
  padding: 0.6rem 1.6rem;
  font-size: 1rem;
  font-weight: 600;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  background: hsla(160, 100%, 37%, 1);
  color: #fff;
  transition: background 0.2s;
}

.reset:hover {
  background: hsla(160, 100%, 37%, 0.8);
}
</style>
