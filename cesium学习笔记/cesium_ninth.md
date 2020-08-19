## CesiumJs第九课->Viewer/Entities组合

1. 演示地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=Box.html&label=Geometries>

2. 代码解读：
```
var viewer = new Cesium.Viewer("cesiumContainer");

var blueBox = viewer.entities.add({
  name: "Blue box",
  position: Cesium.Cartesian3.fromDegrees(-114.0, 40.0, 300000.0),
  box: {
    dimensions: new Cesium.Cartesian3(400000.0, 300000.0, 500000.0),
    material: Cesium.Color.BLUE,
  },
});

var redBox = viewer.entities.add({
  name: "Red box with black outline",
  position: Cesium.Cartesian3.fromDegrees(-107.0, 40.0, 300000.0),
  box: {
    dimensions: new Cesium.Cartesian3(400000.0, 300000.0, 500000.0),
    material: Cesium.Color.RED.withAlpha(0.5),
    outline: true,
    outlineColor: Cesium.Color.BLACK,
  },
});

var outlineOnly = viewer.entities.add({
  name: "Yellow box outline",
  position: Cesium.Cartesian3.fromDegrees(-100.0, 40.0, 300000.0),
  box: {
    dimensions: new Cesium.Cartesian3(400000.0, 300000.0, 500000.0),
    fill: false,
    outline: true,
    outlineColor: Cesium.Color.YELLOW,
  },
});

viewer.zoomTo(viewer.entities);
```
上述代码是一个很简单的几何体创建的例子。
>第一个创建了一个蓝色的长方体，通过使用``viewer``类中的``entities``实体集合类来创建，设置了其所在的位置``position``；形状``box``;``box``的属性：尺寸 ``dimensions``以及材质``material``。

> 第二个是创建了一个红色的长方体，但是在材质``material``中略有不同因为设置了颜色及透明度，还设置了外部边框显示以及外部边框的颜色。

> 第三个创建了一个只有外边框的长方体，这里设置了填充``fill``不显示。

**更多的关于几何体的属性可以自行前往文档中进行查看，示例演示都可以在Geometries分类下进行查看**

3. ``viewer.entitise``就是指向的``EntityCollection``这个类。
4. 一个``entity``是由许多``primitive``图元构成的。使用``entity``能够大大减少创建的复杂度。
> 在``scene.primitives.add``中只能设置一个统一的appearan,但是在``entity``中，每一个几何体能够设置自己的属性。当然``entity``设置完成后，还是是调用了``primitive``去创建的，但是不需要开发者额外去进行更加复杂的创建。


5. Viewer/Entities的作用：
    1. 方便创建直观的对象，同时做到性能优化（billboard、point等）
    2. 提供一些方便使用的函数：flyTo/zoomTo
    3. 赋予Entity对象时间这个属性，对象具备动态特性/Primitive不具备
    4. 提供一些UI（homeButton/sceneModePicker/projectionPicker、baseLayerPicker)
    5. 大量的快捷方式，camera等未必是好事。。
    6. Datasource模式来加载大规模数据：Geojson

6. ``viewer``的结构图示:
    ![avatar](/img/viewer.png)

>``dataSourceDisplay``使用来管理所有的数据源。``entities``是通过``defaultDataSource``来进行管理的。

7. ``DataSource``(示例演示：<http://localhost:8080/Apps/Sandcastle/index.html?src=GeoJSON%20and%20TopoJSON.html&label=DataSources>)
    1. ``DataSource``实际是与数据源绑定在一起的。数据源可以来自文件资源，如``.topojson``、``.geojson``、``.czml``、``kml``等为后缀的文件。
    2. 对应的处理文件资源的类：``CzmlDataSource``、``KmlDataSource``、``GeoJsonDataSource``等。
    3. ``DataSourceDisplay``文档地址:<http://localhost:8080/Build/Documentation/DataSourceDisplay.html?classFilter=datasource>