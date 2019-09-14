---
title: Build indoor floorplans with Mapbox
使用 Mapbox 建立室内楼层平面图
description: Upload a floorplan to create an indoor map.
thumbnail: indoorFloorplans
level: 3
topics:
- uploads
- map design
- third party integration
language:
- Varies
prereq: Familiarity with GIS.
contentType: tutorial
---

You can use Mapbox to put a floor plan or non-geographic data on our maps. There are a couple of routes you can take. The key is to give your data some geographic reference for it to work in a mapping application. You can do this in a couple of ways, depending on your data's format:
您可以通过使用 Mapbox 在地图中放入楼层平面图或者无地理信息的数据。有多种方法可以实现这类需求。关键点在于，当您的数据应用于地图应用时，需要给其一些地理位置参考。地理位置参考根据不同的数据类型分为以下几条：

- If you want to overlay an image of your plan, you need to [georeference](/help/tutorials/georeferencing-imagery/) it.
- 如果您需要在平面中叠加一张图片，您需要将图片地理配准（https://docs.mapbox.com/help/tutorials/georeferencing-imagery/）。
- If you want to use a tool like [Mapbox.js](https://www.mapbox.com/mapbox.js/) to display your data as a tiled image, read [this guide](http://www.macwright.org/2012/08/13/images-as-maps.html) to learn about processing non-map images as maps.
- 如果您想要使用类似 Mapbox.js (https://www.mapbox.com/mapbox.js/) 的工具作为平铺图像来展示您的数据，请阅读这条将非地图图像转为地图图像的说明（http://www.macwright.org/2012/08/13/images-as-maps.html）。
- If you know the bounding coordinates of your image, you can use the `imageOverlay` constructor in Leaflet shown in [this example](https://www.mapbox.com/mapbox.js/example/v1.0.0/imageoverlay-georeferenced/).
- 在已知图片边界信息的情况下，可以参看案例（https://www.mapbox.com/mapbox.js/example/v1.0.0/imageoverlay-georeferenced/）使用 Leaflet 中的 imageOverlay constructor 来完成。
- If you have data in a geographic vector format, such as GeoJSON, KML, or Shapefile, you can upload it to [Mapbox Studio](https://github.com/mapbox/mapbox-studio-classic) as a [tileset](/help/glossary/tileset/), add it to a [style](https://www.mapbox.com/studio-manual/reference/styles/), and embed it in your website using Mapbox GL JS.
- 如果您的数据为地理矢量文件，比如GeoJSON、KML、Shapefile，您可以将这类数据作为 tileset(https://docs.mapbox.com/help/glossary/tileset/) 上传到 Mapbox Studio(https://github.com/mapbox/mapbox-studio-classic)，添加一个 style(https://www.mapbox.com/studio-manual/reference/styles/)，使用 Mapbox GL JS 将其嵌入您的网站。
- If you are using Mapbox GL JS, you can also create 3D floorplans like in [this example](https://www.mapbox.com/mapbox-gl-js/example/3d-extrusion-floorplan/) using `fill-extrusions`.
- 如果您正在使用 Mapbox GL JS，可以参看案例（https://www.mapbox.com/mapbox-gl-js/example/3d-extrusion-floorplan/）使用挤出（fill-extrusions）创建一个三维的楼层平面。
