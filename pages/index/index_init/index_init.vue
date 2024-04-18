<template>
	<view class="content">
		<view>
			<button @click="getWifiList()">获取Wifi列表</button>
			<button @click="startWifi()">startWifi</button>
			<button @click="connectWifi()">connectWifi</button>
			<button @click="connectUDP()">connectUDP</button>
			<button @click="sendDataUDP()">sendDataUDP</button>
		</view>
		<view>
			{{msg}}
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				msg: ""
			}
		},
		onLoad() {

		},
		methods: {
			getWifiList() {
				wx.authorize({
					scope: 'scope.userLocation',
					success() {
						wx.getLocation()

						wx.getWifiList().then(res => {
							console.log("getWifiList SUCC", res);
						}).catch(err => {
							console.log("getWifiList ERR", err);
						})

					},
					fail(err) {
						console.log("authorize", err);
					}
				})
				wx.onGetWifiList((wifiList) => {
					console.log("wifiList", wifiList);
				})

				wx.getSetting({
					success(res) {
						if (!res.authSetting['scope.userLocation']) {

						}
					}
				})


			},
			startWifi() {
				console.log('开始wifi接口');
				wx.startWifi({
					complete(res) {
						console.log(res)
						this.msg = res
					}
				})
			},
			connectWifi() {
				// 连接wifi
				wx.connectWifi({
					SSID: 'TP-LINK_4704',
					forceNewApi: true,
					password: 'ruanjianjishu',
					complete(res2) {
						console.log(res2)
						this.msg = res2
					}
				})
			},
			connectUDP() {
				// 建立udp连接
				this.udp = wx.createUDPSocket()
				if (this.udp === null) {
					console.log('暂不支持')
					return;
				}
				this.udp.connect({
					address: '192.168.4.1',
					port: 8266
				})
				this.udp.bind()
				this.udp.onListening(function(res4) {
					console.log('4监听中...')
					console.log(res4)
					this.msg = res4
				})
				this.udp.onMessage(res3 => {
					console.log('3监听中...')
					console.log(res3);
					this.msg = res3
				})
			},
			sendDataUDP() {
				const msgData = {
					"cmdType": 1,
					"ssid": "TP-LINK_4704",
					"password": "ruanjianjishu"
				}
				this.udp.send({
					address: '192.168.4.1',
					port: 8266,
					message: JSON.stringify(msgData)
				})
			},
		}
	}
</script>

<style>
	.content {
		display: flex;
	}
</style>