<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  

  let board: string[] = ['', '', '', '', '', '', '', '', ''];
  let currentPlayer: string = generateRandomPlayer();
  let winner: string | null = null;
  let startTime: number = Date.now();
  let elapsedTime: number = 0;
  let timerInterval: NodeJS.Timeout;

  function generateRandomPlayer() {
    return Math.random() < 0.5 ? 'X' : 'O';
  }

  function handleClick(cellIndex: number) {
  if (board[cellIndex] === '' && !winner) {
    board[cellIndex] = currentPlayer;
    checkWinner();
    if (!winner) {
      currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
      checkDraw();
    }
  }
}


  function checkWinner() {
    const winningCombinations: number[][] = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];

    for (let combination of winningCombinations) {
      const [a, b, c] = combination;
      if (board[a] !== '' && board[a] === board[b] && board[a] === board[c]) {
        winner = board[a];
        stopTimer();
        break;
      }
    }
  }

  function checkDraw() {
    if (board.every(cell => cell !== '')) {
      winner = 'Draw';
      stopTimer();
    }
  }

  function startTimer() {
    timerInterval = setInterval(() => {
      elapsedTime = Math.floor((Date.now() - startTime) / 1000);
    }, 1000);
  }

  function stopTimer() {
    clearInterval(timerInterval);
  }

  function formatTime(time: number): string {
    const minutes = Math.floor(time / 60).toString().padStart(2, '0');
    const seconds = (time % 60).toString().padStart(2, '0');
    return `${minutes}:${seconds}`;
  }

  function resetGame() {
    board = ['', '', '', '', '', '', '', '', ''];
    currentPlayer = generateRandomPlayer();
    winner = null;
    startTime = Date.now();
    elapsedTime = 0;
    stopTimer();
    startTimer();
  }

  onMount(() => {
    startTimer();
  });

  onDestroy(() => {
    stopTimer();
  });

  
</script>

<svelte:head>
  <title>Tic Tac Toe</title>
  <link rel="icon" href="../assets/85535e05d1f130b16751c8308cfbb19b.avif" />
</svelte:head>
<div class="wrapper">
  <h1>Tic Tac Toe</h1>
  <h3>Now it's {currentPlayer}'s turn</h3>
  <h3>Time: {formatTime(elapsedTime)}</h3>
  <div class="board">
    {#each board as cell, cellIndex}
      <button id="cell-{cellIndex}" class="cell" on:click={() => handleClick(cellIndex)}>
        {#if cell === 'X'}
          <span class="x-symbol">{cell}</span>
        {:else if cell === 'O'}
          <span class="o-symbol">{cell}</span>
        {/if}
      </button>
    {/each}
  </div>
  {#if winner}
    <p>{#if winner === 'Draw'}It's a Draw!{:else}Winner: {winner}{/if}</p>
    <button class="play-again-button" on:click={resetGame}>Play Again</button>
  {/if}
</div>

<style>
  .wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }

  h1 {
    font-size: 48px;
    margin-bottom: 20px;
  }

  h3 {
    font-size: 24px;
    margin-bottom: 20px;
  }

  .board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-gap: 10px;
    width: 320px;
    height: 320px;
    background-color: #ddd;
    padding: 10px;
  }

  .cell {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 72px;
    background-color: #fff;
    cursor: pointer;
    width: 100px;
    height: 100px;
    border: none;
    outline: none;
  }

  .x-symbol {
    color: red;
    animation: scale-up 0.3s ease-in-out;
  }

  .o-symbol {
    color: blue;
    animation: scale-up 0.3s ease-in-out;
  }

  .play-again-button {
    font-size: 16px;
    padding: 10px 20px;
    margin-top: 20px;
    background-color: #4caf50;
    color: #fff;
    border: none;
    outline: none;
    cursor: pointer;
    transition: background-color 0.3s ease-in-out;
  }

  .play-again-button:hover {
    background-color: #45a049;
  }

  p {
    font-size: 20px;
    margin-top: 20px;
  }
  
  @keyframes scale-up {
    0% {
      transform: scale(0);
    }
    100% {
      transform: scale(1);
    }
  }
</style>
