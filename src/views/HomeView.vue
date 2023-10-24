<template>
  <nav>
    <div style="margin-left: 750px">
      <RouterLink to="/list">
        <button>My Videos</button>
      </RouterLink>
      <RouterLink to="/upload">
        <button>Upload</button>
      </RouterLink>
    </div>
    <button @click="logout" to="/">Logout</button>
  </nav>
  <div class="thumbnail-container">
    <div v-for="thumbnail in thumbnails" :key="thumbnail" class="thumbnail-item">
      <!--<video controls :src="video" height="300" width="500"></video>-->
      <img :src="thumbnail" height="300" width="500"/>
    </div>
  </div>
</template>

<style>
nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
nav button {
  height: 50px;
  width: 90px;
}
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
  methods: {
    logout() {
      axios
        .post("http://localhost:5000/logout")
        .then((response) => {
          const auth = useAuthStore();
          auth.clearToken;
          window.location.href = "http://localhost:3000";
        })
        .catch((error) => {
          console.error(error);
        });
    },
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
