<template>
  <div class="jukebox">
    <h1>Jukebox</h1>
    <button @click="showPlaylistManager = !showPlaylistManager">
      {{ showPlaylistManager ? 'Back to Player' : 'Manage Playlists' }}
    </button>
    <PlaylistManager v-if="showPlaylistManager" :playlists="playlists" :currentPlaylistIndex="currentPlaylistIndex" @deletePlaylist="deletePlaylist" @selectPlaylist="selectPlaylist" @createPlaylist="createPlaylist" />
    <div v-else>
      <Player :currentTrack="currentTrack" :isPlaying="isPlaying" :currentTime="currentTime" :duration="duration" :repeatMode="repeatMode" @togglePlay="togglePlay" @seek="seek" @changeRepeatMode="changeRepeatMode" />
      <Playlist :tracks="playlist" :currentTrackIndex="currentTrackIndex" @playTrack="playTrack" @deleteTrack="deleteTrack" />
      <NewTrack @addTrack="addTrack" />
    </div>
  </div>
</template>

<script>
import PlaylistManager from './components/PlaylistManager.vue';
import Player from './components/Player.vue';
import Playlist from './components/Playlist.vue';
import NewTrack from './components/NewTrack.vue';

export default {
  components: {
    PlaylistManager,
    Player,
    Playlist,
    NewTrack
  },
  data() {
    return {
      repeatMode: "playlist",
      playlists: JSON.parse(localStorage.getItem('playlists')) || [{ name: 'Default', tracks: [] }],
      currentPlaylistIndex: 0,
      currentTrackIndex: -1,
      isPlaying: false,
      audioPlayer: new Audio(),
      currentTime: 0,
      duration: 0,
      showPlaylistManager: false,
      newPlaylistName: ''
    };
  },
  computed: {
    playlist() {
      return this.playlists[this.currentPlaylistIndex].tracks;
    },
    currentTrack() {
      return this.playlist[this.currentTrackIndex] || null;
    }
  },
  methods: {
    savePlaylists() {
      localStorage.setItem('playlists', JSON.stringify(this.playlists));
    },
    addTrack(track) {
      this.playlist.push(track);
      this.savePlaylists();
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
    changeRepeatMode(mode) {
      this.repeatMode = mode;
    },
    createPlaylist(name) {
      if (name.trim() === '') {
        alert('Please provide a valid playlist name.');
        return;
      }
      this.playlists.push({ name, tracks: [] });
      this.savePlaylists();
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
      this.showPlaylistManager = false;
    }
  },
  mounted() {
    this.audioPlayer.addEventListener("timeupdate", () => {
      this.currentTime = this.audioPlayer.currentTime;
      this.duration = this.audioPlayer.duration;
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
  }
};
</script>