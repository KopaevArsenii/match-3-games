<template>
  <div class="game-board" @click.self="clearSelection">
    <BoardRow
        v-for="(row, rowIndex) in board"
        :key="rowIndex"
        :row="row"
        :rowIndex="rowIndex"
        :isSelected="isSelected"
        :handleCellClick="handleCellClick"
    />
  </div>
</template>
<script>
import BoardRow from './BoardRow.vue';
export default {
  components: {
    BoardRow
  },
  data() {
    return {
      boardSize: 8,
      board: [],
      selectedCell: null,
    };
  },
  methods: {
    generateBoard() {
      const colors = [
        "#FF0000", "#024217", "#0000FF", "#FFFF00", "#FF00FF", "#00FFFF", "#a87532", "#28fa6e"
      ];
      this.board = Array.from({ length: this.boardSize }, () =>
          Array.from({ length: this.boardSize }, () => ({
            color: colors[Math.floor(Math.random() * colors.length)],
          }))
      );
    },
    handleCellClick(rowIndex, colIndex) {
      if (this.selectedCell) {
        const [selectedRow, selectedCol] = this.selectedCell;
        if (
            (Math.abs(selectedRow - rowIndex) === 1 && selectedCol === colIndex) ||
            (Math.abs(selectedCol - colIndex) === 1 && selectedRow === rowIndex)
        ) {
          const backupBoard = JSON.parse(JSON.stringify(this.board));
          const temp = this.board[rowIndex][colIndex].color;
          this.board[rowIndex][colIndex].color = this.board[selectedRow][selectedCol].color;
          this.board[selectedRow][selectedCol].color = temp;

          if (this.checkAndClearLines()) {
            this.dropBalls();
            this.generateNewBalls();
          } else {
            this.board = backupBoard;
          }
        }
        this.selectedCell = null;
      } else {
        this.selectedCell = [rowIndex, colIndex];
      }
    },
    clearSelection() {
      this.selectedCell = null;
    },
    isSelected(rowIndex, colIndex) {
      return this.selectedCell && this.selectedCell[0] === rowIndex && this.selectedCell[1] === colIndex;
    },
    checkAndClearLines() {
      let hasLineCleared = false;
      let updatedBoard = [...this.board];
      this.board.forEach((row, rowIndex) => {
        row.forEach((_, colIndex) => {
          if (colIndex < this.boardSize - 2) {
            const color = row[colIndex].color;
            if (color === row[colIndex + 1].color && color === row[colIndex + 2].color) {
              updatedBoard[rowIndex][colIndex].color = '';
              updatedBoard[rowIndex][colIndex + 1].color = '';
              updatedBoard[rowIndex][colIndex + 2].color = '';
              hasLineCleared = true;
            }
          }
        });
      });
      this.board.forEach((row, rowIndex) => {
        if (rowIndex < this.boardSize - 2) {
          row.forEach((_, colIndex) => {
            const color = this.board[rowIndex][colIndex].color;
            if (
                color === this.board[rowIndex + 1][colIndex].color &&
                color === this.board[rowIndex + 2][colIndex].color
            ) {
              updatedBoard[rowIndex][colIndex].color = '';
              updatedBoard[rowIndex + 1][colIndex].color = '';
              updatedBoard[rowIndex + 2][colIndex].color = '';
              hasLineCleared = true;
            }
          });
        }
      });
      if (hasLineCleared) {
        this.board = updatedBoard;
      }
      return hasLineCleared;
    },
    dropBalls() {
      this.board.forEach((column, colIndex) => {
        let emptySpaces = 0;
        column.reverse().forEach((cell, rowIndex) => {
          const row = this.board[this.boardSize - 1 - rowIndex];
          if (row[colIndex].color === '') {
            emptySpaces++;
          } else if (emptySpaces > 0) {
            this.board[this.boardSize - 1 - rowIndex + emptySpaces][colIndex].color = row[colIndex].color;
            row[colIndex].color = '';
          }
        });
      });
    },
    generateNewBalls() {
      const colors = [
        "#FF0000", "#024217", "#0000FF", "#FFFF00", "#FF00FF", "#00FFFF", "#a87532", "#28fa6e"
      ];
      this.board.forEach((row, rowIndex) => {
        row.forEach((cell, colIndex) => {
          if (cell.color === '') {
            this.board[rowIndex][colIndex].color = colors[Math.floor(Math.random() * colors.length)];
          }
        });
      });
    }
  },
  created() {
    this.generateBoard();
  }
};
</script>
<style lang="less" scoped>
@import '../styles/_game-board.less';
</style>