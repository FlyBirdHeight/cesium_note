## Cesium第五课->Primitive图元

1. ``Primitive``的结构
![avatar](/img/Primitive.png)

> ``GeometryInstance``是几何体实例，其中包括了所需的几何体以及关于几何体的位置、id、状态等信息，能够通过设置几何体的位置，来实现一个几何体放在不同位置的效果。而且``Geometry``是一笔绘成的，所以其效率很高。

> ``Appearance``是图元的外观，分为了三种，现在介绍一部分：``Material``为图元的材质(主要是通过多种多样的shader来实现);``RenderState``为渲染状态，它能够设置图元的绘制的状态，比如说物体绘制时是否绘制正面与反面等。

2. 代码示例解释

代码示例：
```
var dimensions = new Cesium.Cartesian3(400000.0, 400000.0, 400000.0);//创建一个长宽高为相应参数的四边形
var positionOnEllipsoid = Cesium.Cartesian3.fromDegrees(115.0, 35.0);//设定立方体所在地球上的位置
var boxModelMatrix = Cesium.Matrix4.multiplyByTranslation(
  Cesium.Transforms.eastNorthUpToFixedFrame(positionOnEllipsoid),//创建一个矩阵，空间直角坐标系的原点迁移到所指定的点处
  new Cesium.Cartesian3(0.0, 0.0, dimensions.z * 0.5),//立方体的高度变为1.5倍
  new Cesium.Matrix4()
);
var boxGeometry = Cesium.BoxGeometry.fromDimensions({
  vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
  dimensions: dimensions,
});
var boxGeometryInstance = new Cesium.GeometryInstance({
  geometry: boxGeometry,
  modelMatrix: boxModelMatrix,
  attributes: {
    color: Cesium.ColorGeometryInstanceAttribute.fromColor(
      new Cesium.Color(1.0, 0.0, 0.0, 0.5)
    ),
  },
});
viewer.scene.primitives.add(
  new Cesium.Primitive({
    geometryInstances: boxGeometryInstance,
    appearance: new Cesium.PerInstanceColorAppearance({
      closed: true,
    }),
  })
);

```
