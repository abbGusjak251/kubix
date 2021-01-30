<template>
  <div id="app">
    <v-app>
      <div class="main">
        <h1>Kubix</h1>
        <img style="-webkit-user-drag: none;
            -khtml-user-drag: none;
            -moz-user-drag: none;
            -o-user-drag: none;
            user-drag: none; " 
            width="200px" 
            src="https://freesvg.org/img/cube_of_rubik_1.png"
        />
        <h1>{{displaytimer}}</h1>
        <div class="buttons">
          <v-btn :color="buttoncolor" v-on:click="timerClick">{{buttontext}}</v-btn>
          <br>
          <v-btn v-if="ready" style="color: white;" color="indigo" v-on:click="generateMix">Generate Scramble</v-btn>
          <v-btn v-if="!ready" style="color: white;" disabled>Generate Scramble</v-btn>
        </div>
        <p id="mix">{{mix}}</p>
        <v-text-field style="text-align: center;" v-model="scramblelength" :rules="rules" label="Scramble Length" width="5" dark></v-text-field>
        <v-snackbar v-model="snackbar" timeout="3000">You beat your personal record!</v-snackbar>
      </div>
    </v-app>
  </div>
</template>

<script>
export default {
  name: 'App',

  components: {
  },

  data() {
    return {
      rules: [
        value => value > 0 && value <= 30 || 'Too epic scramble',
        value => value.length <= 2 || 'Too epic scramble'
      ],
      log: [],
      timer: 0.0,
      displaytimer: 0.0,
      snackbar: false,
      interval: "",
      buttontext: "Start Timer",
      buttoncolor: "primary",
      moves: ["L", "R", "U", "D", "B", "F", "L2", "R2", "U2", "D2", "B2", "F2", "R'", "U'", "F'", "D'", "B'", "L'"],
      mix: "Generate a scramble",
      scramblelength: 20,
      ready: true
    }
  },
  created() {
    this.displaytimer = this.displaytimer.toFixed(2);
  },
  mounted() {
  },
  methods: {
    increase() {
      if(!this.ready) {
        this.timer += 0.01;
        this.displaytimer = (this.timer).toFixed(2);
      }else{
        clearInterval(this.interval);
        this.saveScore();
      }
    },
    timerClick() {
      if(this.ready) {
        this.snackbar = false;
        this.ready = false;
        this.timer = 0;
        this.interval = setInterval(() => {
          this.increase();
        }, 10);
        this.buttontext = "Stop Timer";
        this.buttoncolor = "red";
      }else {
        this.ready = true;
        this.buttontext = "Start Timer";
        this.buttoncolor = "primary";
      }
    },
    generateMix() {
      if(this.scramblelength > 30) {
        return;
      }
      this.mix = "";
      let list = this.moves;
      let len = this.scramblelength;
      for(let i=0; i<len; i++) {
        let move = list[Math.floor(Math.random()*list.length)];
        this.mix += move + " ";
      }
    },
    saveScore() {
      this.log.push(this.displaytimer);
      
      let latest = this.log[this.log.length-1];
      let min = Math.min.apply(null, this.log);

      if(min == latest) {
        this.snackbar = true;
      }
    }
  }
};
</script>

<style> 


  #app {
    background-color: indigo;
    user-select: none;
  }
  
  #app::-webkit-scrollbar {
    display: none;
  }

  #mix {
    color: white;
    text-shadow: 1px 1px 1px black;
    text-align: center;
    margin-top: 4vh;
    width: 200px;
  }

  .main {
    display: flex;
    align-items: center;
    flex-direction: column;
    margin-top: 2vh;
    width: 100vw;
  }

  .main h1 {
    color: white;
    text-shadow: 2px 2px 2px black;
    font-size: 100px;
  }

  .buttons {
    display: flex;
    flex-direction: column;
    transition: 1s;
  }

</style>
