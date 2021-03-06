## CesiumJs学习笔记第十课->Property学习

1. ``property``负责在cesium管理一部分数据的功能。

2. ``Property``最大的特点是和时间相互关联，在不同的时间可以动态地返回不同的属性值。而Entity则可以感知这些Property的变化，在不同的时间驱动物体进行动态展示。

示例代码如下：
```
var property = new Cesium.SampledProperty(Cesium.Cartesian3);

property.addSample(Cesium.JulianDate.fromIso8601('2019-01-01T00:00:00.00Z'), 
    new Cesium.Cartesian3(400000.0, 300000.0, 200000.0));

property.addSample(Cesium.JulianDate.fromIso8601('2019-01-03T00:00:00.00Z'), 
    new Cesium.Cartesian3(400000.0, 300000.0, 700000.0));

blueBox.box.dimensions = property;
```
> 在上段代码中不难看出，``property``能够承载不同时间段的状态属性，并且可以赋值给``blueBox.box.dimensions``。

3. ``Property``的分类
![avatar](/img/property.png)

4. Property虚基类

    ![avatar](/img/propertyInterface.png)
    1. getValue 是一个方法，用来获取某个时间点的特定属性值。它有两个参数：第一个是time，用来传递一个时间点；第二个是result，用来存储属性值，当然也可以是undefined。这个result是Cesium的scratch机制，主要是用来避免频繁创建和销毁对象而导致内存碎片。Cesium就是通过调用getValue类似的一些函数来感知Property的变化的，当然这个方法我们在外部也是可以使用的。
    2. isConstant 用来判断该属性是否会随时间变化，是一个布尔值。Cesium会通过这个变量来决定是否需要在场景更新的每一帧中都获取该属性的数值，从而来更新三维场景中的物体。如果isConstant为true，则只会获取一次数值，除非definitionChanged事件被触发。
    3. definitionChanged 是一个事件，可以通过该事件，来监听该Property自身所发生的变化，比如数值发生修改。
    4. equals 是一个方法，用来检测属性值是否相等。

5. 基本Property类型
    1. ``SampleProperty``,用来通过给定多个不同时间点的Sample，然后在每两个时间点之间进行线性插值的一种Property
    2. ``TimeIntervalCollectionProperty``,该Property用来指定各个具体的时间段的属性值，每个时间段内的属性值是恒定的，并不会发生变化，除非已经进入到下一个时间段。代码示例如下:
    ```
    var property = new Cesium.TimeIntervalCollectionProperty(Cesium.Cartesian3);

    property.intervals.addInterval(Cesium.TimeInterval.fromIso8601({
        iso8601 : '2019-01-01T00:00:00.00Z/2019-01-01T12:00:00.00Z',
        isStartIncluded : true,
        isStopIncluded : false,
        data : new Cesium.Cartesian3(400000.0, 300000.0, 200000.0)
    }));
    property.intervals.addInterval(Cesium.TimeInterval.fromIso8601({
        iso8601 : '2019-01-01T12:00:01.00Z/2019-01-02T00:00:00.00Z',
        isStartIncluded : true,
        isStopIncluded : false,
        data : new Cesium.Cartesian3(400000.0, 300000.0, 400000.0)
    }));
    property.intervals.addInterval(Cesium.TimeInterval.fromIso8601({
        iso8601 : '2019-01-02T00:00:01.00Z/2019-01-02T12:00:00.00Z',
        isStartIncluded : true,
        isStopIncluded : false,
        data : new Cesium.Cartesian3(400000.0, 300000.0, 500000.0)
    }));
    property.intervals.addInterval(Cesium.TimeInterval.fromIso8601({
        iso8601 : '2019-01-02T12:00:01.00Z/2019-01-03T00:00:00.00Z',
        isStartIncluded : true,
        isStopIncluded : true,
        data : new Cesium.Cartesian3(400000.0, 300000.0, 700000.0)
    }));

    blueBox.box.dimensions = property;
    ```
    3. ``ConstantProperty``,Constant的意思并不是说这个Property不可改变，而是说它不会随时间发生变化。举个例子，我们可以通过 property.getValue(viewer.clock.currentTime) 方法来获取某个时间点property的属性值。如果property是SampleProperty或者TimeIntervalCollectionProperty的话，不同的时间点，可能getValue出不同的数值。但是如果这个property是ConstantProperty，那么无论什么时间（getValue的第一个参数不起作用），最后返回的数值都是一样的。
    4. ``CompositeProperty``的意思是组合的Property，可以把多种不同类型的ConstantProperty、SampleProperty、TimeIntervalCollectionProperty等Property组合在一起来操作。实际代码如下:
    ```
    var compositeProperty = new Cesium.CompositeProperty();
    compositeProperty.intervals.addInterval(Cesium.TimeInterval.fromIso8601({
        iso8601 : '2019-01-01T00:00:00.00Z/2019-01-02T00:00:00.00Z',
        data : sampledProperty
    }));
    compositeProperty.intervals.addInterval(Cesium.TimeInterval.fromIso8601({
        iso8601 : '2019-01-02T00:00:00.00Z/2019-01-03T00:00:00.00Z',
        isStartIncluded : false,
        isStopIncluded : false,
        data : ticProperty
    }));
    blueBox.box.dimensions = compositeProperty;
    ```
