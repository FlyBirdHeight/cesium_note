## Cesium学习第七课->Appearance类与Material类
1. 文档中提供的Appearance相关类,文档地址:<http://localhost:8080/Build/Documentation/Appearance.html?classFilter=Appearance>

| 类名 | 大致作用 |
| :---: | :---: |
| MaterialAppearance | 针对于任意一个实例设置图案或纹理(很常用)等其他样式，且只会在每一个Instance中作用一个颜色 | 
| PolylineColorAppearance | ``PolylineGeometry``所使用的颜色设置 | 
| EllipsoidSurfaceAppearance | 针对于``PolygonGeometry``、``RectangleGeometry``的颜色属性 | 
| PerInstanceColorAppearance | 针对于任意一个几何体实例的颜色属性(很常用 ) ，且作用在每一个不同的instance中| 
| PolylineMateialAppearance | ``PolylineGeometry``所使用的其他样式设置 | 

> ``PerInstanceColorAppearance``是可以通过在创建instance时提供的颜色来给实例的外观上色，保证每一个实例设置的颜色。

**PerInstanceColorAppearance类使用方法示例**
```
var primitive = new Cesium.Primitive({
  geometryInstances : new Cesium.GeometryInstance({
    geometry : new Cesium.SimplePolylineGeometry({
      positions : Cesium.Cartesian3.fromDegreesArray([
        0.0, 0.0,
        5.0, 0.0
      ])
    }),
    attributes : {
      color : Cesium.ColorGeometryInstanceAttribute.fromColor(new Cesium.Color(1.0, 1.0, 1.0, 1.0))
    }
  }),
  appearance : new Cesium.PerInstanceColorAppearance({
    flat : true,
    translucent : false
  })
});

// Two rectangles in a primitive, each with a different color
var instance = new Cesium.GeometryInstance({
  geometry : new Cesium.RectangleGeometry({
    rectangle : Cesium.Rectangle.fromDegrees(0.0, 20.0, 10.0, 30.0)
  }),
  attributes : {
    color : new Cesium.ColorGeometryInstanceAttribute(1.0, 0.0, 0.0, 0.5)
  }
});

var anotherInstance = new Cesium.GeometryInstance({
  geometry : new Cesium.RectangleGeometry({
    rectangle : Cesium.Rectangle.fromDegrees(0.0, 40.0, 10.0, 50.0)
  }),
  attributes : {
    color : new Cesium.ColorGeometryInstanceAttribute(0.0, 0.0, 1.0, 0.5)
  }
});

var rectanglePrimitive = new Cesium.Primitive({
  geometryInstances : [instance, anotherInstance],
  appearance : new Cesium.PerInstanceColorAppearance()
});
```


2. ``Material``类,文档地址:<http://localhost:8080/Build/Documentation/Material.html?classFilter=Material>

**关于``Material``类中部分属性说明**

<table>
   <tr>
      <td rowspan="1">Type</td>
   </tr>
   <tr>
      <td colspan="1"></td>
      <td>属性名</td>
      <td>用处</td>
   </tr>
   <tr>
      <td></td>
      <td>Color</td>
      <td>颜色设置</td>
   </tr>
   <tr>
      <td></td>
      <td>Image</td>
      <td>图像设置</td>
   </tr>
   <tr>
      <td></td>
      <td>DiffuseMap</td>
      <td>漫反射设置</td>
   </tr>
   <tr>
      <td></td>
      <td>AlphaMap</td>
      <td>介质透明度</td>
   </tr>
   <tr>
      <td></td>
      <td>SpecularMap</td>
      <td>镜面反射设置</td>
   </tr>
    <tr>
      <td></td>
      <td>等等....</td>
      <td>等等....</td>
   </tr>
</table>

**``Material``类的三种声明方法**
```
// 直接设置Material的type，之后再通过uniforms设置其属性值
polygon.material = Cesium.Material.fromType('Color');
polygon.material.uniforms.color = new Cesium.Color(1.0, 1.0, 0.0, 1.0);

// 直接赋予Material类对象，默认构造
polygon.material = new Cesium.Material();

// 创建一个Meterial类对象，并传入相对应json参数
polygon.material = new Cesium.Material({
    fabric : {
        type : 'Color',
        uniforms : {
            color : new Cesium.Color(1.0, 1.0, 0.0, 1.0)
        }
    }
});
```