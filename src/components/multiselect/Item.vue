<template>
  <div
    :id="id"
    :data-multiselect-cell="row + ':' + col"
    :class="['item', { 'is-hover': isHover }, { 'is-selected' : isSelected }]"
  >
  </div>
</template>

<script>

export default {
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
      required: true
    },
    isHover: {
      type: Boolean,
      default: false
    },
    isSelected: {
      type: Boolean,
      default: false
    }
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

.item:hover,
.is-selected:hover {
  cursor: pointer;
}

.item.is-hover,
.item:hover {
  background-color: #87ee90;
  /* background-color: red; */
}

.is-selected {
  background-color: rgb(64, 157, 238);
}

.is-selected.is-hover,
.is-selected:hover {
  background-color: rgb(122, 184, 238);
}
</style>

