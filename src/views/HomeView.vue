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
</style>

<script>
import axios from "axios";
import { useAuthStore } from "../stores/store";
export default {
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
  beforeMount() {
    console.log("running");
    axios
      .get("http://localhost:5000/get_user")
      .then((response) => {
        console.log(response.data.name)
        axios.post("http://localhost:5000/tok", {
          username: response.data.name,
        })
        .then((response) => {
          const auth = useAuthStore();
          auth.setToken(response.data.token);
          console.log(auth.getToken())
        })
      })
      .catch((error) => {
        console.error(error);
      });
  },
};
</script>
