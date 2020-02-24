<template>
  <div is="app">
    <div class="msg">{{ gameMsg }}</div>
    <game-board
      @nextTurn="nextTurn"
      @gameEnd="gameEnd"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import GameBoard from '@/components/GameBoard.vue'

export default defineComponent({
  name: 'app',
  components: {
    GameBoard
  },
  setup () {
    const gameMsg = ref('')
    const nextTurn = (player: string) => {
      gameMsg.value = `${player}の番です`
    }
    const gameEnd = (winner: string) => {
      gameMsg.value = winner ? `${winner}の勝ちです` : '引き分けです'
    }
    return {
      gameMsg,
      nextTurn,
      gameEnd
    }
  }
})
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  .msg {
    font-size: 2em;
  }
}
</style>
