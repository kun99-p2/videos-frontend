<template>
  <h1 style="display: flex; justify-content: center">Upload</h1>
  <div class="container">
    <div
      @dragover.prevent
      @dragenter.prevent
      @drop="handleDrop"
      class="thumbnail"
    >
      <p>{{ this.instruction }}</p>
    </div>
    <div class="details">
      <div class="input">
        <label for="title">Title:</label>
        <input type="text" v-model="title" placeholder="Enter video title" />
      </div>
      <div class="input">
        <label for="description">Description:</label>
        <textarea
          rows="9"
          v-model="desc"
          placeholder="Enter video description"
        ></textarea>
      </div>
      <div class="buttons">
        <RouterLink to="/">
          <button>Cancel</button>
        </RouterLink>
        <button @click="uploadVideo">OK</button>
      </div>
    </div>
  </div>
</template>

<style>
.container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding-left: 300px;
}

.thumbnail {
  width: 400px;
  height: 300px;
  object-fit: cover;
  border: 1px solid #ccc;
}

.details {
  flex: 1;
  margin-left: 20px;
}

.input {
  margin-bottom: 20px;
  width: 600px;
}

.input label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

.input input[type="text"],
.input textarea {
  width: 100%;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
}

.buttons {
  display: flex;
  padding-left: 500px;
  justify-content: space-between;
  width: 100px;
}

buttons button {
  margin-left: 10px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}
</style>

<script>
import axios from "axios";
import { useAuthStore } from "../stores/store";
export default {
  data() {
    return {
      instruction: "Drag and drop a video",
      video: null,
      title: "",
      desc: "",
    };
  },
  methods: {
    handleDrop(event) {
      event.preventDefault();
      this.video = event.dataTransfer.files[0];
      this.instruction = "Video dropped.";
    },
    // no check for video length and thumbnail generation not implemented yet
    uploadVideo() {
      if (this.video && this.video.type.startsWith("video/")) {
        if (this.title && this.desc) {
          this.uploadVideoToS3();
        } else {
          alert("Missing title or description.");
        }
      } else {
        alert("Missing video file.");
      }
    },
    uploadVideoToS3() {
      const auth = useAuthStore();
      axios
        .post("http://localhost:5000/get_user_using_token", {
          token: auth.getToken(),
        })
        .then((response) => {
          let formData = new FormData();
          formData.append("video", this.video);
          formData.append("title", this.title);
          formData.append("desc", this.desc);
          formData.append("user", response.data.username);
          axios
            .post("http://localhost:5001/upload", formData)
            .then((response) => {
              alert(response.data.message);
              console.log("Video uploaded successfully:", response.data);
              window.location = "list";
            })
            .catch((error) => {
              alert(error.response.data.message);
              console.error("Error uploading video:", error);
            });
        })
        .catch((error) => {
          console.error("Couldn't retrieve username:", error);
        });
    },
  },
};
</script>