<template>
  <div
      class="game-board"
      @click.self="clearSelection"
  >
    <BoardCell
        v-for="(cell, index) in board"
        :key="index"
        :index="index"
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
    // it's okay
    generateBoard() {
      this.board = Array.from({ length: this.boardSize ** 2 }, () => ({
        color: COLORS[Math.floor(Math.random() * COLORS.length)],
      }));
    },
    getIndex(row, col) {
      return row * this.boardSize + col;
    },
    getRow(index) {
      return Math.floor(index / this.boardSize);
    },
    getCol(index) {
      return index % this.boardSize;
    },
    swapCells(board, index1, index2) {
      [board[index1], board[index2]] = [board[index2], board[index1]];
    },
    clearSelection() {
      this.selectedCell = null;
    },
    isSelected(index) {
      return this.selectedCell === index;
    },
    isValidMove(index1, index2) {
      const row1 = Math.floor(index1 / this.boardSize);
      const col1 = index1 % this.boardSize;
      const row2 = Math.floor(index2 / this.boardSize);
      const col2 = index2 % this.boardSize;

      const areNeighbors = (Math.abs(row1 - row2) === 1 && col1 === col2) ||
          (Math.abs(col1 - col2) === 1 && row1 === row2);

      if (!areNeighbors) return false;

      const newBoard = [...this.board];
      [newBoard[index1], newBoard[index2]] = [newBoard[index2], newBoard[index1]];

      return this.hasMatch(newBoard);
    },
    hasMatch(board) {
      const size = this.boardSize;
      this.board.forEach((_, index) => {
        const row = this.getRow(index);
        const col = this.getCol(index);

        if (col <= size - 3 && board[index] === board[index + 1] && board[index] === board[index + 2]) {
          return true;
        }
        if (row <= size - 3 && board[index] === board[index + size] && board[index] === board[index + 2 * size]) {
          return true;
        }
      })
      return false;
    },
    handleCellClick(clickedCellIndex) {
      if (this.selectedCell === null) {
        this.selectedCell = clickedCellIndex;
        return;
      }
      this.swapCells(this.board, clickedCellIndex, this.selectedCell);
      this.selectedCell = null;
    },
    dropBalls() {
      const size = this.boardSize;

      Array.from({ length: size }).forEach((_, col) => {
        let emptySpaces = 0;

        [...Array(size)].forEach((_, row) => {
          const index = (size - 1 - row) * size + col;

          if (this.board[index].color === "") {
            emptySpaces++;
            return;
          }

          if (emptySpaces > 0) {
            const targetIndex = this.getIndex(emptySpaces, index);
            [this.board[targetIndex].color, this.board[index].color] = [this.board[index].color, ""];
          }
        });
      });
    },
    // refactoring
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