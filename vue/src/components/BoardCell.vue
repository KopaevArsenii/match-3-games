<template>
  <div
      class="game-board__cell"
      :style="{
      backgroundColor: this.color,
      border: isSelected ? '2px solid #000' : 'none'
    }"
      @click="handleClick"
  >
  </div>
</template>
<script>
export default {
  props: {
    rowIndex: Number,
    colIndex: Number,
    color: String,
    isSelected: Boolean,
    handleCellClick: Function,
  },
  methods: {
    handleClick() {
      this.handleCellClick(this.rowIndex, this.colIndex);
    }
  }
};
</script>
<style lang="less" scoped>
@import '../styles/_board-cell.less';
</style>