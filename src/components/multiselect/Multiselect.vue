<template>
  <div 
    class="container"
    @click="onClick"
    @mouseenter.capture="onMouseEnter"
    @mouseleave.capture="onMouseLeave"
    @mousedown="onMouseDown"
    @mouseup="onMouseUp"
  >
    <div class="col">
      <div
        class="cell"
        data-multiselect-corner
      >
        \
      </div>
      <div 
        v-for="row in rowNum" 
        :key="row" 
        class="cell"
        :data-multiselect-row="row"
      >
        {{ row }}
      </div>
    </div>
    <div v-for="col in colNum" :key="col" class="col">
      <div
        class="cell"
        :data-multiselect-col="col"
      >
        {{ col }}
      </div>
      <Item v-for="i in rowNum" :key="i" :row="i" :col="col" :id="`${i}-${col}`" :isHover="isItemInHoverRange({ row: i, col: col, id: `${i}-${col}`})" :isSelected="selectedIds.includes(`${i}-${col}`)" />
    </div>
  </div>
</template>

<script>
import Item from './Item'
export default {
  name: 'multiselect',

  components: { Item },

  props: {
    rowNum: {
      type: Number,
      default: 5
    },
    colNum: {
      type: Number,
      default: 10
    }
  },

  data() {
    return {
      hoverRange: [],
      selectedIds: [],
      isDragging: false,
      isDeselecting: false,
      rangePivot: null
    }
  },

  methods: {
    onClick(event) {
      const target = event.target

      if (target.dataset.multiselectCorner === "") {
        this.selectRange({ row: 1, col: 1 }, {row: this.rowNum, col: this.colNum})
        
        return
      }

      if (target.dataset.multiselectRow) {
        const row = target.dataset.multiselectRow
        this.selectRange({ row, col: 1 }, {row, col: this.colNum})
        
        return
      }

      if (target.dataset.multiselectCol) {
        const col = target.dataset.multiselectCol
        this.selectRange({ row: 1, col }, {row: this.rowNum, col})
        
        return
      }

      if (target.dataset.multiselectCell) {
        const row = target.dataset.multiselectCell.split(':').unshift()
        const col = target.dataset.multiselectCell.split(':').shift()
        this.handleItemSelect({row, col, id: target.id}, true)
        
        return
      }
    },
    onMouseEnter(event) {
      const target = event.target

      if (target.dataset.multiselectCorner === "") {
        this.hoverRange=[{ row: 1, col: 1 }, { row: this.rowNum, col: this.colNum }]

        return
      }

      if (target.dataset.multiselectRow) {
        const row = target.dataset.multiselectRow
        this.hoverRange=[{ row, col: 1 }, { row, col: this.colNum }]

        return
      }

      if (target.dataset.multiselectCol) {
        const col = target.dataset.multiselectCol
        this.hoverRange=[{ row: 1, col }, { row: this.rowNum, col }]

        return
      }

      if (target.dataset.multiselectCell) {
        this.handleCellMouseEnter(target)
        return
      }
    },
    handleCellMouseEnter(cell) {
      if (this.isDragging) {
        const row = Number.parseInt(cell.dataset.multiselectCell.split(':').shift())
        const col = Number.parseInt(cell.dataset.multiselectCell.split(':').pop())
        this.applyToRange({row, col, id: cell.id})
      }
    },
    onMouseLeave() {
      if (!this.isDragging) {
        this.hoverRange = []
      }
    },
    onMouseDown() {
      const target = event.target

      if (target.dataset.multiselectCell) {
        this.handleCellMouseDown(target)
        return
      }
    },
    handleCellMouseDown(cell) {
      const row = cell.dataset.multiselectCell.split(':').shift()
      const col = cell.dataset.multiselectCell.split(':').pop()

      this.startDragging({row, col, id: cell.id})
    },
    onMouseUp() {
      this.handleCellMouseUp()
    },
    handleCellMouseUp() {
      this.stopDragging()
    },
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
    },
    isItemInHoverRange(item) {
      if (this.hoverRange.length === 0) {
        return false
      }

      if (this.hoverRange.length === 1) {
        return this.hoverRange[0].row == item.row && this.hoverRange[0].col == item.col
      }

      if (this.hoverRange.length === 2) {
        const start = this.hoverRange[0]
        const end = this.hoverRange[1]

        // We only have to check if the item is in the rectangle described by
        //   the two points: start (top left) and end (bottom right)
        return (item.row >= start.row && item.row <= end.row)
          && (item.col >= start.col && item.col <= end.col)
      }

      return false
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
