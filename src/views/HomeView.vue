<template>
  <div class="video-header">
    <label> Bard Hub </label>
    <div class="video-controls">
      <button @click="playPause">Play/Pause</button>

      <input class="slider timeSlider" type="range" min="0" max="100" v-model="timeSlider" @input="changeCurrentTime">
      <button @click="stop">Stop</button>

      <input class="slider rangeSlider" type="range" min="0" max="100" v-model="volumeSlider" @input="changeVolume">
      <button @click="mute">Mute</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      audio: undefined,
      volumeSlider: 80,
      timeSlider: 0
    }
  },
  methods: {
    async play() {
      try {
        if (this.audio != undefined) {
          this.audio = undefined
        }

        const sound = await import("../assets/candy.mp3");
        // Add validation
        let audio = new Audio(sound.default);
        this.audio = audio;
        
        this.audio.addEventListener('loadedmetadata', () => {
            this.audio.volume = parseFloat(this.volumeSlider / 100);
            this.audio.currentTime = audio.duration * this.timeSlider / 100;
            this.audio.play();
            if (this.audio.currentTime < 0) {
              this.audio = undefined
            }
          });

          this.audio.addEventListener('timeupdate', () => {
            this.timeSlider = (this.audio.currentTime / this.audio.duration) * 100;
            if (this.audio.currentTime >= this.audio.duration || this.audio.currentTime < 0) {
              this.timeSlider = 0
              this.audio = undefined
            }
          });
        } catch (error) {
          console.error(`Failed to change play sound ${error}`);
      }
    },
    async playPause() {
      if (this.audio && this.audio.currentTime > 0) {
        this.audio.pause()
      }
      else {
        await this.play()
      }
    },
    stop() {
      try {
        this.audio.pause()
        this.audio.removeEventListener('timeupdate', () => {});
        this.audio.removeEventListener('loadedmetadata', () => {});
        this.timeSlider = 0
        this.audio.currentTime = -1
      }
      catch (error) {
        console.error(`Failed to stop due to ${error}`)
      }
    },
    mute() {
      this.volumeSlider = 0

      if (this.audio) {
        this.audio.volume = 0
      }
    },
    changeVolume(event) {
      try {
        if (this.audio) {
          this.volumeSlider = event.target.value;
        }
      }
      catch (error) {
        console.log(`Failed to change volume sound ${error}`)
      }
    },
    changeCurrentTime(event) {
      try {
        if (this.audio) {
          this.timeSlider = event.target.value
          this.audio.currentTime = parseFloat(this.timeSlider / 100) * this.audio.duration
          this.audio.play();
        }
      }
      catch (error) {
        console.log(`Failed to change current time due to ${error}`)
      }
    }
  }
}
</script>

<style scoped>
.video-header {
  display: flex;
  align-items: center;
  background-color: #333;
  color: white;
  padding: 10px;
}

.video-controls {
  display: flex;
  gap: 10px;
  margin-right: auto;
}

button:first-child {
  margin-left: 15px;
}

button {
  background-color: #555;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
}

button:hover {
  background-color: #777;
}

.slider {
  -webkit-appearance: none;
  height: 8px;
  background: #ddd;
  border-radius: 5px;
  outline: none;
  margin-top: 1vh;
}

.timeSlider {
  width: 155vh;
}

.rangeSlider {
  width: 10vh;
}
</style>
