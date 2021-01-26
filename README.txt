*基本概念：
·GIS：地理信息系统（Geographic Information System或 Geo－Information system，GIS）有时又称为“地学信息系统”。它是一种特定的十分重要的空间信息系统。它是在计算机硬、软件系统支持下，对整个或部分地球表层（包括大气层）空间中的有关地理分布数据进行采集、储存、管理、运算、分析、显示和描述的技术系统。
·WebGIS：网络地理信息系统。是指工作在Web网上的GIS,是传统的GIS在网络上的延伸和发展，具有传统GIS的特点，可以实现空间数据的检索、查询、制图输出、编辑等GIS基本功能，同时也是Internet 上地理信息发布、共享和交流协作的基础。
·GeoJSON：是一种对各种地理数据结构进行编码的格式，基于Javascript对象表示法的地理空间信息数据交换格式。GeoJSON对象可以表示几何、特征或者特征集合。GeoJSON支持下面几何类型：点、线、面、多点、多线、多面和几何集合。GeoJSON里的特征包含一个几何对象和其他属性，特征集合表示一系列特征。 


*坐标系
·地理坐标系（geographic coordinate systems）：地理坐标系一般是指由经度、纬度和高度组成的坐标系，能够标示地球上的任何一个位置 
·投影坐标系（projected coordinate systems）：地理坐标系是三维的，我们要在地图或者屏幕上显示就需要转化为二维，这被称为投影（Map projection）。显而易见的是，从三维到二维的转化，必然会导致变形和失真，失真是不可避免的，但是不同投影下会有不同的失真，这让我们可以有得选择


*GIS地理信息系统的坐标系统:
·WGS-84：GPS坐标系，是国际标准，GPS坐标（Google Earth使用、或者GPS模块）
·GCJ-02：火星坐标系，国测局02年发布的坐标体系，中国坐标偏移标准，Google Map、高德、腾讯使用
·BD-09：百度坐标系，百度自研，百度地图使用。


*projection：投影
·投影：地球大家都知道不是正圆形的球体，他是一个不规则的椭圆体，所以如果我们把把展开在桌面上，发现地图都会和实际有出入。所以人们发明了各种各样的方式来缩小失真的程度，这种方式就是投影
·projection为source、view提供投影选项
·EPSG：
  -- 什么是EPSG：
     WGS84是目前最流行的地理坐标系统。在国际上，每个坐标系统都会被分配一个EPSG代码，EPSG:4326就是WGS84的代码。GPS是基于WGS84的，所以通常我们得到的坐标数据都是WGS84的。一般我们在存储数据时，仍然按WGS84存储。
  -- 常见的EPSG：
     -- EPSG:4326 (WGS84)：一种国际上采用的地心坐标系。坐标原点为地球质心。GPS是基于WGS84的，所以通常我们得到的坐标数据都是WGS84的。
     -- EPSG:3857 (Pseudo-Mercator)：伪墨卡托投影，也被称为球体墨卡托，Web Mercator。它是基于墨卡托投影的，把 WGS84坐标系投影到正方形。我们前面已经知道WGS84是基于椭球体的，但是伪墨卡托投影把坐标投影到球体上，这导致两极的失真变大，但是却更容易计算。这也许是为什么被称为”伪“墨卡托吧。另外，伪墨卡托投影还切掉了南北85.051129°纬度以上的地区，以保证整个投影是正方形的。因为墨卡托投影等正形性的特点，在不同层级的图层上物体的形状保持不变，一个正方形可以不断被划分为更多更小的正方形以显示更清晰的细节。很明显，伪墨卡托坐标系是非常显示数据，但是不适合存储数据的，通常我们使用WGS84存储数据，使用伪墨卡托显示数据。
     -- EPSG:900931：在地图上用单位“米”当作坐标的X/Y对待
·实例化：var projection=new OpenLayers.Projection('EPSG:4326',{})
·方法：
  -- getCode：获取投影code
  -- getUnits：获取投影的单位，度or 米，这取决于我们使用的投影
  -- addTransform（from,to,method）：坐标系转换
  -- transform(point,source,destination)：坐标系转换
