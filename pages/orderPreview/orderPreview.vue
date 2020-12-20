<template>
	<view>
		
		<view class="header">
			<view class="orderType">
				<view v-if="order.type" class="addressControl" @click="changeAddress">
					<view class="addressText">
						待获取店铺名称
					</view>
						<!-- <uni-icons class="icon" style="border-radius: 100%;" type="arrowright" color="#7c7c7c"></uni-icons> -->
						<!-- 有分店的话可以更改店铺 -->
				</view>
				
				<view v-if="!order.type" class="addressControl" @click="changeAddress">
					<view class="addressText">
						{{getAddressText}}
					</view>
						<uni-icons class="icon" style="border-radius: 100%;" type="arrowright" color="#7c7c7c"></uni-icons>
				</view>
				
				<view class="select">
					<view :class="[order.type == 1 ? 'on' : 'off']"  @click="changePeisongType">自取</view>
					<view :class="[order.type == 0 ? 'on' : 'off']"  @click="changePeisongType">外卖</view>
				</view>
				
			</view>
			
			<view class="nameAndPhone">
				{{getNameAndPhone}}
			</view>
			
		</view>
		
		
		<view class="orderDetail">
			<view class="itemList" v-for="(item,index) in order.orderItemList">
				<view class="item">
					<image :src="item.product_picture" mode="aspectFit"></image>
					<view class="message">
						<view class="name">
							{{item.product_name}}
						</view>
						<view class="describe">
							{{item.note}}
						</view>
					</view>
					
					<view class="priceAndNum">
						<view class="price">
							￥ {{item.product_actual_price}}
						</view>
						<view class="num">
							x {{item.count}}
						</view>
					</view>
					
				</view>
			</view>
			
			<view class="mark">
				<view class="text">
					备注
				</view>
					<input class="input" placeholder="可输入口味包装等要求" placeholder-class="input-holder"/>
			</view>
			
			<view class="totalText">
				共计{{getAllCount}}件商品，小计
				<view class="total-price">
					￥{{getOrderTotalPrice}}
				</view>
			</view>
			
		</view>
		
		
		<view class="bottom">
			<view class="total-price">
				合计
				<view class="price">￥{{getOrderTotalPrice}}</view>
			</view>
			<view class="BtnRight">
				<!-- TODO:结算订单函数 -->
				<view @click="settle" class="text">
					支付
				</view>
			</view>
		</view>
		
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				order:{},
			}
		},
		methods: {
			changePeisongType(){
				//修改订单派送类型
				this.order.type = this.order.type == 0 ? 1 : 0;
				if(this.order.type){
					this.addAddressInOrder()
				}else{
					this.order.consignee=''
					this.order.consignee_phone=''
					this.order.consignee_address=''
					this.order.consignee_room=''
				}
			},
			changeAddress(){
				//跳转到选择地址
				uni.setStorageSync('settle_order',this.order)
				uni.navigateTo({
					url:'../selectAddress/selectAddress'
				})
			},
			addAddressInOrder(address){
				if(address==null){
					address=uni.getStorageSync('defaultAddress')
					if(address==null) return
				}
				this.order.consignee=address.user_name
				this.order.consignee_phone=address.user_phone
				this.order.consignee_address=address.user_address
				this.order.consignee_room=address.user_room
			},
		},
		onShow() {
			this.order=uni.getStorageSync('settle_order')
			let selectedAddress=uni.getStorageSync('selectedAddress')
			if(selectedAddress!=null){
				this.addAddressInOrder(selectedAddress)
			}else{
				this.addAddressInOrder(uni.getStorageSync('defaultAddress'))
			}
		},
		computed: {
			// 获得购物车所有商品数量
			getAllCount() {
				let res=0
				for(let i=0;i<this.order.orderItemList.length;i++){
					res+=this.order.orderItemList[i].count
				}
				return res
			},
			getOrderTotalPrice(){
				return this.order.order_price
			},
			getAddressText(){
				if(this.order.consignee_address!=''){
					return this.order.consignee_address+this.order.consignee_room	
				}else{
					return '点击选择收货地址'
				}
			},
			getNameAndPhone(){
				if(this.order.type){
					return this.order.consignee+''+this.order.consignee_phone	
				}else{
					//TODO:需要获取用户信息
					return 'llk 18925542915'
				}
				
			}
		
		},
	}
</script>

