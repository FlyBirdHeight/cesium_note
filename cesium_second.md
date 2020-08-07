## Cesium学习第二课->Model图元使用

### Model图元使用详解
1. Model官方示例：<http://localhost:8080/Apps/Sandcastle/index.html?src=3D%20Models.html>

> 本地址为运行项目时查看

2. 创建一个Model对象:
```
scene.primitives.add(Cesium.Model,fromGltf({
    url:url,
    modelMatrix: modelMatrix,
    minimumPixelSize: 128
})) 
```