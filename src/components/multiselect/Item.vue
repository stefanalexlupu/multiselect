<template>
  <div 
    :class="['item', { 'is-hover': isHover }, { 'is-selected' : isSelected }]"
    @mousedown="onMouseDown"
    @mouseup="onMouseUp"
    @mouseenter="onMouseEnter"
  >
  </div>
</template>

<script>
/* eslint-disable no-console */
import VueEmitter from 'vue-emitter'

export default {
  mixins: [VueEmitter],

  props: {
    row: {
      type: Number,
      required: true
    },
    col: {
      type: Number,
      required: true
    },
    id: {
      type: String,
      required: true
    }
  },

  inject: ['multiselect'],

  computed: {
    isHover() {
      if (!this.multiselect) {
        return false
      }

      if (this.multiselect.hoverRange.length === 0) {
        return false
      }

      if (this.multiselect.hoverRange.length === 1) {
        return this.multiselect.hoverRange[0].row == this.row && this.multiselect.hoverRange[0].col == this.col
      }

      if (this.multiselect.hoverRange.length === 2) {
        const start = this.multiselect.hoverRange[0]
        const end = this.multiselect.hoverRange[1]

        return (this.row >= start.row && this.row <= end.row)
          && (this.col >= start.col && this.col <= end.col)
      }

      return false
    },
    isSelected() {
      if (!this.multiselect) {
        return false
      }

      return this.multiselect.selectedIds.findIndex(element => element === this.id) > -1
    }
  },

  methods: {
    onMouseDown() {
      this.dispatch('multiselect', 'item.mouse-down', { row: this.row, col: this.col })

    },
    onMouseUp() {
      this.dispatch('multiselect', 'item.mouse-up', { row: this.row, col: this.col })
    },
    onMouseEnter() {
      if (this.multiselect.isDragging) {
        this.dispatch('multiselect', 'item.mouse-enter', { row: this.row, col: this.col })
      }
    },
  }
}
</script>

<style scoped>
.item {
  height: 50px;
  width: 50px;

  margin: 5px;

  background-color: #4dd358;
  border: 1px solid #333333;
  border-radius: 2px;

  display: flex;
  align-items: center;
  justify-content: center;

  user-select: none;
}

.is-hover {
  background-color: #87ee90;
  /* background-color: red; */
}

.is-selected {
  background-color: rgb(64, 157, 238);
}
</style>

