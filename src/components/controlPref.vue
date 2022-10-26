<template>
  <!-- <h2>都道府県を選択</h2> -->
	<div class="toggle-title">
		<button type="button">都道府県を選択   ▼</button>
	</div>
  <div class="prefectures-area">
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
        {{ prefecture.name }}
      </label>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import $ from 'jquery'

const ACCESS_TOKEN = "Ea3RqjQjtf8O2XxJHuVlzUJLSxoClMzDpDSwg1IO";

export default {
  data() {
    return {
      prefectures: [],
      population: [],
      selectedPrefectures: [],
    };
  },
  emits: ["onAddSeries", "onRemoveSeries"],
  mounted() {
		this.getPrefectures();

		// ここだけjQueryで記述
		$(function(){
			//クリックで動く
			$('.toggle-title').click(function(){
				console.log("to")
				$('.prefectures-area').toggleClass('active');
				$(this).next('prefectures-area').slideToggle();
			});
		});
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
        this.disabledCheckbox(prefecture.id);
      } else {
        // AppでremeveSeriesを実行する
        this.$emit("onRemoveSeries", prefecture.name);
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
    // 連打防止
    // 連打されるとグラフが二重に表示されてしまう
    disabledCheckbox(id) {
      // prefecture.idを非活性化
      document.getElementById(id).setAttribute("disabled", true);
      // 500m秒後にdisabledを解除
      // 連打しても二重に表示される心配のない時間+正当な利用なら連打の必要はないのでこの時間設定
      setTimeout(function () {
        document.getElementById(id).removeAttribute("disabled");
      }, 500);
    },
  },
};
</script>
<style scoped>
.toggle-title{
	text-align: left;
	margin-left:20px;
	margin:20px;
}
.toggle-title button{
	background-color: #fff;
	font-size:20px;
}
.prefectures-area{
	display:none;
}
.active{
	display:block
}
.prefecture {
  display: inline-block;
  background-color: #f4f4f4;
  border: 1px #000000 solid;
  border-radius: 10px;
  padding: 3px;
  padding-right: 5px;
  margin: 4px;
}
</style>
