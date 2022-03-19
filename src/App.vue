<template>
  <div class="container">
    <div class="ip"> 
      <input class="form-control" type="text" v-model="ipAddress">
      <div v-if="location" class="card">
        <div class="card-body">
          <p><strong>Your Current IP: </strong> {{ this.ipAddress }}</p>
          <p><strong>Country: </strong> {{ this.location.country }}</p>
          <p><strong>City: </strong> {{ this.location.city }}</p>
          <p><strong>Longitude: </strong> {{ this.location.ll[0] }}</p>
          <p><strong>Latitude: </strong> {{ this.location.ll[1] }}</p>
          <p><strong>Timezone: </strong> {{ this.location.timezone }}</p>
        </div> 
      </div>
      <button class="btn btn-primary" @click="getIpAddress">Enter IP Address</button>
    </div>
    <div class="row mt-5">
      <div class="col">
        <h1 class="text-center">COVID-19 DATA</h1>
      </div>
    </div>
    <div class="row mt-5" v-if="arrPositive.length > 0">
      <div class="col">
        <h2 class="text-center">Positive</h2>
        <line-chart
          :chartData="arrPositive"
          :options="chartOptions"
          :chartColors="arrPositiveColors"
          label="Positive"
        />
      </div>
    </div>

    <div class="row mt-5" v-if="arrPopulation.length > 0">
      <div class="col">
        <h2 class="text-center">Population</h2>
        <line-chart
          :chartData="arrPopulation"
          :options="chartOptions"
          :chartColors="arrPopulationColors"
          label="Population"
        />
      </div>
    </div>

    <div class="row mt-5" v-if="arrTest.length > 0">
      <div class="col">
        <h2 class="text-center">Tests</h2>
        <line-chart
          :chartData="arrTest"
          :options="chartOptions"
          :chartColors="arrTestColors"
          label="In Tests"
        />
      </div>
    </div>

    <div class="row mt-5" v-if="arrRecovered.length > 0">
      <div class="col">
        <h2 class="text-center">Recovered</h2>
        <line-chart
          :chartData="arrRecovered"
          :options="chartOptions"
          :chartColors="arrRecoveredColors"
          label="Recovered"
        />
      </div>
    </div>

    <div class="row mt-5 mb-5">
      <div class="col">
        <h2 class="text-center">Deaths</h2>
        <line-chart
          v-if="arrDeaths.length > 0"
          :chartData="arrDeaths"
          :options="chartOptions"
          :chartColors="arrDeathsColors"
          label="Deaths"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";

import LineChart from "./components/LineChart";

export default {
  components: {
    LineChart
  },
  data() {
    return {
      ipAddress: '',
      location: undefined,
      arrPositive: [],
      arrPositiveColors: {
        borderColor: "#077187",
        pointBorderColor: "#0E1428",
        pointBackgroundColor: "#AFD6AC",
        backgroundColor: "#74A57F"
      },
      arrPopulation: [],
      arrPopulationColors: {
        borderColor: "#251F47",
        pointBorderColor: "#260F26",
        pointBackgroundColor: "#858EAB",
        backgroundColor: "#858EAB"
      },
      arrTest: [],
      arrTestColors: {
        borderColor: "#190B28",
        pointBorderColor: "#190B28",
        pointBackgroundColor: "#E55381",
        backgroundColor: "#E55381"
      },
      arrRecovered: [],
      arrRecoveredColors: {
        borderColor: "#4E5E66",
        pointBorderColor: "#4E5E66",
        pointBackgroundColor: "#31E981",
        backgroundColor: "#31E981"
      },
      arrDeaths: [],
      arrDeathsColors: {
        borderColor: "#E06D06",
        pointBorderColor: "#E06D06",
        pointBackgroundColor: "#402A2C",
        backgroundColor: "#EE0F0F"
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false
      }
    };
  }, 
  methods:{
    getIpAddress(){
      axios
      .get('http://localhost:3000/api/get-geo-by-ip?ip='+this.ipAddress ).then((response) => {
        console.log(response);
        this.location = response.data;
      }).catch(err => {
        console.log(err)
      })
    }
  },

  async created() {

    // setInterval(async () => {
      // console.log("this from timeout");
      const { data } = await axios.get("https://corona.lmao.ninja/v2/continents?yesterday&sort");

    data.forEach(d => {
      const date = moment(new Date()).format("DD/MM/YYYY");
      const {
        cases,
        tests,
        population,
        todayRecovered,
        todayDeaths
      } = d;

      this.arrPositive.push({ date, total: cases });
      this.arrTest.push({ date, total: tests });
      this.arrPopulation.push({ date, total: population });
      this.arrRecovered.push({ date, total: todayRecovered });
      this.arrDeaths.push({ date, total: todayDeaths });
    });
    // }, 5000);
    
  }
};
</script>

<style>
</style>
