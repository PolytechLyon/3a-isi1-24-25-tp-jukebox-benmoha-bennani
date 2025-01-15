<template>
  <div class="jukebox">
    <h1>Jukebox</h1>
    <button @click="showPlaylistManager = !showPlaylistManager">
      {{ showPlaylistManager ? 'Back to Player' : 'Manage Playlists' }}
    </button>
    <div v-if="showPlaylistManager">
      <h2>Manage Playlists</h2>
      <ul>
        <li v-for="(playlist, index) in playlists" :key="index">
          {{ playlist.name }} ({{ playlist.tracks.length }} tracks)
          <button @click="deletePlaylist(index)" :disabled="index === currentPlaylistIndex">Delete</button>
          <button @click="selectPlaylist(index)">Select</button>
        </li>
      </ul>
      <form @submit.prevent="createPlaylist">
        <input type="text" v-model="newPlaylistName" placeholder="New playlist name" />
        <button type="submit">Create</button>
      </form>
    </div>
    <div v-else>
      <!-- Player Section -->
      <section class="player">
        <h2>Player</h2>
        <div v-if="currentTrackIndex !== -1" class="current-track">
          <p>Now playing: <strong>{{ playlist[currentTrackIndex].name }}</strong></p>
          <div class="controls">
            <button @click="togglePlay">{{ isPlaying ? 'Pause' : 'Play' }}</button>
            <input type="range" :value="currentTime" :max="duration" @input="seek($event)" @change="seek($event)" />
            <span>{{ formatTime(currentTime) }} / {{ formatTime(duration) }}</span>
          </div>
        </div>
        <p v-else>Choose a track to play.</p>
        <fieldset class="playback-mode">
          <legend>Playback mode</legend>
          <label>
            <input type="radio" name="mode" value="playlist" v-model="repeatMode" />
            Repeat list
          </label>
          <label>
            <input type="radio" name="mode" value="track" v-model="repeatMode" />
            Repeat track
          </label>
          <label>
            <input type="radio" name="mode" value="none" v-model="repeatMode" />
            Don't repeat
          </label>
        </fieldset>
      </section>

      <!-- Playlist Section -->
      <section class="playlist">
        <h2>Playlist</h2>
        <p>Add a new track to the playlist.</p>
        <ul>
          <li
            v-for="(track, index) in playlist"
            :key="index"
            :class="{ playing: currentTrackIndex === index, invalid: track.invalid }"
          >
            {{ track.name }}
            <div class="buttons">
              <button @click="playTrack(index)" :disabled="track.invalid">Play</button>
              <button @click="deleteTrack(index)">Delete</button>
            </div>
          </li>
        </ul>
        <p v-if="playlist.length === 0">The playlist is empty.</p>
      </section>

      <!-- New Track Section -->
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
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      repeatMode: "playlist", // Modes: playlist, track, none
      playlists: JSON.parse(localStorage.getItem('playlists')) || [{ name: 'Default', tracks: [] }],
      currentPlaylistIndex: 0,
      currentTrackIndex: -1,
      trackSource: "url", // Options: url, file
      urlInput: "",
      isPlaying: false,
      audioPlayer: new Audio(),
      currentTime: 0,
      duration: 0,
      isSeeking: false,
      showPlaylistManager: false,
      newPlaylistName: ''
    };
  },
  computed: {
    playlist() {
      return this.playlists[this.currentPlaylistIndex].tracks;
    }
  },
  methods: {
    savePlaylists() {
      localStorage.setItem('playlists', JSON.stringify(this.playlists));
    },
    addTrack() {
      if (this.trackSource === "url") {
        if (this.urlInput.trim() === "") {
          alert("Please provide a valid URL.");
          return;
        }
        const newTrack = { name: this.urlInput, url: this.urlInput, invalid: false };
        this.playlist.push(newTrack);
        this.urlInput = "";
        this.savePlaylists();
      } else if (this.trackSource === "file") {
        const fileInput = this.$refs.fileInput;
        if (fileInput.files.length === 0) {
          alert("Please select a file to upload.");
          return;
        }
        const file = fileInput.files[0];
        const newTrack = { name: file.name, url: URL.createObjectURL(file), invalid: false };
        this.playlist.push(newTrack);
        fileInput.value = "";
      }
    },
    playTrack(index) {
      if (index < 0 || index >= this.playlist.length || this.playlist[index].invalid) return;
      this.currentTrackIndex = index;
      this.audioPlayer.src = this.playlist[index].url;
      this.audioPlayer.play().catch(error => {
        console.error("Error playing track:", error);
        alert("Failed to play the track. Please check the URL and try again.");
      });
      this.isPlaying = true;
    },
    togglePlay() {
      if (this.currentTrackIndex === -1) {
        if (this.playlist.length > 0) {
          this.playTrack(0);
        } else {
          alert("The playlist is empty. Add tracks to start playback.");
        }
      } else {
        if (this.isPlaying) {
          this.audioPlayer.pause();
        } else {
          this.audioPlayer.play().catch(error => {
            console.error("Error playing track:", error);
            alert("Failed to play the track. Please check the URL and try again.");
          });
        }
        this.isPlaying = !this.isPlaying;
      }
    },
    deleteTrack(index) {
      if (index < 0 || index >= this.playlist.length) return;
      this.playlist.splice(index, 1);
      if (this.currentTrackIndex === index) {
        this.audioPlayer.pause();
        this.currentTrackIndex = -1;
        this.isPlaying = false;
      }
      this.savePlaylists();
    },
    seek(event) {
      const newTime = event.target.value;
      this.audioPlayer.currentTime = newTime;
      this.currentTime = newTime;
    },
    formatTime(seconds) {
      const minutes = Math.floor(seconds / 60);
      const secs = Math.floor(seconds % 60);
      return `${minutes}:${secs < 10 ? '0' : ''}${secs}`;
    },
    createPlaylist() {
      if (this.newPlaylistName.trim() === '') {
        alert('Please provide a valid playlist name.');
        return;
      }
      this.playlists.push({ name: this.newPlaylistName, tracks: [] });
      this.savePlaylists();
      this.newPlaylistName = '';
    },
    deletePlaylist(index) {
      if (index === this.currentPlaylistIndex) {
        alert("Cannot delete the currently selected playlist.");
        return;
      }
      this.playlists.splice(index, 1);
      this.savePlaylists();
    },
    selectPlaylist(index) {
      this.currentPlaylistIndex = index;
      this.currentTrackIndex = -1;
      this.isPlaying = false;
      this.showPlaylistManager = false; // Hide the playlist manager and show the main player view
    }
  },
  mounted() {
    this.audioPlayer.addEventListener("timeupdate", () => {
      if (!this.isSeeking) {
        this.currentTime = this.audioPlayer.currentTime;
        this.duration = this.audioPlayer.duration;
      }
    });

    this.audioPlayer.addEventListener("ended", () => {
      if (this.repeatMode === "track") {
        this.audioPlayer.currentTime = 0;
        this.audioPlayer.play();
      } else if (this.repeatMode === "playlist") {
        const nextIndex = (this.currentTrackIndex + 1) % this.playlist.length;
        this.playTrack(nextIndex);
      } else if (this.repeatMode === "none") {
        const nextIndex = this.currentTrackIndex + 1;
        if (nextIndex < this.playlist.length) {
          this.playTrack(nextIndex);
        } else {
          this.currentTrackIndex = -1;
          this.isPlaying = false;
        }
      }
    });
  },
};
</script>

