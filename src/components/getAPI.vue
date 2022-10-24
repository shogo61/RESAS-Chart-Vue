<template>
  <!-- <div class="prefectures-area"> -->
  {{ this.population }}
  <div
    v-for="prefecture in prefectures"
    :key="prefecture.id"
    class="prefecture"
  >
    <label>
      <input
        type="checkbox"
        :id="prefecture.id"
        :checked="prefecture.isChecked"
        :for="prefecture.id"
        @change="changed(prefecture)"
      />
      {{ prefecture.id }}
      {{ prefecture.name }}
    </label>
  </div>
	<div id="chart-area">

	</div>
  <!-- </div> -->
</template>

<script>
import axios from "axios";

const ACCESS_TOKEN = "Ea3RqjQjtf8O2XxJHuVlzUJLSxoClMzDpDSwg1IO";
// var cnt = 0;

export default {
  data() {
    return {
      prefectures: [],
      // population: [],
      selectedPrefectures: [],
    };
  },
	emits:["onAddSeries"],
  mounted() {
    this.getPrefectures();
  },
  methods: {
    fetchAPI: function (path) {
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/${path}`,
        {
          headers: { "X-API-KEY": ACCESS_TOKEN },
        }
      );
      return response;
    },
    getPrefectures: async function () {
      //asyncとawaitを付けないとresponseのresult以降を読み込まない
      const path = "prefectures";
      const response = await this.fetchAPI(path);
      console.log(response);
      // var id;
      this.prefectures = response.data.result.map((val) => {
        // valにprefCode,prefNameを格納する
        return {
          id: val["prefCode"],
          name: val["prefName"],
          isChecked: false,
        };
      });
      console.log(this.prefectures);
    },
    // チェックボックスがクリックされた時の処理
    // チェックがついたらselectedPrefectureに追加し、外れたらspliceで削除する
    changed(prefecture) {
      prefecture.isChecked = !prefecture.isChecked;
      console.log(prefecture);
      if (prefecture.isChecked) {
        this.getPopulation(prefecture);
      } else {
        //prefecture.nameのインデックスを検索して削除
        this.selectedPrefectures.splice(
          this.selectedPrefectures.indexOf(prefecture.name)
        );
        //removeChart();
      }
    },
    // 各年の人口を取得
    getPopulation: async function (prefecture) {

			const population = [];
      const path = `population/composition/perYear?&prefCode=${prefecture.id}`;
      const response = await this.fetchAPI(path);
      for (var i = 0; i < 12; i++) {
				population.push(response.data.result.data[0].data[i].value);
      }
      this.$emit("onAddSeries", population, prefecture.name);
    },
  },
};
</script>
