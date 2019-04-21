<template>
  <div>
    <el-input v-model="username" placeholder="username"></el-input>
    <el-input v-model="password" placeholder="password"></el-input>
    <el-button type="primary" @click="denglu()">dianwo</el-button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      username: "",
      password: ""
    };
  },
  methods: {
    denglu(username, password) {
      axios
        .get(
          "http://192.168.0.102:54321/api/oauth/auth?username=admin&password=111111"
        )
        .then(data => {
          console.log(data);
          if (data.data.code === 200) {
            console.log(data);
            window.localStorage.setItem("mytoken", data.data.data);
          } else {
            console.log("caozuoshibai");
          }
        })
        .catch(function(reason) {
          console.log("shibai" + reason);
        });
    }
  }
};
</script>

<style>
</style>
