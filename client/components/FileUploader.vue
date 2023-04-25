<template>
  <div class="upload-area">
    <div class="upload-area__header">
      <h1 class="upload-area__title">Upload your file</h1>
      <p class="upload-area__paragraph">File can be of any type.</p>
    </div>
    <div
      @dragover.prevent="dropZoneActive = true"
      @dragleave.prevent="dropZoneActive = false"
      @drop.prevent="handleDrop"
      @click="handleClick"
      :class="{ active: dropZoneActive }"
      class="upload-area__drop-zoon drop-zoon"
    >
      <span class="drop-zoon__icon">
        <i class="bx bxs-file-image"></i>
      </span>
      <p class="drop-zoon__paragraph">Drop your file here or Click to browse</p>
      <!-- <span id="loadingText" class="drop-zoon__loading-text" v-if="isLoading"
        >Please Wait</span
      >
      <img
        src=""
        alt="Preview Image"
        id="previewImage"
        class="drop-zoon__preview-image"
        draggable="false"
      /> -->
    </div>
    <div class="files">
      <a
        v-for="(file, fileIndex) in files"
        :key="fileIndex"
        class="file"
        target="_blank"
        rel="noreferrer"
        :class="{ done: getProgress(fileIndex) === 100 }"
        :href="`http://localhost:4000/uploads/${file.finalFilename}`"
      >
        <div class="name">{{ formatFileName(file.name) }}</div>
        <span
          class="right-percent"
          :class="{ done: getProgress(fileIndex) === 100 }"
          >100%</span
        >
        <div
          class="progress"
          :class="{
            done: getProgress(fileIndex) === 100,
          }"
          :style="{ width: `${getProgress(fileIndex)}%` }"
        >
          {{ getProgress(fileIndex) }}%
        </div>
      </a>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import axios from "axios";

export default {
  data() {
    return {
      dropZoneActive: false,
      files: [],
      currentFileIndex: null,
      lastUploadedFileIndex: null,
      currentChunkIndex: null,
    };
  },
  computed: {
    chunkSize() {
      return 10 * 1024; // 10kb
    },
  },
  methods: {
    formatFileName(name) {
      if (name.length > 25) {
        return name.slice(0, 25) + "...";
      } else {
        return name;
      }
    },
    getProgress(fileIndex) {
      const file = this.files[fileIndex];
      console.log(file);
      if (!file) return 0; // Return 0 if the file doesn't exist
      if (file.finalFilename) {
        return 100;
      } else {
        const uploading = fileIndex === this.currentFileIndex;
        const chunks = Math.ceil(file.size / this.chunkSize);
        if (uploading) {
          return Math.round((this.currentChunkIndex / chunks) * 100);
        } else {
          return 0;
        }
      }
    },
    handleDrop(e) {
      e.preventDefault();
      this.files = [...this.files, ...e.dataTransfer.files];
    },
    handleClick() {
      const input = document.createElement("input");
      input.type = "file";
      input.multiple = true;
      input.onchange = (e) => {
        this.files = [...this.files, ...e.target.files];
      };
      input.click();
    },

    readAndUploadCurrentChunk() {
      const reader = new FileReader();
      const file = this.files[this.currentFileIndex];
      if (!file) {
        return;
      }
      const from = this.currentChunkIndex * this.chunkSize;
      const to = from + this.chunkSize;
      const blob = file.slice(from, to);
      reader.onload = (e) => this.uploadChunk(e);
      reader.readAsDataURL(blob);
    },
    uploadChunk(readerEvent) {
      const file = this.files[this.currentFileIndex];
      const data = readerEvent.target.result;
      const params = new URLSearchParams();
      params.set("name", file.name);
      params.set("size", file.size);
      params.set("currentChunkIndex", this.currentChunkIndex);
      params.set("totalChunks", Math.ceil(file.size / this.chunkSize));
      const headers = { "Content-Type": "application/octet-stream" };
      const url = "http://localhost:4000/upload?" + params.toString();
      axios.post(url, data, { headers }).then((res) => {
        const file = this.files[this.currentFileIndex];
        const fileSize = this.files[this.currentFileIndex].size;
        const chunks = Math.ceil(fileSize / this.chunkSize) - 1;
        const isLastChunk = this.currentChunkIndex === chunks;
        if (isLastChunk) {
          console.log(res.data.finalFilename);
          file.finalFilename = res.data.finalFilename;
          console.log(file.finalFilename);
          this.lastUploadedFileIndex = this.currentFileIndex;
          this.currentFileIndex = null;
        } else {
          this.currentChunkIndex = this.currentChunkIndex + 1;
        }
      });
    },
  },
  watch: {
    lastUploadedFileIndex(newIndex) {
      if (newIndex === null) {
        return;
      }
      const isLastFile = newIndex === this.files.length - 1;
      const nextFileIndex = isLastFile ? null : newIndex + 1;
      this.currentFileIndex = nextFileIndex;
    },
    files(newFiles) {
      if (newFiles.length > 0) {
        if (this.currentFileIndex === null) {
          this.currentFileIndex =
            this.lastUploadedFileIndex === null
              ? 0
              : this.lastUploadedFileIndex + 1;
        }
      }
    },
    currentFileIndex(newIndex) {
      if (newIndex !== null) {
        this.currentChunkIndex = 0;
      }
    },
    currentChunkIndex(newIndex) {
      if (newIndex !== null) {
        this.readAndUploadCurrentChunk();
      }
    },
  },
};
</script>

<style>
</style>