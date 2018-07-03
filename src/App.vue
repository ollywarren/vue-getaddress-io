<template>
  <div id="app">
    <img src="./assets/logo.png">
    <div style="width: 50%; margin-left: auto; margin-right:auto;">
      <GetAddress :apiKey="apiKey" v-on:address-selected="logAddress"/>
    </div>
  </div>
</template>

<script>
import GetAddress from './components/getaddress-io.vue'

export default {
  name: 'app',
  components: {
    GetAddress
  },
  data() {
    return {
      apiKey: "K-lNWjgMGkCYDBQIsu0u4g13208"
    }
  },
  methods: {
    logAddress: function(address) {
      /* eslint-disable */ 
      console.log(address);
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
