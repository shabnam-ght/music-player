<template>
  <div class="wrapper">
    <div class="top-container">
      <div class="text-wrapper">
        <p @click="play">
          {{ currentTrack?.name }}
        </p>
        <p>{{ currentTrack?.artist }}</p>
      </div>
      <DarkModeButton class="DarkModeButton" />

      <div class="countDown">
        {{ countDown }}
      </div>
      <div class="progress">
        <div @mousedown="onMouseDown" @touchstart="onTouchStart">
          <div ref="progress" class="progress-bar">
            <div class="progress-current" :style="{ width: barWidth }"></div>
            <div class="needle"></div>
          </div>
        </div>
      </div>
    </div>
    <div class="bottom-container">
      <div :class="[{ isFavorite: currentTrack?.favorited }]" @click="favorite">
        <IconFavorite width="40" height="40" class="svg iconFav" />
      </div>
      <div class="backward-icon">
        <IconBackWard width="40" height="40" class="svg" @click="prevTrack" />
      </div>
      <div class="play-pause-icon" @click="play">
        <IconPause
          v-if="isTimerPlaying"
          width="40"
          height="40"
          class="c-play-audio__icon-pause-audio svg"
        />
        <IconPlay
          v-else
          width="40"
          height="40"
          class="c-play-audio__icon-play-audio svg"
        />
      </div>
      <div class="forward-icon">
        <IconForward width="40" height="40" class="svg" @click="nextTrack" />
      </div>
    </div>
  </div>
</template>

<script>
import IconPlay from 'assets/icon-play.svg'
import IconPause from 'assets/icon-pause.svg'
import IconBackWard from 'assets/icon-back-ward.svg'
import IconForward from 'assets/icon-forward.svg'
import IconFavorite from 'assets/icon-favorite.svg'
import DarkModeButton from '~/components/DarkModeButton.vue'

