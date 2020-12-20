<template>
	<view>
		<view class="selectText">
			选择收货地址
		</view>
		<view class="addressItem" v-for="(item,index) in addressList">
			<view class="left" @click="selectAddress(item)">
				<view class="address">
					{{item.user_address+' '}}{{item.user_room}}
				</view>
				<view class="nameAndPhone">
					{{item.user_name+' '}}{{item.user_phone}}
				</view>
			</view>
			<view class="right">
				<uni-icons size='20' class="icon" type="compose" color="#bdbdbd" @click="editAddress(item)"></uni-icons>
			</view>
		</view>
		<view class="addressItem">
			<view class="left">
				<view class="address">
					深圳职业技术学院留仙洞校区 校门口
				</view>
				<view class="nameAndPhone">
					李良堃 18925542915
				</view>
			</view>
			<view class="right">
				<uni-icons size='20' class="icon" type="compose" color="#bdbdbd"></uni-icons>
			</view>
		</view>


		<view class="bottom" @click="createAddress">
			<!-- <view class="BtnRight"> -->
			<button class="btn" type="default">+ 添加地址</button>
			<!-- </view> -->
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				addressList:[],
			}
		},
		methods: {
			selectAddress(item){
				//选择地址，set进缓存，然后back
				uni.setStorageSync('selectedAddress',item)
				uni.navigateBack({
					animationType:'slide-out-right',
					animationDuration: 800
				})
			},
			editAddress(item){
				//修改地址，点击的地址set进缓存，然后跳转
				uni.setStorageSync('editAddressType','edit')
				uni.setStorageSync('needEditAddress',item)
				uni.navigateTo({
					url:'../editAddress/editAddress'
				})
			},
			createAddress(){
				//新建地址，然后跳转
				uni.setStorageSync('editAddressType','create')
				uni.navigateTo({
					url:'../editAddress/editAddress'
				})
			}

		},
		onShow() {
			//needApi:获取用户地址列表
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #F8F8F8;
	}

	.selectText {
		margin-top: 10px;
		margin-bottom: 10px;
		margin-left: 20px;
		font-size: 13px;
		color: #8a8a8a;
	}

	.addressItem {
		padding: 20px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
		background-color: #FFFFFF;
		border-bottom: #f0f0f0 solid 1rpx;


		.left {
			width: 85%;
			display: flex;
			flex-direction: column;

			.address {
				margin-bottom: 8px;
			}

			.nameAndPhone {
				font-size: 14px;
				color: #999999;
				font-size: 600;
			}
		}

		.right {
			display: flex;
			flex-direction: row;
			justify-content: center;
			align-items: center;
			width: 15%;
		}

	}


	.bottom {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		height: 54px;
		z-index: 99;
		display: flex;
		margin-bottom: 20px;



		.btn {
			display: flex;
			align-items: center;
			justify-content: center;
			width: 40%;
			color: #DBA871;
			font-size: 17px;
			background-color: #FFFFFF;
			border-bottom: #f0f0f0 solid 1rpx;
		}


	}
</style>
