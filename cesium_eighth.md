## CesiumJs第八课学习->Camera和Scene中的部分函数使用

1. ``Scene``中的``SceneSpaceCameraController``类的部分使用方法说明
<table>
   <tr>
      <td rowspan="1">Members成员变量</td>
   </tr>
   <tr>
      <td colspan="1"></td>
      <td>变量名</td>
      <td>用处</td>
      <td>类型及默认值</td>
   </tr>
   <tr>
      <td></td>
      <td>enableInputs</td>
      <td>若为true，则允许其他如trsanslate、zoom、rotate、tilt、look这些事件的使用，若是false,则禁用以上事件。<font style="font-weight:bolder;color:rgb(205,92,92)">在使用时一定要注意作用的时间仅仅为暂时的，会在事件开始时会被设置为false,完成后会变为true,这时候则需要其他事件去控制。</font></td>
      <td>Boolean->true</td>
   </tr>
     <tr>
      <td></td>
      <td>enableLook</td>
      <td>若为true则是可以让用户自由控制视角；若false,只能通过translate和rotate控制视角</td>
      <td>Boolean->true</td>
   </tr>
    <tr>
      <td></td>
      <td>enableRotate</td>
      <td>若true,则可以通过旋转世界来切换用户的位置，作用于3d与2d中；若false,则不可。</td>
      <td>Boolean->true</td>
   </tr>
    <tr>
      <td></td>
      <td>enableTilt</td>
      <td>若为true,则用户可以倾斜摄像头；若为false,则镜头则会被锁定无法倾斜，作用于3d与Columbus view</td>
      <td>Boolean->true</td>
   </tr>
    <tr>
      <td></td>
      <td>enableTranslate</td>
      <td></td>
      <td>Boolean->true</td>
   </tr>
    <tr>
      <td></td>
      <td>enableZoom</td>
      <td>若true，则允许用户进行缩放操作；若false，则会锁定镜头距离</td>
      <td>Boolean->true</td>
   </tr>
    <tr>
      <td></td>
      <td>lookEventTypes</td>
      <td>用户改变观看方向的输入事件。</td>
      <td>CameraEventType|Array|undefined->{ eventType : CameraEventType.LEFT_DRAG, modifier : KeyboardEventModifier.SHIFT }</td>
   </tr>
    <tr>
      <td></td>
      <td>maximumMovementRatio</td>
      <td>最大移动比，范围在[0,1)，用户输入的范围限制于每个动画帧的窗口宽度与高度的百分比。</td>
      <td>Number->0.1</td>
   </tr>
    <tr>
      <td></td>
      <td>等等....</td>
      <td>等等....</td>
   </tr>
</table>




2. ``Camera``的基本操作(示例地址:<http://localhost:8080/Apps/Sandcastle/index.html?src=Camera.html&label=All>)

文档地址:<http://localhost:8080/Build/Documentation/Camera.html?classFilter=Camera>

> ``flyTo``与``setView``之间的些许区别: ``flyTo``会有一个飞跃过去的一个过程，而``setView``则是直接到达目的地

> ``camera``类相对简单，只需多看看文档既可以很好的了解，在此处就不过多解读详细代码了。