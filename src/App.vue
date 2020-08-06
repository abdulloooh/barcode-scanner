<template>
  <div id="app">
    <!-- Leaving this App file unbloated in case you want to add or customize more stuffs or styles -->
    <Scanner />
  </div>
</template>

<script>
import Scanner from "./components/Scanner";

export default {
  name: "App",
  components: {
    Scanner
  }
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

.scandit .scandit-video {
  width: 100%;
  height: 100%;
  object-fit: cover !important;
}
.scandit.scandit-barcode-picker {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #000; /*you can vary background here*/
  max-width: unset !important;
}
</style>
