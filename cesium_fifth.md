## Cesium第五课->Primitive图元

1. ``Primitive``的结构
![avatar](/img/Primitive.png)

> ``GeometryInstance``是几何体实例，其中包括了所需的几何体以及关于几何体的位置、id、状态等信息，能够通过设置几何体的位置，来实现一个几何体放在不同位置的效果。而且``Geometry``是一笔绘成的，所以其效率很高。

> ``Appearance``是图元的外观，分为了三种，现在介绍一部分：``Material``为图元的材质(主要是通过多种多样的shader来实现);``RenderState``为渲染状态，它能够设置图元的绘制的状态，比如说物体绘制时是否绘制正面与反面等。

2. 代码示例解释

代码示例：
```
var dimensions = new Cesium.Cartesian3(400000.0, 400000.0, 400000.0);//设置一个长宽高为相应参数的立方体 
var positionOnEllipsoid = Cesium.Cartesian3.fromDegrees(115.0, 35.0);//设定立方体所在地球上的位置

//设置立方体的偏移值（矩阵乘法）
var boxModelMatrix = Cesium.Matrix4.multiplyByTranslation(
  Cesium.Transforms.eastNorthUpToFixedFrame(positionOnEllipsoid),//创建一个矩阵，空间直角坐标系的原点迁移到所指定的点处
  new Cesium.Cartesian3(0.0, 0.0, dimensions.z * 0.5),//立方体所处高度变为1.5倍
  new Cesium.Matrix4()//返回一个新的矩阵
);
//创建一个几何体
var boxGeometry = Cesium.BoxGeometry.fromDimensions({
  vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
  dimensions: dimensions,
});
//创建几何体的属性(声明几何体为长方体、矩阵为上方已创建几何体矩阵、设置其属性如下方的顶点颜色属性)
var boxGeometryInstance = new Cesium.GeometryInstance({
  geometry: boxGeometry,
  modelMatrix: boxModelMatrix,
  attributes: {
    color: Cesium.ColorGeometryInstanceAttribute.fromColor(
      new Cesium.Color(1.0, 0.0, 0.0, 0.5)
    ),
  },
});
//添加到图元中展示，同时设置了其appearance，PerInstanceColorAppearance是通过顶点颜色来设置图元的颜色
viewer.scene.primitives.add(
  new Cesium.Primitive({
    geometryInstances: boxGeometryInstance,//这里是可以用数组进行传递，可以传多个
    appearance: new Cesium.PerInstanceColorAppearance({
      closed: true,
    }),
  })
);
```
> 上述代码及解释是关于图元创建(几何体)的实例代码，在代码中完整解释了每一块的代码的使用说明。

**下面为使用``outline``时不一样的地方,附上outline的演示地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=development%2FBox%20Outline.html&label=Development>**
```
// BoxOutlineGeometry类提供了outline(只有线框的立方体)的用法
var boxOutlineGeometry = Cesium.BoxOutlineGeometry.fromDimensions({
  dimensions: dimensions,
});
// appearance中设置了renderState属性中LineWidth(线宽)属性。
scene.primitives.add(
  new Cesium.Primitive({
    geometryInstances: boxOutlineInstance,
    appearance: new Cesium.PerInstanceColorAppearance({
      flat: true,
      renderState: {
        lineWidth: Math.min(2.0, scene.maximumAliasedLineWidth),
      },
    }),
  })
);
```
> 在调用fromDegrees函数时，四个参数先后为：西经，南纬，东经，北纬

**关于``scene.primitives``实际返回对象为``PrimitiveCollection``图元集合类。当然关于``PrimitiveCollection``也可以看为一个``Primitive``或者是更多``PrimitiveCollection``的集合，同样``PrimitiveCollection``更加方便管理图元集合，关于``PrimitiveCollection``的文档地址:<http://localhost:8080/Build/Documentation/PrimitiveCollection.html?classFilter=PrimitiveCollec>**

还有一部分很多图元展示在地球上的示例，可以在这个实例中进行效果查看:<http://localhost:8080/Apps/Sandcastle/index.html?src=development%2FGeometry%20and%20Appearances.html&label=Development>
