<template>
    <div>
      <h2>Manage Playlists</h2>
      <ul>
        <li v-for="(playlist, index) in playlists" :key="index">
          {{ playlist.name }} ({{ playlist.tracks.length }} tracks)
          <button @click="$emit('deletePlaylist', index)" :disabled="index === currentPlaylistIndex">Delete</button>
          <button @click="$emit('selectPlaylist', index)">Select</button>
        </li>
      </ul>
      <form @submit.prevent="createPlaylist">
        <input type="text" v-model="newPlaylistName" placeholder="New playlist name" />
        <button type="submit">Create</button>
      </form>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      playlists: {
        type: Array,
        required: true
      },
      currentPlaylistIndex: {
        type: Number,
        required: true
      }
    },
    data() {
      return {
        newPlaylistName: ''
      };
    },
    methods: {
      createPlaylist() {
        this.$emit('createPlaylist', this.newPlaylistName);
        this.newPlaylistName = '';
      }
    }
  };
  </script>