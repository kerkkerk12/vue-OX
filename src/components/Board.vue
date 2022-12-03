<template>
  <header>
    <div class="header">TIC TAC TOE GAMES</div>
  </header>
  <div class="bigblock">
    <h2 v-if="winner && blockLeft === 0">
      the Winner is : {{ winner }} but no score.
    </h2>
    <h2 v-else-if ="winner">the Winner is : {{ winner }}</h2>
    <h2 v-else-if="blockLeft === 0">The game is end no player win.</h2>
    <h2 v-else>Player "{{ player }}" move.</h2>
    <button @click="reset" class="buttonBoard">Reset board</button>
    <div class="container">
      <div v-for="(z, x) in 4" :key="x" class="row">
        <button v-for="(z, y) in 4" :key="y" @click="move(x, y)" class="square">
          {{ squares[x][y] }}
        </button>
      </div>

      {{ (squares[randomX][randomY] = "-") }}
    </div>
    <h2 style="color: red">BLOCK LEFT: {{ blockLeft }}</h2>
    <div class="score">
      <h1>Score</h1>
      <h3>Player X scores: {{ winX }}</h3>
      <h3>Player O scores: {{ winY }}</h3>
      <button @click="resetScore" class="buttonScore">
        Reset Score.
      </button>
    </div>
  </div>
</template>

<!-- ///////////////////////////////////////////// -->
<script>
import { ref, computed, watch, onMounted } from "vue";
// function check who is the winner
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
    [0, 5, 10, 15], //diagonal line
    [3, 6, 9, 12], //diagonal line
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
  // setup player and board
  setup() {
    const blockLeft = ref(15);
    const player = ref("X");
    const squares = ref([
      ["", "", "", ""],
      ["", "", "", ""],
      ["", "", "", ""],
      ["", "", "", ""],
    ]);

    const winner = computed({
      get: () => Winner(squares.value.flat()),
    });

    let randomX = Math.floor(Math.random() * (4 - 0) + 0);
    let randomY = Math.floor(Math.random() * (4 - 0) + 0);

    const move = (x, y) => {
      if (winner.value) return;
      if (squares.value[x][y] !== "") return;

      squares.value[x][y] = player.value;
      player.value = player.value === "O" ? "X" : "O";
      blockLeft.value -= 1;
      console.log(blockLeft);
    };

    const resetScore = () => {
      localStorage.clear();
      window.location.reload();
    };

    const reset = () => {
      window.location.reload();

      console.log(randomX);
      blockLeft.value = 15;
      player.value = "X";
      squares.value = [
        ["", "", "", ""],
        ["", "", "", ""],
        ["", "", "", ""],
        ["", "", "", ""],
      ];
    };

    const winX = ref(0);
    const winY = ref(0);
    const scorePlayer1 = ref(0);
    const scorePlayer2 = ref(0);
    watch(winner, (currentWinner, previous) => {
      if (currentWinner && !previous) {
        if (currentWinner === "X") {
          scorePlayer1.value += blockLeft.value;
          winX.value += scorePlayer1.value;
          localStorage.setItem("winX", JSON.stringify(winX.value));
        } else {
          scorePlayer2.value += blockLeft.value;
          winY.value += scorePlayer2.value;
          localStorage.setItem("winY", JSON.stringify(winY.value));
        }
      }
    });

    onMounted(() => {
      winX.value = JSON.parse(localStorage.getItem("winX"));
      winY.value = JSON.parse(localStorage.getItem("winY"));
      // console.log(winY.value)
    });

    return {
      winner,
      player,
      squares,
      move,
      reset,
      resetScore,
      scorePlayer1,
      scorePlayer2,
      blockLeft,
      randomX,
      randomY,
      winX,
      winY,
    };
  },
};
</script>
