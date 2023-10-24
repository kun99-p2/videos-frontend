<template>
  <h1 style="display: flex; justify-content: center">My Videos</h1>
  <div>
    <div v-for="thumbnail in thumbnails" :key="thumbnail">
      <div class="thumbnail-container">
        <img :src="thumbnail" height="200" width="300" />
        <h3 class="thumbnail-title">{{ thumbnail.title }}</h3>
        <button @click="deleteThumbnail(thumbnail.id)" class="delete-button">
          Delete
        </button>
      </div>
    </div>
  </div>
</template>

<style>
.row img {
  display: flex;
  align-items: start;
  cursor: pointer;
}
.thumbnail-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.thumbnail-title {
  margin: 0 10px;
  flex-grow: 1;
}
.delete-button {
  background-color: red;
  color: white;
  border: none;
  cursor: pointer;
}
</style>

<script>
import axios from "axios";
export default {
  data() {
    return {
      thumbnails: [],
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
          .post("http://localhost:5001/my_thumbnails", {
            username: response.data.name,
          })
          .then((response) => {
            this.thumbnails = response.data.thumbnails;
          })
          .catch((error) => {
            console.error("Couldn't fetch thumbnails:", error);
          });
      })
      .catch((error) => {
        console.error("Couldn't retrieve username:", error);
      });
  },
};
</script>
