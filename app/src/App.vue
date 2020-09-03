<template>
  <v-app>
    <v-main>
      <Header />
      <router-view></router-view>
      <Footer />
    </v-main>
  </v-app>
</template>

<script>
import Header from "./views/Header";
import Footer from "./views/Footer";
import firebase from "firebase";
const firebaseConfig = require("./environment/" + process.env.NODE_ENV);
firebase.initializeApp(firebaseConfig);
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
