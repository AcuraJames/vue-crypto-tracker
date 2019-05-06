<template>
  <div id="app">
    <TrackerContainer />
  </div>
</template>

<script>
import TrackerContainer from './components/TrackerContainer.vue'

export default {
  name: 'app',
  components: {
    TrackerContainer
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