export default {
  name: 'PlayAudio',
  components: {
    DarkModeButton,
    IconPlay,
    IconPause,
    IconBackWard,
    IconForward,
    IconFavorite,
  },
  props: {},
  data() {
    return {
      audio: this.url,
      duration: null,
      isTimerPlaying: false,
      currentTime: null,
      currentTrack: null,
      barWidth: null,
      countDown: null,
      isPlayerActive: false,
      isDragActive: false,
      currentTrackIndex: 0,
      tracks: [
        {
          name: 'Mekanın Sahibi',
          artist: 'Norm Ender',
          source:
            'https://github.com/shabnam-ght/music-player/blob/main/mp3/1.mp3?raw=true',
          url: 'https://www.youtube.com/watch?v=z3wAjJXbYzA',
          favorited: false,
        },
        {
          name: 'Everybody Knows',
          artist: 'Leonard Cohen',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/2.mp3',
          url: 'https://www.youtube.com/watch?v=Lin-a2lTelg',
          favorited: true,
        },
        {
          name: 'Extreme Ways',
          artist: 'Moby',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/3.mp3',
          url: 'https://www.youtube.com/watch?v=ICjyAe9S54c',
          favorited: false,
        },
        {
          name: 'Butterflies',
          artist: 'Sia',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/4.mp3',
          url: 'https://www.youtube.com/watch?v=kYgGwWYOd9Y',
          favorited: false,
        },
        {
          name: 'The Final Victory',
          artist: 'Haggard',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/5.mp3',
          url: 'https://www.youtube.com/watch?v=0WlpALnQdN8',
          favorited: true,
        },
        {
          name: 'Genius ft. Sia, Diplo, Labrinth',
          artist: 'LSD',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/6.mp3',
          url: 'https://www.youtube.com/watch?v=HhoATZ1Imtw',
          favorited: false,
        },
        {
          name: 'The Comeback Kid',
          artist: 'Lindi Ortega',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/7.mp3',
          url: 'https://www.youtube.com/watch?v=me6aoX0wCV8',
          favorited: true,
        },
        {
          name: 'Overdose',
          artist: 'Grandson',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/8.mp3',
          url: 'https://www.youtube.com/watch?v=00-Rl3Jlx-o',
          favorited: false,
        },
        {
          name: "Rag'n'Bone Man",
          artist: 'Human',
          source:
            'https://raw.githubusercontent.com/shabnam-ght/music-player/main/mp3/9.mp3',
          url: 'https://www.youtube.com/watch?v=L3wKzyIN1yk',
          favorited: false,
        },
      ],
    }
  },

  watch: {
    isTimerPlaying(value) {
      if (value) {
        this.isPlayerActive = true
      }
    },
    countDown(value) {
      if (value === '00:00' && !this.isDragActive) {
        this.isTimerPlaying = false
        this.isPlayerActive = false
      }
    },
  },
  created() {
    this.currentTrack = this.tracks[0]
    this.audio = new Audio()
    this.audio.src = this.currentTrack.source
    this.audio.addEventListener('canplay', () => {
      this.generateTime()
      this.audio.ontimeupdate = () => {
        this.generateTime()
      }
      this.audio.onloadedmetadata = () => {
        this.generateTime()
      }
      this.audio.onended = () => {
        this.isTimerPlaying = true
      }
    })
  },
  mounted() {
    window.addEventListener('mousemove', this.onMouseMove)
    window.addEventListener('mouseup', this.onMouseUp)
    window.addEventListener('touchmove', this.onTouchMove)
    window.addEventListener('touchend', this.onTouchEnd)
    window.addEventListener('resize', this.onResize)
  },
  destroyed() {
    this.audio.pause()
    window.removeEventListener('mousemove', this.onMouseMove)
    window.removeEventListener('mouseup', this.onMouseUp)
    window.removeEventListener('touchmove', this.onTouchMove)
    window.removeEventListener('touchend', this.onTouchEnd)
    window.removeEventListener('resize', this.onResize)
  },
  methods: {
    play() {
      if (this.audio.paused) {
        this.audio.play()
        this.isTimerPlaying = true
      } else {
        this.audio.pause()
        this.isTimerPlaying = false
      }
    },

    generateTime() {
      if (!isNaN(this.audio.duration)) {
        const width = (100 / this.audio.duration) * this.audio.currentTime
        this.barWidth = width + '%'
        const durmin = Math.floor(this.audio.duration / 60)
        const dursec = Math.floor(this.audio.duration - durmin * 60)
        const curmin = Math.floor(this.audio.currentTime / 60)
        const cursec = Math.floor(this.audio.currentTime - curmin * 60)

        let countDownMin = Math.floor(
          (this.audio.duration - this.audio.currentTime) / 60
        )
        let countDownSec = Math.floor(
          this.audio.duration - this.audio.currentTime - countDownMin * 60
        )
        if (countDownMin < 10) {
          countDownMin = '0' + countDownMin
        }
        if (countDownSec < 10) {
          countDownSec = '0' + countDownSec
        }
        this.duration = durmin + ':' + dursec
        this.currentTime = curmin + ':' + cursec
        this.countDown = countDownMin + ':' + countDownSec
      }
    },
    updateBar(x) {
      const { progress } = this.$refs
      const { left } = progress.getBoundingClientRect()
      const maxduration = this.audio.duration
      const position = x - left
      let percentage = (100 * position) / progress.offsetWidth
      if (percentage > 100) {
        percentage = 100
      }
      if (percentage < 0) {
        percentage = 0
      }
      this.barWidth = percentage + '%'

      this.audio.currentTime = (maxduration * percentage) / 100
      this.audio.play()
    },

    prevTrack() {
      if (this.currentTrackIndex > 0) {
        this.currentTrackIndex--
      } else {
        this.currentTrackIndex = this.tracks.length - 1
      }
      this.currentTrack = this.tracks[this.currentTrackIndex]
      this.resetPlayer()
    },
    nextTrack() {
      if (this.currentTrackIndex < this.tracks.length - 1) {
        this.currentTrackIndex++
      } else {
        this.currentTrackIndex = 0
      }
      this.currentTrack = this.tracks[this.currentTrackIndex]
      this.resetPlayer()
    },
    resetPlayer() {
      this.barWidth = 0
      this.audio.currentTime = 0
      this.audio.src = this.currentTrack.source
      setTimeout(() => {
        if (this.isTimerPlaying) {
          this.audio.play()
        } else {
          this.audio.pause()
        }
      }, 300)
    },
    favorite() {
      this.tracks[this.currentTrackIndex].favorited =
        !this.tracks[this.currentTrackIndex].favorited
    },
    onMouseDown(e) {
      if (e.button === 0) {
        this.isTimerPlaying = true
        this.audio.pause()
        this.updateBar(e.pageX)
        this.isDragActive = true
      }
    },
    onTouchStart(e) {
      const pageXArray = [...e.targetTouches].map((touch) => {
        return touch.pageX
      })
      let pageX = 0
      pageXArray.forEach((x) => {
        pageX += x
      })
      pageX /= pageXArray.length

      this.isTimerPlaying = true
      this.audio.pause()
      this.updateBar(pageX)
      this.isDragActive = true
    },
    onMouseMove(e) {
      if (this.isDragActive) {
        this.isTimerPlaying = true
        this.updateBar(e.pageX)
      }
    },
    onTouchMove(e) {
      if (this.isDragActive) {
        const pageXArray = [...e.touches].map((touch) => {
          return touch.pageX
        })
        let pageX = 0
        pageXArray.forEach((x) => {
          pageX += x
        })
        pageX /= pageXArray.length
        this.isTimerPlaying = true
        this.updateBar(pageX)
      }
    },
    onMouseUp(e) {
      if (e.button === 0 && this.isDragActive) {
        this.isDragActive = false
        if (this.countDown === '00:00') {
          this.isTimerPlaying = false
          this.isPlayerActive = false
        }
      }
    },
    onTouchEnd(e) {
      if (e.touches.length === 0 && this.isDragActive) {
        this.isDragActive = false
        if (this.countDown === '00:00') {
          this.isTimerPlaying = false
          this.isPlayerActive = false
        }
      }
    },
    onResize() {
      this.audio.pause()
      this.isTimerPlaying = false
    },
  },
}
</script>

