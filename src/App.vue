<template>
  <div id="app">
    <Multiselect />
  </div>
</template>

<script>
import Multiselect from './components/multiselect/Multiselect.vue'

export default {
  name: 'app',
  components: {
    Multiselect
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
