<template>
  <nav>
    <div class="home-button">
      <RouterLink to="/">
        <button>Home</button>
      </RouterLink>
    </div>
    <div class="center-buttons">
      <RouterLink to="/list">
        <button>My Videos</button>
      </RouterLink>
      <RouterLink to="/upload">
        <button>Upload</button>
      </RouterLink>
    </div>
    <div class="logout-button">
      <button @click="logout" to="/">Logout</button>
    </div>
  </nav>
</template>

<style>
nav {
  display: flex;
}
.home-button {
  flex: 1;
  display: flex;
  justify-content: start;
}
.center-buttons {
  flex: 1;
  display: flex;
  justify-content: center;
}
.logout-button {
  flex: 1;
  display: flex;
  justify-content: end;
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
};
</script>