<style>
body {
  background: var(--background-color-secondary);
  font-family: 'Bitter', serif;
}

* {
  box-sizing: border-box;
}
.wrapper {
  width: 30%;
  display: block;
  align-items: center;
  justify-content: center;
  margin-top: 20%;
  margin-left: 30%;
  font-size: 30px;
}

.progress-bar {
  height: 2px;
  width: 100%;
  cursor: pointer;
  background-color: #0291aa;
  display: flex;
  border-radius: 10px;
}
.progress-current {
  height: inherit;
  width: 0%;
  background-color: #015665;
  border-radius: 10px;
}
.bottom-container {
  display: flex;
  align-items: center;
  justify-content: flex-end;

  box-shadow: 0px 15px 35px -5px rgba(0, 69, 88, 0.32);
  border-radius: 15px;
  padding: 20px 120px 20px 120px;
  margin-top: -20px;
  margin-right: -10px;
  margin-left: -10px;
  background-color: var(--background-color-primary);
}
.top-container {
  background-color: var(--background-color-primary);
  box-shadow: 0px 15px 35px -5px rgba(0, 42, 54, 0.32);
  border-radius: 15px;
  padding: 20px 100px 40px 100px;
}
.text-wrapper {
  color: var(--text-primary-color);
}
.countDown {
  font-size: 15px;
  display: inline-block;
  position: relative;
  left: 105%;
  color: var(--countdown-color);
  top: 15px;
}
.backward-icon {
  color: #0088a0cd;
}
.svg {
  color: #0088a0cd;
}

.svg:hover path {
  filter: drop-shadow(0 0 2.5px rgba(2, 81, 105, 0.575));
}
.iconFav {
  position: relative;
  right: 200px;
}
.forward-icon {
  color: #0088a0cd;
}
.play-pause-icon {
  margin: 10px;
  color: #0088a0cd;
}
.needle {
  width: 10px;
  height: 10px;
  background-color: var(--text-primary-color);
  border-radius: 50px;
  margin-top: -4px;
}
.isFavorite .svg {
  color: red;
}
.DarkModeButton {
  position: relative;
  left: 300px;
  bottom: 85px;
}

:root {
  --background-color-primary: #ebf6ff;
  --background-color-secondary: #dfe7ef;
  --countdown-color: #9c9c9c;
  --text-primary-color: #02404b;
  --element-size: 4rem;
}

/* Define styles for the root window with dark - mode preference */
:root.dark-theme {
  --background-color-primary: #1e1e1e;
  --background-color-secondary: #2d2d30;
  --countdown-color: #c1c1c1;
  --text-primary-color: #0088a0cd;
}
</style>
