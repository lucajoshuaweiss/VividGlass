<script setup>
import { ref, watch } from "vue";

const emit = defineEmits(["file-selected"]);

// Incoming files from the parent
const props = defineProps({
  files: {
    type: Array,
    required: true,
  },
});

const fileList = ref([]);

const fileKey = (file, index) => `${file.name}-${file.size}-${file.lastModified}-${index}`;

// Append newly loaded files to local list (avoid duplicates)
watch(
  () => props.files,
  (newFiles) => {
    if (!Array.isArray(newFiles)) return;
    for (const f of newFiles) {
      const exists = fileList.value.some(
        (x) =>
          x.name === f.name &&
          x.size === f.size &&
          x.lastModified === f.lastModified
      );
      if (!exists) fileList.value.push(f);
    }
  },
  {
    immediate: true
  }
);
</script>

<template>
  <div class="loaded-files">
    <h3>Loaded Files</h3>
    <ul>
      <li v-for="(file, index) in fileList" :key="fileKey(file, index)" @click="emit('file-selected', file)">
        {{ file.name }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
.loaded-files {
  padding: 16px;
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.15);
  color: white;
  transition: border-color 0.3s, transform 0.2s;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  padding: 8px 12px;
  margin-bottom: 6px;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.05);
  cursor: pointer;
  transition: background 0.3s, transform 0.2s;
}

li:hover {
  background: rgba(94, 206, 255, 0.2);
}
</style>
