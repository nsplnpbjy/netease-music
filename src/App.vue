<template>
  <div id="app">
    <hostPage/>
  </div>
</template>

<script>
import hostPage from "./components/hostPage.vue"
export default {
  name: 'App',
  components: {
    hostPage
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
  margin: 0;
  padding:0;
  background-color: whitesmoke;
}
body{
  margin:0;
  padding:0;
}
</style>
