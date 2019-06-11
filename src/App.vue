<template>
  <div id="app">
    <Main />
  </div>
</template>

<script>
import Main from '@/views/main/main'
export default {
  name: 'App',
  components: {Main}
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
* {
  margin: 0;
  padding: 0;
}
</style>
