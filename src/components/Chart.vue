<template lang="html">
  <div class="chart-wrapper">
    <lineChart
      v-if="load"
      :chartData="dataCollections"
      :options="options"
      :styles="styles"
    ></lineChart>
    <div class="switcher">
      <div class="wrapper" v-for="currency in currencys" :key="currency.code">
        <input
          type="radio"
          :value="currency.code"
          class="state"
          :id="currency.code"
          v-model="currentCurrency"
        />
        <label
          class="label"
          :for="currency.code"
          @click="getData(currency.code)"
        >
          <div class="indicator"></div>
          <span class="text">{{ currency.name }}</span></label
        >
      </div>
    </div>
  </div>
</template>

<script>
const cryptoApi = axios.create({
  baseURL: `https://api.cryptonator.com/api/full/`,
  withCredentials: false,
  headers: {
    Accept: "application/json",
    "Content-Type": "application/json"
  },
  timeout: 10000
});

import numeral from "numeral";
import axios from "axios";
import lineChart from "@/components/LineChart";
export default {
  name: "sales-chart",
  data() {
    return {
      styles: {
        height: "100%",
        width: "40%",
        position: "relative"
      },
      options: {
        scales: {
          yAxes: [
            {
              ticks: {
                callback: value => numeral(value).format("$0,0")
              }
            }
          ]
        },
        tooltips: {
          callbacks: {
            label(tooltipItem, data) {
              const label = data.datasets[tooltipItem.datasetIndex].label;
              const value = numeral(tooltipItem.yLabel).format("$0,0");

              return `${label}: ${value}`;
            }
          }
        },
        xAxes: [
          {
            gridLines: {
              display: false
            }
          }
        ],
        legend: {
          display: true
        },
        responsive: true,
        maintainAspectRatio: false
      },
      currencys: [
        {
          code: "btc-usd",
          name: "Bitcoin"
        },
        { code: "eth-usd", name: "Ethereum" },
        { code: "xrp-usd", name: "XRP" },
        { code: "ltc-usd", name: "Litecoin" }
      ],
      currentCurrency: "btc-usd",
      load: false,
      dataPrice: null,
      dataMarkets: null,
      dataCollections: null
    };
  },
  components: {
    lineChart
  },
  methods: {
    getData(currency) {
      cryptoApi.get(currency).then(data => {
        let results = data.data.ticker.markets;
        let prices = results.map(a => a.price);
        let markets = results.map(a => a.market);

        this.dataPrice = prices;
        this.dataMarkets = markets;

        this.dataCollections = {
          labels: this.dataMarkets,
          datasets: [
            {
              label: this.currentCurrency,
              backgroundColor: "#f87979",
              data: this.dataPrice
            }
          ]
        };

        this.load = true;
      });
    }
  },
  mounted() {
    this.getData(this.currentCurrency);
  }
};
</script>

<style lang="css" scoped>
.chart-wrapper {
  display: flex;
  height: 50%;
  width: 100%;
  min-width: 50%;
  justify-content: center;
  min-height: 0;
}
.chart-wrapper > div {
}

.wrapper {
  margin: 8px 0;
  width: 100%;
  display: flex;
  justify-content: flex-start;
}

.state {
  position: absolute;
  top: 0;
  right: 0;
  opacity: 1e-5;
  pointer-events: none;
}

.label {
  display: inline-flex;
  align-items: center;
  cursor: pointer;
  color: #394a56;
}

.text {
  margin-left: 16px;
  opacity: 0.6;
  transition: opacity 0.2s linear, transform 0.2s ease-out;
}

.indicator {
  position: relative;
  border-radius: 50%;
  height: 30px;
  width: 30px;
  box-shadow: -8px -4px 8px 0px #ffffff, 8px 4px 12px 0px #d1d9e6;
  overflow: hidden;
}

.indicator::before,
.indicator::after {
  content: "";
  position: absolute;
  top: 10%;
  left: 10%;
  height: 80%;
  width: 80%;
  border-radius: 50%;
}

.indicator::before {
  box-shadow: -4px -2px 4px 0px #d1d9e6, 4px 2px 8px 0px #fff;
}

.indicator::after {
  background-color: #ecf0f3;
  box-shadow: -4px -2px 4px 0px #fff, 4px 2px 8px 0px #d1d9e6;
  transform: scale3d(1, 1, 1);
  transition: opacity 0.25s ease-in-out, transform 0.25s ease-in-out;
}

.state:checked ~ .label .indicator::after {
  transform: scale3d(0.975, 0.975, 1) translate3d(0, 10%, 0);
  opacity: 0;
}

.state:focus ~ .label .text {
  transform: translate3d(8px, 0, 0);
  opacity: 1;
}

.label:hover .text {
  opacity: 1;
}
.switcher {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-left: 50px;
}

</style>
