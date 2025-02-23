<template>
  <div class="game-container">
    <h2>Speed Typing Game</h2>

    <transition name="fade">
      <p class="sentence" v-if="sentenceVisible">
        <span v-for="(char, index) in sentence" :key="index" :class="getCharClass(index)">
          {{ char }}
        </span>
      </p>
    </transition>

    <textarea 
      v-model="userInput"
      @input="startTimer"
      @keyup.enter="checkCompletion"
      placeholder="Start typing here..."
    ></textarea>

    <p class="stats">
      ⏳ Time: <strong>{{ timeElapsed }}s</strong> | 📝 WPM: <strong>{{ wpm }}</strong> | 🎯 Accuracy: <strong>{{ accuracy }}%</strong>
    </p>

    <div class="controls">
      <select v-model="difficulty" @change="changeDifficulty">
        <option value="easy">Easy</option>
        <option value="medium">Medium</option>
        <option value="hard">Hard</option>
      </select>

      <button @click="restartGame">🔄 Restart</button>
    </div>

    <h3>🏆 Leaderboard (Top 3 Scores)</h3>
    <ul class="leaderboard">
      <li v-for="(entry, index) in topLeaderboard" :key="index">
        <strong>{{ entry.wpm }} WPM</strong> - {{ entry.accuracy }}% Accuracy
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, computed } from "vue";

export default {
  setup() {
    const sentences = {
      easy: ["Vue is amazing!", "Type fast.", "Enjoy coding!"],
      medium: ["JavaScript is the language of the web.", "Vue.js simplifies frontend development.", "Good developers always keep learning."],
      hard: [
        "The evolution of JavaScript frameworks has significantly improved user experience across the web.",
        "Mastering algorithms and data structures is essential for efficient problem solving in software engineering.",
        "The synergy between UI/UX design and front-end development creates a seamless user experience."
      ]
    };

    const difficulty = ref("easy");
    const sentence = ref("");
    const userInput = ref("");
    const timeElapsed = ref(0);
    const timer = ref(null);
    const sentenceVisible = ref(true);
    const leaderboard = ref([]);

    const startTimer = () => {
      if (!timer.value) {
        timer.value = setInterval(() => {
          timeElapsed.value++;
        }, 1000);
      }
    };

    const wpm = computed(() => {
      const wordsTyped = userInput.value.trim().split(/\s+/).length;
      return timeElapsed.value > 0 ? Math.round((wordsTyped / timeElapsed.value) * 60) : 0;
    });

    const accuracy = computed(() => {
      let correctChars = 0;
      for (let i = 0; i < userInput.value.length; i++) {
        if (userInput.value[i] === sentence.value[i]) correctChars++;
      }
      return userInput.value.length > 0 ? Math.round((correctChars / userInput.value.length) * 100) : 100;
    });

    const getCharClass = (index) => {
      if (index < userInput.value.length) {
        return userInput.value[index] === sentence.value[index] ? "correct" : "incorrect";
      }
      return "";
    };

    const checkCompletion = () => {
      clearInterval(timer.value);
      timer.value = null;
      leaderboard.value.push({ wpm: wpm.value, accuracy: accuracy.value });
      leaderboard.value.sort((a, b) => b.wpm - a.wpm);
      if (leaderboard.value.length > 3) leaderboard.value.pop();
      alert(`Finished! WPM: ${wpm.value}, Accuracy: ${accuracy.value}%`);
    };

    const restartGame = () => {
      sentenceVisible.value = false;
      setTimeout(() => {
        sentence.value = sentences[difficulty.value][Math.floor(Math.random() * sentences[difficulty.value].length)];
        userInput.value = "";
        timeElapsed.value = 0;
        clearInterval(timer.value);
        timer.value = null;
        sentenceVisible.value = true;
      }, 500);
    };

    const changeDifficulty = () => {
      restartGame();
    };

    const topLeaderboard = computed(() => leaderboard.value.slice(0, 3));

    restartGame();

    return {
      sentence,
      userInput,
      timeElapsed,
      startTimer,
      checkCompletion,
      wpm,
      accuracy,
      restartGame,
      getCharClass,
      sentenceVisible,
      difficulty,
      changeDifficulty,
      topLeaderboard
    };
  },
};
</script>

<style scoped>
/* Global Styles */
/* Full page background */
  html, body {
      background: black;
      color: white;
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
      height: 100%;
  }

.game-container {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  text-align: center;
  background: #aac5e0;
  border-radius: 10px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

h2 {
  color: #080762;
  margin-bottom: 15px;
}

.sentence {
  font-size: 1.2rem;
  font-weight: bold;
  margin: 15px 0;
  padding: 10px;
  background: #e3f2fd;
  border-radius: 5px;
  display: inline-block;
}

/* Typing Indicator */
.correct {
  color: #28a745;
  font-weight: bold;
}

.incorrect {
  color: #dc3545;
  text-decoration: underline;
  font-weight: bold;
}

/* Textarea */
textarea {
  width: 100%;
  height: 80px;
  padding: 10px;
  font-size: 1rem;
  border: 2px solid #ddd;
  border-radius: 5px;
  outline: none;
  transition: border-color 0.3s ease;
}

textarea:focus {
  border-color: #007bff;
}

/* Stats */
.stats {
  margin: 15px 0;
  font-size: 1rem;
  color: #555;
}

/* Controls */
.controls {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 15px;
}

select, button {
  padding: 8px 12px;
  font-size: 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

select {
  background: #fff;
  border: 2px solid #ddd;
}

button {
  background: #007bff;
  color: white;
  transition: 0.3s;
}

button:hover {
  background: #0056b3;
}

/* Leaderboard */
.leaderboard {
  list-style: none;
  padding: 0;
}

.leaderboard li {
  background: #e3f2fd;
  padding: 8px;
  margin: 5px 0;
  border-radius: 5px;
  font-size: 1rem;
  font-weight: bold;
}

/* Transitions */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
