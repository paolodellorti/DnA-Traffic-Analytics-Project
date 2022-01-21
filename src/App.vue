<template>
  <div id="app">
    <h1>DnA Traffic Analitycs</h1>
    <div class="controllersContainer">
      <ControllerButton v-for="(value, name) in trafficData" 
                        :key="name" 
                        :name="name" 
                        :value="value"
                        :class="{ selected: name === 'subscriptions' }"
                        @click.native="renderChart(name)" 
                        />
    </div>
    <div class="chartContainer">
      <ChartComponent :chart-data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<script>
import ControllerButton from './components/ControllerButton.vue'
import ChartComponent from './components/ChartComponent.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    ControllerButton,
    ChartComponent
  },
  data() {
    return {
      chartData: null,
      chartOptions: {
        legend: {
          labels: {
            fontColor: "#fff"
          }
        },
        borderWidth: 5,
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          yAxes: [{
            gridLines: { color: "#9e9e9e" },
            ticks: {
              beginAtZero: true,
              color: "#9e9e9e"
            },
          }],
          xAxes: [{
            gridLines: { color: "#9e9e9e" }
          }]
        },
      },
      trafficData: {},
      currentChart: ""
    }
  },
  methods: {
    renderChart(label) { //renderizzo il grafico ogni volta che cambiano i dati, o l'utente seleziona un paramentro differente
      Chart.defaults.global.defaultFontColor = '#fff'
      this.currentChart = label
      this.chartData = {
        labels: Object.keys(this.trafficData[label].history).map(date => date.slice(-2) + " Jan"), //i dati arrivano da trafficData
        datasets: [{
          label,
          data: Object.values(this.trafficData[label].history),
          backgroundColor: "rgba(254, 74, 73, 0.7)",
          borderColor: "#FE4A49",
          pointBorderWidth: 10,
          pointHoverBorderWidth: 15,
          pointHitRadius: '3'
        }]
      }
    },
    updateData() { //eseguo un aggiornamento dei dati costante, ogni 5s
      let i = this.randomNumber(0, 3)
      let chart = Object.keys(this.trafficData)[i] //prendo uno dei 3 parametri a caso
      let sum = chart === "subscriptions" ? this.randomNumber(5, 15) :
                chart === "impressions" ? this.randomNumber(70, 120) :
                chart === "clicks" ? this.randomNumber(1, 7) :
                (Math.random() * 2 + 15).toFixed(2) //sommo un valore ipotetico e casuale, in un range differente per ogni parametro
      if (chart === "avgTime") {
        let difference = sum - this.trafficData[chart].total
        this.trafficData[chart].history["2021-01-07"] = parseFloat(sum)
        sum = difference.toFixed(2)
      } else {
        this.trafficData[chart].history["2021-01-07"] += sum
      }
      if (this.currentChart === chart) {
        this.renderChart(chart) //se l'aggiornamento dati avviene sul grafico visualizzato, lo ri-renderizzo 
      }                         //in modo da visualizzarlo in tempo reale sul grafico
      setTimeout(this.updateData, 5000)
    },
    randomNumber(min, max) { 
      return Math.floor(Math.random() * (max - min + 1) + min)
    }
  },
  mounted() { //appena montato il componente principale, eseguo la fetch e assegno i dati ottenuti all'oggetto trafficData
    axios
      .get('https://ott-fogliata.github.io/vuejs-s2i-repository/stats.json')
      .then(res => {
        this.trafficData = { ...res.data }
        this.renderChart('subscriptions')
        this.updateData()
      })
      .catch(err => console.log(err))      
  }
}
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Ubuntu:wght@300;400;500;700&display=swap');  
  
  body {
    text-align: center;
    font-family: 'Ubuntu', sans-serif;
    margin: 0;
    overflow-x: hidden;
    background: #505050;
    color: #fff;
  }
  .controllersContainer {
    display: flex;
    align-items: center;
    justify-content: space-around;
    flex-wrap: wrap;
    background-color: #505050;
    margin: 20px 0;
  }
  .chartContainer {
    width: 80%;
    margin: 0 auto;
  }
</style>
