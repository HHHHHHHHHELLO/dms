<template>
	<view>
		<view class="content">
			<view>
				<view style="font-size: 26px;font-weight: 550;">SoftAp 配网</view>
				<view style="font-size: 18px;font-weight: 550;margin-top: 8px;">选择路由器Wifi</view>
			</view>


		</view>
		<view style="padding: 6px 0 6px 20px; border-top: 1px lightgrey solid;border-bottom: 1px lightgrey solid;">
			<view style="display: flex;align-items: center;">
				<view style="color: #888888; font-size: 14px;margin-right: 10%;">
					WIFI
				</view>
				<view style="flex:1">
					<picker style="font-size: 14px;" mode="selector" :range="wifiList" :value="index"
						@change="bindPickerChange">
						<view style="color:lightgrey;" v-if="selectWifiStyle === '请点击选择Wifi'">请点击选择WIFI</view>
						<view v-else style="color:black">{{selectWifiStyle}}</view>
					</picker>
				</view>
				<view>
					<uni-icons  type="right" size="18" style="color: #999;margin-right:3px"></uni-icons>
				</view>
			</view>
		</view>
		<view style=" padding: 6px 0 6px 20px; border-bottom: 1px lightgrey solid;">
			<view style="display: flex;align-items: center;">
				<view style="color: #888888; font-size: 14px;margin-right: 10.5%;">
					密码
				</view>
				<view style="flex:1">
					<input placeholder="请输入WIFI密码" placeholder-style="font-size: 14px;color:lightgrey;" :type="inputType" />
				</view>
				<view>
					<uni-icons v-if="eyeVis" @click="showEye(true)" type="eye" size="18" style="color: #999;margin-right:3px"></uni-icons>
					<uni-icons v-else @click="showEye(false)" type="eye-slash" size="18" style="color: #999;margin-right:3px"></uni-icons>
				</view>
			</view>
		</view>
		<view class="content" style="margin-top: 85%;">
			<button type="primary" style="font-size: 16px;height: 40px;line-height: 40px;">一键配网</button>
		</view>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				wifiList: [],
				index: 0,
				eyeVis:true,
				inputType:"password"
			}
		},
		onLoad() {

		},
		methods: {
			showEye(isShow){
				if(isShow){
					this.eyeVis = false
					console.log("眼睛亮");
					this.inputType = ""
				}else{
					this.eyeVis = true
					this.inputType = "password"
				}
			},
			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为', e.detail.value)
				this.index = e.detail.value
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