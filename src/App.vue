<template>
  <div id="app">
    <label for="setRowNum">
      <p>How many rows?</p>
      <input v-model="rowNum" type="number" id="setRowNum">
    </label>
    <label for="setColNum">
      <p>How many columns?</p>
      <input v-model="colNum" type="number" id="setColNum">
    </label>
    <Multiselect :rowNum="Number.parseInt(rowNum)" :colNum="Number.parseInt(colNum)" />
  </div>
</template>

<script>
import Multiselect from './components/multiselect/Multiselect.vue'

export default {
  name: 'app',
  components: {
    Multiselect
  },

  data() {
    return {
      rowNum: 10,
      colNum: 15
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
