<template>
  <div
      class="game-board__cell"
      :style="{
      backgroundColor: cell.color,
      border: isSelected ? '2px solid #000' : 'none'
    }"
      @click="handleClick"
  ></div>
</template>
<script>
export default {
  props: {
    rowIndex: Number,
    colIndex: Number,
    cell: Object,
    isSelected: Boolean,
    handleCellClick: Function,
  },
  methods: {
    handleClick(e) {
      this.handleCellClick(this.rowIndex, this.colIndex, e);
    }
  }
};
</script>
<style lang="less" scoped>
@import '../styles/_board-cell.less';
</style>