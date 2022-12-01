<template>
  <div class="container">
    <h2 v-if="winner">the Winner is : {{ winner }} score plus {{blockLeft}}</h2>
    <h2 v-else-if="player ==='-' ">Press the first block to remove.</h2>
    <h2 v-else-if="blockLeft === 0 ">The game is end no player win.</h2>
    <h2 v-else >Player "{{ player }}" move.</h2>
    <button @click="reset" class="btn btn-success mb-3">Reset</button>
    <div v-for="(z, x) in 4" :key="x" class="row">
      <button v-for="(z, y) in 4" :key="y" @click="move(x, y)" class="square">
        {{ squares[x][y] }}
      </button>
    </div>
  </div>
  <div class ="score">
    <h1>Score</h1>
    <h3>Player X : {{ scorePlayer1 }}</h3>
    <h3>Player O : {{ scorePlayer2 }}</h3>
  </div>
  <h2 style="color: red">BLOCK LEFT: {{ blockLeft }}</h2>
</template>

<style scoped>
.square {
  background: rgb(240, 27, 27);
  border: 1px solid #999;
  float: left;
  font-size: 70px;
  font-weight: bold;
  line-height: 34px;
  height: 100px;
  margin-right: 2px;
  margin-top: 2px;
  padding: 0;
  text-align: center;
  width: 100px;
}
</style>
<style>
.score {
  margin: auto;
}

</style>
<!-- ///////////////////////////////////////////// -->
<script>
import { ref, computed, watch, onMounted } from "vue";
const Winner = (squares) => {
  const lines = [
    [0, 1, 2, 3],
    [4, 5, 6, 7],
    [8, 9, 10, 11],
    [12, 13, 14, 15],
    [0, 4, 8, 12],
    [1, 5, 9, 13],
    [2, 6, 10, 14],
    [3, 7, 11, 15],
    [0, 5, 10, 15],
    [3, 6, 9, 12],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c, d] = lines[i];
    if (
      squares[a] &&
      squares[a] === squares[b] &&
      squares[a] === squares[c] &&
      squares[a] === squares[d]
    ) {
      return squares[a];
    }
  }
};
export default {
  setup() {
    const blockLeft = ref(16);
    const player = ref("-");
    const squares = ref([
      ["", "", "", ""],
      ["", "", "", ""],
      ["", "", "", ""],
      ["", "", "", ""],
    ]);
    const winner = computed(() => Winner(squares.value.flat()));
    const move = (x, y) => {
      if (winner.value) return;

      if (squares.value[x][y] !== "") return;
        
      

      squares.value[x][y] = player.value;
      player.value = player.value === "X" ? "O" : "X";
      blockLeft.value -= 1;
      console.log(blockLeft);
    };

    const reset = () => {
      blockLeft.value = 16;
      player.value = "-";
      squares.value = [
        ["", "", "", ""],
        ["", "", "", ""],
        ["", "", "", ""],
        ["", "", "", ""],
      ];
    };
    const scorePlayer1 = ref(0);
    const scorePlayer2 = ref(0);
    watch(winner, (currentWinner) => {
      if (currentWinner) {
        if (currentWinner === "X") {
          scorePlayer1.value += blockLeft.value;
        } else {
          scorePlayer2.value += blockLeft.value;
        }
      }
    });

    return {
      winner,
      player,
      squares,
      move,
      reset,
      scorePlayer1,
      scorePlayer2,
      blockLeft,
    };
  },
};
</script>
