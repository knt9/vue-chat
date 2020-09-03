<template>
  <div class="Chat">
    <v-container>
      <transition-group name="chat" tag="div" class="list content">
        <section
          v-for="{ key, name, image, message, date } in chat"
          :key="key"
          class="item"
        >
          <div class="item-image">
            <img :src="image" width="40" height="40" />
          </div>
          <div class="item-detail">
            <div class="item-name">
              {{ name }}:{{ date }}

              <v-btn small color="dark" icon @click="deRemove(key)">
                <v-icon>delete</v-icon>
              </v-btn>
            </div>
            <div class="item-message">
              <nl2br tag="div" :text="message" />
            </div>
          </div>
        </section>
      </transition-group>
      <form action @submit.prevent="doSend" class="form">
        <div class="error">{{ errMsg }}</div>
        <textarea
          v-model="input"
          :disabled="!user.uid"
          @keydown.enter.exact.prevent="doSend"
        ></textarea>
        <button type="submit" :disabled="!user.uid" class="send-button">
          Tweet
        </button>
      </form>
    </v-container>
  </div>
</template>

<script>
import firebase from "firebase";
import Nl2br from "vue-nl2br";
export default {
  name: "Chat",
  components: { Nl2br },
  data() {
    return {
      user: {},
      chat: [],
      input: "",
      errMsg: ""
    };
  },
  created() {
    firebase.auth().onAuthStateChanged((user) => {
      this.user = user ? user : {};
      const ref_message = firebase.database().ref("message");
      if (user) {
        this.chat = [];
        ref_message.limitToLast(10).on("child_added", this.childAdded);
        ref_message.on("child_removed", this.childRemoved);
      } else {
        ref_message.limitToLast(10).off("child_added", this.childAdded);
        ref_message.off("child_removed", this.childRemoved);
      }
    });
  },
  methods: {
    scrollBottom() {
      this.$nextTick(() => {
        window.scrollTo(0, document.body.clientHeight);
      });
    },
    deRemove(key) {
      firebase
        .database()
        .ref("message")
        .child(key)
        .remove(() => {
          this.input = "";
        });
    },
    childAdded(snap) {
      const message = snap.val();
      this.chat.push({
        key: snap.key,
        name: message.name,
        image: message.image,
        message: message.message,
        date: message.date
      });
      this.scrollBottom();
    },
    childRemoved(snap) {
      const index = this.chat.findIndex(function(message) {
        return snap.key === message.key;
      });
      this.chat.splice(index, 1);
      this.scrollBottom();
    },
    doSend() {
      if (!this.input.length) {
        this.errMsg = "未入力です!";
      }
      const date = new Date();

      if (this.user.uid && this.input.length) {
        firebase
          .database()
          .ref("message")
          .push(
            {
              message: this.input,
              name: this.user.displayName,
              image: this.user.photoURL,
              date: date.toLocaleString()
            },
            () => {
              this.input = "";
            }
          );
      }
    }
  }
};
</script>
<style scoped>
* {
  margin: 0;
  box-sizing: border-box;
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
  margin-top: 500px;
  padding-bottom: 40px;
  align-items: center;
  bottom: 0;
  height: 130px;
  width: 100%;
  background: #f5f5f5;
  z-index: 0;
}
.error {
  display: block;
}
.form textarea {
  display: flex;
  border: 1px solid #ccc;
  border-radius: 2px;
  height: 4em;
  padding-bottom: 10px;
  width: calc(100% - 6em);
  resize: none;
}
.list {
  margin-bottom: 150px;
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
  display: block;
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
