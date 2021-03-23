<template>
  <div class="main-window">
    <div class="game-window" v-bind:style='{borderColor: windowColor}'>
      <div class="top-part">
        <div 
          class="part"
          v-bind:style='{background: parts[0].current}'
          v-on:click='sendPart(0)'
        >
        </div>
      </div>
      <div class="right-part">
        <div 
          class="part"
          v-bind:style='{background: parts[1].current}'
          v-on:click='sendPart(1)'
        >
        </div>
      </div>
      <div class="left-part">
        <div
          class="part"
          v-bind:style='{background: parts[2].current}'
          v-on:click='sendPart(2)'
        >
        </div>
      </div>
      <div class="bottom-part">
        <div
          class="part" 
          v-bind:style='{background: parts[3].current}'
          v-on:click='sendPart(3)'
        >
        </div>
      </div>
    </div>
    <div class="settings">
      <div>
        <div> Стадия: {{ stage + 1 }} </div>
        <div> Осталось в раунде: {{ stage - userStage + 1 }} </div>
      </div>
      <div class="setting-time"> Сложность:
        <div>
          <label for="easy">Легкая</label>
          <input id="easy" type="radio" name="time" value="1500" v-model="time">
        </div>
        <div>
          <label for="medium">Cредняя</label>
          <input id="medium" type="radio" name="time" value="1000" v-model="time">
        </div>
        <div>
          <label for="high">Сложная</label>
          <input id="high" type="radio" name="time" value="400" v-model="time">
        </div>
      </div>
      <button v-on:click='newGame(1000)'>Новая игра</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Game',

  data: () => ({
    parts: [
      {current: 'darkred', active: 'lightcoral', default: 'darkred', audio: './assets/part1.mp3' },
      {current: 'darkblue', active: 'lightblue', default: 'darkblue', audio: './assets/part2.mp3' },
      {current: 'darkgreen', active: 'lightgreen', default: 'darkgreen', audio: './assets/part3.mp3' },
      {current: 'orange', active: 'yellow', default: 'orange', audio: './assets/part4.mp3' },
    ],
    stage: 0,
    sequence: [],
    userStage: 0,
    userProgress: false,
    gameProgress: false,
    time: 1000,
    windowColor: 'black',

    settings: {
      breakBetweenRounds: 2000,
      breakInRound: 1000,
      clickAnimationTime: 150,
      stageAnimationTime: 250
    }
  }),

  methods: {

    newGame() {

      if (this.gameProgress) return

      this.setSettings()
      this.stage = 0
      this.userStage = 0
      this.sequence = []
      this.gameProgress = true
      this.userProgress = false
      this.windowColor = 'black'

      this.newStage(this.settings.breakInRound)
    },

    sendPart(index) {

      if (!this.userProgress) return

      this.cheerPart(index, this.settings.clickAnimationTime)
      this.checkMove(index)
    },

    cheerPart(index, time) {
      this.parts[index].current = this.parts[index].active

      this.playSound(this.parts[index].audio)

      setTimeout( () => {
        this.parts[index].current = this.parts[index].default
      },time) 
    },

    newStage(time) {

      if (this.userProgress) return

      if (this.sequence.length <= this.stage) {

        const random = Math.floor(Math.random() * 4)

        this.sequence.push(random)

        this.cheerPart(random, this.settings.stageAnimationTime)
        
        setTimeout( () => {
          this.newStage(time)
        }, time)
      }
      else this.userProgress = true 
    },

    checkMove(part) {

      if (this.sequence[this.userStage] !== part) {

        this.gameProgress = false
        this.userProgress = false
        this.stage = 0
        this.userStage = ''
        this.windowColor = 'red'

        return
      }
      else {

        this.userStage++
      }
      
      if (this.sequence.length === this.userStage) {

        this.stage++
        this.sequence = []
        this.userStage = 0
        this.userProgress = false 

        setTimeout( () => {

          this.newStage(this.settings.breakInRound)
        }, this.settings.breakBetweenRounds)
      }
    },

    playSound(audio) {

      const sound = new Audio(audio)

      sound.play()
    },

    setSettings() {

      if (this.gameProgress) return

      this.settings.breakInRound = +this.time
    },
  }
}
</script>
