<template>
  <div class="video_frame">
    <div class="main_video_box">
      <video
        class="main_video"
        ref="video"
        preload="auto"
        @click="isPlay ? pause() : play()"
        @timeupdate="timeupdate"
        @loadedmetadata="loadedmetadata"
        @play="isPlay = true"
        @progress="progress"
      >
        <source :src="src" />
      </video>
      <div class="video_icon">
        <img v-if="!isPlay && !seeking" src="~@/assets/icon_play.svg"/>
      </div>
    </div>
    <video
      v-show="false"
      ref="videoHidden"
      preload="auto"
    >
      <source :src="src" />
    </video>
    <div class="controll_box">
      <VideoControll
        :play="isPlay"
        :mute="isMute"
        :currentTime="currentTime"
        :duration="duration"
        :volume="volume"
        :buffered="buffered"
        @play="play"
        @pause="pause"
        @mute="mute"
        @unmute="unmute"
        @seek="seek"
        @startSeek="startSeek"
        @endSeek="endSeek"
        @mousemoveOnSeekBar="mousemoveOnSeekBar"
        @changeVolume="changeVolume"
      >
      <canvas
        slot="seekSnap"
        ref="canvas"
      />
      </VideoControll>
    </div>
  </div>
</template>

<script>
import VideoControll from '@/components/VideoControll'
export default {
  components: {
    VideoControll
  },
  data: () => ({
    isPlay: false,
    isMute: false,
    currentTime: 0,
    duration: 1,
    volume: 0,
    snapping: false,
    seeking: false,
    buffered: []
  }),
  props: {
    src: { type: String, required: true }
  },
  computed: {
    atFirst() {
      return this.currentTime === 0
    },
    atLast() {
      return this.currentTime === this.duration
    }
  },
  methods: {
    progress() {
      this.buffered = []
      for (let i = 0; i < this.$refs.video.buffered.length; i++) {
        this.buffered.push({
          s: this.$refs.video.buffered.start(i),
          e: this.$refs.video.buffered.end(i)
        })
      }
    },
    play() {
      this.isPlay = true
      if (this.atLast) this.currentTime = 0
      this.startFrame()
      this.$refs.video.play()
    },
    pause() {
      this.isPlay = false
      this.$refs.video.pause()
    },
    mute() {
      this.isMute = true
      this.$refs.video.muted = true
    },
    unmute() {
      this.isMute = false
      this.$refs.video.muted = false
    },
    seek(value) {
      this.currentTime = Math.max(Math.min(value, this.duration), 0)
      this.$refs.video.currentTime = this.currentTime
    },
    startSeek() {
      this.seeking = true
      if (!this.isPlay) return
      this.$refs.video.pause()
    },
    endSeek() {
      this.seeking = false
      if (!this.isPlay) return
      if (this.atLast) return
      this.$refs.video.play()
    },
    timeupdate() {
      this.currentTime = this.$refs.video.currentTime
    },
    loadedmetadata() {
      this.duration = this.$refs.video.duration
      const rate = 160 / this.$refs.video.videoWidth
      this.$refs.canvas.width = this.$refs.video.videoWidth * rate
      this.$refs.canvas.height = this.$refs.video.videoHeight * rate
      this.volume = this.$refs.video.volume
      this.progress()
    },
    startFrame() {
      const me = () => {
        if (!this.isPlay) return
        if (this.atLast && !this.seeking) {
          this.isPlay = false
          return
        }
        this.currentTime = this.$refs.video.currentTime
        this.progress()
        requestAnimationFrame(me)
      }
      requestAnimationFrame(me)
    },
    mousemoveOnSeekBar(currentTime) {
      this.snap(currentTime)
    },
    snap({ value }) {
      if (this.snapping) return
      this.snapping = true
      this.$refs.videoHidden.currentTime = value
      setTimeout(() => {
        this.$refs.canvas
          .getContext('2d')
          .drawImage(
            this.$refs.videoHidden,
            0,
            0,
            this.$refs.canvas.width,
            this.$refs.canvas.height
          )
        this.snapping = false
      }, 10)
    },
    changeVolume(volume) {
      this.volume = volume
      this.$refs.video.volume = this.volume
    }
  }
}
</script>

<style lang="postcss" scoped>
.video_frame {
  width: 80%;
  height: auto;
  position: relative;
  font-size: 0;
  .main_video_box {
    position: relative;
    .main_video {
      max-width: 100%;
      max-height: 100%;
      cursor: pointer;
    }
    .video_icon {
      display: flex;
      align-items: center;
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      pointer-events: none;
      img {
        width: 30%;
        max-width: 6.4rem;
        height: auto;
        margin: auto;
        transition: all 0.1s;
      }
    }
    &:hover .video_icon img {
      width: 32%;
      max-width: 7rem;
      opacity: 0.9;
    }
  }
  .controll_box {
    // position: absolute;
    // bottom: 0;
    width: 100%;
  }
}
</style>
