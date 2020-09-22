<template>
	<view class="content">
		<!-- #ifdef APP-PLUS || H5 -->
		<view :prop="option1" :change:prop="echarts.updateEcharts1" :l="chart1" :change:l="echarts.updateL1" :p="preDataIndex" :change:p="echarts.updateP" id="echarts1" class="echarts"></view>
		<view :prop="option2" :change:prop="echarts.updateEcharts2" :l="chart2" :change:l="echarts.updateL2" id="echarts2" class="echarts"></view>
		<!-- #endif -->
		<!-- #ifndef APP-PLUS || H5 -->
		<view>非 APP、H5 环境不支持</view>
		<!-- #endif -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				chart1: "",
				chart2: "",
				option1: "",
				option2: "",
				name:"zhangsan",
				login: false,
				preDataIndex: 0
			}
		},
		watch:{
			name: function(n) {
				this.changeOption()
			}
		},
		onLoad() {
			this.option1 = {
					tooltip: {
					        trigger: 'item',
					        formatter: '{a} <br/>{b}: {c} ({d}%)'
					    },
					    series: [
					        {
					            name: '访问来源',
					            type: 'pie',
					            radius: ['50%', '70%'],
					            avoidLabelOverlap: false,
					            label: {
					                show: false,
					                position: 'center'
					            },
					            emphasis: {
					                label: {
					                    show: true,
					                    fontSize: '30',
					                    fontWeight: 'bold'
					                }
					            },
					            labelLine: {
					                show: false
					            },
					            data: [
					                {value: 335, name: '直接访问'},
					                {value: 310, name: '邮件营销'},
					                {value: 234, name: '联盟广告'},
					                {value: 135, name: '视频广告'},
					                {value: 1548, name: '搜索引擎'}
					            ]
					        }
					    ]
				}
		this.option2 = {
				tooltip: {
				        trigger: 'item',
				        formatter: '{a} <br/>{b}: {c} ({d}%)'
				    },
				    series: [
				        {
				            name: '访问来源',
				            type: 'pie',
				            radius: ['50%', '70%'],
				            avoidLabelOverlap: false,
				            label: {
				                show: false,
				                position: 'center'
				            },
				            emphasis: {
				                label: {
				                    show: true,
				                    fontSize: '30',
				                    fontWeight: 'bold'
				                }
				            },
				            labelLine: {
				                show: false
				            },
				            data: [
				                {value: 335, name: '直接访问'},
				                {value: 310, name: '邮件营销'},
				                {value: 234, name: '联盟广告'},
				                {value: 135, name: '视频广告'},
				                {value: 1548, name: '搜索引擎'}
				            ]
				        }
				    ]
			}
		},
		methods: {
			changeOption() {
				const data = this.option2.series[0].data
				// 随机更新示例数据
				data.forEach((item, index) => {
					data.splice(index, 1, Math.random() * 40)
				})
			},
			changePre(o) {
				console.log(o)
				this.name = o.index
			},
			onViewClick(options) {
				console.log("options")
			}
		}
	}
</script>

<script module="echarts" lang="renderjs">
	let myChart;
	export default {
		mounted() {
			
			if (typeof window.echarts === 'function') {
				this.initEcharts()
			} else {
				// 动态引入较大类库避免影响页面展示
				const script = document.createElement('script')
				// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
				script.src = 'static/echarts.js'
				script.onload = this.initEcharts.bind(this)
				document.head.appendChild(script)
			}
		},
		methods: {
			initEcharts() {
				// UniViewJSBridge.publishHandler('onWxsInvokeCallMethod', {
				
				//         cid: this._$id,  
				
				//         method:'onViewClick',  
				
				//         args:{}  
				
				//       })
				let that = this
				myChart = echarts.init(document.getElementById('echarts1'))
				// 观测更新的数据在 view 层可以直接访问到
				myChart.setOption(this.option1)
				// this.login = "lo"
				// console.log(this.chart, 123)
				window.$chart1 = myChart
				
				window.$chart1.on("click", (e) => {
					UniViewJSBridge.publishHandler('onWxsInvokeCallMethod', {
					
					        cid: this._$id,  
					
					        method:'changePre',  
					
					        args:{index: e.dataIndex}  
					
					      })
					this.preDataIndex = e.dataIndex
					// console.log(this.preDataIndex, 111)
					this.option1.series[0].data.map((item, index) => {
						if (index === this.preDataIndex) {
							// console.log(index, this.preDataIndex)
							window.$chart1.dispatchAction({
								type: "highlight",
								seriesIndex: 0,
								dataIndex: this.preDataIndex
							});
							// this.renderChart2();
						} else {
							window.$chart1.dispatchAction({
								type: "downplay",
								seriesIndex: 0,
								dataIndex: index
							});
						}
					});
				})
				
				myChart = echarts.init(document.getElementById('echarts2'))
				// 观测更新的数据在 view 层可以直接访问到
				myChart.setOption(this.option2)
				// this.login = "lo"
				// console.log(this.chart, 123)
				window.$chart2 = myChart
			},
			updateEcharts1(newValue, oldValue, ownerInstance, instance) {
				// console.log(this)
				// 监听 service 层数据变更
				window.$chart1.setOption(newValue)
			},
			updateEcharts2(newValue, oldValue, ownerInstance, instance) {
				// console.log(this)
				// 监听 service 层数据变更
				window.$chart2.setOption(newValue)
			},
			onClick(event, ownerInstance) {
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.echarts {
		margin-top: 100px;
		width: 100%;
		height: 300px;
	}
</style>
