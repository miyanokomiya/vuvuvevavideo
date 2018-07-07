<template>
  <div class="vide_controll">
    <button class="play" @click="$emit(play ? 'pause' : 'play')">
      <img v-if="play" src="~@/assets/icon_pause.svg"/>
      <img v-else src="~@/assets/icon_play.svg"/>
    </button>
    <div class="slider_box">
      <VideoSlider
        :value="currentTime"
        :max="duration"
        @input="val => $emit('seek', val)"
        @startDrag="$emit('startSeek')"
        @endDrag="$emit('endSeek')"
        @mousemoveOnBar="mousemoveOnBar"
        @mouseoutOnBar="showSnapCanvas = false"
      />
      <div
        v-show="showSnapCanvas"
        class="snap_canvas"
        ref="snapCanvas"
        :style="{left: `${snapCanvasLeft}px`}"
      >
        <slot name="seekSnap"/>
        <p>{{timeLabel}}</p>
      </div>
    </div>
    <button class="mute" @click="$emit(mute ? 'unmute' : 'mute')">
      <img v-if="mute" src="~@/assets/icon_mute.svg"/>
      <img v-else src="~@/assets/icon_volume.svg"/>
    </button>
  </div>
</template>

<script>
import VideoSlider from '@/components/VideoSlider'

export default {
  components: {
    VideoSlider
  },
  data: () => ({
    snapCanvasLeft: 0,
    showSnapCanvas: false,
    snapTime: 0
  }),
  props: {
    play: { type: Boolean, default: false },
    mute: { type: Boolean, default: false },
    currentTime: { type: Number, default: 0 },
    duration: { type: Number, default: 1 }
  },
  computed: {
    timeLabel() {
      return Math.round(this.snapTime * 100) / 100
    }
  },
  methods: {
    mousemoveOnBar({ value, x }) {
      this.$emit('mousemoveOnSeekBar', { value })
      if (this.$slots.seekSnap.length === 0) return
      this.showSnapCanvas = true
      this.snapTime = value
      this.snapCanvasLeft = x - this.$slots.seekSnap[0].elm.width / 2
    }
  }
}
</script>

<style lang="postcss" scoped>
.vide_controll {
  display: flex;
  align-items: center;
}
.play,
.mute {
  width: 6rem;
  height: 3rem;
  cursor: pointer;
  user-select: none;
  background-color: white;
  user-select: none;
  outline: none;
  border-bottom: inset;
  border: 0.1rem solid black;
  border-radius: 0.2rem;
  img {
    width: auto;
    height: 80%;
    vertical-align: middle;
  }
  &:hover img {
    height: 90%;
  }
}
.slider_box {
  position: relative;
  width: calc(100% - 12rem - 2rem);
  margin: 0 1.2rem;
}
.snap_canvas {
  position: absolute;
  bottom: 2rem;
  border-radius: 1rem;
  overflow: hidden;
  font-size: 0;
  p {
    margin: 0;
    text-align: center;
    background-color: black;
    color: white;
    font-size: 1.4rem;
  }
}
</style>
