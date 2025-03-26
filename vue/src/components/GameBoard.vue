<template>
  <div
      class="game-board"
      @click.self="clearSelection"
  >
    <BoardCell
        v-for="(cell, index) in board"
        :key="index"
        :rowIndex="Math.floor(index / boardSize)"
        :colIndex="index % boardSize"
        :color="cell.color"
        :isSelected="isSelected(index)"
        :handleCellClick="handleCellClick"
    />
  </div>
</template>

<script>
import BoardCell from "./BoardCell.vue";

const COLORS = [
  "#FF0000", "#024217", "#0000FF", "#FFFF00",
  "#FF00FF", "#00FFFF", "#a87532", "#28fa6e"
];

export default {
  components: { BoardCell },
  data() {
    return {
      boardSize: 8,
      board: [],
      selectedCell: null,
    };
  },
  methods: {
    generateBoard() {
      this.board = Array.from({ length: this.boardSize ** 2 }, () => ({
        color: COLORS[Math.floor(Math.random() * COLORS.length)],
      }));
    },
    handleCellClick(rowIndex, colIndex) {
      const index = rowIndex * this.boardSize + colIndex;

      if (this.selectedCell === null) {
        this.selectedCell = index;
        return;
      }

      const selectedIndex = this.selectedCell;
      const selectedRow = Math.floor(selectedIndex / this.boardSize);
      const selectedCol = selectedIndex % this.boardSize;

      if (
          (Math.abs(selectedRow - rowIndex) === 1 && selectedCol === colIndex) ||
          (Math.abs(selectedCol - colIndex) === 1 && selectedRow === rowIndex)
      ) {
        const backupBoard = [...this.board];
        [this.board[selectedIndex].color, this.board[index].color] = [
          this.board[index].color,
          this.board[selectedIndex].color,
        ];

        if (this.checkAndClearLines()) {
          this.dropBalls();
          this.generateNewBalls();
        } else {
          this.board = backupBoard;
        }
      }
      this.selectedCell = null;
    },
    clearSelection() {
      this.selectedCell = null;
    },
    isSelected(index) {
      return this.selectedCell === index;
    },
    checkAndClearLines() {
      let hasLineCleared = false;
      let updatedBoard = [...this.board];

      for (let i = 0; i < this.board.length; i++) {
        const row = Math.floor(i / this.boardSize);
        const col = i % this.boardSize;
        const color = this.board[i].color;

        if (col < this.boardSize - 2 &&
            color === this.board[i + 1]?.color &&
            color === this.board[i + 2]?.color) {
          updatedBoard[i].color = "";
          updatedBoard[i + 1].color = "";
          updatedBoard[i + 2].color = "";
          hasLineCleared = true;
        }

        if (row < this.boardSize - 2 &&
            color === this.board[i + this.boardSize]?.color &&
            color === this.board[i + this.boardSize * 2]?.color) {
          updatedBoard[i].color = "";
          updatedBoard[i + this.boardSize].color = "";
          updatedBoard[i + this.boardSize * 2].color = "";
          hasLineCleared = true;
        }
      }

      if (hasLineCleared) {
        this.board = updatedBoard;
      }
      return hasLineCleared;
    },
    dropBalls() {
      for (let col = 0; col < this.boardSize; col++) {
        let emptySpaces = 0;
        for (let row = this.boardSize - 1; row >= 0; row--) {
          let index = row * this.boardSize + col;
          if (this.board[index].color === "") {
            emptySpaces++;
          } else if (emptySpaces > 0) {
            this.board[index + emptySpaces * this.boardSize].color = this.board[index].color;
            this.board[index].color = "";
          }
        }
      }
    },
    generateNewBalls() {
      this.board.forEach((cell) => {
        if (cell.color === "") {
          cell.color = COLORS[Math.floor(Math.random() * COLORS.length)];
        }
      });
    },
  },
  created() {
    this.generateBoard();
  },
};
</script>

<style lang="less" scoped>
@import '../styles/_game-board.less';
</style>