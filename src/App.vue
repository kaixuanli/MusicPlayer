<template>
  <div id="app">
    <!-- <Time></Time>
    <Weather></Weather> -->
    <MyMusic></MyMusic>
    
  </div>
</template>

<script>
import MyMusic from './components/MyMusic.vue'
// import Time from './components/Time'
// import Weather from './components/Weather'

export default {
  name: 'App',
  components: {
    MyMusic,
    // Time,
    // Weather
  }
}
</script>

<style>
#app {
  /* background-color:#FFE4E1; */
  position: fixed;
  width: 100%;
  height: 100%;
}
</style>
