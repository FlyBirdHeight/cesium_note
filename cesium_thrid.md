## Cesium学习第三课-> Billboard Label PointPrimitives图元

1. ``Billboard``始终面朝屏幕，关于``Billboard``的示例构建地址为: <http://localhost:8080/Apps/Sandcastle/index.html?src=development%2FBillboards.html&label=All>

```
function addBillboard() {
  Sandcastle.declare(addBillboard);

  var billboards = scene.primitives.add(
    new Cesium.BillboardCollection()
  );
  billboards.add({
    image: "../images/Cesium_Logo_overlay.png",
    position: Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883),
  });
}
```
上段示例代码中，``billboards``是通过图元类``primitives(也是一个集合)``添加完成后返回的对象赋予(链式操作返回)，``Cesium.BillboardCollection``函数返回了Billboards集合对象。
> 在``billboards``调用add函数时，第一个属性为需要添加的图像，第二个属性为空间直角坐标系中所处的位置(使用经纬度代替，避免值过大不方便使用)。其他属性值设置可以前往文档中查看。

```
function changeBillboardProperties() {
  Sandcastle.declare(changeBillboardProperties);

  var billboards = scene.primitives.add(
    new Cesium.BillboardCollection()
  );

  // add() returns a Billboard object containing functions to change
  // the billboard's position and appearance.
  var b = billboards.add({
    image: "../images/Cesium_Logo_overlay.png",
  });

  b.position = Cesium.Cartesian3.fromDegrees(
    -75.59777,
    40.03883,
    300000.0
  );
  b.scale = 3.0;
  b.color = new Cesium.Color(1.0, 1.0, 1.0, 0.25);
}
```
> 上段代码示例中，在创建完``billboards``之后，会返回一个Billboard对象，通过修改其可修改的属性值，自定义``Billboard``属性。

```
function inReferenceFrame() {
  Sandcastle.declare(inReferenceFrame);

  var billboards = scene.primitives.add(
    new Cesium.BillboardCollection()
  );
  var center = Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883);
  billboards.modelMatrix = Cesium.Transforms.eastNorthUpToFixedFrame(
    center
  );

  var facilityUrl = "../images/facility.gif";

  // center
  billboards.add({
    image: facilityUrl,
    position: new Cesium.Cartesian3(0.0, 0.0, 0.0),
  });
  // east
  billboards.add({
    image: facilityUrl,
    position: new Cesium.Cartesian3(1000000.0, 0.0, 0.0),
  });
  // north
  billboards.add({
    image: facilityUrl,
    position: new Cesium.Cartesian3(0.0, 1000000.0, 0.0),
  });
  // up
  billboards.add({
    image: facilityUrl,
    position: new Cesium.Cartesian3(0.0, 0.0, 1000000.0),
  });
}
```
> 上段代码示例中主要是表示``BillboardCollection``对象建立之后，重新设置了其所在的中心点，使其不在以地球中心作为中心建立直角坐标系，而是使用设置之后的坐标(**此坐标还是以地球为圆心的空间直角坐标系上的坐标**)为中心，建立新的空间直角坐标系，并设置其相应的``position``


**关于其他的``Billboard``设置，可以前往文档中查看,此处附上文档地址:<http://localhost:8080/Build/Documentation/Billboard.html?classFilter=Billboard>,其具体效果可以再:<http://localhost:8080/Apps/Sandcastle/index.html?src=development%2FBillboards.html&label=All>进行查看(因为BillBoard是一个集合，所以理所当然可以设置多个)**

2. Label
**关于Label,其实本质上是与Billboard是相通的，两者最大的区别就是一个是显示图片，一个是显示文字，所以此处不再过多的赘述，附上文档地址: <http://localhost:8080/Build/Documentation/Label.html?classFilter=Label>,演示地址：<http://localhost:8080/Apps/Sandcastle/index.html?src=development%2FLabels.html&label=All>**

3. Point
**关于Point,如上,附上文档地址: <http://localhost:8080/Build/Documentation/PointPrimitive.html?classFilter=Point>,演示地址：<http://localhost:8080/Apps/Sandcastle/index.html?src=development%2FPointPrimitives.html&label=Development>**