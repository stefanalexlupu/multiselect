<template>
  <div class="container" @mousedown="startDragging" @mouseup="stopDragging">
    <div class="col">
      <div
        class="cell"
        @click="selectRange({ row: 1, col: 1 }, {row: 5, col: 10})"
        @mouseenter="hoverRange=[{ row: 1, col: 1 }, {row: 5, col: 10}]"
        @mouseleave="hoverRange=[]"
        @mousedown.stop=""
        @mouseup.stop=""
      >
        \
      </div>
      <div 
        v-for="i in 5" 
        :key="i" 
        class="cell" 
        @click="selectRange({ row: i, col: 1 }, {row: i, col: 10})"
        @mouseenter="hoverRange=[{ row: i, col: 1 }, {row: i, col: 10}]"
        @mouseleave="hoverRange=[]"
        @mousedown.stop=""
        @mouseup.stop=""
      >
        {{ i }}
      </div>
    </div>
    <div v-for="col in 10" :key="col" class="col">
      <div
        class="cell"
        @click="selectRange({ row: 1, col: col }, {row: 5, col: col})"
        @mouseenter="hoverRange=[{ row: 1, col: col }, {row: 5, col: col}]"
        @mouseleave="hoverRange=[]"
        @mousedown.stop=""
        @mouseup.stop=""
      >
        {{ col }}
      </div>
      <Item v-for="i in 5" :key="i" :row="i" :col="col" :id="`${i}-${col}`" />
    </div>
  </div>
</template>

<script>
import Item from './Item'
export default {
  name: 'multiselect',

  components: { Item },

  data() {
    return {
      hoverRange: [],
      selectedIds: [],
      isDragging: false,
      isDeselecting: false,
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
    this.$on('item.click', (item) => {
      this.handleItemSelect(item, true)
    })
  },

  methods: {
    startDragging(item) {
      this.isDragging = true
      
      if (item.row && item.col) {
        this.hoverRange = [{row: item.row, col: item.col}]
        this.rangePivot = item
      }

      if (this.selectedIds.findIndex(id => id === item.id) >= 0) {
        this.isDeselecting = true
      }
    },
    stopDragging() {
      if (this.hoverRange.length === 2) {
        this.selectRange(this.hoverRange[0], this.hoverRange[1])
      }

      this.isDragging = false
      this.isDeselecting = false
      this.rangePivot = null
      this.hoverRange = []
    },
    applyToRange(item) {
      if (!this.rangePivot) {
        this.hoverRange = [{row: item.row, col: item.col}]
        this.rangePivot = item
        return
      }

      let start = { row: this.rangePivot.row, col: this.rangePivot.col }
      let end = { row: item.row, col: item.col }

      // There are four cases, for dragging in 4 directions. We want to
      //   transform all the cases to case 1 (dragging to the bottom right) only
      //   once per range change here, so we do not have to treat the case
      //   inside the Item component for hover state detection.

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
    },
    handleItemSelect(item, byClick = false) {
      const index = this.selectedIds.findIndex(element => {
        if (item.id) {
          return element === item.id
        } else {
          return element === item
        }
      })

      if (index >= 0) {
        if (byClick || this.isDeselecting) {
          this.selectedIds.splice(index, 1)
        }
      } else {
        if (!this.isDeselecting) {
          this.selectedIds.push(item.id || item)
        }
      }
    },
    selectRange(start, end) {
      if (start && end) {
        for (let i = start.row; i <= end.row; i++) {
          for (let j = start.col; j <= end.col; j++) {
            this.handleItemSelect(`${i}-${j}`)
          } 
        }
      }
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

.cell {
  height: 50px;
  width: 50px;
  margin: 5px;
  border: 1px solid #333;
  border-radius: 2px;
  display: flex;
  align-items: center;
  justify-content: center;
  user-select: none;
}

.cell:hover {
  background-color: #dcffda;
}
</style>
