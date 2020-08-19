## Cesium学习第六课->3dtiles学习

1. ``Cesium3dTiles``也是图元的一种

```
var tileset = new Cesium.Cesium3DTileset({
  url: Cesium.IonResource.fromAssetId(40866),
});

viewer.scene.primitives.add(tileset);
viewer.zoomTo(tileset);
```
上述代码就是一个简单的Cesium3dTiles对象的创立，同时创立完成后，将其添加到图元``primitives``中，并在``globe``中确认的位置展示。

2. ``3dTiles``中的拾取操作代码解读
```
//设置拾取对象颜色特征的变化
function selectFeature(feature) {
  var element = feature.getProperty("element");
  setElementColor(element, Cesium.Color.YELLOW);
  selectedFeature = feature;
}
//未拾取对象的颜色特征设置
function unselectFeature(feature) {
  if (!Cesium.defined(feature)) {
    return;
  }
  var element = feature.getProperty("element");
  setElementColor(element, Cesium.Color.WHITE);
  if (feature === selectedFeature) {
    selectedFeature = undefined;
  }
}
//画布中拾取事件的处理
var handler = new Cesium.ScreenSpaceEventHandler(scene.canvas);
handler.setInputAction(function (movement) {
  if (!picking) {
    return;
  }

  var feature = scene.pick(movement.endPosition);

  unselectFeature(selectedFeature);

  if (feature instanceof Cesium.Cesium3DTileFeature) {
    selectFeature(feature);
  }
}, Cesium.ScreenSpaceEventType.MOUSE_MOVE);
```
> 通过鼠标移动中，如果选中了相对应的拾取对象，就会调用``selectFeature``这个方法，去设置拾取对象的颜色，让其与未拾取到的对象有明显区分。``ScreenSpaceEventHandler``对象是``scene``中专门处理交互事件的类，具体可以在``Documentation``中查看。示例演示地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=3D%20Tiles%20BIM.html>

3. 3dTiles中的裁切(示例演示地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=3D%20Tiles%20Clipping%20Planes.html>)

> ``ClippingPlane``是裁切所需要使用的类，它的第一个参数是裁切平面的相对法线方向(根据空间直角坐标系确定),第二个参数为距离(沿着法线的方向去移动其位置距离)。文档地址：<http://localhost:8080/Build/Documentation/ClippingPlane.html?classFilter=ClippingPlane>

**若存在很多个3dTiles的话，每一个3dTiles的中心点都会不同，所以在``ClippingPlane``中设置的是其全局的中心点，所以可以很好地解决这个情况**

4. 3dTiles styling，根据不同场景设置颜色显示(示例地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=3D%20Tiles%20Feature%20Styling.html>)

```
function colorByHeight() {
  tileset.style = new Cesium.Cesium3DTileStyle({
    color: {
      conditions: [
        ["${Height} >= 300", "rgba(45, 0, 75, 0.5)"],
        ["${Height} >= 200", "rgb(102, 71, 151)"],
        ["${Height} >= 100", "rgb(170, 162, 204)"],
        ["${Height} >= 50", "rgb(224, 226, 238)"],
        ["${Height} >= 25", "rgb(252, 230, 200)"],
        ["${Height} >= 10", "rgb(248, 176, 87)"],
        ["${Height} >= 5", "rgb(198, 106, 11)"],
        ["true", "rgb(127, 59, 8)"],
      ],
    },
  });
}
```
> 上述代码就是一个根据高度来显示3dTiles不同模型颜色的``styling``设置,就是通过``Cesium3DTileStyle``这个类来进行设定，相应的文档地址：<http://localhost:8080/Build/Documentation/Cesium3DTileStyle.html?classFilter=Cesium3DTileStyle>

5. 3dTiles Inspector(项目展示地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=3D%20Tiles%20Inspector.html>)

6. classification (封闭的几何体，示例地址：<http://localhost:8080/Apps/Sandcastle/index.html?src=Classification.html&label=All>)
> ``classification``能够附着在``3dtiles``实例上实现代码：
```classificationType:Cesium.ClassificationType.CESIUM_3D_TILE```