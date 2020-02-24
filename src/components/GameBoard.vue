<template>
  <div class="game-board" ref="nekoneko">
    <game-cell class="game-cell"
      v-for="(cell, index) in cells" :key="index"
      :owner="cell.owner"
      @click="placeStone(index)"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue'
import GameCell from './GameCell.vue'

type Player = 'p1' | 'p2'
type Owner = Player | undefined
interface CellState {
  owner: Owner
}
interface GameState {
  cells: CellState[],
  turn: Player
}

/** Player1を示す定数（画像ファイルの名前に使用するためSymbolにはしていません） */
const P1: Player = 'p1'
/** Player2を示す定数（画像ファイルの名前に使用するためSymbolにはしていません） */
const P2: Player = 'p2'
/** まだ石が置かれていないセル */
const BLANK: Owner = undefined
/** プレイヤー毎の置かれる石（文字） */
const STONES = { [P1]: '○', [P2]: '×' }
/** 一直線に並ぶセルの組み合わせ。8個しかないので列挙 */
const LINES = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 4, 8],
  [2, 4, 6]
]

export default defineComponent({
  components: { GameCell },
  setup (props, ctx) {
    // 1. 元 data = reactive(), ref()でラップする
    const cells = ref<CellState[]>(Array(9).fill(0).map(_ => ({ owner: BLANK })))
    let turn = ref<Player>(P1)

    // 2. 元 computedの要素 = computed()でラップする
    const winner = computed<Player | undefined>(() => {
      let winnerPName: Player | undefined
      LINES.forEach(line => {
        const ownersInLine = line.map(index => cells.value[index].owner)
        const isAllSame = ownersInLine[0] === ownersInLine[1] && ownersInLine[0] === ownersInLine[2]
        if (isAllSame && ownersInLine[0] !== BLANK) {
          winnerPName = ownersInLine[0]
        }
      })
      return winnerPName
    })
    const isGameEnded = computed<boolean>(() => {
      return !!(winner.value || cells.value.filter(cell => cell.owner === BLANK).length === 0)
    })

    // 3. 元 methodsの要素 = そのまま（素の関数）
    const placeStone = (index: number) => {
      if (isGameEnded.value) { return }
      if (cells.value[index].owner !== BLANK) { return }
      cells.value[index].owner = turn.value
      if (isGameEnded.value) {
        // どちらかの勝ち or 引き分け
        ctx.emit('gameEnd', winner.value ? STONES[winner.value] : undefined)
      } else {
        // ターン交代
        turn.value = turn.value === P1 ? P2 : P1
        ctx.emit('nextTurn', STONES[turn.value])
      }
    }

    // 4. 元 ライフサイクルフック = on + フック名の関数でラップ
    onMounted(() => {
      // 最初のターンを通知するためにイベントを発行
      ctx.emit('nextTurn', STONES[turn.value])
    })

    // 最後にテンプレートから参照される物を全て返す
    return {
      cells,
      turn,
      winner,
      isGameEnded,
      placeStone
    }
  }
})
</script>

<style lang="scss" scoped>
.game-board{
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  .game-cell {
    box-sizing: border-box;
    width: 33%;
    margin: 0 -1px -1px 0;
    font-size: 100px;
    text-align: bottom;
    line-height: 100%;
    &::before {
      content: '';
      padding-top: 100%;
      display: block;
    }
  }
}
</style>
