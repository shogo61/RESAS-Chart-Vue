<template>
	<div id="app">
    <h1>都道府県別 人口の推移</h1>
    <controlPref @onAddSeries="addSeries" @onRemoveSeries="removeSeries" />
    <div id="chart-area">
      <Highcharts :options="options" />
    </div>
  </div>
</template>

<script>
import controlPref from "./components/controlPref.vue";
import Highcharts from "highcharts";

export default {
  name: "App",
  components: {
    Highcharts: Highcharts,
    controlPref: controlPref,
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
          // 実測値が2015年まで。それ以降は推測値
          categories: [
            1960, 1965, 1970, 1975, 1980, 1985, 1990, 1995, 2000, 2005, 2010,
            2015,
          ],
        },
        yAxis: {
          title: {
            text: "人口数",
          },
        },
        tooltip: {
          // 初期状態のtooltipだと「年」、「人」が表示されず、人口も3桁おきに空白が空いてしまう
          // formatter()で3桁区切りのみ行うつもりだったが、他の値が表示されなくなってしまったので一度に全て表示させる
          // returnは「年」+「年単位(改行)」+「グラフの色の丸」+「都道府県名」+「：」+「3桁おきにカンマを追加した人口」+「人単位」
          // ユーザーからは見やすくなりましたが、コードがわかりにくくなってしまいました。。。
          formatter: function () {
            return (
              this.x +
              "年<br>" +
              `<span style="color:${this.color}"> ● </span>` +
              this.series.name +
              ": " +
              Highcharts.numberFormat(Math.round(this.y), 0, "", ",") +
              "人"
            );
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
      // highchartsでグラフを描画するためにデータを格納する
      this.options.series.push({
        name: name,
        data: population,
      });
      // グラフを上書きする
      this.drawChart(this.options);
    },
    // seriesからデータを削除する
    removeSeries: function (prefectureName) {
      //nameキーがprefectureNameと一致しているインデックスを返す
      var deleteIndex = this.options.series.indexOf(
        this.options.series.find((value) => value.name == prefectureName)
      );
      //seriesからdeleteIndex番目を削除する
      this.options.series.splice(deleteIndex, 1);
      //グラフを上書きする
      this.drawChart(this.options);
    },
  },
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin: 0px 100px;
}
h1 {
  font-size: 50px;
  margin: 40px;
}
h2 {
  text-align: left;
  margin-top: 0px;
  margin-left: 20px;
}
#chart-area {
  margin: 50px 0px;
}
@media screen and (max-width: 100px) {
  #app {
    margin: 500px;
  }
}
@media screen and (max-width: 500px) {
  #app {
    margin: 0px;
  }
  h1 {
    font-size: 35px;
  }
}
</style>