·坐标转换
  -- 使用ol的transform方法：
      //创建两个投影对象
      var proj_4326 = new OpenLayers.Projection('EPSG:4326');
      var proj_900913 = new OpenLayers.Projection('EPSG:900913'); 
      //再创建一个点
      var Point_to_transform=new OpenLayers.LonLat(-79,42);
      //开始转换
      Point_to_transform.transform(proj_4326,proj_900913);
      //打印结果
      console.log(point_to_transform);
  -- 使用地理坐标转换工具：gcsTransUtil.js


*分辨率：
·分辨率的简单定义是屏幕上的1像素表示的现实世界的地面实际距离：
  以Google在线地图为例，层级0使用了一个瓦片，层级1使用了4个瓦片。通过计算可以知道层级0的整个地球图像（瓦片）为256*256像素大小，层级1整个地球图像为512*512像素大小。而层级0和层级1表示的地球范围都是一样的（经度[-180°, 180°]，纬度[-90°, 90°]）。在层级0的时候，一个像素在水平方向就表示360°/256 = 1.40625°这么长的经度范围（以度为单位），在竖直方向就表示180°/256 = 0.703125°这么长的纬度范围（以度为单位）。而这两个数字就是分辨率了，即一个像素所表示的现实世界的范围是多少，这个范围可能是度（在地理坐标系统中），可能是米（在投影坐标系统中），或者其他单位，根据具体的情况而定。
·因为不同的在线瓦片地图可能采用不一样的分辨率，比如百度在线瓦片地图。所以在使用在线瓦片地图或者自己制作的瓦片地图时，都需要知道使用的分辨率是多少。如若不然，可能会出现位置偏移。
·瓦片数量计算：
  层级0的瓦片数是 1 = 2^​0 ​​∗ 2^​0
  层级1的瓦片数是 4 = 2^1 * 2^1
  层级2的瓦片数是 16 = 2^2 * 2^2
  层级3的瓦片数是 64 = 2^3 * 2^3
  层级z的瓦片数是 2^z * 2^z
·OpenLayers默认采用的分辨率和Google在线瓦片地图一样，他们都是采用的ESPG:3857
·ol获取默认分辨率：map.getView().getResolution()
·根据EPSG获取各层级分辨率：
    var projectionExtent = ol.proj.get("EPSG:3857").getExtent();  //获取EPSG:3857投影的范围
    var tilePixel = 256;  //单张瓦片的像素，通常都为256，也有少部分为512，根据不同的源而定
    var maxResolution = ol.extent.getWidth(projectionExtent) / tilePixel;  //最大分辨率 = 0层投影范围的宽度 / 单张瓦片的像素
    var MaxZoom = 21;  //地图可放大层级数，要求服务端有相应的层级的瓦片、矢量地图包
    var resolutions = new Array(MaxZoom);  //分辨率数组
    var matrixIds = new Array(MaxZoom);  //层级数组
    for (var i = 0; i < MaxZoom; i++) {
      resolutions[i] = maxResolution / Math.pow(2, i);
      matrixIds[i] = i;
    }
·文档：
  -- https://zhuanlan.zhihu.com/p/72316089
  -- http://www.godeyes.cn/html/2011/05/20/google_earth_11460.html



*Openlayers的DOM结构
  OpenLayers会在我们自定义的div元素中创建一个ol-viewport的类，地图中的所有内容都放置在ol-viewport中呈现。在ol-viewport容器中分别创建了如下3个元素层，分别渲染呈现地图容器中的内容：
·地图渲染层（class="ol-unselectable"）—— 这是一个canvas元素，地图基于canvas 2D方式渲染
·内容叠加层（class="ol-overlaycontainer"）—— 用于放置叠置层（ol.Overlay）内容，如在地图上添加弹窗、图片等等
·地图控件层（class="ol-overlaycontainer-stopevent"） —— 用于放置控件，默认情况下会放置ol.control.Zoom（用于控制地图放大、缩小）、ol.control.Rotate（用于控制地图旋转）、ol.control.Attribution（用于控制地图右下角标记）这三个控件。


