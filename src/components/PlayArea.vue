<template>
    <div class="text-container">
        <div class="music-player">
            <div class="img-area">
              <img src="../assets/Rosallia.jpg">
            </div>
            <h2>{{ playlist[currentIndex].title }}</h2>
            <p>{{ playlist[currentIndex].artist }}</p>
            <div class="controls">
            <button @click="nextSong">Playlist</button>
            <button @click="play">{{ isPlaying ? 'Pause' : 'Play' }}</button>
            <input
            class="slider volume"
            type="range"
            v-model="volumeSlider"
            @input="changeVolume"
            >
            </div>
            <input
            class="slider"
            type="range"
            :min="0"
            v-model="timeSlider"
            @input="changeCurrentTime"
            >
            <p>{{ currentTime }} / {{ duration }}</p>
        </div>
    </div>
    
</template>
<script>

export default {
  data() {
    return {
      audio: undefined,
      currentIndex: 0,
      isPlaying: false,
      timeSlider: 0,
      volumeSlider: 100,
      playlist: [
        { title: 'Candy', artist: 'Rosallia', src: "../assets/candy.mp3", picture: "../assets/Rosallia.PNG"}
      ],
      currentTime: "0:00",
      duration: "0:00"
    }
  },
  methods: {
    async play() {
        if (this.isPlaying) {
            this.isPlaying = false
            this.audio.pause()
            return
        }
        this.isPlaying = true
        const sound = await import("../assets/candy.mp3");
        // Add validation
        this.audio = new Audio(sound.default);
        this.audio.play()


        this.audio.addEventListener('loadedmetadata', () => {
            this.duration = this.formatTime(this.audio.duration)
            this.audio.volume = parseFloat(this.volumeSlider / 100);
            this.audio.currentTime = this.audio.duration * this.timeSlider / 100;
            this.audio.play();
            this.isReset()
        });

        this.audio.addEventListener('timeupdate', () => {
            this.currentTime = this.formatTime(this.audio.currentTime)
            this.timeSlider = (this.audio.currentTime / this.audio.duration) * 100;
            this.isReset()
        });
    },
    isReset() {
        if (this.audio.currentTime >= this.audio.duration) {
            this.timeSlider = 0
            this.audio = undefined
            this.currentTime = "0:00"
            this.duration = "0:00"
            console.info("reset")
        }
    },
    changeCurrentTime(event) {
      try {
        if (this.audio) {
          this.timeSlider = event.target.value
          this.audio.currentTime = parseFloat(this.timeSlider / 100) * this.audio.duration
        }
      }
      catch (error) {
        console.log(`Failed to change current time due to ${error}`)
      }
    },
    changeVolume(event) {
      try {
        if (this.audio) {
          this.volumeSlider = event.target.value;
          this.audio.volume = parseFloat(this.volumeSlider / 100);
        }
      }
      catch (error) {
        console.log(`Failed to change volume sound ${error}`)
      }
    },
    formatTime(time){
      const minutes = Math.floor(time / 60);
      const seconds = Math.floor(time % 60);
      return `${minutes}:${seconds.toString().padStart(2, '0')}`;
    }
  },
};
</script>

<style scoped>
.text-container {
    margin: 0 auto;
    max-width: 25vh;
}

.music-player {
    margin-top: 100%;
    padding: 40px;
    border-radius: 20px;
    text-align: center;
    background-color: #1b1d30;
    color: white;
}

.controls {
    display: flex;
    justify-content: space-between;
    margin: 20px 0;
}

input[type="range"] {
    width: 100%;
}

button {
    background-color: #2c3e50;
    border: none;
    color: white;
    padding: 10px 16px;
    cursor: pointer; 
    border-radius: 12px;
    transition: background-color 0.3s;
}

button:hover {
    background-color: #3e5974;
}

.slider {
  -webkit-appearance: none;
  height: 8px;
  background: #ddd;
  border-radius: 5px;
  outline: none;
  margin-top: 1vh;
}

.slider.volume {
    width: 50px;
}

.img-area {
  width: 100%;
  height: 230px;
  overflow: hidden;
  margin-top: 25px;
  border-radius: 50%;
  box-shadow: 0 0 0 5px #ffffff,
  0 0 2px #fff,
  13px 13px 20px #31313163,
  -5px -5px 10px #e6f6ff;
}

.img-area img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>