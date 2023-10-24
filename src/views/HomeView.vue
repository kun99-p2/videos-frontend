<template>
  <div class="thumbnail-container">
    <div
      v-for="thumbnail in thumbnails"
      :key="thumbnail"
      class="thumbnail-item"
    >
      <!--<video controls :src="video" height="300" width="500"></video>-->
      <img :src="thumbnail" height="300" width="500" />
    </div>
  </div>
</template>

<style>
.thumbnail-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.thumbnail-item {
  margin-left: 10px;
  margin-right: 10px;
  margin-top: 20px;
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
