<template>
    <nav>
        <div style="margin-left: 750px;">
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
import axios from 'axios';
export default {
  methods: {
    logout() {
      axios.post('http://localhost:5000/logout')
      .then(response => {
        console.log(response.data);
        window.location.href = "http://localhost:3000";
      })
      .catch(error => {
        console.error(error);
      });
    }
  }
};
</script>