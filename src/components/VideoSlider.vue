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
      class="item"
      :style="{left: `calc(${percent}% - 0.8rem)`}"
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
    }
  },
  methods: {
    createValueFromPosition(e) {
      const box = this.$refs.bar.getBoundingClientRect()
      const x = e.pageX - box.left + window.pageXOffset
      const y = e.pageY - box.top + window.pageYOffset
      const value = (x / box.width) * this.max
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
      this.$emit('input', this.createValueFromPosition(e))
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
.video_slider {
  display: flex;
  align-items: center;
  width: 100%;
  position: relative;
  user-select: none;
  .bar {
    width: 100%;
    height: 1.6rem;
    border: 0.1rem solid black;
    border-radius: 0.8rem;
    cursor: pointer;
  }
  .item {
    width: 2rem;
    height: 2rem;
    position: absolute;
    background-color: rgba(30, 30, 30, 1);
    border-radius: 50%;
    cursor: pointer;
    &:hover {
      background-color: rgba(80, 80, 80, 1);
    }
  }
}
</style>
