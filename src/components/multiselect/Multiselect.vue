<template>
  <div class="container">
    <div v-for="col in 5" :key="col" class="col">
      <Item v-for="i in 5" :key="i" :row="i" :col="col" />
    </div>
  </div>
</template>

<script>
import Item from './Item'
export default {
  components: { Item }
}
</script>

<style scoped>
.container {
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
}

.col {
  display: flex;
  flex-flow: column nowrap;
}
</style>
