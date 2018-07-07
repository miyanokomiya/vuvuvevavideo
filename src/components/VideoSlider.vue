<template>
  <div class="video_slider"
    @mousedown="startDrag"
  >
    <div
      ref="bar"
      class="bar"
      @mousemove.self="mousemoveOnBar"
      @mouseout.self="$emit('mouseoutOnBar')"
    />
    <div
      ref="item"
      class="item"
      :style="{left: itemLeft}"
    />
  </div>
</template>

<script>
export default {
  props: {
    value: { type: Number, default: 0 },
    max: { type: Number, default: 100 },
    min: { type: Number, default: 0 }
  },
  computed: {
    percent() {
      return ((this.value - this.min) / (this.max - this.min)) * 100
    },
    itemLeft() {
      if (!this.$refs.bar || !this.$refs.item) return `${this.percent}%`
      const barBox = this.$refs.bar.getBoundingClientRect()
      const itemBox = this.$refs.item.getBoundingClientRect()
      return `${(this.percent * (barBox.width - itemBox.width)) /
        barBox.width}%`
    }
  },
  methods: {
    createValueFromPosition(e) {
      const barBox = this.$refs.bar.getBoundingClientRect()
      const itemBox = this.$refs.item.getBoundingClientRect()
      const x = e.pageX - barBox.left + window.pageXOffset
      const y = e.pageY - barBox.top + window.pageYOffset
      const value =
        ((x - itemBox.width / 2) / (barBox.width - itemBox.width)) * this.max
      return {
        value: Math.max(Math.min(value, this.max), this.min),
        x,
        y
      }
    },
    startDrag(e) {
      window.addEventListener('mousemove', this.drag)
      window.addEventListener('mouseup', this.endDrag)
      this.$emit('startDrag')
      this.drag(e)
    },
    drag(e) {
      this.$emit('input', this.createValueFromPosition(e).value)
    },
    endDrag(e) {
      window.removeEventListener('mousemove', this.drag)
      window.removeEventListener('mouseup', this.endDrag)
      this.$emit('endDrag')
    },
    mousemoveOnBar(e) {
      this.$emit('mousemoveOnBar', this.createValueFromPosition(e))
    }
  },
  beforeDestroy() {
    window.removeEventListener('mousemove', this.drag)
    window.removeEventListener('mouseup', this.endDrag)
  }
}
</script>

<style lang="postcss" scoped>
$bar_size: 0.4rem;
$bar_size_half: 0.2rem;
$bar_hover_size: 1rem;
$bar_hover_size_half: 0.5rem;
$item_size: 1.6rem;
$item_size_half: 0.8rem;
.video_slider {
  display: flex;
  align-items: center;
  width: 100%;
  height: $item_size;
  position: relative;
  user-select: none;
  cursor: pointer;
  .bar {
    transition: all 0.2s;
    width: 100%;
    height: $bar_size;
    background-color: white;
    border-radius: $bar_size_half;
  }
  .item {
    width: $item_size;
    height: $item_size;
    position: absolute;
    background-color: rgb(80, 80, 80);
    border: 0.1rem solid white;
    border-radius: 50%;
    cursor: pointer;
    &:hover {
      background-color: rgb(80, 80, 80);
    }
  }
  &:hover {
    .bar {
      height: $bar_hover_size;
      border-radius: $bar_size_half;
    }
  }
}
</style>
