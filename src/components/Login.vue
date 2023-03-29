<template>
  <div class="login-container">
    <div v-if="!loggedIn" class="login-box">
      <h1>Login</h1>
      <v-text-field v-model="username" label="Username"></v-text-field>
      <v-text-field
        v-model="password"
        label="Password"
        type="password"
      ></v-text-field>
      <v-btn @click="login" color="primary">Login</v-btn>
      <p v-if="error" class="error">{{ error }}</p>
    </div>
    <div v-if="loggedIn">
      <h2>Welcome, {{ user.firstName }}</h2>
      <div class="user-info">
        <div class="avatar">
          <img :src="user.image" alt="User avatar" />
        </div>
        <div class="details">
          <p><strong>Name:</strong> {{ user.firstName }} {{ user.lastName }}</p>
          <p><strong>Hair color:</strong> {{ user.hair.color }}</p>
        </div>
      </div>
      <v-btn @click="logout" color="primary">Logout</v-btn>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      username: "",
      password: "",
      error: "",
      loggedIn: false,
      user: {},
    };
  },
  methods: {
    async login() {
      try {
        const response = await axios.post("https://dummyjson.com/auth/login", {
          username: this.username,
          password: this.password,
        });
        const { data } = response;
        console.log(response);
        if (data.username == this.username) {
          this.loggedIn = true;
          const userResponse = await axios.get(
            `https://dummyjson.com/users/${data.id}`
          );
          this.user = userResponse.data;
        } else {
          this.error = "Incorrect username or password";
        }
      } catch (error) {
        console.log(error);
        this.error = "An error occurred while logging in";
      }
    },
    logout() {
      this.username = "";
      this.password = "";
      this.loggedIn = false;
      this.user = {};
      this.error = "";
    },
  },
};
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f2f2f2;
}

.login-box {
  width: 100%;
  max-width: 400px;
  padding: 24px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.login-box h1 {
  font-size: 28px;
  margin-bottom: 24px;
  text-align: center;
}

.login-box .error {
  color: #ff0000;
  margin-top: 8px;
}

.user-info {
  display: flex;
  margin-top: 16px;
}

.user-info .avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  overflow: hidden;
  margin-right: 16px;
}

.user-info .avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.user-info .details {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.user-info p {
  margin: 0;
}
</style>
