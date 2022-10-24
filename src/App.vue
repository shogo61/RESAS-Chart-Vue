<template>
  <div id="app">
    <h1>都道府県別人口推移</h1>
    <div class="prefectures-area">
			<getAPI @onAddSeries="addSeries" />
    </div>
		<div id="chart-area">
			<Highcharts :options="options" />
		</div>
  </div>
</template>

<script>
import getAPI from './components/getAPI.vue'
import Highcharts from '../node_modules/highcharts'

export default {
  name: 'App',
  components: {
		Highcharts: Highcharts,
		getAPI: getAPI,
  },
	data(){
		return {
      // Highchartsの処理
      options: {
        // seriesにオブジェクトを追加するとグラフに描画される
        series: [],
        title: {
          style: {
            display: "none"
          }
        },
        legend: {
          align: "right",
          verticalAlign: "top",
          layout: "vertical"
        },
        xAxis: {
          title: {
            text: "年度"
          },
          categories: [
            1960,
            1965,
            1970,
            1975,
            1980,
            1985,
            1990,
            1995,
            2000,
            2005,
            2010,
            2015,
          ]
        },
        yAxis: {
          title: {
            text: "人口数"
          }
        }
      }
    };
  },
	methods:{
		drawChart: async function(options){
			Highcharts.chart("chart-area",options)
		},
		addSeries: async function(population,name){
			console.log(population)
			this.options.series.push({
				name: name,
				data: population
			});
			this.drawChart(this.options);
			console.log(this.options)
			console.log("")
		}
	}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
