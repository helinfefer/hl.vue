<!--要创建一个可复用的地图组件，应该专注于将地图的初始化和核心功能封装在内，同时提供接口（如props和events）来允许外部传入定制化的数据和配置。以下是一个简化的、可复用的地图组件示例，您可以根据具体的地图库和项目需求进一步调整：  -->
<template >
  <div class="main-container">
    <div ref="map-root" class="map-container"></div>
  </div>

</template>

<script>
// 引入编辑子组件
import 'ol/ol.css'
// import * as echarts from 'echarts'
import Map from 'ol/Map.js';
import View from 'ol/View.js';
import OSM from 'ol/source/OSM';
import TileLayer from 'ol/layer/Tile'
import LayerSwitcher from 'ol-layerswitcher';
// 引用popup
import Popup from 'ol-popup'; 
// import { fromLonLat } from 'ol/proj';  // 导入 fromLonLat
// import Overlay from 'ol/Overlay';

export default {
  name:'ReusableMapComponent',
  data() {
    return {
      map: null, // 用于存储地图实例，
      echartOverlay: null,
      echartsInstance: null,
  }
},
props: {
  // 定义您希望从外部传入的配置项，例如：
  initialCenter: {
    type: Array,
    default:()=> [114.1692, 30.494],
  },
  initialZoom: { 
    type: Number,
    default: 10
  },
  layerGroup:{
    type:Object,
    default:null,
  },
},

methods: {
  initializeMap() {
    // 创建地图视图实例
    const view = new View({
      center: this.initialCenter,
      zoom: this.initialZoom,
      projection: 'EPSG:4326', // 设置视图的投影坐标系
    });

    // 设置基础的底图
    const baseLayer = new TileLayer({
      source: new OSM(),
      title:'OSM',
    });
    // 创建地图实例
    this.popup = new Popup();
    const layers = [baseLayer];
    if (this.layerGroup) {
      layers.push(this.layerGroup); // 仅在 layerGroup 有效时才添加
    }

    this.map = new Map({
      target: this.$refs['map-root'],
      layers: layers,
      view: view, // 设置地图的视图,
    });

    // 在地图初始化之后添加 LayerSwitcher 控件
    this.layerSwitcher = new LayerSwitcher({
      reverse: true,
      groupSelectStyle: 'group'
    });
    this.map.addControl(this.layerSwitcher);
    this.map.addOverlay(this.popup); 


    // 设置点击事件监听器来显示 Popup
    this.map.on('singleclick', (evt) => {
      const feature = this.map.forEachFeatureAtPixel(evt.pixel, function(feature) {
        return feature;
      });

      if (feature) {
        const properties = feature.getProperties();
        // 创建要显示的内容，例如显示属性信息
        let content = '<h3>要素属性</h3>';
        for (let key in properties) {
          if (key !== 'geometry') { // 排除几何信息
            content += `<p><strong>${key}:</strong> ${properties[key]}</p>`;
          }
        }
        this.popup.show(evt.coordinate, content);
      }
    });

  },
    
  
},
mounted() {
    this.initializeMap();
},

}
</script>

<style scoped>
.main-container{
  height: 600px;
  width: 100%;
  position: relative;
}

.map-container {
  height: 600px;
  width: 100%;
  position: relative;
}

</style>

