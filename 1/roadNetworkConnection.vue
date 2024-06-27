<template>
	<main id="roadNetworkConnection">
		<input  type='file' @change="handleFileChange" style="margin-bottom: 20px;margin-top:20px;font-size: 20px">
		<button @click="handleRun" :disabled="!file" style="font-size: 20px">运行</button>
		<!-- 使用 key 属性强制重新渲染 -->
		<ReusableMapComponent
		:key="mapKey"                             
		:initialCenter="mapCenter"
		:initialZoom="mapZoom"
		:layerGroup="layerGroup"
		></ReusableMapComponent>
	</main>


</template>

<script>
import ReusableMapComponent from '../components/ReusableMapComponent.vue'

import { mapState} from 'vuex';
// import axios from 'axios';
export default {
	name: 'roadNetworkConnection',
	components:{ReusableMapComponent,
	},
	data(){
		return{
        mapCenter:[114.1692, 30.494],
        mapZoom:15,
		mapKey: 0,  // 用于强制重新渲染子组件的 key
		file:null,  // 用于存储上传的文件
		};
		
	},
	methods:{
		handleFileChange(event) {
			this.file = event.target.files[0]; // 获取文件
		},
		handleRun() {
			if (this.file) {
				// console.log(file);
				const reader = new FileReader();
				reader.onload = (e) => {
				const fileContent = e.target.result;
				const geojsonData = JSON.parse(fileContent); // 将文件内容解析为JSON
				console.log("GeoJSON数据:", geojsonData); // 在这里可以进一步处理GeoJSON数据
				this.$store.dispatch('roadNetworkConnection/loadMapData',geojsonData);
				this.file = null;  //用于清空上传文件框
				}
				reader.readAsText(this.file); // 读取文件内容为文本
			}
		}
  },
    watch: {
    layerGroup() {
      // 每当 layerGroup 变化时，更新 mapKey 来重新渲染 ReusableMapComponent
      this.mapKey += 1;
    },
  },





				
			// this.$store.dispatch('roadNetworkConnection/loadMapData',file)  
			// }
			// else{
			// 	console.log("文件为空");
			// }

			// this.loadMapData(file); // 调用Vuex action处理文件
		// },
		// ...mapActions('roadNetworkConnection', ['loadMapData']),
		// loadMapData(){
		// 		// const file = event.target.files[0]; // 获取文件
        //     this.$store.dispatch('roadNetworkConnection/loadMapData',file)  
        // },

	// },

	computed: {
		// layerGroup(){
		// 	return this.$store.state.roadNetworkConnection.layerGroup;
		// }

		// 数组写法 ,借助map写模块化前面加上模块名称
        ...mapState('roadNetworkConnection',[
            'layerGroup',
        ]),
    },

	created(){
		// 不借助mapaction写，模块化用"/"完成
		this.$store.dispatch('roadNetworkConnection/loadMapData')  
	},
	mounted(){
		// console.log(this.store);
	},
}

</script>

<style scoped>
</style>