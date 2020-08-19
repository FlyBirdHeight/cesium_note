## Cesium第四课学习-> 地形影像的加载
1. 地形的加载相关类的关系
![avatar](/img/TerrainProvider.png)

2. 地形加载的创建以及使用
```
var worldTerrain = Cesium.createWorldTerrain({
  requestWaterMask: true,
  requestVertexNormals: true,
});
```
> 使用Cesium.createWorldterrain函数创建一个地形示例实际返回的为``CesiumTerrainProvider``类对象。

实际使用如下：
```
{
    text: "CesiumTerrainProvider - Cesium World Terrain",
    onselect: function () {
        viewer.terrainProvider = worldTerrain;
        //viewer.scene.globe.terrainProvider = worldTerrain;
        viewer.scene.globe.enableLighting = true;
    },
}
```
> 可以看到``view.terrainProvider``被赋予了``worldTerrain``，此时地形将被进行使用。

**其他关于地形的使用可以参考项目案例及文档，附上地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=Terrain.html&label=Development>,<http://localhost:8080/Build/Documentation/TerrainProvider.html?classFilter=Terrain>**


2. ImageryProvider类(影像)所包含的内容
![avatar](/img/imageryProvider.png)

```
var layers = viewer.scene.imageryLayers;
var blackMarble = layers.addImageryProvider(
  new Cesium.IonImageryProvider({ assetId: 3812 })
);
```

> ``ImageryProvider``与``ImageryLayers``是不相同的，``ImageryProvider``是一个影像数据源的提供者，如上图所示中，具有很多的数据源提供；``ImageryLayer``则是一个影像的集合，内部一定会有``ImageryProvider``,但是还是会有其他的属性。具体可以查看对应文档：<http://localhost:8080/Build/Documentation/ImageryLayer.html?classFilter=ImageryL>,以及``ImageryLayerCollection``相关的文档:<http://localhost:8080/Build/Documentation/ImageryLayerCollection.html?classFilter=ImageryL>