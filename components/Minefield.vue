<template>
  <div id="minefield" oncontextmenu="return false;">
    <div class="row" v-for="row in minefield" :key="row.x">
      <minefield-cell
        v-for="cell in row"
        :key="cell.y"
        :cellData="cell"
        :bombIcon="bombIcon"
        :flagIcon="flagIcon"
        @onCellLeftClicked="onCellLeftClicked"
        @onCellRightClicked="onCellRightClicked"
      ></minefield-cell>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    minefield: Array,
    bombIcon: {
      type: String,
      default: '👽',
    },
    flagIcon: {
      type: String,
      default: '❗',
    },
  },
  data() {
    return {}
  },
  methods: {
    onCellLeftClicked(coords) {
      this.$emit('onCellLeftClicked', coords)
    },
    onCellRightClicked(coords) {
      this.$emit('onCellRightClicked', coords)
    },
  },
}
</script>

<style lang="scss" scoped>
#minefield {
  overflow: auto;
  margin: 6px auto 18px auto;
}

.row {
  display: table;
  margin: 0 auto;
}
.row + .row {
  margin-top: 0 !important;
}
</style>
