<template>
  <div id="app">
    <button @click="changePaging">Сменить пагинацию</button>
    <SortWrapper :changePaging="staticPaging" />
  </div>
</template>

<script>
import SortWrapper from "@/components/table/index.vue";
export default {
  name: "App",
  components: {
    SortWrapper,
  },
  data() {
    return {
      staticPaging: true,
    };
  },
  methods: {
    changePaging() {
      this.staticPaging
        ? (this.staticPaging = false)
        : (this.staticPaging = true);
      if (this.staticPaging === true) {
        window.location.reload();
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.close-button {
  margin-left: -18px;
}

tr {
  height: 6em !important;
}
</style>
