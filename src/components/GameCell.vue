<template>
  <div class="game-cell"
    :class="owner"
    @click="$emit('click')"
  >
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  props: {
    owner: { type: String }
  }
})
</script>

<style lang="scss" scoped>
.game-cell {
  border: 1px solid gray;
  &.p1 {
    background-image: url(../assets/stone_p1.svg)
  }
  &.p2 {
    background-image: url(../assets/stone_p2.svg)
  }
}
</style>
