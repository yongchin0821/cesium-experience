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

  //加载geojson
  const geojson = Cesium.GeoJsonDataSource.load(
    "https://geo.datav.aliyun.com/areas_v3/bound/500000_full.json",
    {
      stroke: Cesium.Color.HOTPINK,
      fill: Cesium.Color.fromRandom({
        alpha: 0.5,
      }),
      strokeWidth: 3,
      markerSymbol: "?",
    }
  );
  geojson.then((dataSource) => {
    viewer.dataSources.add(dataSource);
    let entities = dataSource.entities.values;
    entities.forEach((entity, i) => {
      entity.polygon.material = new Cesium.ColorMaterialProperty(
        Cesium.Color.fromRandom({
          alpha: 0.5,
        })
      );
      entity.polygon.outline = false;
      entity.polygon.extrudedHeight = 100;
    });
  });
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
