<template>
	<view>
		<view class="content">
			<view>
				<view style="font-size: 26px;font-weight: 550;">SoftAp 配网</view>
				<view style="font-size: 18px;font-weight: 550;margin-top: 8px;">选择路由器Wifi</view>
			</view>


		</view>
		<view style="margin-top: 20px; padding: 15px 0 15px 20px; border-top: 1px lightgrey solid;border-bottom: 1px lightgrey solid;">
			<view style="display: flex;align-items: center;">
				<view style="color: #888888; font-size: 14px;margin-right: 10%;">
					WIFI
				</view>
				<view style="flex:1">
					<picker @click="getWifi()" style="font-size: 14px;" mode="selector" :range="wifiList" :value="index"
						@change="bindPickerChange">
						<view style="color:lightgrey;" v-if="selectWifiStyle === '请点击选择Wifi'">请点击选择WIFI</view>
						<view v-else style="color:black">{{selectWifiStyle}}</view>
					</picker>
				</view>
				<view>
					<uni-icons type="right" size="22" style="color: #999;margin-right:6px"></uni-icons>
				</view>
			</view>
		</view>
		<view style=" padding: 15px 0 15px 20px; border-bottom: 1px lightgrey solid;">
			<view style="display: flex;align-items: center;">
				<view style="color: #888888; font-size: 14px;margin-right: 10.5%;">
					密码
				</view>
				<view style="flex:1">
					<input
						v-model="password"
						placeholder="请输入WIFI密码" placeholder-style="font-size: 14px;color:lightgrey;"
						:type="inputType" />
				</view>
				<view>
					<uni-icons v-if="eyeVis" @tap="showEye(true)" type="eye" size="22"
						style="color: #999;margin-right:6px"></uni-icons>
					<uni-icons v-else @tap="showEye(false)" type="eye-slash" size="22"
						style="color: #999;margin-right:6px"></uni-icons>
				</view>
			</view>
		</view>
		<view class="content" style="margin-top: 60vh;">
			<button @click="connent()" type="primary" style="font-size: 16px;height: 40px;line-height: 40px;">一键配网</button>
		</view>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				wifiList: [],
				index: 0,
				eyeVis: true,
				inputType: "password",
				password:""
			}
		},
		onLoad() {

		},
		methods: {
			showEye(isShow) {
				if (isShow) {
					this.eyeVis = false
					console.log("眼睛亮");
					this.inputType = ""
				} else {
					this.eyeVis = true
					this.inputType = "password"
				}
			},
			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为', e.detail.value)
				this.index = e.detail.value
			},
			getWifi() {
				wx.authorize({
					scope: 'scope.userLocation',
					success() {
						wx.getLocation()
						wx.startWifi({
							complete(res) {
								console.log("startWifi", res)
								wx.getWifiList().then(res => {
									console.log("getWifiList SUCC");
								}).catch(err => {
									console.log("getWifiList ERR");
								})
							}
						})


					},
					fail(err) {
						console.log("authorize", err);
					}
				})
				wx.onGetWifiList((wifiList) => {
					
					let info = wx.getSystemInfoSync()
					if(info.platform == 'ios'){
						if (wifiList.wifiList.length) {
					    wx.setWifiList({
					      wifiList: [{
					        SSID: wifiList.wifiList[0].SSID,
					        BSSID: wifiList.wifiList[0].BSSID,
					        password: this.password
					      }]
					    })
					  } else {
					    wx.setWifiList({
					      wifiList: []
					    })
					  }
					}
					
					
					
					console.log("wifiList", wifiList);
					this.wifiList = wifiList.wifiList.map(item=>item.SSID)
					console.log("this.wifiList", this.wifiList);
				})
			},
			connent(){
				try{
						wx.connectWifi({
						SSID: this.wifiList[this.index],
						forceNewApi: true,
						password: this.password,
						
					}).then(res=>{
						console.log(res);
						this.connectUDP().then(res=>{
							this.sendDataUDP()
						})
					}).catch(err=>{
						console.log(err);
					})
				}catch(e){
					//TODO handle the exception
					console.log(e,"e");
				}
				
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
					console.log(res4,"res4")
				})
				this.udp.onMessage(res3 => {
					console.log('3监听中...')
					console.log(res3,"res3");
				})
			},
			sendDataUDP() {
				const msgData = {
					"cmdType": 1,
					"ssid": this.wifiList[this.index],
					"password": this.password
				}
				this.udp.send({
					address: '192.168.4.1',
					port: 8266,
					message: JSON.stringify(msgData)
				})
			},
		},
		computed: {
			selectWifiStyle() {
				if (this.wifiList.length === 0) {
					return "请点击选择Wifi"
				} else {
					return this.wifiList[this.index]
				}
			}
		}
	}
</script>

<style lang="scss">
	.content {
		padding: 20px;
	}
</style>