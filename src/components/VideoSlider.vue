<template>
  <div class="video_slider"
    @mousedown="startDrag"
  >
    <div
      ref="bar"
      class="bar"
      @mousemove.self="mousemoveOnBar"
      @mouseout.self="$emit('mouseoutOnBar')"
    >
      <div
        class="buffered"
        v-for="(interval, key) in bufferedIntervals"
        :key="key"
        :style="interval"
      />
    </div>
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
    min: { type: Number, default: 0 },
    buffered: { type: Array, default: () => [] }
  },
  computed: {
    percent() {
      return this.getRate(this.value) * 100
    },
    itemLeft() {
      const barBox = this.createBarBox()
      const itemBox = this.createItemBox()
      return `${(this.percent * (barBox.width - itemBox.width)) /
        barBox.width}%`
    },
    bufferedIntervals() {
      const barBox = this.createBarBox()
      return this.buffered.reduce((p, c) => {
        p[c.s] = {
          left: `${this.getRate(c.s) * barBox.width}px`,
          width: `${((c.e - c.s) / this.max) * barBox.width}px`
        }
        return p
      }, {})
    }
  },
  methods: {
    getRate(value) {
      return (value - this.min) / (this.max - this.min)
    },
    createBarBox() {
      if (!this.$refs.bar) return { left: 0, top: 0, width: 1, height: 1 }
      return this.$refs.bar.getBoundingClientRect()
    },
    createItemBox() {
      if (!this.$refs.item) return { left: 0, top: 0, width: 1, height: 1 }
      return this.$refs.item.getBoundingClientRect()
    },
    createValueFromPosition(e) {
      const barBox = this.createBarBox()
      const itemBox = this.createItemBox()
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
    position: relative;
    overflow: hidden;
  }
  .buffered {
    height: 100%;
    position: absolute;
    background-color: #aaa;
    pointer-events: none;
    transition: all 0.2s;
  }
  .item {
    width: $item_size;
    height: $item_size;
    position: absolute;
    background-color: rgb(80, 80, 80);
    border: 0.1rem solid white;
    border-radius: 50%;
    cursor: pointer;
    transition: all 0.1s;
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
