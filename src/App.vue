<template>
  <div>

    <div class="Reset-btn" v-if="finishMessage">
      <button @click="reset()" class="Flat-btn">Try again?</button>
    </div>

    <div class="Score-keep">
      <div class="top-row">
        <div class="turns">
          Turns: {{turns}}
        </div>
        <div class="timer">
          Time: {{timer.minutes}} : 
          <span v-if="timer.seconds < 10">
            0{{timer.seconds}}
          </span>
          <span v-else>
            {{timer.seconds}}
          </span>
        </div>
      </div>
      <div class="bottom-row">
        <div class="message" v-if="finishMessage">
          {{finishMessage}}
        </div>
      </div>
    </div>
  <CardTable 
    :cards=cards
    @flip-card="flipCard"
  ></CardTable>
  </div>
</template>

<script>
import CardTable from './components/CardTable.vue'

export default {
  name: 'App',
  components: {
    CardTable
  },
  data () {
    return this.initialData();
  },

  created() {
    this.generateRandomCards();
  },

  methods: {

    initialData () {
      return {
          cards: [],
          timer: {
            minutes: 0,
            seconds: 0,
          },
          turns: 0,
          matchCheck: [],
          gameStatus: 'prestarted',
          totalMatchedCards: 0,
          finishMessage: '',
        }
    },

    generateRandomCards () {
      // using fontawesome icons as our cards
      let cardArray = [];
      const iconsToChooseFrom = [
        'star', 'umbrella', 'leaf', 'paperclip',
        'rocket', 'taxi', 'birthday-cake', 'bank',
        'bug', 'eye', 'graduation-cap', 'key',
      ];
      const colors = [
        'gold', 'black', 'green', 'darkslategray',
        'red', 'moccasin', 'pink', 'darkgreen',
        'lightseagreen', 'dodgerblue', 'blue', 'brown',
      ];

      // generate half of our cards
      for (let i = 0; i < 12; i++) {
        let randomNumber = Math.floor(Math.random() * iconsToChooseFrom.length);
        cardArray.push({
          'icon': iconsToChooseFrom[randomNumber],
          'color': colors[randomNumber],
          'flipped': false,
          'matched': false,
        });
        iconsToChooseFrom.splice(randomNumber, 1);
        colors.splice(randomNumber, 1);
      }

      // now generate the other half so we're sure each will have a pair
      let secondHalf = [];
      for (let i = 0; i < cardArray.length; i++) {
        secondHalf.push(JSON.parse(JSON.stringify(cardArray[i])));
      }

      // bring them together, shuffle, and set state
      cardArray = cardArray.concat(secondHalf);
      cardArray = cardArray.map(card => [Math.random(), card])
      .sort((a,b) => a[0] - b[0])
      .map(a => a[1]);

      // give each card a unique key for iteration
      cardArray.forEach((card, index) => {
        card.number = index;
      });

      this.cards.splice(0);
      this.cards = cardArray;
    },

    flipCard (a) {
      // keep the user from selecting the same card twice in a row
      if (a.flipped || a.matched || this.matchCheck.length === 2) {
        return false;
      }
      a.flipped = true;

      // start the timer
      if (this.timer.seconds < 1) {
        this.startTimer();
        this.gameStatus = 'started';
      }
      
      // push vard ref to array
      this.matchCheck.push(a);
      if (this.matchCheck.length === 2) {
        this.turns++;
        setTimeout(() => { // a short delay to let the user see the result
        if (this.matchCheck[0].icon === this.matchCheck[1].icon) {
          this.matchCheck[0].matched = true;
          this.matchCheck[1].matched = true;
          this.totalMatchedCards = this.totalMatchedCards + 2;
        } else {
          this.matchCheck[0].flipped = false;
          this.matchCheck[1].flipped = false;          
        }

        this.matchCheck.splice(0);
        this.checkForFinish();
        }, 600)
      }

      if (this.totalMatchedCards > this.cards.length-1) {
        this.gameStatus = 'finished';
        this.stopTimer();
      }

    },

    checkForFinish () {
      // watch our total number of matches to know when to stop
      if (this.totalMatchedCards > this.cards.length-1) {
        this.gameStatus = 'finished';
       
        // a little score keeping in local storage
        let best = localStorage.getItem('bestscore') || '';
        if (this.turns !== 0 && (this.turns < best || best === '')) {
          this.finishMessage = `A new best! ${this.turns} turns!`;
          localStorage.setItem('bestscore', this.turns);
        } else {
          this.finishMessage = `Your previous best was ${best} turns.`;
        }

      }
    },

    startTimer () {
      // Todo: Setinterval is gross of course,
      // but building an every 500ms timecheck fix
      // is too much for this. Should look at
      // vue-timers mixin sometime though.
      const timerInterval = setInterval(() => {
        if (this.gameStatus === 'finished') {
          clearInterval(timerInterval)
        }
          this.timer.seconds = this.timer.seconds + 1;
          if (this.timer.seconds > 59) {
            this.timer.minutes = this.timer.minutes + 1;
            this.timer.seconds = 0;
          }
      }, 1000)
    },

    reset () {
        Object.assign(this.$data, this.$options.data.call(this));
        
        setTimeout(()=> {
          this.generateRandomCards();
        }, 500);
    }
  }
}
</script>

<style>
body {
    font-family: 'Lato', sans-serif;
    margin: 0;
    padding: 0;
}

.Flat-btn {
  color: #000;
  width: 100px;
  height: 40px;
  outline: none;
  cursor: pointer;
  background: #fff;
  transition: .5s;
  font-size: 18px;
  border-radius: 5px;
  font-family: 'Lato',sans-serif;
  z-index:999
}
.Flat-btn:hover{
  border: none;
  color: #4b99e7;
  transform:scale(1.1);
}

.Score-keep {
  width: 50%;
  background: #4b99e7;
  border-bottom-right-radius: 5px;
  border-bottom-left-radius: 5px;
  margin: 0 auto;
  height: 50px;
}

.top-row {
  display:flex;
  justify-content: space-around;
}

.turns, .timer {
  background: #fff;
  display: inline;
  font-weight: bold;
  width: 20%;
  text-align: center;
  height: 20px;
  border-bottom-right-radius: 5px;
  border-bottom-left-radius: 5px;
}

.bottom-row {
  width: 100%;
  color: #fff;
  text-align: center;
  padding: 5px 0;
}

.Reset-btn {
  margin: -20px 0 0 -50px;
  position: absolute;
  top: 50%;
  left: 50%;

}

@media (max-width: 1024px) {
  .Score-keep { 
    width: 70%;
  }
  .turns, .timer {
    width: 30%;
  }
}

@media (max-width: 768px) {
  .turns, .timer {
    width: 40%;
  }
}
</style>
