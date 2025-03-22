<template>
  <div class="game-board" @click.self="clearSelection">
    <div
        v-for="(row, rowIndex) in board"
        :key="rowIndex"
        class="row"
    >
      <div
          v-for="(cell, colIndex) in row"
          :key="colIndex"
          class="cell"
          :style="{
          backgroundColor: cell.color,
          border: isSelected(rowIndex, colIndex) ? '2px solid #000' : 'none'
        }"
          @click="handleCellClick(rowIndex, colIndex)"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
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
        "#FF0000",
        "#024217",
        "#0000FF",
        "#FFFF00",
        "#FF00FF",
        "#00FFFF",
        "#a87532",
        "#28fa6e"
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
      let updatedBoard = [...this.board];
      let hasLineCleared = false;

      for (let row = 0; row < this.boardSize; row++) {
        for (let col = 0; col < this.boardSize - 2; col++) {
          const color = this.board[row][col].color;
          if (
              color === this.board[row][col + 1].color &&
              color === this.board[row][col + 2].color
          ) {
            updatedBoard[row][col].color = "";
            updatedBoard[row][col + 1].color = "";
            updatedBoard[row][col + 2].color = "";
            hasLineCleared = true;
          }
        }
      }

      for (let col = 0; col < this.boardSize; col++) {
        for (let row = 0; row < this.boardSize - 2; row++) {
          const color = this.board[row][col].color;
          if (
              color === this.board[row + 1][col].color &&
              color === this.board[row + 2][col].color
          ) {
            updatedBoard[row][col].color = "";
            updatedBoard[row + 1][col].color = "";
            updatedBoard[row + 2][col].color = "";
            hasLineCleared = true;
          }
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
          if (this.board[row][col].color === "") {
            emptySpaces++;
          } else if (emptySpaces > 0) {
            // Move the ball down
            this.board[row + emptySpaces][col].color = this.board[row][col].color;
            this.board[row][col].color = "";
          }
        }
      }
    },
    generateNewBalls() {
      // Generate new balls from the top
      const colors = [
        "#FF0000",
        "#024217",
        "#0000FF",
        "#FFFF00",
        "#FF00FF",
        "#00FFFF",
        "#a87532",
        "#28fa6e"
      ];

      for (let col = 0; col < this.boardSize; col++) {
        for (let row = 0; row < this.boardSize; row++) {
          if (this.board[row][col].color === "") {
            this.board[row][col].color = colors[Math.floor(Math.random() * colors.length)];
          }
        }
      }
    }
  },
  created() {
    this.generateBoard();
  },
};
</script>

<style scoped>
.game-board {
  display: grid;
  grid-template-columns: repeat(8, 40px);
  grid-template-rows: repeat(8, 40px);
  gap: 4px;
  width: 100%;
  height: 100%;
}

.row {
  display: contents;
}

.cell {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: gray;
  transition: border 0.3s ease;
}
</style>
