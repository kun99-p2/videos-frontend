<template>
  <div class="videos-container">
    <div
      v-for="thumbnail in thumbnails"
      :key="thumbnail"
      class="thumbnail-item"
    >
      <!--<video controls :src="video" height="300" width="500"></video>-->
      <img :src="thumbnail"/>
    </div>
  </div>
</template>

<style>
.videos-container {
  display: flex;
  flex-wrap: wrap;
}

.thumbnail-item {
  margin-top: 20px;
  flex: 1;
  display: flex;
  justify-content: center;
  cursor: pointer;
}
.thumbnail-item img {
  height: 300px;
  width: 400px;
}
</style>

<script>
import axios from "axios";
import { useAuthStore } from "../stores/store";
export default {
  data() {
    return {
      videos: [],
      thumbnails: [],
    };
  },
  mounted() {
    axios
      .get("http://localhost:5001/thumbnails")
      .then((response) => {
        this.thumbnails = response.data.thumbnails;
      })
      .catch((error) => {
        console.error("Couldn't fetch thumbnails:", error);
      });
  },
  beforeMount() {
    axios
      .get("http://localhost:5000/fetch_username")
      .then((response) => {
        console.log("user: ", response.data.name);
        axios
          .post("http://localhost:5000/get_token", {
            username: response.data.name,
          })
          .then((response) => {
            const auth = useAuthStore();
            auth.setToken(response.data.token);
          });
      })
      .catch((error) => {
        console.error(error);
      });
  },
};
</script>
