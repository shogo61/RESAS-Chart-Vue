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
      <!-- {{ prefecture.id }} -->
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
    changed(prefecture) {
			// 初期値はfalse
      prefecture.isChecked = !prefecture.isChecked;
      console.log(prefecture);
			// 初期状態だと必ずtrueになる
      if (prefecture.isChecked) {
				// 人口を取得する
        this.getPopulation(prefecture);
      } else {
				// AppでremeveSeriesを実行する
				this.$emit("onRemoveSeries",prefecture.name)
      }
    },
    // 各年の人口を取得
    getPopulation: async function (prefecture) {
			// 1960-2015までの人口が5年おきに格納されていく
			const population = [];
			// データを取得
      const path = `population/composition/perYear?&prefCode=${prefecture.id}`;
      const response = await this.fetchAPI(path);
			// 2015年までなのでi<12
			//欲しいのは総人口なので常にdata[0]
      for (var i = 0; i < 12; i++) {
				population.push(response.data.result.data[0].data[i].value);
      }
			// AppでaddSeriesを実行する
      this.$emit("onAddSeries", population, prefecture.name);
    },
  },
};
</script>
<style>
.prefecture{
	display: inline-block;
}
</style>