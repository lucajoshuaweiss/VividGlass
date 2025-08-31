<script setup>
import { ref } from "vue";
import FileLoader from "./components/FileLoader.vue";
import LoadedFiles from "./components/LoadedFiles.vue";
import PlayingArea from "./components/PlayingArea.vue";

const loadedFiles = ref([]);
const currentTrack = ref(null);

const handleFilesLoaded = (files) => {
  loadedFiles.value = files;
};

const setTrack = (track) => {
  currentTrack.value = track;
};
</script>

<template>
  <main>
    <div class="grid-container">
      <FileLoader class="file-loader" @files-loaded="handleFilesLoaded" />
      <LoadedFiles class="loaded-files" :files="loadedFiles" @file-selected="setTrack" />
      <PlayingArea class="playing-area" :track="currentTrack" />
    </div>
  </main>
</template>

<style>
.grid-container {
  display: grid;
  grid-template-columns: auto auto;
  gap: 10px;
  padding: 10px;
}

.file-loader {
  grid-row: 1 / span 1;
}

.loaded-files {
  grid-row: 2 / span 1;
}

.playing-area {
  grid-row: 1 / span 2;
}
</style>
