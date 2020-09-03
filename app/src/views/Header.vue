<template>
  <header class="header">
    <h1>Vue-Chat</h1>
    <div v-if="user.uid" key="login">
      [{{ user.displayName }}]
      <button type="button" @click="doLogout">ログアウト</button>
    </div>
    <div v-else key="logout">
      <button type="button" @click="doLogin">ログイン</button>
    </div>
  </header>
</template>
<script>
import firebase from "firebase";
export default {
  name: "Header",
  data() {
    return {
      user: {}
    };
  },
  created() {
    firebase.auth().onAuthStateChanged(user => {
      this.user = user ? user : {};
    });
  },
  methods: {
    // ログイン処理
    doLogin() {
      const provider = new firebase.auth.TwitterAuthProvider();
      firebase.auth().signInWithPopup(provider);
      this.$router.push("/chat");
    },
    // ログアウト処理
    doLogout() {
      firebase.auth().signOut();
      this.user = {};
      this.$router.push("/");
    }
  }
};
</script>
<style scoped>
* {
  margin: 0;
  box-sizing: border-box;
}
.header {
  background: #3ab383;
  margin-bottom: 1em;
  padding: 0.4em 0.8em;
  color: #fff;
}
.content {
  margin: 0 auto;
  padding: 0 10px;
  max-width: 600px;
}
.form {
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: center;
  bottom: 0;
  height: 80px;
  width: 100%;
  background: #f5f5f5;
}
.form textarea {
  border: 1px solid #ccc;
  border-radius: 2px;
  height: 4em;
  width: calc(100% - 6em);
  resize: none;
}
.list {
  margin-bottom: 100px;
}
.item {
  position: relative;
  display: flex;
  align-items: flex-end;
  margin-bottom: 0.8em;
}
.item-image img {
  border-radius: 20px;
  vertical-align: top;
}
.item-detail {
  margin: 0 0 0 1.4em;
}
.item-name {
  font-size: 75%;
}
.item-message {
  position: relative;
  display: inline-block;
  padding: 0.8em;
  background: #deefe8;
  border-radius: 4px;
  line-height: 1.2em;
}
.item-message::before {
  position: absolute;
  content: " ";
  display: block;
  left: -16px;
  bottom: 12px;
  border: 4px solid transparent;
  border-right: 12px solid #deefe8;
}
.send-button {
  height: 4em;
}
/* トランジション用スタイル */
.chat-enter-active {
  transition: all 1s;
}
.chat-enter {
  opacity: 0;
  transform: translateX(-1em);
}
</style>