<template>
  <h1 style="display: flex; justify-content: center">Upload</h1>
  <div class="containers-container">
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
  </div>
</template>

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
      user: "",
    };
  },
  methods: {
    async handleDrop(event) {
      event.preventDefault();
      this.video = event.dataTransfer.files[0];
      this.instruction = "Video dropped.";
    },
    async uploadVideo() {
      if (this.video && this.video.type.startsWith("video/")) {
        if (this.title && this.desc) {
          const duration = await this.checkVideoDuration();
          if (duration <= 60) {
            await this.uploadVideoToS3();
          } else {
            alert("Video exceeding 60 seconds.");
          }
        } else {
          alert("Missing title or description.");
        }
      } else {
        alert("Missing video file.");
      }
    },
    async checkVideoDuration() {
      return new Promise((resolve, reject) => {
        const videoElement = document.createElement("video");
        videoElement.src = URL.createObjectURL(this.video);
        videoElement.addEventListener("loadedmetadata", () => {
          this.videoDuration = videoElement.duration;
          resolve(this.videoDuration);
        });
        videoElement.addEventListener("error", (error) => {
          reject(error);
        });
      });
    },
    async uploadVideoToS3() {
      const formData = new FormData();
      formData.append("title", this.title);
      formData.append("user", this.user);
      try {
        const response = await axios.post(
          "http://localhost:5001/get_presigned_url",
          formData
        );
        const presignedUrl = response.data.url;
        console.log(presignedUrl);
        await axios.put(presignedUrl, this.video, {
          headers: {
            "x-amz-meta-title": this.title,
            "x-amz-meta-id": response.data.id,
            "x-amz-meta-time": response.data.datetime,
            "Content-Type": "video/mp4",
            "Metadata": {
              "x-amz-meta-title": this.title,
              "x-amz-meta-id": response.data.id,
              "x-amz-meta-time": response.data.datetime
            }
          },
        });
        console.log("Video uploaded successfully");
        await axios.post("http://localhost:5001/thumbnail", {
          key: "videos/"+this.user+"/"+this.title,
          tb_key: "thumbnail/"+this.user+"/"+this.title,
          title: this.title,
          id: response.data.id
        });
        console.log("Thumbnail fetched")
        await axios.post("http://localhost:5000/initialize", {
          video_id: response.data.id,
        });
        console.log("Added to db");
        window.location = "list";
      } catch (error) {
        console.error("Error uploading video:", error);
      }
    },
  },
  created() {
    //getting username using authentication token
    const auth = useAuthStore();
    axios
      .post("http://localhost:5000/get_user_using_token", {
        token: auth.getToken(),
      })
      .then((response) => {
        this.user = response.data.username;
      })
      .catch((error) => {
        console.log(error.response.data.message);
      });
  },
};
</script>

<style>
.containers-container {
  display: flex;
  justify-content: center;
}
.container {
  display: flex;
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
