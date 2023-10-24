<template>
  <h1 style="display: flex; justify-content: center">My Videos</h1>
  <div>
    <div v-for="thumbnail in thumbnails" :key="thumbnail">
      <div class="thumbnail-container">
        <img :src="thumbnail[0].file" @click="clickedVideo(thumbnail[0].metadata.id)"/>
        <h3 class="thumbnail-title">{{ thumbnail[0].metadata.title }}</h3>
        <button
          @click="deleteVideo(thumbnail[0].metadata.title, thumbnail[0].metadata.id)"
          class="delete-button"
        >
          X
        </button>
      </div>
    </div>
  </div>
</template>

<style>
.thumbnail-container img {
  display: flex;
  align-items: start;
  cursor: pointer;
  height: 200px;
  width: 20%;
}
.thumbnail-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
  margin: auto 50px;
  margin-top: 20px;
  background-color: aliceblue;
}
.thumbnail-title {
  margin: 0 10px;
  flex-grow: 1;
  margin-left: 50px;
}
.delete-button {
  background-color: red;
  color: white;
  border: none;
  cursor: pointer;
  margin-right: 10px;
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
    clickedVideo(id) {
      window.location.href = "http://localhost:4001/video?vid="+ id;
    },
    deleteVideo(title, id) {
      axios
        .get("http://localhost:5000/fetch_username")
        .then((response) => {
          axios
            .delete("http://localhost:5001/delete", {
              data: {
                username: response.data.name,
                title: title,
                id: id,
              },
            })
            .then((response) => {
              alert(response.data.message);
              window.location.href = "http://localhost:4000/list";
            })
            .catch((error) => {
              console.error(error);
            });
        })
        .catch((error) => {
          console.error("Couldn't delete thumbnail:", error);
        });
    },
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
            console.log(this.thumbnails);
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
