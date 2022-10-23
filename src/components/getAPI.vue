<!-- <template>
  <div id="prefectures">
    <ul>
      <li v-for="prefecture in prefectures" :key="prefecrure.id">
        <input type="checkbox">
        {{prefecture}}
      </li>
    </ul>
  </div>
</template> -->
<template>
  <div class="prefectures-area">
		{{selectedPrefectures}}
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
				{{prefecture.id}}
        {{prefecture.name}}
      </label>
    </div>
		<div id="chart-area">
			{{getPopulation(selectedPrefectures)}}
		</div>
  </div>
</template>

<!-- <script src="https://unpkg.com/axios@0.25.0/dist/axios.min.js"></script> -->
<script>
import axios from "axios";
// import Highcharts from "https://code.highcharts.com/es-modules/masters/highcharts.src.js";

const ACCESS_TOKEN = "Ea3RqjQjtf8O2XxJHuVlzUJLSxoClMzDpDSwg1IO";
// var cnt = 0;

export default {
	data() {
		return {
			prefectures: [],
			populationData: [],
			selectedPrefectures: []
    };
  },
  mounted() {
    this.getPrefectures();
    this.displayChart();
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
      console.log(response)
			// var id;
      this.prefectures = response.data.result.map((val) => {
        // valにprefCode,prefNameを格納する
        return {
          id: val["prefCode"],
          name: val["prefName"],
          isChecked: false,
        };
      });
			// var id = prefecrure.id
      console.log(this.prefectures)
    },
		// チェックボックスがクリックされた時の処理
		// チェックがついたらselectedPrefectureに追加し、外れたらspliceで削除する
		changed(prefecture){
			prefecture.isChecked = !prefecture.isChecked
			console.log(prefecture)
			if(prefecture.isChecked){
				let val = {id: prefecture.id, name:prefecture.name};
				this.selectedPrefectures.push(val);
			}else{
				//prefecture.nameのインデックスを検索して削除
				this.selectedPrefectures.splice(this.selectedPrefectures.indexOf(prefecture.name),1)
			}
		},
		// 各年の人口を取得
    getPopulation: async function(selectedPrefecture){
			for(var i=0;i<this.selectedPrefectures.length;i++){
				this.populationData.splice(0);
				const path = `population/composition/perYear?cityCode=11362&prefCode=${selectedPrefecture[i].id}`;
				const response = await this.fetchAPI(path);
				console.log(response);
			// cnt ++;
			// console.log(cnt)
				
				// console.log(response)
				for(var j=0;j<8;j++){
					this.populationData.push(response.data.result.data[0].data[j].value)
				}
			}
			console.log(this.populationData)
		}
  },
};
</script>
