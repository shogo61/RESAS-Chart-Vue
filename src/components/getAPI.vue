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
		{{showPrefectures}}
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
				<!-- <p>
					{{displayChart(prefecture.id)}}
				</p>
				{{populationData}} -->
      </label>
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
			showPrefectures: []
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
		changed(prefecture){
			prefecture.isChecked = !prefecture.isChecked
			console.log(prefecture)
			if(prefecture.isChecked){
				this.showPrefectures.push(prefecture.name);
			}else{
				this.showPrefectures.splice(this.showPrefectures.indexOf(prefecture.name),1)
			}
		}
		// 各年の人口を取得
    // displayChart: async function(id) {
		// 	console.log(id)
    //   const path = `population/composition/perYear?cityCode=11362&prefCode=${id}`;
    //   const response = await this.fetchAPI(path);
    //   console.log(response);
		// 	this.populationData = []
		// 	cnt ++;
		// 	// console.log(cnt)
		// 	if(cnt<47){
		// 		for(var i=0;i<8;i++){
		// 			this.populationData.push(response.data.result.data[0].data[i].value)
		// 		}
		// 	}
		// }
  },
};
</script>
