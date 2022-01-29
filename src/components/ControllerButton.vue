<template>
  <button class="ControllerButton" @click="toggle" :ref="name">
      <h3> {{ name }} </h3>
      <h1> {{ value }} </h1>
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
      type: Number
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
      this.$emit('changeChart', this.name)
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
  watch: { //per ogni modifica dei dati calcolo la differenza col valore precedente e lo mostro
    value(newVal, oldVal) {
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
    background-color: #eee;
    transition: all 0.2s;
  }
  .ControllerButton:first-letter {
    text-transform: capitalize;
  }
  .selected {
    /* background-color: #30A3E6; */
    border: 5px solid #30A3E6;
    color: #30A3E6;
    transition: all 0.2s;
    box-shadow: 0 10px 50px rgb(92 99 105 / 60%);
  }
  .selected:hover {
    background-color: #fff;
  }
  .variation {
    opacity: 0;
    color: #71d62d;
    transition: all 0.2s;
  }
  .positiveVar {
    color: #58db00;
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