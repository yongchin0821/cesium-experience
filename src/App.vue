<script setup>
import { ref, onMounted } from "vue";
import * as Cesium from "cesium";
import "./Widgets/widgets.css";

window.CESIUM_BASE_URL = "/";

// const cesiumContainer = ref(null);

onMounted(() => {
  const viewer = new Cesium.Viewer("cesiumContainer");
});
</script>

<template>
  <div id="cesiumContainer" ref="cesiumContainer"></div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
}

#cesiumContainer {
  width: 100vw;
  height: 100vh;
}
</style>
