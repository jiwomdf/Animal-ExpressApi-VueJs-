<template>
  <v-app-bar app color="white" dark dense flat>
    <v-btn text v-on:click="redirectHome()" color="teal">Animal Api</v-btn>

    <v-divider class="mx-4" inset vertical color="lightgrey"></v-divider>

    <v-spacer></v-spacer>

    <v-divider class="mx-4" inset vertical color="lightgrey"></v-divider>

    <v-btn text v-if="!isLogin" v-on:click="login()" color="teal">
      <span class="mr-2">Login</span>
      <v-icon>mdi-login</v-icon>
    </v-btn>
    <v-btn text v-if="isLogin" v-on:click="logout()" color="red accent-4">
      <span class="mr-2">Logout</span>
      <v-icon>mdi-logout</v-icon>
    </v-btn>
  </v-app-bar>
</template>

<script>
const axios = require("axios");
const { apiUrl } = require("../constant");

export default {
  name: "Navbar",
  data() {
    return {};
  },
  computed: {
    isLogin() {
      return this.$store.getters.getLogin != "" ? true : false;
    },
  },
  methods: {
    init() {
      let accessToken = this.$cookie.get("accessToken");
      let refreshToken = this.$cookie.get("refreshToken");

      if (accessToken == null || refreshToken == null)
        this.$store.getters.getLogin = "";
    },
    redirectHome() {
      let url = window.location.href;
      if (url != "http://localhost:8080/#/" || url != "http://128.199.125.19")
        this.$router.push("/");
    },
    login() {
      this.$router.push("/Login");
    },
    logout() {
      let refreshToken = this.$cookie.get("refreshToken");

      try {
        const url = `${apiUrl}/auth/logout`;
        const data = {
          userName: this.$store.getters.getLogin,
          refreshToken: refreshToken,
        };
        const headers = { "Content-Type": "application/json" };

        axios.post(url, data, headers);

        this.$cookie.delete("accessToken");
        this.$cookie.delete("refreshToken");
        this.$store.commit("delLogin");

        this.$router.push("/");
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>