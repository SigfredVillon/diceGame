<script setup>
import { ref } from "vue";

const betAmount = ref(0);
const totMoney = ref(100);
const gameStarted = ref(false);
const images = [
  "/dice1.png",
  "/dice2.png",
  "/dice3.png",
  "/dice4.png",
  "/dice5.png",
  "/dice6.png"
];
const currentImageIndex = ref(0);
const rolledNumber = ref(null);
const playerRolledNumber = ref(null);
const displayText = ref("");

const addToBet = () => {
  if (betAmount.value < totMoney.value) {
    betAmount.value += 10;
  }
};

const subtractToBet = () => {
  if (betAmount.value > 0) {
    betAmount.value -= 10;
  }
};

const rollDice = (turn, callback) => {
 
  setTimeout(() => {
 
    currentImageIndex.value = Math.floor(Math.random() * images.length);

    rolledNumber.value = currentImageIndex.value + 1; 

    if (turn === "Player") {
    
      playerRolledNumber.value = rolledNumber.value;
    }

    
    displayText.value = `${turn} rolled the number ${rolledNumber.value}`;
    callback();
  }, 2000);
};

const endGame = (playerWon) => {
  const doubledBet = betAmount.value;
  if (playerWon) {
    totMoney.value += doubledBet;
    displayText.value = `Player won! Total money: ${totMoney.value}`;
    console.log("Player won! Total money:", totMoney.value);
  } else if (playerWon === false) {
    totMoney.value -= doubledBet;
    if (totMoney.value < 0) {
      totMoney.value = 0; 
    }
    displayText.value = `Player lost! Total money: ${totMoney.value}`;
    console.log("Player lost! Total money:", totMoney.value);
  } else {

    displayText.value = "It's a tie!";
    console.log("It's a tie! Total money:", totMoney.value);
  }


  displayText.value = displayText.value;
  
  gameStarted.value = false;
  betAmount.value = 0;
  currentImageIndex.value = 0;
  rolledNumber.value = null;
  playerRolledNumber.value = null;
};

const startGame = () => {
  if (betAmount.value > 0) {
    console.log("Game started with bet amount:", betAmount.value);
    gameStarted.value = true;

    // Player's turn
    rollDice("Player", () => {
      // After player's turn, initiate banker's turn after 2 seconds
      setTimeout(() => {
        // Reset variables for the next turn
        currentImageIndex.value = 0;
        rolledNumber.value = null;
        displayText.value = "";

        // Banker's turn
        rollDice("Banker", () => {
          // After banker's turn, display the result for 2 seconds
          setTimeout(() => {
            // Compare the rolled numbers to determine the winner
            if (playerRolledNumber.value > rolledNumber.value) {
              // Player won
              displayText.value = "Player won!";
              endGame(true);
            } else if (playerRolledNumber.value < rolledNumber.value) {
              // Banker won
              displayText.value = "Player lost!";
              endGame(false);
            } else {
              // It's a tie
              displayText.value = "It's a tie!";
              endGame(null);
            }
          }, 2000);
        });
      }, 2000); // Wait for 2 seconds after player's turn
    });
  } else {
    console.log("Please place a bet before starting the game.");
  }
};
</script>

<template>
  <main>
    <div class="container">
      <h4>Your Money</h4>
      <h1>${{ totMoney }}</h1>

      <!-- Display dice.gif or random image -->
      <img v-if="gameStarted && currentImageIndex === 0" src="/dice.gif" alt="Rolling Dice" />
      <img v-if="gameStarted && currentImageIndex > 0" :src="images[currentImageIndex]" alt="Random Image" width="214" length="211" />

      <!-- Display dynamic text -->
      <h1 v-if="gameStarted && rolledNumber !== null" class="result">{{ displayText }}</h1>

      <h4>Bet Amount</h4>
      <h1>${{ betAmount }}</h1>
      <div class="button-container">
        <button @click="addToBet" :disabled="gameStarted">+</button>
        <button @click="subtractToBet" :disabled="gameStarted">-</button>
      </div>
      <br>
      <br>
      <button @click="startGame" :disabled="gameStarted">Start Game</button>
    </div>
  </main>
</template>

<style scoped>
main {
  background-color: #ffd2d2;
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #333;
}

.container {
  text-align: center;
}

.result {
  margin-top: 20px;
  font-size: 1.5em;
}

.button-container {
  margin-top: 20px;
}

button {
  padding: 10px;
  margin: 0 5px;
  font-size: 1em;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}
</style>
