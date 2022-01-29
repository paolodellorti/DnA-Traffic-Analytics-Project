<template>
  <div id="app">
    <h1>&#x1F4C8; DnA Traffic Analytics</h1>
    <div class="controllersContainer">
      <ControllerButton v-for="(value, name) in trafficData" 
                        :key="name" 
                        :name="name" 
                        :value="totalValue(name, value)"
                        :class="{ selected: name === 'subscriptions' }"
                        @changeChart="renderChart" 
                        />
    </div>
    <div class="chartContainer">
      <ChartComponent :chart-data="chartData" :options="chartOptions" />
    </div>
    <div class="credits">
      <a href="https://www.linkedin.com/in/paolo-dell-orti/" target="_blank">&copy; Paolo Dell'Orti</a> | 
      <a href="https://github.com/paolodellorti/DnA-Traffic-Analytics-Project" target="_blank">GitHub</a>
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
          display: false
        },
        borderWidth: 5,
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          yAxes: [{
            gridLines: { color: "#9e9e9e" },
            ticks: {
              beginAtZero: true
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
    renderChart(label) { //renderizzo il grafico ogni volta che cambiano i dati, o l'utente seleziona un grafico differente
      Chart.defaults.global.defaultFontColor = '#9e9e9e'
      this.currentChart = label
      this.chartData = {
        labels: Object.keys(this.trafficData[label].history).map(date => date.slice(-2) + " Jan"), //i dati arrivano da trafficData
        datasets: [{
          data: Object.values(this.trafficData[label].history),
          backgroundColor: "rgba(48, 163, 230, 0.7)",
          borderColor: "#30A3E6",
          pointBorderWidth: 10,
          pointHoverBorderWidth: 15,
          pointHitRadius: '3'
        }]
      }
    },
    updateData() { //eseguo un aggiornamento dei dati casuale e costante, ogni 3-8s
      let i = this.randomNumber(0, 3)
      let chart = Object.keys(this.trafficData)[i] //prendo uno dei 4 parametri a caso
      let sum = chart === "subscriptions" ? this.randomNumber(5, 15)
              : chart === "impressions" ? this.randomNumber(70, 120)
              : chart === "clicks" ? this.randomNumber(1, 7)
              : Number((Math.random() * 2 - 1).toFixed(2)) //sommo un valore ipotetico e casuale, in un range differente per ogni parametro
      this.trafficData[chart].history["2021-01-07"] += sum //poi lo aggiungo al dato di oggi.
      if (this.currentChart === chart) {//Se l'aggiornamento dati avviene sul grafico visualizzato, lo ri-renderizzo
        this.renderChart(chart)  //in modo da visualizzarlo in tempo reale sul grafico
      }                         
      setTimeout(this.updateData, this.randomNumber(3, 8) * 1000)
    },
    randomNumber(min, max) { 
      return Math.floor(Math.random() * (max - min + 1) + min)
    },
    totalValue(name, value) {//dopo averlo calcolato ritorno il totale del parametro scelto, da passare al button
      return name !== "avgTime" ? this.sumObj(value)
                                : this.avgObj(value)
    },
    sumObj(obj) { //ottengo somma dei valori del parametro scelto
      return Object.values(obj.history)
                   .reduce((prev, curr) => prev + curr)
    },
    avgObj(obj) {
      let sum = this.sumObj(obj) //ottengo la media dei valori di avgTime
      return parseFloat((sum / Object.values(obj.history).length).toFixed(2))
    },
  },
  mounted() { //appena montato il componente principale, eseguo la fetch e copio i dati ottenuti nell'oggetto trafficData
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
    position: relative;
  }
  .controllersContainer {
    display: flex;
    align-items: center;
    justify-content: space-around;
    flex-wrap: wrap;
    margin: 20px 0;
  }
  .chartContainer {
    width: 80%;
    margin: 0 auto;
  }
  .credits {
    position: absolute;
    top: 10px;
    right: 10px;
    font-size: 0.7rem;
    text-shadow: none;
  }
  .credits>a:link,
  .credits>a:visited {
    color: black;
    text-decoration: none;
    transition: all 0.2s;
  }
  .credits>a:hover {
    color: #30A3E6;
    transition: all 0.2s;
  }
  @media (max-width: 840px) {
    .ControllerButton {
      flex: 0 35%;
      height: 200px;
    }
    .credits {
      position: relative;
      padding: 15px 0;
    }
    .chartContainer {
      width: 98%;
    }
  }
</style>
