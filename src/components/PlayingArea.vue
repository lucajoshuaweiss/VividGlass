<script setup>
import { ref, watch, onMounted, onUnmounted, nextTick } from "vue";

// Incoming track from the parent
const props = defineProps({
  track: {
    type: Object,
    default: null,
  },
});

const currentTrack = ref(props.track);
const isPlaying = ref(false);
const currentTrackUrl = ref("");
const audioPlayer = ref(null);
const currentTime = ref(0);
const duration = ref(0);
const volume = ref(1);

watch(
  () => props.track,
  (newTrack) => {
    if (newTrack) {
      currentTrack.value = newTrack;
      currentTrackUrl.value = URL.createObjectURL(newTrack);
      currentTime.value = 0;

      nextTick(() => {
        if (audioPlayer.value) {
          audioPlayer.value.currentTime = 0;
          audioPlayer.value.play();
          isPlaying.value = true;
          audioPlayer.value.volume = volume.value;
        }
      });
    }
  }
);

const playPauseAudio = () => {
  const audio = audioPlayer.value;
  if (!audio) return;

  if (audio.paused) {
    audio.play();
    isPlaying.value = true;
  } else {
    audio.pause();
    isPlaying.value = false;
  }
};

const updateTime = () => {
  currentTime.value = audioPlayer.value.currentTime;
  duration.value = audioPlayer.value.duration;
};

const seekAudio = (event) => {
  const sliderWidth = event.currentTarget.offsetWidth;
  const clickX = event.offsetX;
  const newTime = (clickX / sliderWidth) * duration.value;

  audioPlayer.value.currentTime = newTime;
  currentTime.value = newTime;
};

const formatTime = (time) => {
  if (isNaN(time)) return "0:00";
  const minutes = Math.floor(time / 60);
  const seconds = Math.floor(time % 60);
  return `${minutes}:${seconds < 10 ? "0" : ""}${seconds}`;
};

const onTrackEnded = () => {
  currentTime.value = 0;
  if (audioPlayer.value) {
    audioPlayer.value.currentTime = 0;
    audioPlayer.value.play();
    isPlaying.value = true;
  }
};

const changeVolume = (event) => {
  const newVolume = event.target.value;
  volume.value = newVolume;
  if (audioPlayer.value) {
    audioPlayer.value.volume = newVolume;
  }
};

const handleKeydown = (event) => {
  if (!audioPlayer.value) return;

  switch (event.key) {
    case "ArrowLeft":
      audioPlayer.value.currentTime = Math.max(
        audioPlayer.value.currentTime - 5,
        0
      );
      break;
    case "ArrowRight":
      audioPlayer.value.currentTime = Math.min(
        audioPlayer.value.currentTime + 5,
        duration.value
      );
      break;
    case "ArrowUp":
      if (audioPlayer.value && volume.value < 1) {
        volume.value = Math.min(parseFloat(volume.value) + 0.05, 1);
        audioPlayer.value.volume = volume.value;
      }
      break;
    case "ArrowDown":
      if (audioPlayer.value && volume.value > 0) {
        volume.value = Math.max(parseFloat(volume.value) - 0.05, 0);
        audioPlayer.value.volume = volume.value;
      }
      break;
    case " ":
      event.preventDefault();
      playPauseAudio();
      break;
  }
};

onMounted(() => {
  window.addEventListener("keydown", handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleKeydown);
});
</script>


<template>
  <div class="playing-area">
    <h3>Now Playing</h3>

    <div v-if="currentTrack">
      <p>{{ currentTrack.name }}</p>

      <audio ref="audioPlayer" :src="currentTrackUrl" @timeupdate="updateTime" @ended="onTrackEnded"></audio>

      <div class="controls">
        <button @click="playPauseAudio">
          {{ isPlaying ? "Pause" : "Play" }}
        </button>

        <div class="progress-bar" @click="seekAudio">
          <div class="progress" :style="{ width: (currentTime / duration) * 100 + '%' }"></div>
        </div>

        <span>{{ formatTime(currentTime) }} / {{ formatTime(duration) }}</span>

        <label for="volume">🔊</label>
        <input name="volume" type="range" min="0" max="1" step="0.01" v-model="volume" @input="changeVolume" />
      </div>
    </div>

    <div v-else>
      <p>Select a track to play.</p>
    </div>
  </div>
</template>

<style scoped>
.playing-area {
  padding: 16px;
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.15);
  color: white;
  transition: border-color 0.3s, transform 0.2s;
}

.controls {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 10px;
}

button {
  padding: 8px 16px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  background: rgba(255, 255, 255, 0.1);
  color: white;
  border-radius: 12px;
  transition: background 0.3s, transform 0.2s;
  cursor: pointer;
}

button:hover {
  background: #7b00ff;
}

.progress-bar {
  flex: 1;
  height: 8px;
  min-width: 100px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 4px;
  cursor: pointer;
  overflow: hidden;
}

.progress {
  height: 100%;
  background: linear-gradient(90deg, #5eceff, #7b00ff);
  transition: width 0.2s linear;
}

span {
  font-size: 14px;
  color: white;
  white-space: nowrap;
}

input[type="range"] {
  width: 100px;
  accent-color: #5eceff;
  cursor: pointer;
  -webkit-appearance: none;
  appearance: none;
  height: 8px;
  background: linear-gradient(90deg, #5eceff, #7b00ff);
  border-radius: 4px;
  outline: none;
}

input[type="range"]::-webkit-slider-runnable-track {
  height: 8px;
  background: linear-gradient(90deg, #5eceff, #7b00ff);
  border-radius: 4px;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
}

input[type="range"]::-moz-range-track {
  height: 8px;
  background: linear-gradient(90deg, #5eceff, #7b00ff);
  border-radius: 4px;
}

input[type="range"]::-moz-range-thumb {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
}
</style>
