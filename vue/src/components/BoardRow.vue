<template>
  <div class="game-board__row">
    <BoardCell
        v-for="(cell, colIndex) in row"
        :key="colIndex"
        :rowIndex="rowIndex"
        :colIndex="colIndex"
        :cell="cell"
        :isSelected="isSelected(rowIndex, colIndex)"
        :handleCellClick="handleCellClick"
    />
  </div>
</template>
<script>
import BoardCell from './BoardCell.vue';
export default {
  components: {
    BoardCell
  },
  props: {
    row: Array,
    rowIndex: Number,
    isSelected: Function,
    handleCellClick: Function,
  }
};
</script>
<style lang="less" scoped>
@import '../styles/_board-row.less';
</style>