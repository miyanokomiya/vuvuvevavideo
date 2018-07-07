<template>
  <div id="app">
    <VideFrame/>
  </div>
</template>

<script>
import VideFrame from './components/VideFrame'

export default {
  name: 'App',
  components: {
    VideFrame
  }
}
</script>

<style>
html {
  font-size: 62.5%; /* -> 10px; */
}
body {
  font-size: 1.4em; /* -> 14px; */
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
