<template>
    <section class="playlist">
      <h2>Playlist</h2>
      <p>Add a new track to the playlist.</p>
      <ul>
        <li
          v-for="(track, index) in tracks"
          :key="index"
          :class="{ playing: currentTrackIndex === index, invalid: track.invalid }"
        >
          {{ track.name }}
          <div class="buttons">
            <button @click="$emit('playTrack', index)" :disabled="track.invalid">Play</button>
            <button @click="$emit('deleteTrack', index)">Delete</button>
          </div>
        </li>
      </ul>
      <p v-if="tracks.length === 0">The playlist is empty.</p>
    </section>
  </template>
  
  <script>
  export default {
    props: {
      tracks: {
        type: Array,
        required: true
      },
      currentTrackIndex: {
        type: Number,
        required: true
      }
    }
  };
  </script>