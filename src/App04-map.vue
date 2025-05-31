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

onMounted(() => {
  const viewer = new Cesium.Viewer("cesiumContainer", {
    // geocoder: false,
    homeButton: false,
    sceneModePicker: false,
    baseLayerPicker: false,
    navigationHelpButton: false,
    animation: false,
    timeline: false,
    fullscreenButton: false,
    // imageryProvider: new Cesium.WebMapTileServiceImageryProvider({
    //   url: "http://t0.tianditu.gov.cn/vec_w/wmts?tk=4341a89d7314a3381655b1f89c8b4f33",
    //   layer: "tdtBasicLayer",
    //   style: "default",
    //   format: "image/jpeg",
    //   tileMatrixSetID: "GoogleMapsCompatible",
    // }),
    //OSM地图
    // imageryProvider: new Cesium.OpenStreetMapImageryProvider({
    //   url: "https://a.title.openstreetmap.org",
    // }),
    //高德矢量地图
    imageryProvider: new Cesium.UrlTemplateImageryProvider({
      url: "https://webrd02.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
      // layer: "tdtVecBasicLayer",
      // style: "default",
      // format: "image/png",
      // tileMatrixSetID: "GoogleMapsCompatible",
        credit: 'High-resolution imagery provided by Gaode Maps'
    }),
  });

  //隐藏logo
  viewer.cesiumWidget.creditContainer.style.display = "none";
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
