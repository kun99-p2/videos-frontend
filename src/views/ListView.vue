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
  methods: {
    deleteVideo() {},
  },
  mounted() {
    axios
      .get("http://localhost:5000/fetch_username")
      .then((response) => {
        axios
          .post("http://localhost:5001/my_videos", {
            username: response.data.name,
          })
          .then((response) => {
            this.videos = response.data.videos;
          })
          .catch((error) => {
            console.error("Couldn't fetch videos:", error);
          });
      })
      .catch((error) => {
        console.error("Couldn't retrieve username:", error);
      });
  },
};
</script>

<style scoped>
</style>
