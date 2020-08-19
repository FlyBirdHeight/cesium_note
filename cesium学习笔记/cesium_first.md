# Cesium 学习第一课

## 第一：下载并运行项目
1. 从github中克隆Cesium源码项目，链接：<https://github.com/CesiumGS/cesium.git> ,建议直接下载包，clone会比较慢。
2. 项目下载完成后，进行一下操作
```
npm install;
npm run build;
npm start;
```
完成以上操作之后，项目开始运行，在浏览器中输入: <localhost:8080>查看项目

## 第二：CesiumWidget
#### CesiumWidget主要分为如下5个对象:
| 对象名称 | 大概作用 |
| :-----: | :-----: |
| clock | 用来记录时间，通过时间确认某一帧的绘制内容 | 
| container | 容器，是构造函数的参数，为了获取呈现容器的id |
| canvas | 在container上构建的Canvas对象，用于获取WebGL绘制的画笔 |
| screenSpaceEventHandler | Canvas对象中各种交互事件的封装(鼠标) |
| scene | 用于承载3D场景中的对象 |
>注：clock与scene之间并无关系,scene本身并没用时间的概念


#### Scene(图元)
1. Scene 包含了以下环境对象 `` globe(地球) ``、`` skyBox(天空盒) ``、`` sun(太阳)``、 `` moon(月亮) ``等。
> Scene还有两个由用户控制的存放对象的数组:`` primitives(属于地球上构造的普通3D对象)`` 、 `` groundPrimitives(贴在地球表面上的对象)``。
> Scene管理图元。

2. Primitivers(图元类)对象包含了以下内容:

|对象名| 作用 |
|:-----:|:-----:|
| Model | 一个gltf对象(从3dmax或其他软件导出gltf文件，使用model进行渲染，在前端呈现) |
| Primitive(图元) | 图元的一种与其他图原相当于平级关系，可以自定义Geometry(几何体)。Geometry(几何体)通常是较为抽象的Geometry(几何体)(cesium官方内置的Geometry(几何体)) ；Geometry(几何体)外观可被覆盖，使用Appearance来确认|
| Billboards/Labels/Points | Billboards（电子标牌）就是三维窗口中的一个图片，在地球上的某一个位置,同时始终面向屏幕。Billboards能够根据视角远近变化大小，不会变化；Labels是一个三维中文字，会容易模糊，指定在三维地球上的某一个位置；Points指的就是一个点。 |
| ViewportQuad | 附着在平面中东西，在三维对象绘制完成后才进行绘制，作用不是特别大。（不会被随意更改，在控制台中不能被更改） |

> Scene中图元都是一笔绘制出来的。绘制的批次越少，渲染所需要的性能越少。

![avatar](/img/CesiumWidget_Object.png)