*Map：
·Map的属性：
  -- target
  -- view
  -- layers
  -- interaction
  -- controls
  -- overlay
  -- event
·Map的方法：
  -- 获取容器：var targets=map.getTarget()
  -- 设置容器：map.setTarget()
  -- 获取view：var view=map.getView()
  -- 设置view：map.setView(view)
  -- 获取图层：var layers=map.getLayers();
  -- 添加图层：map.addLayer(layer);
  -- 移除图层：map.removeLayer((layer)
  -- 添加交互：map.addInteraction(interaction)
  -- 移除交互：map.removeInteraction(interaction)
  -- 获取控件：var controls=map.getControls()
  -- 添加控件：map.addControl(new ol.control.FullScreen())
  -- 移除控件：map.removeControl(fullControl)
  -- 获取叠置层：var overlays=map.getOverlays()
  -- 添加叠置层：map.addOverlay(overlay)
  -- 移除叠置层：map.removeOverlay(overlay)
  -- 添加事件：map.on("click", function (evt) { });
  -- 移除事件：map.un("click", function (evt) { });
  -- 获取触发事件的坐标：var coordinate=map.getEventCoordinate()
  -- 获取触发事的像素位置：var pixel=map.getEventPixel()
·Map demo：
  var map = new ol.Map({
        target: 'map',	//指定了地图要显示在id为map的div中；
        layers: [
          new ol.layer.Tile({	//定义了一个图层,数据来源是OpenStreetMap提供的切片数据；
            source: new ol.source.OSM()
          })
        ],
        view: new ol.View({	//定义了地图的中心位置，范围和层级。
          center: ol.proj.fromLonLat([103.73,36.03]),	//是将一个经纬度坐标转换成当前地图投影的坐标；
          zoom: 8,
          minZoom:5,
          maxZoom:12
      }),
      controls: ol.control.defaults().extend([    // 往地图增加组件
                new ol.control.ZoomSlider(),	//滑块组件
                new ol.control.FullScreen()   	//全屏组件
      ])
  });


*View
·设置中心点：view.setCenter([x,y])
·获取层级：view.getZoom()
·设置层级：view.setZoom(5)
·获取projection：view.getProjection()


*Layer：
·4种基本的图层类型：
  -- Tile
  -- Image
  -- Vector
  -- VectorTile
·地图数据根据数据源（Source）可分为Image、Tile、Vector三大类型的数据源类，对应设置到地图图层（Layer）的Image、Tile、Vector三大类的图层中。其中，矢量图层Vector通过样式（Style）来设置矢量要素渲染的方式和外观。
·创建图层：
  var layer = new ol.layer.Vector({
    source: new ol.source.Vector()
  });
·设置值：layer.set("layername", "points");
·添加feature：layer.getSource().addFeature(feature);
·设置要在地图上其他图层之上呈现的图层：layer.setMap(map)
·设置图层的显示与隐藏：layer.setVisible(false)
·ol.layer.Group：是一个用于将多个图层存储在一起的集合类
·热力图：
    var heatmapLayer = new ol.layer.Heatmap({
      gradient: ['#8ea2fb', '#0ff', '#0f0', '#ff0', '#f00'],
      source: new ol.source.Vector({
        features: [new ol.Feature({geometry: new ol.geom.Point([lontitude,latitude])]
      })
    });
    heatmapLayer.set("layername", "heatmap");
    map.addLayer(heatmapLayer);

*interaction（交互）：
·交互需要指定source参数来指定可以对哪些地图源进行交互，例如产生图形、图形编辑、数据捕获等
·创建绘图交互：
  var draw = new ol.interaction.Draw({
        source: vectorSource,
        type: type,
        geometryFunction: geometryFunction
  });
·创建移动交互：
  var modify = new ol.interaction.Modify({
    source: vectorSource
  });
·文档：https://blog.csdn.net/weitaming1/article/details/87805287


*controls
·常用控件：
  -- 归属控件（Attribution） —— 用于展示地图资源的版权或者归属，它会默认加入到地图中。
  -- 全屏控件（FullScreen） —— 控制地图全屏展示
  -- 坐标拾取控件（MousePosition） —— 用于在地图上拾取坐标
  -- 鹰眼控件（OverviewMap） —— 生成地图的一个概览图
  -- 旋转控件（Rotate） —— 用于鼠标拖拽旋转地图，它会默认加入到地图中。
  -- 比例尺控件（ScaleLine） —— 用于生成地图比例尺
  -- 滑块缩放控件（ZoomSlider） —— 以滑块的形式缩放地图
  -- 缩放至特定位置控件（ZoomToExtent） —— 用于将地图视图缩放至特定位置
  -- 普通缩放控件（Zoom） —— 普通缩放控件，它会默认加入到地图中。
·控件示例：
  var controls=ol.control.defaults().extend([    
    new ol.control.ZoomSlider(),
    new ol.control.ZoomToExtent({  
       extent: [12667718, 2562800,12718359, 2597725]
    }),
    new ol.control.FullScreen(),
    new ol.control.MousePosition(),
    new ol.control.OverviewMap({        // 实例化一个OverviewMap类的对象，并加入到地图中
      collapsed: false
    }),
    new ol.control.ScaleLine()
  ])
·自定义控件：
  -- 图层切换控件：https://zhuanlan.zhihu.com/p/71700141


*Overlay（叠置层）:
·feature为vector图层的数据源提供数据，而叠置层的原理是将DOM元素动态地移动并覆盖到地图中的指定位置，它不属于图层
·创建叠置层：
   var overlay = new ol.Overlay({
      position: viennaPos,
      positioning: 'center-center',
      element: document.getElementById('marker'),
      stopEvent: false
   })
·设置位置：
  overlay.setPosition(coordinate)
·清空叠置层：
  map.getOverlays().clear()



*Event：
·openlayers中实现注册和解除监听函数功能的类是 ol.Observable，因此任何继承于 ol.Observable 的类的对象实例都具有注册或者解除注册监听器的能力，并且可以分发事件。
·ol.Observable的方法：
  -- changed()，增加事件对象的版本号（每次事件对象变化，都会增加版本号），并分发 change 事件；
  -- dispatchEvent(event)，分发事件，并调用所有的监听该类型事件的监听器；
  -- getRevision()，获取事件对象的版本号；
  -- on(type, listener, opt_this)，监听 type 类型的事件，若事件触发，则调用 listener 函数；
  -- once(type, listener, opt_this)，监听 type 类型的事件（仅一次），若事件触发，则调用 listener 函数，并移除该监听器；
  -- un(type, listener, opt_this)，移除监听 type 类型的监听函数 listener；
  -- unByKey(key)，移除对应 key 的监听器，key 一般是由 on() 或者 once() 返回的。
·常见事件type：https://blog.csdn.net/freeland1/article/details/50127427
  -- click：无论单击，还是双击事件，必定先触发click事件。只不过双击触发两次，单击触发一次
  -- singleclick：单击事件
  -- dblclick：双击事件
  -- pointerdrag：鼠标拖拽事件。是鼠标按下pan地图时触发，在拖拽地图时，pointermove事件也会触发。部分功能类似一般地图的’moving’事件。
  -- pointermove：鼠标移动事件。pointermove很像是mousemove事件，但他们之间并不是一回事。在移动触屏设备中，pointermove是当地图panned之后触发，性质相当于web页面上moveend之后的意思
  -- change:
     -- change:layerGroup :地图图层增删时触发。
     -- change:size :地图窗口发生变化就会触发，与我们常用的窗口resize接近。
     -- change:target :地图绑定的div发生更改时触发，如map.setTartget方法就会触发该事件。
     -- change:view :地图view对象发生变化触发。
  -- select：鼠标经过事件
  -- mouseenter：鼠标进入事件
  -- moveend：地图被移动后触发
  -- movestart：地图开始移动时触发
  -- postrender：地图被渲染后触发
  -- drawend：划线完成
  -- postcompose：地图渲染中。
  -- precompose：准备渲染，未渲染。
  -- postrender：渲染全部结束。
·propertychange：当属性更改时触发
·contextmenu：鼠标右键事件名



*source：
·为layer的子类、interaction的子类提供数据源
·数据源：
  -- Tile类为瓦片抽象基类，其子类作为各类瓦片数据的数据源。
  -- Vector类为矢量数据源基类，为矢量图层提供具体的数据来源，包括直接组织或读取的矢量数据（Features）、远程数据源的矢量数据（即通过url设置数据源路径）等。若是url设置的矢量数据源，则通过解析器Format（即ol.format.Feature的子类）来解析各类矢量数据，如XML、Text、JSON、GML、KML、GPS、WFS、WKT、GeoJSON等地图数据。
  -- Image类为单一图像基类，其子类为画布（canvas）元素、服务器图片、单个静态图片、WMS单一图像等的数据源。它与Tile类的区别在于，Image类对应的是一整张大图片，而不像瓦片那样很多张小图片，从而无需切片，也可以加载一些地图，适用于一些小场景地图。
·清除数据：source.clear()
·openLayers现在支持的Source：
  ol.source.Source
    ├─ol.source.Tile
    │     ├─ol.source.TileDebug：可用于调试瓦片坐标系
    │     ├─ol.source.TileUTFGrid
    │     └─ol.source.UrlTile
    │           ├─ol.source.VectorTile
    │           └─ol.source.TileImage
    │                 ├─ol.source.TileArcGISRest
    │                 ├─ol.source.BingMaps
    │                 ├─ol.source.TileJSON
    │                 ├─ol.source.TileWMS
    │                 ├─ol.source.WMTS
    │                 ├─ol.source.Zoomify
    │                 └─ol.source.XYZ
    │                       ├─ol.source.CartoDB
    │                       ├─ol.source.OSM
    │                       └─ol.source.Stamen
    ├─ol.source.Image
    │     ├─ol.source.ImageArcGISRest
    │     ├─ol.source.ImageCanvas
    │     ├─ol.source.ImageMapGuide
    │     │─ol.source.ImageStatic
    │     ├─ol.source.ImageWMS
    │     └─ol.source.Raster
    └─ol.source.Vector
          └─ol.source.Cluster
·从复杂度来分析，Image类和Vector类都不复杂，其数据格式和来源方式都简单。而Tile类则不一样，由于一些历史问题，多个服务提供商，多种标准等诸多原因，导致要支持世界上大多数的瓦片数据，就需要针对这些差异（这些差异主要是瓦片坐标系不同、分辨率不同等）提供不同的Tile数据源支持
·最为复杂的ol.source.Tile，其叶子节点类有很多，大致可以分为几类：
  -- 在线服务的Source：包括ol.source.BingMaps（微软提供的Bing在线地图数据）、ol.source.Stamen（Stamen提供的在线地图数据）。没有自己的地图服务器的情况下，可直接使用它们，加载地图底图。
  -- 支持协议标准的Source：包括ol.source.TileArcGISRest、ol.source.TileWMS、ol.source.WMTS、ol.source.UTFGrid、ol.source.TileJSON。如果要使用它们，首先你得先学习对应的协议，之后必须找到支持这些协议的服务器来提供数据源，这些服务器可以是底图服务提供商提供的，也可以是自己搭建的服务器，关键是得支持这些协议。
  -- ol.source.XYZ：这个需要单独提一下，因为是可以直接使用的，而且现在很多地图服务（在线的，或者自己搭建的服务器）都支持xyz方式的请求。国内在线的地图服务，高德、天地图等，都可以通过这种方式加载，本地离线瓦片地图也可以，用途广泛，且简单易学。
·瓦片地图加载实例：
  if (isGoogleMap) {// 谷歌地图
    var projectionExtent = ol.proj.get("EPSG:3857").getExtent();
    var maxResolution = ol.extent.getWidth(projectionExtent) / 256;
    var MaxZoom = 21;  
    var resolutions = new Array(MaxZoom);  
    var matrixIds = new Array(MaxZoom);  
    for (var i = 0; i < MaxZoom; i++) {
      resolutions[i] = maxResolution / Math.pow(2, i);
      matrixIds[i] = i;
    }
    // 创建底图瓦片图层
    mapWidget.baseLayer = new ol.layer.Tile({
      useInterimTilesOnError: false,
      source: new ol.source.XYZ({
        projection: "EPSG:3857",
        tileGrid: new ol.tilegrid.WMTS({
          origin: ol.extent.getTopLeft(projectionExtent),
          resolutions: resolutions,
          matrixIds: matrixIds
        }),
        tileUrlFunction: function (tileCoord) {
          var z = tileCoord[0];
          var x = tileCoord[1];
          var y = -tileCoord[2] - 1;
          var url = Vue.prototype.U2VD_URL.MAP_URL + '/static_map_common/' + z + '/' + x + '/' + y + '.png';
          return url;
        },
        tileLoadFunction: function (imageTile, src) {
          var formData = new FormData();
          formData.append("imageStr", src);
          let httpDefault = {
            method: 'POST',
            url: U2VD_API.LAWCASE_MAPCROSSDOMAIN,
            data: formData,
            responseType: 'blob'
          }
          http(httpDefault)
            .then(res => {
              imageTile.getImage().src = URL.createObjectURL(res);
            })
            .catch(err => { })
        },
        wrapX: true,
      })
    });
    mapWidget.baseLayer.set("layername", "baseLayer");
    var point = LatLonTransform.gcj_encrypt(
      parseFloat(mapWidget.mapCenterPoint[1]),
      parseFloat(mapWidget.mapCenterPoint[0]));// 84坐标系转换成火星坐标系
    var center = ol.proj.transform([point.lon, point.lat], "EPSG:4326",
      "EPSG:3857");

    // 创建地图对象
    mapWidget.view = new ol.View({
      minZoom: 3,
      maxZoom: 20,
      zoom: 16,
      projection: "EPSG:3857",
      center: center
    });
    mapWidget.map = new ol.Map({
      controls: ol.control.defaults().extend([
        // new ol.control.FullScreen(),
        new ol.control.OverviewMap({}) // ,
        // new ol.control.ZoomSlider({})
      ]),
      target: mapId,
      view: mapWidget.view,
      logo: null
    });
    // 添加基础图层
    mapWidget.map.addLayer(mapWidget.baseLayer);
  } else {// PGIS 地图
    var url = mapWidget.titeServePath + '/Maps/default_new';
    var projectionExtent = ol.proj.get("EPSG:4326").getExtent();
    var maxResolution = ol.extent.getWidth(projectionExtent) / 256;
    var MaxZoom = 21;
    var resolutions = new Array(MaxZoom);
    var matrixIds = new Array(MaxZoom);
    for (var i = 0; i < MaxZoom; i++) {
      resolutions[i] = maxResolution / Math.pow(2, i);
      matrixIds[i] = i;
    }
    mapWidget.baseLayer = new ol.layer.Tile({
      source: new ol.source.WMTS({
        name: mapWidget.layerName,
        projection: "EPSG:4326",
        tileGrid: new ol.tilegrid.WMTS({
          origin: ol.extent.getTopLeft(projectionExtent),
          resolutions: resolutions,
          matrixIds: matrixIds
        }),
        url: url,
        format: 'image/png',
        layer: mapWidget.layerName
      })
    });
    mapWidget.baseLayer.set("layername", "baseLayer");
    //创建地图对象
    mapWidget.view = new ol.View({
      minZoom: 3,
      maxZoom: 20,
      zoom: 16,
      projection: "EPSG:4326",
      center: mapWidget.mapCenterPoint,
    });
    mapWidget.map = new ol.Map({
      controls: ol.control.defaults().extend([
        new ol.control.FullScreen()
      ]),
      target: mapId,
      view: mapWidget.view,
      logo: null
    });
    //添加基础图层
    mapWidget.map.addLayer(mapWidget.baseLayer);
  }
·clusterSource：
  -- Cluster概念聚合，在地图上查询结果通常以标记点的形式展现，但是如果标记点较多，不仅会大大增加客户端的渲染时间，让客户端变得很卡，而且会让人产生密集恐惧症，为了解决这一问题，我们需要一种手段能在用户有限的可视区域范围内，利用最小的区域展示出最全面的信息，而又不产生重叠覆盖。
  -- clusterSource为layer提供数据源
  -- demo:
      var clusterSource=new ol.source.Cluster({
        distance:35,  //表示多远距离进行聚合。distance为0时，不进行聚合
        projection:ol.proj.get('EPSG:4326'),
        source:new ol.source.Vector({features:new ol.Feature(...)})
      })
      var clustersLayer = new ol.layer.Vector({});
      clustersLayer.setSource(clusterSource)
      clustersLayer.set("layername", "clusters");
      map.addLayer(clustersLayer);
  -- 样式设置/范围计算：https://www.jianshu.com/p/330b45f1c9ac?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation
·《OpenLayers教程八：多源数据加载之数据组织》：https://zhuanlan.zhihu.com/p/71882117



*Feature：
·Feature（要素），即地图上的几何对象，包括点（Point），线（LineString）,多边形（Polygon），圆（Circle），通过ol.interaction.Drew绘制。
·Feature为vector图层的数据源提供数据
·Feature类是Vector类用来在地图上展示几何对象，是Vector图层类一个属性。
·Feature类有3个部分：
  -- Geometry对象:Geometry类是feature对象的基本组成部分，Vector类采用Geometry类来存储一个要素的几何信息
  -- attributes属性：attributes包含要素相关的数据
  -- style属性
·方法：
  -- destroy():销毁要素对象
  -- clone：复制要素对象 
  -- getVisibilrty():判断指定要素是否显示出来
  -- move():将要素对象移动到location，location一般是OpenLayers.LonLat对象
·创建：
  var feature = new ol.Feature(new ol.geom.Point([lon, lat]),attributes,style)
·设置样式：
  var style = new ol.style.Style({
    image: new ol.style.Icon(({
      anchorXUnits: 'fraction',
      anchorYUnits: 'pixels',
      src: '../image/hdCameraPoint.png'
    }))
  });
  feature.setStyle([style]);
·将feature添加到矢量图层中的数据源中
  vectorSource.addFeature(feature);
·设置值：
  feature.setProperties({})
  feature.set("sign", "车辆布控");
  feature.set("camera", camera);
·获取值：
  feature.getProperties({})
  feature.get("sign");



*动画：
·动画实现的3种方式：
  -- 在div中使用CSS3动画，以overlay的形式添加
  -- 使用openlayers3自带的postcompose事件。这个事件在地图渲染时都会触发，而我们只要改变地图上的某个feature或者layer或者其他任何东西，就会触发重新渲染。因此我们在postcompose中改变feature的样式，改变样式后重新触发postcompose事件，一只循环下去，形成动画效果。
  -- openlayers6采用postrender and vectorContext实现动画：https://openlayers.org/en/latest/examples/index.html?q=animation
  -- 使用js的requestAnimationFrame。requestAnimationFrame的方式与其他的js中方法相比的优势如下：1.经过浏览器优化，动画更流畅2.窗口没激活时，动画将停止，省计算资源3.更省电，尤其是对移动终端。



*文档：
·《官网》：https://openlayers.org/
·《OpenLayers汇总目录·不睡觉的怪叔叔》：https://blog.csdn.net/qq_35732147/article/details/81667929
·《瓦片地图原理》：https://segmentfault.com/a/1190000011276788#articleHeader1
·《openlayers基础系列》：https://blog.csdn.net/xtfge0915/article/details/82763727
·《openlayers基础系列·知乎》：https://zhuanlan.zhihu.com/c_1098918318844612608


*许可协议：
  本项目所有文档遵循CC-BY-SA 4.0协议（ https://creativecommons.org/licenses/by-sa/4.0/deed.zh ）。使用者可以对本创作进行转载、节选、混编、二次创作，可以将其运用于商业用途，唯须署名作者，并且采用本创作的内容必须同样采用本协议进行授权


