<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
  <div id="app">
    <h1>都道府県別人口推移</h1>
    <div class="prefectures">
      <getAPI></getAPI>

    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import getAPI from './components/getAPI.vue'

export default {
  name: 'App',
  components: {
    getAPI
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
