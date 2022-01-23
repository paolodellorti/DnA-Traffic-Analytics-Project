<template>
  <button class="ControllerButton" @click="toggle" :ref="name">
      <h3> {{ name }} </h3>
      <h1> {{ liveValue }} </h1>
      &nbsp;
      <h2 class="variation"> {{ liveVariation }} </h2>
  </button>
</template>

<script>
export default {
  name: 'ControllerButton',
  props: {
    name: {
      type: String
    },
    value: {
      type: Object
    }
  },
  data() {
    return {
      liveVariation: 0
    }
  },
  methods: {
    toggle() { //assegno la classe .selected al bottone cliccato
      document.querySelectorAll(".ControllerButton").forEach(but => but.classList.remove('selected'))
      this.$refs[this.name].classList.add('selected')
    },
    sumObj(obj) { //ottengo somma dei valori dell'oggetto history
      return Object.values(obj.history)
                   .reduce((prev, curr) => prev + curr)
    },
    avgObj(obj) {
      let sum = this.sumObj(obj) //ottengo la media dei valori dell'oggetto history di avgTime
      return (sum / Object.values(obj.history).length).toFixed(2)
    },
    showVariation(diff) { //mostro l'incremento del valore per 2.5 sec
      this.liveVariation = diff >= 0 ? "+" + diff : diff
      let color = diff >= 0 ? "positiveVar" : "negativeVar"
      this.$refs[this.name].classList.add(color)
      setTimeout(() => {
        this.$refs[this.name].classList.remove(color)
      }, 2500)
    }
  },
  computed: { //per ogni modifica dei dati ricalcolo la somma (o la media, nel caso di avgTime)
    liveValue() {
      return this.name !== "avgTime" ? this.sumObj(this.value) :
                                       this.avgObj(this.value)
    }
  },
  watch: { //per ogni modifica dei dati calcolo la differenza col valore precedente e lo mostro
    liveValue(newVal, oldVal) {
      if (oldVal) {
        this.showVariation((newVal - oldVal).toFixed(2).replace(/[.,]00$/, ""))
      }
    }
  }
}
</script>

<style>
  .ControllerButton {
    width: 210px;
    color: white;
    background-color: transparent;
    border: 5px solid transparent;
    padding: 15px;
    padding-left: 3%;
    text-align: left;
    font-weight: bold;
    transition: all 0.2s;
    border-radius: 10px;
  }
  .ControllerButton:hover{
    background-color: #454545;
    transition: all 0.2s;
  }
  .ControllerButton:first-letter {
    text-transform: capitalize;
  }
  .selected {
    background-color: #30A3E6;
    border: 5px solid #000;
    transition: all 0.2s;
  }
  .selected:hover {
    background-color: #30A3E6;
  }
  .variation {
    opacity: 0;
    color: #0f0;
    transition: all 0.2s;
  }
  .positiveVar {
    color: #0f0;
    transition: all 0.2s;
  }
  .negativeVar {
    color: #f00;
    transition: all 0.2s;
  }
  .negativeVar>h2 {
    color: #f00;
    transition: all 0.2s;
  }
  .positiveVar>h2,
  .negativeVar>h2 {
    opacity: 1;
  }
  h1, h2 {
    display: inline-block;
  }
</style>