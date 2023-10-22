<template>
  <h1 style="display: flex; justify-content: center">My Videos</h1>
  <div v-for="video in videos" :key="video">
    <video controls :src="video" width="400"></video>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      videos: [],
    };
  },
  mounted() {
    axios
      .get("http://localhost:5001/videos")
      .then((response) => {
        this.videos = response.data.videos;
      })
      .catch((error) => {
        console.error("Couldn't fetch videos:", error);
      });
  },
};
</script>
