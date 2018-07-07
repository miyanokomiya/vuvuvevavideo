<template>
  <div class="vide_controll">
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
        <p>{{spanTimeLabel}}</p>
      </div>
    </div>
    <div class="tool_box">
      <button class="play" @click="$emit(play ? 'pause' : 'play')">
        <img v-if="play" src="~@/assets/icon_pause.svg"/>
        <img v-else src="~@/assets/icon_play.svg"/>
      </button>
      <button class="back" @click="$emit('seek', currentTime - 10)">
        <span>10</span>
      </button>
      <div class="current_time"><span>{{currentTimeLabel}}</span></div>
      <button class="next" @click="$emit('seek', currentTime + 10)">
        <span>10</span>
      </button>
      <button class="mute" @click="$emit(mute ? 'unmute' : 'mute')">
        <img v-if="mute" src="~@/assets/icon_mute.svg"/>
        <img v-else src="~@/assets/icon_volume.svg"/>
      </button>
      <VideoSlider
        class="volume_sloder"
        :value="volume"
        :max="1"
        @input="val => $emit('changeVolume', val)"
      />
    </div>
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
    duration: { type: Number, default: 1 },
    volume: { type: Number, default: 0 }
  },
  computed: {
    spanTimeLabel() {
      return this.createTimeLabel(this.snapTime)
    },
    currentTimeLabel() {
      return this.createTimeLabel(this.currentTime)
    }
  },
  methods: {
    createTimeLabel(second) {
      const h = Math.floor(second / 60 / 60)
      const m = Math.floor((second / 60) % 60)
      const s = Math.floor(second % 60)
      // const mm = Math.floor((second - 60 * m - s) * 100)
      const beforeH = `${`0${m}`.slice(-2)}:${`0${s}`.slice(-2)}`
      return h > 0 ? `${h}:${beforeH}` : beforeH
    },
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
  background-color: rgb(75, 75, 75);
}
.slider_box {
  position: relative;
  padding: 0.2rem 0;
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
}
.tool_box {
  display: flex;
  align-items: center;
  button {
    background-color: white;
    border-bottom: inset;
    border: none;
    padding: 0;
    outline: none;
    user-select: none;
    cursor: pointer;
  }
  .play,
  .mute {
    width: 3rem;
    height: 3rem;
    img {
      width: auto;
      height: 80%;
      vertical-align: middle;
      transition: all 0.1s;
    }
    &:hover img {
      height: 90%;
    }
  }
  .back,
  .next {
    width: 3rem;
    height: 3rem;
    background-color: white;
    border: none;
    img {
      width: auto;
      height: 80%;
      vertical-align: middle;
    }
    span {
      border-radius: 50%;
      background-color: rgb(75, 75, 75);
      color: white;
      font-size: 1rem;
      transition: all 0.1s;
    }
    &:hover span {
      font-size: 1.2rem;
    }
  }
  .play {
    margin-right: 1rem;
  }
  .back {
    margin-right: 1rem;
    background-image: url('~@/assets/icon_back.svg');
  }
  .current_time {
    margin-right: 1rem;
    min-width: 8rem;
    background-color: rgb(75, 75, 75);
    color: white;
    font-size: 1.6rem;
    border-radius: 0.6rem;
    text-align: center;
  }
  .next {
    background-image: url('~@/assets/icon_next.svg');
  }
  .mute {
    margin-left: auto;
  }
  .volume_sloder {
    width: 30%;
  }
}
</style>
