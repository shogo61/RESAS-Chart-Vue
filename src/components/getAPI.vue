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
    <div v-for="prefecture in prefectures" :key="prefecture.id" class="prefecture">
      <label :for="prefecture.id">
        <input type="checkbox" :id="prefecture.id" :checked="prefecture.isChecked" />
        {{ prefecture.name }}
      </label>
    </div>
  </div>
</template>

<!-- <script src="https://unpkg.com/axios@0.25.0/dist/axios.min.js"></script> -->
<script>
import axios from "axios";

const ACCESS_TOKEN = "WF4rwuik57LeNZclYjH0rdOmObq9tgTWmhtbBQoC"

export default {
  data: function () {
    return {
      prefectures: []
    }
  },
  mounted() {
    this.getPrefectures();
  },
  methods: {
    fetchAPI: function (path) {
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/${path}`,
        {
          headers: { "X-API-KEY": ACCESS_TOKEN }
        }
      );
      return response;
    },

    getPrefectures: async function () {
      const path = "prefectures";

      // const fs = require('fs');
      // const response = JSON.parse(fs.readFileSync(this.fetchAPI(path)))
      const response = await this.fetchAPI(path);
      console.log(response)
      this.prefectures = response.data.result.map(val => {
        return {
          id: val["prefCode"],
          name: val["prefName"],
          isChecked: false
        };
      });
      // console.log(this.prefectures)
    }
  }
}
</script>