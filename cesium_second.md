## Cesium学习第二课->Model图元使用

### Model图元使用详解
1. Model官方示例：<http://localhost:8080/Apps/Sandcastle/index.html?src=development%2F3D%20Models.html&label=Development>

> 本地址为运行项目时查看,同时需要在运行前使用Build后才可查看

2. 创建一个Model对象:
```
height = Cesium.defaultValue(height, 0.0);
heading = Cesium.defaultValue(heading, 0.0);
pitch = Cesium.defaultValue(pitch, 0.0);
roll = Cesium.defaultValue(roll, 0.0);
var hpr = new Cesium.HeadingPitchRoll(heading, pitch, roll);
var origin = Cesium.Cartesian3.fromDegrees(
    -123.0744619,
    44.0503706,
    height
  );
var modelMatrix = Cesium.Transforms.headingPitchRollToFixedFrame(origin, hpr);
scene.primitives.add(Cesium.Model,fromGltf({
    url:url,
    modelMatrix: modelMatrix,(控制模型摆放在地球上的位置)
    minimumPixelSize: 128(在屏幕中呈现最小也得为128个像素，Cesium会自动根据视线远近保证图像像素大小)
})) 
//资源创建需要一个过程就是下一段代码中的。
```
> HeadingPitchRoll是设置欧拉角，三个角度来确定图像的朝向，三个角度分别为Heading(头部角度，左右角度)、Pitch(抬头低头角度)、Roll(视线角度)。这三个角度都相对于图像自身。* 初始角度：Heading朝东方向为0(相当于地面);Pitch上正角度、下负角度。角度为从左到右。

> origin为图像在空间直角坐标系(以地球为圆心所做的空间直角坐标系)所处于的位置。x轴朝向赤道、y轴朝向亚洲、z轴朝向北极。origin的值都比较偏大。Cesium.Cartesian3.fromDegrees函数为从经纬度来构建origin，第一个参数为经度、第二个参数为维度、第三个参数为高度，通过这三个值来确认origin在空间直角坐标系中所处的位置。

> modelMatrix最终为一个四乘四的矩阵，通过此来确认图像在地球上的最终位置与姿态。

3. Model模型准备完成
```
//模型准备完成后的回调函数
model.readyPromise
    .then(function (model) {
      model.color = Cesium.Color.fromAlpha(
        getColor(viewModel.color),
        Number(viewModel.alpha)
      );
      model.colorBlendMode = getColorBlendMode(
        viewModel.colorBlendMode
      );
      model.colorBlendAmount = viewModel.colorBlendAmount;
      // Play and loop all animations at half-speed
      model.activeAnimations.addAll({
        multiplier: 0.5,
        loop: Cesium.ModelAnimationLoop.REPEAT,
      });

      var camera = viewer.camera;

      // Zoom to model
      var controller = scene.screenSpaceCameraController;
      var r =
        2.0 *
        Math.max(model.boundingSphere.radius, camera.frustum.near);
      controller.minimumZoomDistance = r * 0.5;

      var center = Cesium.Matrix4.multiplyByPoint(
        model.modelMatrix,
        model.boundingSphere.center,
        new Cesium.Cartesian3()
      );
      var heading = Cesium.Math.toRadians(230.0);
      var pitch = Cesium.Math.toRadians(-20.0);
      camera.lookAt(
        center,
        new Cesium.HeadingPitchRange(heading, pitch, r * 2.0)
      );
    })
    .otherwise(function (error) {
      window.alert(error);
    });
}
```
> ``colorBlendMode``函数用于控制模型的颜色混合模式，分别有：Highlight(高亮)、Replace(替代)、Mix(混合)三种形式。具体效果可以在展示页中查看。

> ``Cesium.Color.fromAlpha``函数用于控制模型的透明度设置。

> camera(相机),通过lookAt函数来调整视角(不仅设置了初始角度，同时绑定了模型的所在的位置并且不会发生改变)。在lookAt函数中的第一个参数center即为模型的正中心(可自行定义)，第二个参数为视角的角度与距离。Cesium.HeadingPitchRange函数代表了视角的角度与距离视角的距离，第一个参数Heading(头部角度)、第二个参数Pitch(抬头低头角度)、第三个参数则为距离视角的距离。

> lookAtTransform函数能够解除与模型位置的绑定(具体可在文档中查看)

**注：文档在项目中查看需要运行``npm run generateDocuumentation``命令，然后可以在项目首页中查看到Document选项，点进去后即可查看文档**

4. 事件的包装
```
var handler = new Cesium.ScreenSpaceEventHandler(scene.canvas);
handler.setInputAction(function (movement) {
  var pick = scene.pick(movement.endPosition);
  //判断拾取对象是否存在，但是node与mesh属性不一定是存在的。
  if (
    Cesium.defined(pick) &&
    Cesium.defined(pick.node) &&
    Cesium.defined(pick.mesh)
  ) {
    // Output glTF node and mesh under the mouse.
    console.log(
      "node: " + pick.node.name + ". mesh: " + pick.mesh.name
    );
  }
}, Cesium.ScreenSpaceEventType.MOUSE_MOVE);
```
> ScreenSpaceEventHandler类即为Scene中的事件处理类。
> setInputAction函数为事件输入处理函数。当函数在MOUSE_MOVE的事件中就会被执行。

> movement.endPosition相当于鼠标所处的位置(二维窗口中的位置)。scene.pick为场景中的拾取动作(就是在场景中创建了一条射线，观察在三维场景中与哪一个三维物体发生了求交关系，找到最近的一个求交关系并进行反馈)。

**注：所有的UI图形界面的操作均是由knockout.js来提供的**

**``ModelInstanceCollection``类对象的意思为模型实例集合，即不需要一个一个去手动创建Model,可以通过``ModelInstanceCollection``去批量创建Model,但是其渲染批次不是用一批，而是分批次渲染，渲染批次不定**

5. Model子节点控制，具体实例地址在: <http://localhost:8080/Apps/Sandcastle/index.html?src=development%2F3D%20Models%20Node%20Explorer.html&label=Development>
```
Cesium.knockout
      .getObservable(viewModel, "matrix")
      .subscribe(function (newValue) {
        var node = model.getNode(viewModel.nodeName);
        if (!Cesium.defined(node.originalMatrix)) {
          node.originalMatrix = node.matrix.clone();
        }
        node.matrix = Cesium.Matrix4.multiply(
          node.originalMatrix,
          newValue,
          new Cesium.Matrix4()
        );
      });
  })
```
此段代码示例涉及到子节点控制改变。
