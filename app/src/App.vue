<template>
  <div id="app">
    <Header />
    <router-view></router-view>
    <Footer />
  </div>
</template>

<script>
// firebase モジュール
import firebase from "firebase";

const firebaseConfig = require("./environment/" + process.env.NODE_ENV);

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
import Header from "./views/Header";
import Footer from "./views/Footer";
export default {
  name: "App",
  components: {
    Header,
    Footer
  },
  data: () => ({
    //
  })
};
</script>
<style scoped>
</style>
