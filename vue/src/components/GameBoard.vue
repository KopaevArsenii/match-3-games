<template>
  <div
      class="game-board"
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
    getNeighbors(index) {
      const neighbors = [];
      const row = this.getRow(index);
      const col = this.getCol(index);

      // top
      if (row > 0) neighbors.push(this.getIndex(row - 1, col));
      // bottom
      if (row < this.boardSize - 1) neighbors.push(this.getIndex(row + 1, col));
      // left
      if (col > 0) neighbors.push(this.getIndex(row, col - 1));
      // right
      if (col < this.boardSize - 1) neighbors.push(this.getIndex(row, col + 1));

      return neighbors;
    },
    handleCellClick(clickedCellIndex) {
      if (this.selectedCell === null) {
        this.selectedCell = clickedCellIndex;
        return;
      }
      const neighbors = this.getNeighbors(this.selectedCell);
      if (!neighbors.find((item) => item === clickedCellIndex)) return;

      const afterSwapBoard = [...this.board];
      this.swapCells(afterSwapBoard, clickedCellIndex, this.selectedCell);

      if (this.hasMatches(afterSwapBoard)) {
        this.board = afterSwapBoard;
        this.processMatches();
      }

      this.clearSelection();
    },
    async processMatches() {
      do {
        if (!this.hasMatches(this.board)) break;

        await this.$nextTick();
        this.clearMatches();
        await this.$nextTick();
        this.dropBalls();
        await this.$nextTick();
        this.generateNewBalls();
        await this.$nextTick();

      } while (this.hasMatches(this.board));
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
    hasMatches(board) {
      let matchFound = false;

      board.forEach((cell, i) => {
        if (!cell.color) return;

        const row = this.getRow(i);
        const col = this.getCol(i);

        // By horizontal
        if (
            col < this.boardSize - 2 &&
            cell.color === board[i + 1]?.color &&
            cell.color === board[i + 2]?.color
        ) {
          matchFound = true;
        }

        // By vertical
        if (
            row < this.boardSize - 2 &&
            cell.color === board[i + this.boardSize]?.color &&
            cell.color === board[i + this.boardSize * 2]?.color
        ) {
          matchFound = true;
        }
      });

      return matchFound;
    },
    clearMatches() {
      console.info('called')
      this.board.forEach((cell, i) => {
        if (!cell.color) return;

        const row = this.getRow(i);
        const col = this.getCol(i);

        // By horizontal
        if (
            col < this.boardSize - 2 &&
            cell.color === this.board[i + 1]?.color &&
            cell.color === this.board[i + 2]?.color
        ) {
          this.board[i].color = "";
          this.board[i + 1].color = "";
          this.board[i + 2].color = "";
        }

        // By vertical
        if (
            row < this.boardSize - 2 &&
            cell.color === this.board[i + this.boardSize]?.color &&
            cell.color === this.board[i + this.boardSize * 2]?.color
        ) {
          this.board[i].color = "";
          this.board[i + this.boardSize].color = "";
          this.board[i + this.boardSize * 2].color = "";
        }
      });
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
    this.processMatches();
  },
};
</script>

<style lang="less" scoped>
@import '../styles/_game-board.less';
</style>