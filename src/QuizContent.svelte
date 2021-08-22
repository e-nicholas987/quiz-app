<script>
  export let quiz;
  import { fly } from "svelte/transition";
  import {onMount, afterUpdate} from 'svelte'
  let index=-1;
  let score = 0;
  $: scorePercentage = ((score / (quiz.length)) * 100).toFixed(0);
  let ansInd;
  let displayGreen = false;
  let disableClick = false;

// function setStorage(){
//     localStorage.setItem('score',JSON.stringify(score))
//     localStorage.setItem('index',JSON.stringify(index))
// }
// onMount(()=>{
//     index=localStorage.getItem('index')
//     ?JSON.parse(localStorage.getItem('index')):-1
// })
// afterUpdate(()=>{
//     setStorage()

// })

  function restart() {
    index = 0;
    score = 0;
    ansInd = null;
    disableClick = false;
    displayGreen = false;
  }
  function next() {
    index++;
    ansInd = null;
    displayGreen = false;
    disableClick = false;
    
  }

  function updateScore(answer) {
    if (quiz[index].correct === answer) {
      score++;
    }
    console.log(score);
  }
  $: console.log(scorePercentage);
</script>

<style>
  .disabled {
    background-color: rgba(192, 192, 192, 0.897);
    color: rgb(133, 131, 131);
  }

  .progress-bar {
    transition: 0.5s ease-in;
  }

  .center-content {
    font-weight:600;
    display: flex;
    align-items: center;
    justify-content: space-evenly;
    height: 100%;
    flex-direction: column;
  }
  .disabled:hover {
    border: none;
  }
  .green {
    background-color: green;
    color: white;
    transition: 0.5s ease-in;
  }

  .red {
    background-color: red;
    color: white;
    transition: 0.5s ease-in;
  }
</style>

{#if index === -1 && index < quiz.length}
  <div in:fly={{ y: -200, delay: 200 }} class="content">
    <div class="center-content">
      <p style="text-align:center">Welcome to quiz API from opendb.com, click the start button to begin</p>
      <button
        style="width:100%; text-align:center"
        on:click={() => {
          index++;
        }}>
        Start Quiz
      </button>
    </div>
  </div>
{:else if index !== -1 && index < quiz.length}
  <div class="content">
    <div class="question">{quiz[index].question}</div>

    <div class="answer-grid">
      {#each quiz[index].answers as answer, i}
        <div in:fly={{ x: 200, delay: (i + 1) * 100 }}>
          <button
            class={ansInd === i && quiz[index].correct !== answer ? 'red' : ''}
            style="width: 100%"
            on:click={() => {
              ansInd = i;
              updateScore(answer);
              displayGreen = true;
              disableClick = true;
            }}
            class:green={quiz[index].correct == answer ? displayGreen : ''}
            disabled={disableClick}
            class:disabled={disableClick}>
            {answer}
          </button>
        </div>
      {/each}
    </div>

    <footer>
      <div class="progress">
        <div
          style="width:{((index + 1) / quiz.length) * 100}%; height: 100%;
          background-color:  rgba(0, 0, 0, 0.918); border-radius: 4px "
          class="progress-bar" />
      </div>
      <button
        class="next-btn"
        on:click={() => {
          next();
        }}
        disabled={!disableClick}
        class:disabled={!disableClick}>
        Next
      </button>

    </footer>

  </div>
{:else}
  <div class="content" in:fly={{ y: -200, delay: 200 }}>
    <div class="center-content">
      {#if scorePercentage < 1}
        <p style="text-align:center">Terrible! You scored:</p>
      {:else if scorePercentage > 0 && scorePercentage < 30}
        Buckle up! You scored:
      {:else if scorePercentage > 29 && scorePercentage < 50}
        <p style="text-align:center">You can do better! You scored:</p>
        
      {:else if scorePercentage > 49 && scorePercentage < 60}
        <p style="text-align:center"> Halfway there! You scored:</p>
        
      {:else if scorePercentage > 59 && scorePercentage < 70}
         <p style="text-align:center">Way to go! You scored:</p>
      {:else if scorePercentage > 69 && scorePercentage < 99}
         <p style="text-align:center">Almost there!</p>
      {:else} <p style="text-align:center">Congratulations! You scored:</p>
      {/if}
      <p style="font-weight:600;font-size:2rem">{scorePercentage}%</p>
      <button
        style="width:100%; text-align:center"
        on:click={() => {
          restart();
        }}>
        Restart Quiz
      </button>
    </div>

  </div>
{/if}
