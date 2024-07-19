<template>
  <div class="container p-5 mb-4 bg-light rounded-3">
    <div class="container-fluid py-5">
      <h1 class="display-5 fw-bold">Show the whole world who has the 10 fastest fingers ;)</h1>
      <p class="col-md-8 fs-4">Test your speed of writing.</p>
      <hr>

      <div v-if="isFinished" class="alert alert-info">
        <h1>Game Over</h1>
        <p class="display-4">{{ mps }} MPS <span class="small" style="font-size: 2rem;">(Minute per second)</span></p>

        <span>Accuracy percentage {{ accurancyPercent.toFixed(2) }} %</span>
        <br>
        <span>Correct words {{ trueCount }}</span>
        <br>
        <span>False words {{ falseCount }}</span>
        <br>
        <button @click="newGame" class="btn btn-success btn-lg mt-4 big-button">
          New game
        </button>
      </div>
      <div v-else class="card">
        <div class="card-body">
         <span v-bind:class="key!=0 || writtenWordBackground" :key="key"
               v-for="(word,key) in words.filter((data,index)=>index<15)"
               style="margin-left: 5px;" class="word">
           {{ word }}
         </span>
        </div>
        <div class="card">
          <div class="card-body bg-secondary">
            <div class="input-group input-group-lg">
              <input type="text" class="form-control" v-model="writtenWord">
              <button class="btn btn-light disabled" type="button">{{ timer }}</button>
              <button :disabled="isRunning" @click="getWords" class="btn btn-light" type="button">Restart</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import listOfWords from '@/assets/words.json'

export default {
  data () {
    return {
      words: ['domates', 'biber', 'patlican', 'php', 'codeigniter', 'restApi'],
      writtenWord: null,
      isTrueWord: true,
      trueCount: 0,
      falseCount: 0,
      timer: 60,
      interval: null,
      isRunning: false,
      isFinished: false,
      listOfWords: listOfWords
    }
  },
  watch: {
    writtenWord (val) {
      if (!val || val === ' ') {
        this.writtenWord = ''
        return
      }
      if (!this.isRunning) this.toggleTimer()
      const word = this.words[0].slice(0, val.length)
      const userWord = val.replace(' ', '')
      this.isTrueWord = userWord === word

      if (val.indexOf(' ') !== -1) {
        this.isTrueWord ? this.trueCount++ : this.falseCount++
        this.words.splice(0, 1)
        this.writtenWord = ''
        this.isTrueWord = true
      }
    }
  },
  computed: {
    writtenWordBackground () {
      return this.isTrueWord ? 'written-word' : '.written-word bg-danger'
    },
    mps () {
      return 300 - this.words.length
    },
    accurancyPercent () {
      const percentage = (100 / this.mps)
      const accurancy = percentage * this.trueCount
      return isNaN(accurancy) ? 0 : accurancy
    }

  },
  mounted () {
    this.getWords()
  },
  methods: {
    newGame () {
      this.getWords()
      this.timer = 60
      this.isRunning = false
      this.isFinished = false
      this.isTrueWord = true
      this.writtenWord = ''
    },
    getWords () {
      // this is getting random 300 data  from our list of words
      this.words = this.listOfWords.sort(() => Math.random() - 0.5).splice(0, 300)
    },
    toggleTimer () {
      this.isRunning = true
      this.interval = setInterval(this.timeProcessing, 1000)
    },
    timeProcessing () {
      if (this.timer === 0) {
        clearInterval(this.interval)
        this.isFinished = true
      }
      this.timer--
    }
  }
}
</script>

<style>
.word {
  font-size: 25px;
  font-weight: 400;
}

.written-word {
  background-color: darkgray;
  border-radius: 5px;
  color: white;
  padding-top: 5px;
}

.big-button {
  width: 100%; /* Make the button take up the full width of its container */
  display: block; /* Ensure the button is displayed as a block element */
}
</style>
