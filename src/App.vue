<template>
  <div id="app">
    <h1>Simon game</h1>
    <div class="game-wrap">
      <div class="game" :class="{ lose: isLose }">
        <div class="section-wrap">
          <div class="section blue" ref="1" @click="clicked(1)"></div>
          <div class="section yellow" ref="2" @click="clicked(2)"></div>
        </div>
        <div class="section-wrap">
          <div class="section red" ref="3" @click="clicked(3)"></div>
          <div class="section green" ref="4" @click="clicked(4)"></div>
        </div>
      </div>
      <div class="settings">
        <div>Раунд: {{ round }}</div>
        <button @click="start()">Играть</button>
        <label for="level">Уровень сложности:</label>
        <select id="level" v-model="time">
          <option label="" selected value="1500">Легкий</option>
          <option value="1000">Средний</option>
          <option value="400">Сложный</option>
        </select>
        <div>{{ info }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import "@/styles.scss";
export default {
  name: "App",
  data() {
    return {
      time: "1500",
      info: "",
      round: 0,
      queue: [],
      queueInterval: null,
      playerQueue: [],
      isLose: false,
      isPlaying: false,
      sounds: [
        new Audio("https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"),
        new Audio("https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"),
        new Audio("https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"),
        new Audio("https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"),
      ],
    };
  },

  methods: {
    clicked(section) {
      if (this.isPlaying) {
        this.setSectionActive(section);
        this.playerQueue.push(section);
        this.compareQueues();
      }
    },
    setSectionActive(section) {
      this.sounds[section - 1].play();
      this.$refs[section].classList.add("active");
      setTimeout(() => {
        this.$refs[section].classList.remove("active");
      }, 250);
    },
    start() {
      this.isLose = false;
      this.queue = [];
      this.playerQueue = [];
      this.score = 0;
      clearInterval(this.queueInterval);
      this.playQueue();
    },
    playQueue() {
      this.info = "Запоминайте";
      let currentIndex = 0;
      this.isPlaying = false;
      this.queue.push(Math.floor(Math.random() * 4 + 1));
      this.queueInterval = setInterval(() => {
        if (currentIndex >= this.queue.length) {
          clearInterval(this.queueInterval);
          this.info = "Повторите";
          return (this.isPlaying = true);
        }
        this.setSectionActive(this.queue[currentIndex]);
        currentIndex++;
      }, this.time);
    },
    compareQueues() {
      for (let i = 0; i < this.playerQueue.length; i++) {
        if (this.playerQueue[i] !== this.queue[i]) {
          this.playerQueue = [];
          this.sounds.forEach((sound) => {
            sound.play();
          });
          this.isLose = true;
          this.isPlaying = false;
          this.info = "Вы проиграли на " + this.round + " раунде! Сыграем еще?";
        }
      }
      if (this.playerQueue.length === this.queue.length) {
        this.playerQueue = [];
        this.isPlaying = false;
        this.round++;
        this.playQueue();
      }
    },
  },
};
</script>

<style></style>
