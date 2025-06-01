<script setup>
import { ref, onMounted } from "vue";
import * as Cesium from "cesium";
import "./Widgets/widgets.css";

Cesium.Ion.defaultAccessToken =
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI0MjQwYjhjOC04MmQ3LTQ2ZTctOThlOS0wNDYxNzUyYThkNmYiLCJpZCI6MzA3NjYzLCJpYXQiOjE3NDg1NjcwNjR9.YR6EIfvHzIYkDdcWf0hkOeckR8pmzHcYnPYUM4XKwMM";

window.CESIUM_BASE_URL = "/";

//设置默认视角
Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(
  //西边的经度
  89.5,
  //南边的纬度
  20.4,
  //东边的经度
  110.4,
  //北边的纬度
  61.2
);

onMounted(async () => {
  const viewer = new Cesium.Viewer("cesiumContainer", {
    // geocoder: false,
    homeButton: false,
    sceneModePicker: false,
    baseLayerPicker: false,
    navigationHelpButton: false,
    animation: false,
    timeline: false,
    fullscreenButton: false,

    //添加地形
    terrainProvider: await Cesium.createWorldTerrainAsync({
      requestVertexNormals: true,
      requestWaterMask: true,
    }),
  });

  //隐藏logo
  viewer.cesiumWidget.creditContainer.style.display = "none";

  const position = Cesium.Cartesian3.fromDegrees(106.585, 29.566, 1000);

  viewer.camera.flyTo({
    destination: position,
    orientation: {
      heading: Cesium.Math.toRadians(0),
      pitch: Cesium.Math.toRadians(-90),
      roll: 0,
    },
  });

  //添加建筑物
  const osmBuilding = viewer.scene.primitives.add(
    await Cesium.Cesium3DTileset.fromIonAssetId(96188)
  );

  //创建一个点
  // const point = viewer.entities.add({
  //   position: Cesium.Cartesian3.fromDegrees(106.585, 29.566, 10),
  //   point: {
  //     color: Cesium.Color.RED,
  //     pixelSize: 10,
  //     outlineColor: Cesium.Color.BLACK,
  //     outlineWidth: 4,
  //   },
  // });
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
