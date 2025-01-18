<template>
    <section class="new-track">
      <h2>New track</h2>
      <form @submit.prevent="addTrack">
        <label for="track-source">Add track:</label>
        <select id="track-source" v-model="trackSource">
          <option value="url">By URL</option>
          <option value="file">Via file upload</option>
        </select>
        <input
          v-if="trackSource === 'url'"
          type="text"
          placeholder="Provide URL"
          v-model="urlInput"
        />
        <input v-if="trackSource === 'file'" type="file" ref="fileInput" />
        <button type="submit">
          {{ trackSource === 'url' ? 'Add by URL' : 'Add uploaded file' }}
        </button>
      </form>
    </section>
  </template>
  
  <script>
  export default {
    data() {
      return {
        trackSource: "url",
        urlInput: ""
      };
    },
    methods: {
      addTrack() {
        if (this.trackSource === "url") {
          if (this.urlInput.trim() === "") {
            alert("Please provide a valid URL.");
            return;
          }
          const newTrack = { name: this.urlInput, url: this.urlInput, invalid: false };
          this.$emit('addTrack', newTrack);
          this.urlInput = "";
        } else if (this.trackSource === "file") {
          const fileInput = this.$refs.fileInput;
          if (fileInput.files.length === 0) {
            alert("Please select a file to upload.");
            return;
          }
          const file = fileInput.files[0];
          const newTrack = { name: file.name, url: URL.createObjectURL(file), invalid: false };
          this.$emit('addTrack', newTrack);
          fileInput.value = "";
        }
      }
    }
  };
  </script>