6. ``PositionProperty``
    > PositionProperty和Property一样，是一个虚类，并不能直接实例化，他扩展了Property的接口，增加了referenceFrame，同时只能用来表示position。
    ![avatar](/img/positionProperty.png)
    ``referenceFrame``是用来表示position的参考架。目前Cesium有以下两种参考架。
    ![avatar](/img/referenceFrame.png)
    ``FIXED``是默认类型，它是以地球中心为坐标系的原地，X轴指向赤道和本初子午线的交点，所以当给定Cestisan坐标后，它在地球上位置固定。
    ``INERTIAL``的类型是以太阳系的质心为原点的坐标偏移到地球中心，如果给定一个Cestisan坐标，那么它表示为不同时间的地球上的不同位置。
    基于PositionProperty的类型有以下几种：
    ``CompositePositionProperty``;
    ``ConstantPositionProperty``;
    ``PositionProperty``;
    ``PositionPropertyArray``;
    ``SampledPositionProperty``;
    ``TimeIntervalCollectionPositionProperty``。

7. ``SampledPositionProperty``具体代码示例如下：
    ```
    var property = new Cesium.SampledPositionProperty();

    property.addSample(Cesium.JulianDate.fromIso8601('2019-01-01T00:00:00.00Z'), 
        Cesium.Cartesian3.fromDegrees(-114.0, 40.0, 300000.0));
    
    property.addSample(Cesium.JulianDate.fromIso8601('2019-01-03T00:00:00.00Z'), 
        Cesium.Cartesian3.fromDegrees(-114.0, 45.0, 300000.0));

    blueBox.position = property;
    ```
    ``SampleProperty``和``SampledPositionProperty``有一个特有的方法：``setInterpolationOptions``，用来修改不同的插值方式。共有三种不同的插值方式分别为：线性插值``Linear``;拉格朗日插值``Lagrange``;``Hermite``插值。

8. ``MaterialProperty``
    >MaterialProperty是用来专门表示材质的Property，它对Property进行了扩展，增加了getType方法，用来获取材质类型。
    
    ``MaterialProperty``的派生类
    ![avatar](/img/MaterialProperty.png)
    具体实例列举: **ColorMaterialProperty的动态变化**
    ```
    var colorProperty = new Cesium.SampledProperty(Cesium.Color);
    colorProperty.addSample(Cesium.JulianDate.fromIso8601('2019-01-01T00:00:00.00Z'), 
        new Cesium.Color(0, 1, 0));
    
    colorProperty.addSample(Cesium.JulianDate.fromIso8601('2019-01-03T00:00:00.00Z'), 
        new Cesium.Color(0, 0, 1));

    blueBox.box.material = new Cesium.ColorMaterialProperty(colorProperty);
    ```

9. ``CallbackProperty``
> CallbackProperty是自由度最高的一种Property，让用户通过自定义，回调函数，来返回需要的值。回调函数中，用户可以使用time来给定value，也可以以自己的方式给给定。

代码示例:
```
var l = 200000.0;
var property = new Cesium.CallbackProperty(function (time, result) {
    result = result || new Cesium.Cartesian3(0, 0, 0);

    l += 10000.0;
    if (l > 700000.0) {
        l = 200000.0;
    }

    result.x = 400000.0;
    result.y = 300000.0;
    result.z = l;

    return result;
}, false);

blueBox.box.dimensions = property;
```

10. ``ReferenceProperty``
> 该Property可以直接链接到别的对象的Property上，相当于引用，避免重复构建了。
```
var collection = viewer.entities;
redBox.box.dimensions = new Cesium.ReferenceProperty(collection, blueBox.id, ['box', 'dimensions']);
```
> ReferenceProperty构造函数的参数有三个。第一个参数用来指定需要引用的对象所属的collection，如果没有自己专门创建EntityCollection的话，可以直接使用viewer.entities。第二个参数传递所指对象的id。第三个参数指定属性的位置的数组，如果是有层级的属性，可以依次写入。

11. ``PropertyBag``
> PropertyBag虽然不是以Property结尾，但实际上也是一个Property。它的特点是可以包装一个对象(JS中的对象概念)，该对象的每一个属性(JS中的属性概念)，都可以作为一个动态的Property。
示例代码：
```
var zp = new Cesium.SampledProperty(Number);
zp.addSample(Cesium.JulianDate.fromIso8601('2019-01-01T00:00:00.00Z'), 200000.0);
zp.addSample(Cesium.JulianDate.fromIso8601('2019-01-03T00:00:00.00Z'), 700000.0);

blueBox.box.dimensions = new Cesium.PropertyBag({
    x: 400000.0,
    y: 300000.0,
    z: zp
});
```