<style lang="scss">
	
	page{
		background-color: #F7F7F7;
	}
	
	.header{
		padding-left: 20px;
		padding-right: 20px;
		padding-top: 25px;
		padding-bottom: 25px;
		display: flex;
		flex-direction: column;
		background-color: #FFFFFF;
		margin-bottom: 10px;
		
		
		.orderType{
			display: flex;
			flex-direction: row;
			justify-content: space-between;
			width: 100%;
			.addressControl{
				width: 70%;
				display: flex;
				flex-direction: row;
				justify-content: flex-start;
				align-items: center;
				.addressText{
					font-size: 20px;
					font-weight: 500;
					color: #000000;
					overflow: hidden;
					word-break: break-all;
					/* break-all(允许在单词内换行。) */
					text-overflow: ellipsis;
					/* 超出部分省略号 */
					display: -webkit-box;
					/** 对象作为伸缩盒子模型显示 **/
					-webkit-box-orient: vertical;
					/** 设置或检索伸缩盒对象的子元素的排列方式 **/
					-webkit-line-clamp: 1;
					/** 显示的行数 **/
				}
				
				.icon{
					width: 5%;
					display: flex;
					flex-direction: row;
					justify-content: center;
					align-items: center;
				}
			}
			
			.select {
				width: 30%;
				display: flex;
				align-items: center;
				font-size: 25rpx;
				width: 180rpx;
				height: 60rpx;
				border-radius: 32rpx;
				background-color: #f6f6f6;
			}
			
			.select .on {
				width: 90rpx;
				height: 60rpx;
				background-color: #343434;
				color: #ffffff;
				border-radius: 32rpx;
				text-align: center;
				line-height: 60rpx;
			}
			
			.select .off {
				width: 90rpx;
				height: 60rpx;
				color: #d2d2d2;
				border-radius: 25rpx;
				text-align: center;
				line-height: 60rpx;
			}
			
			
		}
	
	
		.nameAndPhone{
			color: #999999;
			font-size: 13px;
		}
	}
	
	
	.orderDetail{
		background-color: #FFFFFF;
		padding-left: 20px;
		padding-right: 20px;
		display: flex;
		flex-direction: column;
		
		
		.itemList{
			border-bottom: #f0f0f0 solid 1rpx;
			padding-bottom: 15px;
			
			.item{
				padding-top: 20px;
				padding-bottom: 20px;
				display: flex;
				flex-direction: row;
				flex-wrap: nowrap;
				justify-content: flex-start;
				align-items: flex-start;
				align-content: center;
				&>image{
					width: 20%;
					width: 120rpx;
					height: 120rpx;
					margin-right: 16rpx;
					margin-left: 2px;
				}
				.message{
					width: 70%;
					.name{
						color: #000000;
						font-size: 17px;
						font-weight: 500;
						margin-top: 10rpx;
						margin-bottom: 10rpx;
					}
					.describe{
						font-size: 24rpx;
						color: #999;
						// overflow: hidden;
						// word-break: break-all;
						// /* break-all(允许在单词内换行。) */
						// text-overflow: ellipsis;
						// /* 超出部分省略号 */
						// display: -webkit-box;
						// /** 对象作为伸缩盒子模型显示 **/
						// -webkit-box-orient: vertical;
						// /** 设置或检索伸缩盒对象的子元素的排列方式 **/
						// -webkit-line-clamp: 2;
						// /** 显示的行数 **/
					}
				}
				.priceAndNum{
					display: flex;
					flex-direction: column;
					justify-content: flex-start;
					align-items: flex-end;
					width: 20%;
					.price{
						font-size: 16px;
						font-weight: 400;
						color: #343434;
					}
					.num{
						margin-top: 7px;
						font-size: 13px;
						color: #999999;
					}
				}
				
			}
		}
		
		.mark{
			margin-top: 25px;
			margin-bottom: 25px;
			
			
			.text{color: #343434;margin-bottom: 10px;}
			.input{}
			.input-holder{
				font-weight: 500;
			}
		}
		
		.totalText{
			display: flex;
			flex-direction: row;
			justify-content: flex-end;
			align-items: center;
			font-size: 15px;
			color: #343434;
			margin-top: 25px;
			margin-bottom: 25px;
			
			.total-price{
				color: #363636;
				font-size: 20px;
				font-weight: 500;
				margin-left: 5px;
			}
			
		}
		
		
	}
	
	
	.bottom{
		margin-bottom: var(--window-bottom);
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		height: 54px;
		z-index: 99;
		display: flex;
		background-color: #F0F0F1;
		
		.total-price {
			display: flex;
			flex-direction: row;
			justify-content: flex-end;
			align-items: center;
			flex: 4;
			font-size: 15px;
			font-weight: 500;
				color: #363636;
		
		
			.price {
				color: #363636;
				font-size: 20px;
				font-weight: 500;
				margin-right: 26px;
				margin-left: 5px;
			}
		}
		
		.BtnRight {
			flex: 2;
			background-color: #DBA871;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 17px;
			font-weight: 500;
			color: #FFFFFF;
		
		
			.text {
				
			}
		
		}
	}

</style>
