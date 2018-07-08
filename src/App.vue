<template>
  <div id="app">
    <VideFrame src="https://player.vimeo.com/external/272460160.hd.mp4?s=ef7cfe6db7b4db13cc8bf33dae6978e30359dfee&profile_id=175&oauth2_token_id=57447761&download=1"/>
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
