<script setup>
import { ref } from "vue";

const emit = defineEmits();

const dragActive = ref(false);
const fileInput = ref(null);

const handleFiles = (files) => {
  const selectedFiles = Array.from(files).filter(file => file.type.startsWith("audio/"));
  
  if (selectedFiles.length > 0) {
    emit("files-loaded", selectedFiles);
  } else {
    alert("Only audio files are allowed!");
  }
};

const handleFileChange = (event) => {
  handleFiles(event.target.files);
};

const triggerFileDialog = () => {
  fileInput.value?.click();
};

const handleDrop = (event) => {
  event.preventDefault();
  dragActive.value = false;
  if (event.dataTransfer?.files) {
    handleFiles(event.dataTransfer.files);
  }
};

const handleDragOver = (event) => {
  event.preventDefault();
  dragActive.value = true;
};

const handleDragLeave = () => {
  dragActive.value = false;
};
</script>

<template>
  <div class="file-loader" :class="{ 'drag-active': dragActive }" @click="triggerFileDialog" @drop="handleDrop"
    @dragover="handleDragOver" @dragleave="handleDragLeave">
    <h3>Load Your Music Files</h3>
    <p>Drag & drop files here or click to select</p>
    <input ref="fileInput" type="file" accept="audio/*" multiple @change="handleFileChange" />
  </div>
</template>

<style scoped>
.file-loader {
  padding: 16px;
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.15);
  color: white;
  cursor: pointer;
  transition: border-color 0.3s, transform 0.2s;
}

.file-loader:hover {
  border-color: #5eceff;
}

.file-loader.drag-active {
  border-color: #7b00ff;
}

input[type="file"] {
  display: none;
}
</style>

