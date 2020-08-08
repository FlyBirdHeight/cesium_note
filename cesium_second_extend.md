## Cesium中所使用的MVVM模式的记录
```
<tr>
    <td>Mode</td>
    <td>
        <select data-bind="options: colorBlendModes, value: colorBlendMode"></select>
    </td>
</tr>
```
上文中data-bind与value就是在Cesium中的数据绑定方式，该数据绑定方式是由knockout.js文件提供的，具体的可以前往其源码中查看

```
var viewModel = {
  color: "White",
  colors: ["White", "Red", "Green", "Blue", "Yellow", "Gray"],
  alpha: 1.0,
  colorBlendMode: "Highlight",
  colorBlendModes: ["Highlight", "Replace", "Mix"],
  colorBlendAmount: 0.5,
  colorBlendAmountEnabled: false,
};
Cesium.knockout.track(viewModel);
```
在JS文件中，使用这种方式去提供视图数据。

> 但是在Cesium中，其双向绑定不是自动进行的，无法像VUE中那样提供自动数据更换，需要开发者在代码中去完成数据的替换，如下段代码所示:
> 
```
Cesium.knockout
  .getObservable(viewModel, "colorBlendMode")
  .subscribe(function (newValue) {
    var colorBlendMode = getColorBlendMode(newValue);
    model.colorBlendMode = colorBlendMode;
    viewModel.colorBlendAmountEnabled =
      colorBlendMode === Cesium.ColorBlendMode.MIX;
  });
 ```
 通过knockout对象中的getObservable函数去手动替换数据。