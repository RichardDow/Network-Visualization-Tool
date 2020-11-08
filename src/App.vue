<template>
  <b-container class="mt-5">
    <d3Network />
  </b-container>
</template>

<script>
import d3Network from './components/d3Network.vue'
export default {
  name: 'App',
  components: {
    d3Network,
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
