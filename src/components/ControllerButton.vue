<template>
  <button class="ControllerButton" @click="toggle" :ref="name + 'button'">
      <h3> {{ name }} </h3>
      <h1> {{ liveValue }} </h1>
      &nbsp;
      <h2 class="variation" :ref="name"> {{ liveVariation }} </h2>
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
      this.$refs[this.name + "button"].classList.add('selected')
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
      this.$refs[this.name].style.opacity = 1
      this.$refs[this.name + "button"].style.color = "#4f0"
      setTimeout(() => {
        this.$refs[this.name].style.opacity = 0
        this.$refs[this.name + "button"].style.color = "#fff"
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
    width: 200px;
    color: white;
    background-color: #505050;
    border: none;
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
    background-color: #fe4949;
    transition: all 0.2s;
  }
  .selected:hover {
    background-color: #fe4949;
  }
  .variation {
    color: #4f0;
    opacity: 0;
    transition: all 0.2s;
  }
  h1, h2 {
    display: inline-block;
  }
  @media (max-width: 830px) {
    .ControllerButton {
      flex: 0 35%;
    }
  }
</style>