<style scoped>
body {
  font-family: 'Roboto', sans-serif;
  background-color: #f0f2f5;
  color: #333;
  margin: 0;
  padding: 0;
}

.jukebox {
  max-width: 800px;
  margin: 40px auto;
  background: #fff;
  padding: 30px;
  border: 1px solid #ddd;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

h1,
h2 {
  text-align: center;
  margin: 20px 0;
  font-weight: bold;
  color: #333;
}

button {
  background-color: #007bff;
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #0056b3;
}

fieldset {
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 15px;
  margin-bottom: 20px;
}

legend {
  font-size: 16px;
  font-weight: bold;
  color: #555;
}

label {
  display: inline-block;
  margin-right: 20px;
  font-size: 16px;
  cursor: pointer;
}

input[type="radio"] {
  margin-right: 10px;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 20px 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border: 1px solid #ddd;
  margin: 10px 0;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

li.playing {
  background-color: #dff0d8;
}

li.invalid {
  color: red;
  text-decoration: line-through;
}

li:hover {
  background-color: #f9f9f9;
}

.buttons button {
  margin-left: 10px;
}

form {
  display: flex;
  align-items: center;
  gap: 15px;
  flex-wrap: wrap;
}

select,
input[type="text"],
input[type="file"],
button {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}

input[type="range"] {
  margin-left: 10px;
  margin-right: 10px;
  vertical-align: middle;
}
</style>