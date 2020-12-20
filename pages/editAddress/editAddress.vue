<template>
	<view class="content">
		<view class="bb15">

		</view>
		<view class="list-box">
			<view class="row">
				<text class="tit">联系人</text>
				<input class="input" type="text" v-model="addressData.user_name" placeholder="请填写收货人姓名" placeholder-class="placeholder" />
			</view>
			<view class="row">
				<text class="tit">手机号</text>
				<input class="input" type="number" v-model="addressData.user_phone" placeholder="请填写收货手机号码" placeholder-class="placeholder" />
			</view>
			<view class="row">
				<text class="tit">收货地址</text>
				<text @click="chooseLocation" class="input">
					{{addressData.user_address}}
				</text>
				<uni-icons class="icon" style="border-radius: 100%;" type="arrowright" color="#7c7c7c"></uni-icons>
			</view>
			<view class="row">
				<text class="tit">门牌号</text>
				<input class="input" type="text" v-model="addressData.user_room" placeholder="例: B座6楼602室" placeholder-class="placeholder" />
			</view>

		</view>
			<button class="add-btn" type="default" @click="confirm">保存</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				addressData: {
					user_name: '',
					user_phone: '',
					user_address: '在地图选择',
					user_room: '',
					default: false
				}
			}
		},
		methods: {
			//地图选择地址
			chooseLocation() {
				uni.chooseLocation({
					success: (data) => {
						this.addressData.user_address = data.name;
					}
				})
			},

			//提交
			confirm() {
				let data = this.addressData;
				if (!data.user_name) {
					this.$api.msg('请填写收货人姓名');
					return;
				}
				if (!/(^1[3|4|5|7|8][0-9]{9}$)/.test(data.user_phone)) {
					this.$api.msg('请输入正确的手机号码');
					return;
				}
				if (!data.user_address) {
					this.$api.msg('请在地图选择所在位置');
					return;
				}
				if (!data.user_room) {
					this.$api.msg('请填写门牌号信息');
					return;
				}
				
				//needApi:需要修改和新建地址的api

				//this.$api.prePage()获取上一页实例，可直接调用上页所有数据和方法，在App.vue定义
				// this.$api.prePage().refreshList(data, this.manageType);
				// this.$api.msg(`地址${this.manageType=='edit' ? '修改': '添加'}成功`);
				// setTimeout(()=>{
				// 	uni.navigateBack()
				// }, 800)
			},
		},
		onShow(option) {
			let editType=uni.getStorageSync('editAddressType')
			let title = '新增收货地址';
			if (editType === 'edit') {
				title = '编辑收货地址'
			}
			this.manageType = option.type;
			uni.setNavigationBarTitle({
				title
			})
		},
	}
</script>

<style lang="scss">
	page{
		background-color: #F7F7F7;
	}
	.bb15 {
		padding-top: 10px;
	}

	.list-box {
		display: flex;
		flex-direction: column;
		background-color: #fff;
		border-radius: 10px;
		overflow: hidden;
		margin: 0 10px;
		padding-left: 15px;
		padding-right: 15px;
	}

	.row {
		display: flex;
		flex-direction: row;
		align-items: center;
		height: 110upx;
		background: #fff;
		border-bottom: #dadada solid 1rpx;

		.tit {
			color: #2f2f2f;
			width: 100px;
			font-size: 30upx;
		}

		.input {
			flex: 1;
			font-size: 30upx;
			
		}

		.icon-locationfill {
			font-size: 36upx;
		}
	}

	.add-btn {
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 20px;
		font-size: 20px;
		font-weight: 500;
		background-color: #343434;
		color: #FFFFFF;
		width: 90%;
		height:55px;
	}
</style>
