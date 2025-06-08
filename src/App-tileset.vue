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
    // homeButton: false,
    // sceneModePicker: false,
    // baseLayerPicker: false,
    // navigationHelpButton: false,
    // animation: false,
    // timeline: false,
    // fullscreenButton: false,
    // //Start in Columbus Viewer
    // // sceneMode: Cesium.SceneMode.COLUMBUS_VIEW,
    // //Use Cesium World Terrain
    // terrainProvider: await Cesium.createWorldTerrainAsync({
    //   requestVertexNormals: true,
    //   requestWaterMask: true,
    // }),

    homeButton: false,
    sceneModePicker: false,
    baseLayerPicker: false,
    navigationHelpButton: false,
    animation: false,
    timeline: false,
    fullscreenButton: false,
    terrainProvider: await Cesium.createWorldTerrainAsync(),

    //Use OpenStreetMaps
    // imageryProvider: new Cesium.UrlTemplateImageryProvider({
    //   url: "https://webrd0{s}.is.autonavi.com/appmaptile?lang=zh_cn&size=1&style=6&x={x}&y={y}&z={z}",
    //   subdomains: ["1", "2", "3", "4"],
    //   tilingScheme: new Cesium.WebMercatorTilingScheme(),
    //   maximumLevel: 18,
    //   credit: "高德地图",
    // }),
  });

  //隐藏logo
  viewer.cesiumWidget.creditContainer.style.display = "none";

  const position = Cesium.Cartesian3.fromDegrees(106.585, 29.566, 1000);

  const tileset = await Cesium.createOsmBuildingsAsync();
  viewer.scene.primitives.add(tileset);

  viewer.camera.setView({
    destination: position,
    orientation: {
      heading: Cesium.Math.toRadians(0),
      pitch: Cesium.Math.toRadians(-90),
      roll: 0,
    },
  });
  console.log(tileset);
  tileset.style = new Cesium.Cesium3DTileStyle({
    defines: {
      distance:
        "distance(vec2(${feature['cesium#longitude']},${feature['cesium#latitude']}), vec2(106.585, 29.566))",
    },
    color: {
      conditions: [
        // ["${feature['cesium#estimatedHeight']} > 300", "color('red')"],
        // ["${feature['cesium#estimatedHeight']} > 200", "color('blue')"],
        // ["${feature['cesium#estimatedHeight']} > 100", "color('orange')"],
        // ["${feature['cesium#estimatedHeight']} > 50", "color('yellow')"],
        // ["${feature['cesium#estimatedHeight']} > 0", "color('green')"],
        ["${distance} < 0.01", "color('blue')"],
        ["${distance} < 0.02", "color('green')"],
        ["${distance} < 0.03", "color('yellow')"],
        ["${distance} < 0.04", "color('orange')"],
        ["true", "color('white')"],
      ],
    },
  });

  var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
  handler.setInputAction(function (movement) {
    var pick = viewer.scene.pick(movement.position);
    console.log(pick);
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK);
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
