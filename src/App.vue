<script setup>
import { ref, onMounted } from "vue";
import * as Cesium from "cesium";
import "./Widgets/widgets.css";

Cesium.Ion.defaultAccessToken =
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI0MjQwYjhjOC04MmQ3LTQ2ZTctOThlOS0wNDYxNzUyYThkNmYiLCJpZCI6MzA3NjYzLCJpYXQiOjE3NDg1NjcwNjR9.YR6EIfvHzIYkDdcWf0hkOeckR8pmzHcYnPYUM4XKwMM";

window.CESIUM_BASE_URL = "/";

//设置默认视角
// Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(
//   //西边的经度
//   89.5,
//   //南边的纬度
//   20.4,
//   //东边的经度
//   110.4,
//   //北边的纬度
//   61.2
// );

onMounted(async () => {
  const viewer = new Cesium.Viewer("cesiumContainer", {
    homeButton: false,
    sceneModePicker: false,
    baseLayerPicker: false,
    navigationHelpButton: false,
    animation: true,
    timeline: true,
    fullscreenButton: false,
    shouldAnimate: true,
    terrainProvider: await Cesium.createWorldTerrainAsync(),
  });

  //隐藏logo
  viewer.cesiumWidget.creditContainer.style.display = "none";

  const destination = Cesium.Cartesian3.fromDegrees(116.38, 39.9, 2000);

  const tileset = await Cesium.createOsmBuildingsAsync();
  viewer.scene.primitives.add(tileset);

  viewer.camera.setView({
    destination: destination,
    orientation: {
      heading: Cesium.Math.toRadians(0),
      pitch: Cesium.Math.toRadians(-90),
      roll: 0,
    },
  });

  var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
  handler.setInputAction(function (movement) {
    var pick = viewer.scene.pick(movement.position);
    console.log(pick);
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK);

  function performVisibilityAnalysis(observerPos, radius, fov, segments) {
    const viewer = window.viewer;
    const scene = viewer.scene;
    const pointsVisible = [];
    const pointsBlocked = [];

    for (let angle = 0; angle < fov; angle += fov / segments) {
      const heading = Cesium.Math.toRadians(angle);
      const targetCartographic = Cesium.Cartographic.fromCartesian(observerPos);
      const targetPos = Cesium.Cartesian3.fromRadians(
        targetCartographic.longitude + 0.001 * Math.cos(heading),
        targetCartographic.latitude + 0.001 * Math.sin(heading),
        targetCartographic.height
      );

      const ray = new Cesium.Ray(
        observerPos,
        Cesium.Cartesian3.normalize(
          Cesium.Cartesian3.subtract(
            targetPos,
            observerPos,
            new Cesium.Cartesian3()
          ),
          new Cesium.Cartesian3()
        )
      );

      const intersection = scene.globe.pick(ray, scene);

      if (
        !intersection ||
        Cesium.Cartesian3.distance(observerPos, intersection) >=
          Cesium.Cartesian3.distance(observerPos, targetPos)
      ) {
        pointsVisible.push(targetPos);
      } else {
        pointsBlocked.push(intersection);
      }
    }

    drawResult(pointsVisible, pointsBlocked);
  }
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
