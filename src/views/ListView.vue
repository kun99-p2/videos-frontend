<template>
  <h1 class="flex justify-center text-white">My Videos</h1>
  <div class="flex justify-center items-center">
    <div>
      <div class="mb-4" v-for="thumbnail in thumbnails" :keys="thumbnail">
        <div
          class="flex flex-col items-center bg-white border border-gray-200 rounded-lg shadow md:flex-row md:max-w-2xl hover:bg-gray-100"
        >
          <img
            class="object-cover w-full rounded-t-lg h-96 md:h-auto md:w-48 md:rounded-none md:rounded-l-lg cursor-pointer"
            :src="thumbnail[0].file"
            @click="
              clickedVideo(
                thumbnail[0].metadata.id,
                thumbnail[0].metadata.user,
                thumbnail[0].metadata.title
              )
            "
          />
          <div class="flex flex-col justify-between p-4 leading-normal">
            <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900">
              {{ thumbnail[0].metadata.title }}
            </h5>
            <p class="mb-3 font-normal text-gray-700">
              {{ thumbnail[0].metadata.desc }}
            </p>
          </div>
          <button
            @click="
              deleteVideo(thumbnail[0].metadata.title, thumbnail[0].metadata.id)
            "
            class="cursor-pointer bg-red-300"
          >
            X
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.thumbnail-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
  margin: auto 50px;
  margin-top: 20px;
  background-color: aliceblue;
}
</style>

<script>
import axios from "axios";
export default {
  data() {
    return {
      user: "",
      thumbnails: [],
    };
  },
  methods: {
    clickedVideo(id, user, title) {
      window.location.href =
        "http://localhost:4001/video?vid=" +
        id +
        "&user=" +
        user +
        "&title=" +
        title;
    },
    deleteVideo(title, id) {
      axios
        .delete("http://localhost:5001/delete", {
          data: {
            username: this.user,
            title: title,
            id: id,
          },
        })
        .then((response) => {
          axios.delete("http://localhost:5000/remove_views/${id}");
          alert(response.data.message);
          window.location.href = "http://localhost:4000/list";
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
        this.user = response.data.name;
        console.log(this.user);
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
