<template>
    <section class="player">
      <h2>Player</h2>
      <div v-if="currentTrack" class="current-track">
        <p>Now playing: <strong>{{ currentTrack.name }}</strong></p>
        <div class="controls">
          <button @click="$emit('togglePlay')">{{ isPlaying ? 'Pause' : 'Play' }}</button>
          <input type="range" :value="currentTime" :max="duration" @input="$emit('seek', $event)" @change="$emit('seek', $event)" />
          <span>{{ formatTime(currentTime) }} / {{ formatTime(duration) }}</span>
        </div>
      </div>
      <p v-else>Choose a track to play.</p>
      <fieldset class="playback-mode">
        <legend>Playback mode</legend>
        <label>
          <input type="radio" name="mode" value="playlist" :checked="repeatMode === 'playlist'" @change="$emit('changeRepeatMode', 'playlist')" />
          Repeat list
        </label>
        <label>
          <input type="radio" name="mode" value="track" :checked="repeatMode === 'track'" @change="$emit('changeRepeatMode', 'track')" />
          Repeat track
        </label>
        <label>
          <input type="radio" name="mode" value="none" :checked="repeatMode === 'none'" @change="$emit('changeRepeatMode', 'none')" />
          Don't repeat
        </label>
      </fieldset>
    </section>
  </template>
  
  <script>
  export default {
    props: {
      currentTrack: {
        type: Object,
        default: null
      },
      isPlaying: {
        type: Boolean,
        required: true
      },
      currentTime: {
        type: Number,
        required: true
      },
      duration: {
        type: Number,
        required: true
      },
      repeatMode: {
        type: String,
        required: true
      }
    },
    methods: {
      formatTime(seconds) {
        const minutes = Math.floor(seconds / 60);
        const secs = Math.floor(seconds % 60);
        return `${minutes}:${secs < 10 ? '0' : ''}${secs}`;
      }
    }
  };
  </script>