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
  });

  //隐藏logo
  viewer.cesiumWidget.creditContainer.style.display = "none";

  const position = Cesium.Cartesian3.fromDegrees(106.585, 29.566, 1000);

  // viewer.camera.flyTo({
  //   destination: position,
  //   orientation: {
  //     heading: Cesium.Math.toRadians(0),
  //     pitch: Cesium.Math.toRadians(-90),
  //     roll: 0,
  //   },
  // });

  //添加建筑物
  const osmBuilding = viewer.scene.primitives.add(
    await Cesium.Cesium3DTileset.fromIonAssetId(96188)
  );

  // entity
  const entity = viewer.entities.add({
    rectangle: {
      coordinates: Cesium.Rectangle.fromDegrees(
        106.585,
        29.566,
        116.586,
        34.567
      ),
      material: Cesium.Color.RED.withAlpha(0.5),
    },
  });

  // primitives
  const geometry = new Cesium.RectangleGeometry({
    rectangle: Cesium.Rectangle.fromDegrees(96.585, 29.566, 105.585, 34.567),
    vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
  });

  const geometryInstance = new Cesium.GeometryInstance({
    geometry: geometry,
    attributes: {
      color: Cesium.ColorGeometryInstanceAttribute.fromColor(
        Cesium.Color.RED.withAlpha(0.5)
      ),
    },
  });

  const appearance = new Cesium.PerInstanceColorAppearance({
    flat: true,
  });

  const primitive = new Cesium.Primitive({
    geometryInstances: geometryInstance,
    appearance: appearance,
  });

  viewer.scene.primitives.add(primitive);
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
