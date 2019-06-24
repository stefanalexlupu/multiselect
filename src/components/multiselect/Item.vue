<template>
  <div 
    :class="['item', { isHover: 'is-hover' }, { isSelected: 'is-selected' }]"
  >
    {{ row }} / {{ col }}
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
</style>

