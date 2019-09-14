---
title: Get started with Mapbox GL JS expressions
title: 了解  Mapbox GL JS 表达式
description: Learn how to write expressions in Mapbox GL JS.
thumbnail: glJsExpressions
level: 2
prereq: Familiarity with front-end development concepts.
prereq: 熟悉前端开发的相关内容
topics:
- web apps
- map design
language:
- JavaScript
prependJs:
  - "import * as constants from '../../constants';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import BookImage from '@mapbox/dr-ui/book-image';"
  - "import Icon from '@mapbox/mr-ui/icon';"
  - "import UserAccessToken from '../../components/user-access-token';"
  - "import DemoIframe from '@mapbox/dr-ui/demo-iframe';"
  - "import Button from '@mapbox/mr-ui/button';"
contentType: tutorial
---

In this guide you'll learn how to write expressions in Mapbox GL JS to style custom data based on a data property and by zoom level.
通过这个指南您将会学到如何在 Mapbox GL JS 中通过编写的表达式的方式来实现基于数据属性和缩放级别来自定义数据。

{{
  <DemoIframe src="/help/demos/intro-to-expressions/step-four.html" />
}}

## Getting started
## 准备工作

- **A Mapbox account and access token**. Sign up for an account at [mapbox.com/signup](https://www.mapbox.com/signup/). You can find your [access tokens](/help/how-mapbox-works/access-tokens/) on your [Account page](https://www.mapbox.com/account/).
- **一个 Mapbox 账号和 access token**。在 [mapbox.com/signup](https://www.mapbox.com/signup/) 登录账号，在 [Account page](https://www.mapbox.com/account/) 中可以找到 [access tokens](/help/how-mapbox-works/access-tokens/)。
- **Mapbox GL JS**. You'll be using, Mapbox GL JS, our JavaScript library.
- **Mapbox GL JS**。您需要使用我们的 JavaScript library, Mapbox GL JS。
- **A style URL**. You'll need a [style URL](/help/glossary/style-url/) to initialize your map. You'll walk through how to find your style URL in the _Add a designer style to your account_ section of this guide.
- **style URL**。您在初始化地图时需要一个 [style URL](/help/glossary/style-url/)。在 _Add a designer style to your account_ 中有关于找到 style URL 的步骤指导。
- **Data**. In this tutorial, you'll be using a CSV file of [HPC Landmarks from Open Minneapolis](http://opendata.minneapolismn.gov/datasets/hpc-landmarks/data).
- **数据**。在本教程中，您将会用到 [HPC Landmarks from Open Minneapolis](http://opendata.minneapolismn.gov/datasets/hpc-landmarks/data) 中的 CSV 文件

{{
<Button href="/help/demos/intro-to-expressions/HPC_landmarks.csv" passthroughProps={{ download: "HPC_landmarks" }} >
    <Icon name='arrow-down' inline={true} /> Download the data
</Button>
}}

## What are expressions?
## 什么是表达式？

In the Mapbox Style Specification, the value for any layout property, paint property, or filter may be specified as an expression. Expressions define how one or more feature property value and/or the current zoom level are combined using logical, mathematical, string, or color operations to produce the appropriate style property value or filter decision.
在Mapbox样式规范中，可以将任何布局属性，绘图属性或筛选器的值以表达式的形式出现。表达式的定义是用来使用逻辑，数学，字符串或颜色操作组合一个或多个要素属性值和/或当前缩放级别，以生成适当的样式属性值或筛选结果。

A **property expression** is any expression defined using a reference to feature property data. Property expressions allow the appearance of a feature to change with its properties. They can be used to visually differentiate types of features within the same layer or create data visualizations.
**property expression** 是使用对要素属性数据的引用定义的任何表达式。property expression 允许所赋予的外观随其属性而变化。可用于在视觉上区分同一层中的不同要素类型或创建数据可视化。

{{
  <Note title='Property expressions vs. property functions' imageComponent={<BookImage />}>
    <p>If you have worked with Mapbox GL JS before, you may be familiar with property <em>functions</em>. Property <em>expressions</em> can help you achieve similar effects as property <em>functions</em>, with much more flexibility and functionality. Property expressions were introduced in <a href='https://github.com/mapbox/mapbox-gl-js/blob/master/CHANGELOG.md#0410-october-11-2017'>Mapbox GL JS v0.41.0</a>. <strong>While property functions are available, they will ultimately be deprecated and replaced by property expressions.</strong></p>
  </Note>
}}
{{
  <Note title='属性表达式 vs. 属性函数' imageComponent={<BookImage />}>
    <p>如果您曾经使用过 Mapbox GL JS，您会对属性 <em>functions</em> 熟悉。属性 <em>expressions</em> 可以帮助您实现与属性函数类似的效果，具有更多的灵活性和功能。属性表达式在 <a href='https://github.com/mapbox/mapbox-gl-js/blob/master/CHANGELOG.md#0410-october-11-2017'>Mapbox GL JS v0.41.0</a>中引入。虽然属性函数目前仍可使用，但它们最终将被弃用并由属性表达式替换。</strong></p>
  </Note>
}}

### Uses
### 用途

There are countless ways to apply property expressions to your application, including:
有许多方法可以将属性表达式应用于您的应用程序，包括：

- **Data-driven styling**: Specify style rules based on one or more data attribute.
- **Data-driven styling**: 根据一个或多个数据属性定义样式规则。
- **Arithmetic**: Do arithmetic on source data, for example performing calculations to convert units.
- **Arithmetic**:对源数据进行算术运算，例如执行转换单位的计算。
- **Conditional logic**: Use if-then logic, for example to decide exactly what text to display for a label based on which properties are available in the feature or even the length of the name.
- **Conditional logic**:使用if-then逻辑，例如，根据要素中可用的属性甚至名称的长度，准确确定要为标签显示的文本。
- **String manipulation**: Take control over label text with things like uppercase, lowercase, and title case transforms without having to edit, re-prepare and re-upload your data.
- **String manipulation**:无需编辑，重新准备和重新上传数据，使用大写，小写和标题大小写转换来控制标签文本。

### Syntax
### 句法

Mapbox GL JS expressions uses a Lisp-like syntax, represented using JSON arrays. Expressions follow this format:
Mapbox GL JS表达式使用类似Lisp的语法，使用JSON数组表示。表达式遵循以下格式：

```
[expression_name, argument_0, argument_1, ...]
```

The `expression_name` is the **expression operator**, for example, you would use [`'*'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-*) to multiply two arguments or [`'case'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-case) to create conditional logic. For a complete list of all available expressions see the [Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions).
`expression_name`是 **expression operator**(表达式运算符)，例如，您可以使用[`'*'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-*)乘以两个参数或[`'case'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-case)来创建条件逻辑。有关所有可用表达式的完整列表，请参阅[Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions)。

The **arguments** are either _literal_ (numbers, strings, or boolean values) or else themselves expressions. The number of arguments varies based on the expression.
**arguments**（参数） 可以是 _literal _(数字，字符串或布尔值)，也可以是自身表达式。参数的数量因表达式而异。

Here's one example using an expression to calculate an arithmetic expression (π * 3<sup>2</sup>):
这是使用表达式计算算术表达式(π * 3<sup>2</sup>)的一个示例：

```
['*', ['pi'], ['^', 3, 2]]
```

This example uses a [`'*'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-*) expression to multiply two arguments. The first argument is [`'pi'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-pi), which is an expression that returns the mathematical constant pi. The second arguement is another expression: a [`'^'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-^) expression with two arguments of its own. It will return 3<sup>2</sup>, and the result will be multiplied by π.
此示例使用[`'*'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-*)表示将两个参数相乘。第一个参数是[`'pi'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-pi)，它是一个返回数学常数pi的表达式。第二个参数是[`'^'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-^)表达式，它有两个自己的参数。它将返回3<sup>2</sup>，结果将乘以π。

## Set up a map
## 设置地图

Now that you've had an introduction to the uses and syntax for expressions, it's time to test it out yourself! Initialize a map with Mapbox GL JS and add the custom data as a circle layer.
现在您已经初步了解了表达式的用法和语法，眼过千遍不如手过一遍，使用Mapbox GL JS初始化地图，并将自定义数据添加到circle layer。

### Add a designer style to your account
### 为您的帐户添加设计师样式

The map in this guide uses one of our designer styles. To use a designer style you'll need to start by adding it to your account. Sign into your Mapbox account and visit the designer map page to add a new style to your account:
本指南中的地图使用了 Mapbox 的设计师风格之一。要使用设计师样式，您需要先将其添加到您的帐户。登录您的Mapbox帐户并访问设计师地图页面，为您的帐户添加新样式：

1. Visit [mapbox.com/designer-maps](https://www.mapbox.com/designer-maps/).
1. 访问 [mapbox.com/designer-maps](https://www.mapbox.com/designer-maps/)
1. Find the _North Star_ style and click **Add this style**.
1. 找到 _North Star_ 样式，然后单击**Add this style**。
1. The style will automatically be added to your Mapbox account.
1. 该样式将自动添加到您的Mapbox帐户。

When the style is added to your account, it will appear on the Styles page in Mapbox Studio. From here, you can find the [style URL](/help/glossary/style-url) for the style. You'll use the style URL to add this style to your map:
将样式添加到您的帐户后，它将显示在Mapbox Studio的“样式”页面上。从这里，您可以找到样式的样式URL。您将使用 [style URL](/help/glossary/style-url) 将此样式添加到地图中：

1. Find the style on your [Styles page](https://www.mapbox.com/studio/styles).
1. 在 [Styles page](https://www.mapbox.com/studio/styles)页面上找到样式。
1. Click on the **Menu {{<Icon name='menu' inline={true} />}}** next to the style name.
1. 单击样式名称旁边的 **Menu {{<Icon name='menu' inline={true} />}}**。
1. Find the _Style URL_. You can use the {{<Icon name='clipboard' inline={true} />}} clipboard icon to copy the style URL, which you'll use in the _Initialize a map with data_ section.
1. 找到 _Style URL_ 。您可以使用剪贴板图标复制style URL，您将在 _Initialize a map with data_ 选项中使用。

### Upload data
### 上传数据

In this guide, you'll use a [vector tileset](/help/glossary/tileset) to display data in your application. You can create a vector tileset by uploading the CSV you downloaded earlier to Mapbox Studio:
在本指南中，您将使用 [vector tileset](/help/glossary/tileset) 在应用程序中显示数据。您可以通过将之前下载的 CSV 上传到 Mapbox Studio 来创建vector tileset：

1. Visit the [Tilesets page](https://www.mapbox.com/studio/tilesets) in Mapbox Studio.
1. 访问Mapbox Studio中的 [Tilesets page](https://www.mapbox.com/studio/tilesets)页面。
1. Click **New tileset**.
1. 点击 **New tileset**
1. Select the CSV you downloaded at the beginning of this tutorial and click **Confirm**.
1. 下载在本教程开头的 CSV 文件，然后单击 **Confirm**。
1. A popover will appear in the bottom right showing the progress of your upload.
1. 右下方会显示一个弹出窗口，显示上传的进度。
1. Once the upload has _Succeeded_, the tileset will be ready to use! Click on the name of the tileset in the popover, which will open the tileset information page.
1. 上传显示 _Succeeded_ 后，tileset即可使用。通过单击popover中tileset的名称来打开tileset信息页面。
1. Take note of the *tileset ID* on the right side of the tileset information page. You will use the ID to add this tileset to your map in the next step.
1. 记下tileset信息页面右侧的tileset ID。在下一步中，通过使用ID将此tileset添加到地图中。

### Initialize a map with data
### 使用数据初始化地图

In a text editor, create a new `index.html` file and use the following code to initialize your map. You will need to:
在文本编辑器中，创建一个新的 `index.html` 文件并使用以下代码初始化您的地图。你需要：

1. Make sure `mapboxgl.accessToken` is set equal to one of your [access tokens](/help/glossary/access-token/).
1. 确保将 `mapboxgl.accessToken` 设置为等于您的一个[access tokens](/help/glossary/access-token/)。
1. Replace `your-style-url-here` with your [style URL](/help/glossary/style-url/).
1. 将your-style-url-here替换为您的[style URL](/help/glossary/style-url/)。
1. Replace `your-tileset-id-here` with the [tileset ID](/help/glossary/tileset-id) for the tileset you created.
1. 将 `your-tileset-id-here` 替换为您创建的tileset的[tileset ID](/help/glossary/tileset-id)。
1. Replace `your-source-layer-here` with the name of the [source layer](/help/glossary/source-layer/) in your tileset.
1. 将您的 `your-source-layer-here` 替换为tileset中 [source layer](/help/glossary/source-layer/)的名称。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8' />
    <title></title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.tiles.mapbox.com/mapbox-gl-js/{{constants.VERSION_MAPBOXGLJS}}/mapbox-gl.js'></script>
    <link href='https://api.tiles.mapbox.com/mapbox-gl-js/{{constants.VERSION_MAPBOXGLJS}}/mapbox-gl.css' rel='stylesheet' />
    <style>
      body {
        margin: 0;
        padding: 0;
      }

      #map {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 100%;
      }
    </style>
  </head>
  <body>

  <div id='map'></div>
  <script>
  mapboxgl.accessToken = '{{ <UserAccessToken /> }}';
  var map = new mapboxgl.Map({
    container: 'map', // container id
    style: 'your-style-url-here', // stylesheet location
    center: [-93.261, 44.971], // starting position [lng, lat]
    zoom: 10 // starting zoom
  });

  map.on('load', function() {
    map.addLayer({
      id: 'historical-places',
      type: 'circle',
      source: {
        type: 'vector',
        url: 'mapbox://your-tileset-id-here'
      },
      'source-layer': 'your-source-layer-here',
    });
  });

  </script>

  </body>
</html>
```

Run your application in a browser, and you will see a light colored map centered on Minneapolis with black dots scattered across the city.
在浏览器中运行您的应用程序，您将看到以明尼阿波利斯为中心的浅色地图，整个城市地图上方散布着黑色圆点。

{{
  <DemoIframe src="/help/demos/intro-to-expressions/step-one.html" />
}}

## Write a property expression
## 编写属性表达式

Next, you'll write an expression to style the radius of each circle based on the age of each historic landmark. In this data file, provided by the City of Minneapolis's open data portal, the age of the historic landmark is not provided, but the year of construction is provided. You can use arithmetic to calculate the age based on the current year and style the `circle-radius` paint property based on the age.
接下来，您将根据每个历史地标的存在时间编写一个表达式来设置每个圆的半径。数据由明尼阿波利斯市的开放数据门户提供，未提供历史地标的年龄，但提供了建造年份。您可以使用算术根据当前年份计算历史地标的存在时间，并根据存在时间设置圆半径绘制属性的样式。

### Calculate the age of each landmark
### 计算每个地标的存在时间

Start by calculating the age of the landmark. Here's the expression you'll use:
首先计算地标的存在时间。可以使用一下表达式计算：

```
['-', 2017, ['number', ['get', 'Constructi'], 2017]]
```

Use a `'-'` expression with two arguments. The first argument is a number (the current year, `2017`). The second argument is another expression (a `'number'` expression) with two arguments. This expression will convert the first argument to a number. If the first argument doesn't have a value, then the second argument, the current year `2017`, will be used.
使用带有两个参数的 `'-'` 表达式。第一个参数是一个数字（当前年份，2017年）。第二个参数是另一个带有两个参数的表达式（一个 `'number'` 表达式）。此表达式将第一个参数转换为数字。如果第一个参数没有值，那么将使用第二个参数，即当前年份2017。

The first argument for the `'number'` expression is yet another expression &mdash; this time a `'get'` expression that retrieves the object property value of its only argument, `'Constructi'`. (`'Constructi'` is the `'Construction_Date'` from the original CSV file, but the name of the field was shortened in the upload process.) The value that is retrieved using `'get'` is turned into a `'number'`.
 `'number'` 表达式的第一个参数是另一个表达式 - 这次是一个 `'get'` 表达式，它检索其唯一参数`'Constructi'` 的对象属性值。 （`'Constructi'` 是原始CSV文件中的 `'Construction_Date'` ，但在上传过程中缩短了字段的名称。）使用 `'get'` 检索的值变为 `'number'`。

Ultimately, this expression results in the age of the landmark, essentially `2017 - year of construction`.
最终，表达式输出地标的存在时间，结果呈现为 2017年 - 建造年份。

### Specify the circle radius
### 指定圆半径

Use this expression inside the existing `addLayer` function in your code:
在代码中的现有addLayer函数中使用此表达式：

```js
map.on('load', function() {
  map.addLayer({
    id: 'historical-places',
    type: 'circle',
    source: {
      type: 'vector',
      url: 'mapbox://your-tileset-id-here'
    },
    'source-layer': 'your-source-layer-here',
    paint: {
      'circle-radius': ['-', 2017, ['number', ['get', 'Constructi'], 2017]],
      'circle-opacity': 0.8,
      'circle-color': 'rgb(171, 72, 33)'
    }
  });
});
```

<!-- copyeditor ignore built-->
Refresh your browser and you'll see that the circle radius has changed dramatically. With the current expression, the radius of a circle representing a landmark that was built in 1950 would be 67 pixels. Next, you'll adjust the radius to be more appropriate in this context.
刷新浏览器，您会发现圆半径会发生变化。使用当前表达式，表示在1950年建立的地标的圆的半径将是67像素。下一步，您将在下文的说明中调整以使其更合适的半径。

{{
  <DemoIframe src="/help/demos/intro-to-expressions/step-two.html" />
}}

### Adjust the circle radius
### 调整圆半径

Wrap the expression in one other expression to make the size of the circles look better in this context. In this case, you'll divide the age of the landmark by `10`.
将表达式换行到另一个表达式中，以使圆圈的大小看起来更清晰合适。您可以将地标的存在时间数字除以 `10`。

```js
map.on('load', function() {
  map.addLayer({
    id: 'historical-places',
    type: 'circle',
    source: {
      type: 'vector',
      url: 'mapbox://your-tileset-id-here'
    },
    'source-layer': 'your-source-layer-here',
    paint: {
      'circle-radius': [
        '/',
        ['-', 2017, ['number', ['get', 'Constructi'], 2017]],
        10
      ],
      'circle-opacity': 0.8,
      'circle-color': 'rgb(171, 72, 33)'
    }
  });
});
```

Refresh your browser and you'll see an updated map.
刷新浏览器，您将看到更新后的地图。

{{
  <DemoIframe src="/help/demos/intro-to-expressions/step-three.html" />
}}

## Add a zoom expression
## 添加缩放表达式

The circles are still overlapping quite a bit at the starting zoom level, `10.5`, but they look good at higher zoom levels. You can use a zoom expression to address this issue. A **zoom expression** is any expression defined using `['zoom']`. Such expressions allow the appearance of a layer to change with the map’s zoom level. Zoom expressions can be used to create the illusion of depth and control data density.
在开始缩放级别`10.5`时，圆圈仍然重叠面积较多，但它们在更高的缩放级别上看起来很好。您可以使用 zoom expression来解决此问题。 **zoom expression** 是使用`['zoom']`定义的任何表达式。此类表达式允许图层的外观随地图的缩放级别而变化。 zoom expression可用于创建深度和控制数据密度的错觉。

Use an [`'interpolate'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-interpolate) expression. An `'interpolate'` expression produces continuous, smooth results by interpolating between pairs of input and output values ("stops"). In this case, use `['linear']` to interpolate linearly between a pair of stops slightly less than and slightly greater than the input. Then, specify that the radius should be the `age of the landmark / 30` at zoom level `10` and `age of the landmark / 10` at zoom level `13`.
使用 [`'interpolate'`](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions-interpolate) 表达式。`'interpolate'` 表达式通过在输入和输出值对之间进行插值（“stops”）来产生连续，平滑的结果。在这种情况下，使用`['linear']`在略微小于和略大于输入的一对参数之间线性插值。然后，指定半径应该是在缩放级别10处的地标/ 30的年龄和在缩放级别13处的地标/ 10的地标的存在时间。

```js
map.on('load', function() {
  map.addLayer({
    id: 'historical-places',
    type: 'circle',
    source: {
      type: 'vector',
      url: 'mapbox://your-tileset-id-here'
    },
    'source-layer': 'your-source-layer-here',
    paint: {
      'circle-radius': [
        'interpolate', ['linear'], ['zoom'],
        10, ['/', ['-', 2017, ['number', ['get', 'Constructi'], 2017]], 30],
        13, ['/', ['-', 2017, ['number', ['get', 'Constructi'], 2017]], 10],
      ],
      'circle-opacity': 0.8,
      'circle-color': 'rgb(171, 72, 33)'
    }
  });
});
```

{{
  <Note imageComponent={<BookImage />}>
    <p>If you've worked with <a href='https://www.mapbox.com/mapbox-gl-js/style-spec/#types-function'>property <em>functions</em></a> before, notice that <code>interpolate</code> expressions allow you to achieve the same effect as stop functions.</p>
  </Note>
}}
{{
  <注意 imageComponent={<BookImage />}>
    <p>如果您之前使用过 <a href='https://www.mapbox.com/mapbox-gl-js/style-spec/#types-function'>property <em>functions</em></a> ，请注意插值表达式允许您实现与停止函数相同的效果。</p>
  </Note>
}}

Refresh your browser and zoom into the map to see the resulting effect.
刷新浏览器和放大地图，您将看到处理后的地图结果。

{{<img src='/help/img/gl-js/expressions-step-four.gif' alt='animated screenshot zooming in and out and panning around a map with custom data styled using expressions' className='mx-auto block' />}}

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8' />
    <title>Minneapolis Landmarks</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.tiles.mapbox.com/mapbox-gl-js/{{constants.VERSION_MAPBOXGLJS}}/mapbox-gl.js'></script>
    <link href='https://api.tiles.mapbox.com/mapbox-gl-js/{{constants.VERSION_MAPBOXGLJS}}/mapbox-gl.css' rel='stylesheet' />
    <style>
      body {
        margin: 0;
        padding: 0;
      }

      #map {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 100%;
      }
    </style>
  </head>
  <body>

  <div id='map'></div>
  <script>
    mapboxgl.accessToken = '{{ <UserAccessToken /> }}';
    var map = new mapboxgl.Map({
      container: 'map', // container id
      style: 'your-style-url-here', // stylesheet location
      center: [-93.261, 44.971], // starting position [lng, lat]
      zoom: 10.5 // starting zoom
    });

    map.on('load', function() {
      map.addLayer({
        id: 'historical-places',
        type: 'circle',
        source: {
          type: 'vector',
          url: 'mapbox://your-tileset-id-here'
        },
        'source-layer': 'your-source-layer-here',
        paint: {
          'circle-radius': [
            'interpolate', ['linear'], ['zoom'],
            10, ['/', ['-', 2017, ['number', ['get', 'Constructi'], 2017]], 30],
            13, ['/', ['-', 2017, ['number', ['get', 'Constructi'], 2017]], 10],
          ],
          'circle-opacity': 0.8,
          'circle-color': 'rgb(171, 72, 33)'
        }
      });
    });
  </script>
  </body>
</html>
```

## Final product
## 恭喜

You've styled custom data using expressions in Mapbox GL JS!
您已经使用Mapbox GL JS中的表达式设置了自定义数据样式。

{{
  <DemoIframe src="/help/demos/intro-to-expressions/step-four.html" />
}}

## Next steps
## 下一步

There are many other ways you can use expressions in Mapbox GL JS. For more information:
在Mapbox GL JS中还有许多其他方法可以使用表达式。可以阅读以下内容获得更多信息：

- Read about expressions in the [Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions).
- 阅读 [Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions)中的表达式。
- Explore other [Mapbox GL JS examples](https://www.mapbox.com/mapbox-gl-js/example/simple-map/) that use expressions:
- 尝试使用表达式的其他 [Mapbox GL JS examples](https://www.mapbox.com/mapbox-gl-js/example/simple-map/) that use expressions:
  - [Create and style clusters](https://www.mapbox.com/mapbox-gl-js/example/cluster/)
  - 创建和设置群集样式 [Create and style clusters](https://www.mapbox.com/mapbox-gl-js/example/cluster/)
  - [Create a hover effect](https://www.mapbox.com/mapbox-gl-js/example/hover-styles/)
  - 创建悬停效果 [Create a hover effect](https://www.mapbox.com/mapbox-gl-js/example/hover-styles/)
  - [Create a time slider](https://www.mapbox.com/mapbox-gl-js/example/timeline-animation/)
  - 创建时间滑块 [Create a time slider](https://www.mapbox.com/mapbox-gl-js/example/timeline-animation/)
  - [Filter features within map view](https://www.mapbox.com/mapbox-gl-js/example/filter-features-within-map-view/)
  - 过滤地图视图中的要素 [Filter features within map view](https://www.mapbox.com/mapbox-gl-js/example/filter-features-within-map-view/)
  - [Update a choropleth layer by zoom level](https://www.mapbox.com/mapbox-gl-js/example/updating-choropleth/)
  - 通过缩放级别更新等值区图层 [Update a choropleth layer by zoom level](https://www.mapbox.com/mapbox-gl-js/example/updating-choropleth/)
