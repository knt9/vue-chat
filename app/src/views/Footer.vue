<template>
  <footer class="footer"></footer>
</template>
<script>
export default {
  name: "Footer",
  data() {
    return {};
  }
};
</script>
<style scoped>
* {
  margin: 0;
  box-sizing: border-box;
}
.footer {
  position: fixed;
  display: inline-block;
  justify-content: center;
  margin-top: 80px;
  align-items: center;
  bottom: 0;
  background: #3ab383;
  margin-bottom: 1em;
  padding: 0.4em 0.8em;
  color: #fff;
  height: 40px;
  width: 100%;
  z-index: 500;
}
</style>