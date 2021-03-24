<template>
  <div id="app">
    <Sorting></Sorting>
  </div>
</template>

<script>
import Sorting from "./components/Sorting.vue";

export default {
  name: "App",
  components: {
    Sorting,
  },
};
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
