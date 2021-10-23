<template>
  <div class="tictactoe-board">
    <div v-for="(n, i) in 3" :key="i">
      <div v-for="(n, j) in 3" :key="i + j">
        <cell @click="performMove(i, j)" :value="board[i][j]"></cell>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      board: [
        [" ", " ", " "],
        [" ", " ", " "],
        [" ", " ", " "],
      ],
    };
  },
  methods: {
    performMove(x, y) {
      if (this.board[x][y] !== " ") {
        // Invalid move.
        return;
      }
      this.board[x][y] = "x";
      // TODO implement some AI super-overlord algorithm that will calculate
      //  the computers move.
      
      this.getComputedMoveFromApi();

      this.$forceUpdate();
    },

    getComputedMoveFromApi() {
      const boardInput = this.convertBoardToString(this.board);
      axios
        .get("https://wave-titactoe.herokuapp.com", {
          params: { board: boardInput },
        })
        .then((res) => {
          const response = res.data;
          console.log(response);
          this.board = this.createBoard(response);
          // this.$forceUpdate();
        })
        .catch((error) => {
          console.log(error);
          // Manage errors if found any
        });
    },

    createBoard(input) {
      const inputCharArray = input.split("");
      let currentPos = 0;
      const board = [];
      const row1 = [];
      for (let i = 0; i < 3; i++) {
        row1.push(inputCharArray[currentPos + i]);
      }
      board.push(row1);

      currentPos = 3;
      const row2 = [];
      for (let i = 0; i < 3; i++) {
        row2.push(inputCharArray[currentPos + i]);
      }
      board.push(row2);

      currentPos = 6;
      const row3 = [];
      for (let i = 0; i < 3; i++) {
        row3.push(inputCharArray[currentPos + i]);
      }
      board.push(row3);

      return board;
    },

    convertBoardToString(board) {
      let output = [];
      for (let row of board) {
        output.push(...row);
      }
      return output.join("");
    },
  },
};
</script>

<style>
.tictactoe-board {
  display: flex;
  flex-wrap: wrap;
  width: 204px;
  height: 204px;
}
</style>
