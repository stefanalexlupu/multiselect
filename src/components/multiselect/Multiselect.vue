<template>
  <div class="container" @mousedown="startDragging" @mouseup="stopDragging">
    <div v-for="col in 10" :key="col" class="col">
      <Item v-for="i in 5" :key="i" :row="i" :col="col" :id="`${i}-${col}`" />
    </div>
  </div>
</template>

<script>
/* eslint-disable no-console */
import Item from './Item'
export default {
  name: 'multiselect',

  components: { Item },

  data() {
    return {
      hoverRange: [],
      selectedIds: [],
      isDragging: false,
      rangePivot: null
    }
  },

  provide() {
    return { multiselect: this }
  },

  created() {
    this.$on('item.mouse-down', this.startDragging)
    this.$on('item.mouse-up', this.stopDragging)
    this.$on('item.mouse-enter', this.applyToRange)
  },

  methods: {
    startDragging(itemPosition) {
      this.isDragging = true
      if (itemPosition.row && itemPosition.col) {
        this.hoverRange = [itemPosition]
        this.rangePivot = itemPosition
      }
    },
    stopDragging() {
      this.isDragging = false

      // TODO: select items
      if (this.hoverRange.length === 2)
      for (let i = this.hoverRange[0].row; i <= this.hoverRange[1].row; i++) {
        for (let j = this.hoverRange[0].col; j <= this.hoverRange[1].col; j++) {
          this.selectedIds.push(`${i}-${j}`)
        } 
      } else if (this.hoverRange.length === 1) {
        console.log('here')
        this.selectedIds.push(`${this.hoverRange[0].row}-${this.hoverRange[0].col}`)
      }

      this.rangePivot = null
      this.hoverRange = []
    },
    applyToRange(itemPosition) {
      if (!this.rangePivot) {
        this.hoverRange = [itemPosition]
        this.rangePivot = itemPosition
        return
      }

      let start = { ...this.rangePivot }
      let end = { ...itemPosition }

      // CASE 2: Dragging up
      if (start.row > end.row) {
        const aux = start.row
        start.row = end.row
        end.row = aux
      }

      // CASE 3: Dragging left
      if (start.col > end.col) {
        const aux = start.col
        start.col = end.col
        end.col = aux
      }
      this.hoverRange = [start, end]
    }
  }
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
