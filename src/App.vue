<template>
  <div id="app">
    <h1>都道府県別人口推移</h1>
    <div class="prefectures-area">
      <getAPI @onAddSeries="addSeries" @onRemoveSeries="removeSeries" />
    </div>
    <div id="chart-area">
      <Highcharts :options="options" />
    </div>
  </div>
</template>

<script>
import getAPI from "./components/getAPI.vue";
import Highcharts from "../node_modules/highcharts";

export default {
  name: "App",
  components: {
    Highcharts: Highcharts,
    getAPI: getAPI,
  },
  data() {
    return {
      // Highchartsの処理
      options: {
        // seriesにオブジェクトを追加するとグラフに描画される
        series: [],
        title: {
          style: {
            display: "none",
          },
        },
        legend: {
          align: "right",
          verticalAlign: "top",
          layout: "vertical",
        },
        xAxis: {
          title: {
            text: "年度",
          },
					// 実測値が2015年まで
          categories: [
            1960, 1965, 1970, 1975, 1980, 1985, 1990, 1995, 2000, 2005, 2010, 2015,
          ],
        },
        yAxis: {
          title: {
            text: "人口数",
          },
        },
      },
    };
  },
  methods: {
		//optionsを用いてグラフを描く
    drawChart: async function (options) {
      Highcharts.chart("chart-area", options);
    },
		// seriesにデータを追加する
    addSeries: async function (population, name) {
      console.log(population);
      this.options.series.push({
        name: name,
        data: population,
      });
			// グラフを上書きする
      this.drawChart(this.options);
      console.log(this.options);
      console.log("");
    },
		// seriesからデータを削除する
    removeSeries: function (prefectureName) {
			//nameキーがprefectureNameと一致しているインデックスを返す
			var deleteIndex = this.options.series.indexOf(this.options.series.find(value => value.name == prefectureName))
			//seriesからdeleteIndex番目を削除する
			this.options.series.splice(deleteIndex,1)
			//グラフを上書きする
			this.drawChart(this.options)
			console.log(this.options.series)
    },
  },
};
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
.prefectures-area {
  margin: 100px;
}
</style>
