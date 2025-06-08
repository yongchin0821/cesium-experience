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

  const clock = viewer.clock;
  clock.startTime = Cesium.JulianDate.fromIso8601("2023-01-01T00:00:00Z");
  clock.stopTime = Cesium.JulianDate.fromIso8601("2023-01-01T00:00:20Z");
  clock.currentTime = clock.startTime;
  clock.clockRange = Cesium.ClockRange.LOOP_STOP; // 到达终点后循环
  clock.multiplier = 1; // 10倍速播放

  const positionProperty = new Cesium.SampledPositionProperty();

  // 添加时间-位置样本（经度,纬度,高度）
  positionProperty.addSample(
    clock.startTime,
    Cesium.Cartesian3.fromDegrees(116.38, 39.9, 1000)
  );
  positionProperty.addSample(
    clock.stopTime,
    Cesium.Cartesian3.fromDegrees(116.42, 39.92, 1000)
  );

  // 设置插值方式（线性/Lagrange）
  positionProperty.setInterpolationOptions({
    interpolationDegree: 1, // 1=线性, 5=高阶拉格朗日
    interpolationAlgorithm: Cesium.LagrangePolynomialApproximation,
  });

  // 绑定到实体
  const aircraft = viewer.entities.add({
    availability: new Cesium.TimeIntervalCollection([
      new Cesium.TimeInterval({
        start: clock.startTime,
        stop: clock.stopTime,
      }),
    ]),
    position: positionProperty,
    orientation: new Cesium.VelocityOrientationProperty(positionProperty),
    model: { uri: "/public/Assets/GroundVehicle.glb" },
    path: new Cesium.PathGraphics({
      width: 5,
      material: new Cesium.PolylineGlowMaterialProperty({
        glowPower: 0.1,
        color: Cesium.Color.YELLOW,
      }),
    }),
  });

  //相机追踪物体
  viewer.trackedEntity = aircraft;

  // const czml = [
  //   {
  //     id: "document",
  //     name: "CZML Point",
  //     version: "1.0",
  //   },
  //   {
  //     id: "point",
  //     availability: "2023-01-01T00:00:00Z/2023-01-01T00:00:20Z",
  //     position: {
  //       epoch: "2023-01-01T00:00:00Z",
  //       cartographicDegrees: [0, 116.38, 39.9, 1000, 20, 116.42, 39.92, 1000],
  //     },
  //     point: {
  //       pixelSize: 10,
  //       color: {
  //         rgba: [255, 0, 0, 255],
  //       },
  //     },
  //   },
  // ];

  // let promiseData = Cesium.CzmlDataSource.load(czml);
  // promiseData
  //   .then((dataSource) => {
  //     viewer.dataSources.add(dataSource);
  //     // viewer.flyTo(destination);
  //   })
  //   .catch((error) => {
  //     console.error("Error loading CZML data:", error);
  //   });
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
