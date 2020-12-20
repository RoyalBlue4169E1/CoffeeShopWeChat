<template>
	<view>

		<uni-popup class="product-card" ref="productPopup" type="center" :animation="true">
			<view class="card-content">

				<scroll-view class="product-form" scroll-y="true">
					<view class="ctn">
						<view class="leftBox">
							<view class="ring" style="background-color:#b5b5b5">
								<uni-icons type="closeempty" color="#FFFFFF" @click="closeProductPopup"></uni-icons>

							</view>
						</view>
					</view>

					<view class="product-image">
						<image mode="heightFix" :src="selectProduct.picture"></image>
					</view>

					<view class="formContent">
						<view class="name">
							{{selectProduct.name}}
						</view>

						<uni-tag class="sort" text="含乳制品、茶" :circle="true" size="small"></uni-tag>
						<uni-tag v-if="selectProduct.heated" class="sort" text="可做热饮" :circle="true" size="small"></uni-tag>
						
						<view class="describe">
							<view class="title">产品描述</view>
							<view class="content">{{selectProduct.description}}</view>
						</view>

						<view class="size">
							<view class="title">规格</view>
							<view class="content">
								<view class="content filter-body-section-body style-flex style-flex-wrap">
									<axb-check-box ref="radio3" :list="size" @change="selectSize"></axb-check-box>
								</view>
							</view>
						</view>

						<view class="sweet">
							<view class="title">甜度</view>
							<view class="content filter-body-section-body style-flex style-flex-wrap">
								<axb-check-box ref="radio3" :list="sweet" @change="selectSweet"></axb-check-box>
							</view>
						</view>

						<!-- TODO：可不可做热饮的时候要更改标签，搞成disable -->
						<view class="size">
							<view class="title">温度</view>
							<view class="content">
								<view class="content filter-body-section-body style-flex style-flex-wrap">
									<axb-check-box ref="radio3" :list="ice" @change="selectIce"></axb-check-box>
								</view>
							</view>
						</view>

						<view class="Topping">
							<view class="title">加料</view>
							<view>
								<view class="filter-body-section-body style-flex style-flex-wrap">
									<!-- TODO:加料算价钱还没有做 -->
									<axb-check-box ref="radio1" :multi="true" :list="topping" @change="selectTopping"></axb-check-box>
								</view>
							</view>
						</view>
						<view class="fillHeight"></view>
					</view>




				</scroll-view>
				<view class="formBottom" v-model="formData">
					<view class="textAndcontrol">
						<view class="bottomText">
							<view class="price">￥{{formData.product_actual_price}}</view>
							<view class="mark" v-model="formData">{{formData.size}}{{formData.size==''?'':','}}{{formData.ice}}{{formData.ice==''?'':','}}{{formData.sweet}}{{formData.sweet==''?'':','}}{{formData.toppingTotal}}</view>
						</view>
						<view class="control">
							<view class="des">
								<image style="width: 100%;height: 100%;" src="../../static/shopcart/des.png" mode="aspectFill" @click="desFormDataCount"></image>
							</view>

							<text>{{formData.count}}</text>
							<view class="add">
								<uni-icons @click="addFormDataCount" style="background-color: #DBA871;border-radius: 100%;height: 100%;width: 100%;display: flex;align-items: center;justify-content: center;"
								 type="plusempty" color="#FFFFFF"></uni-icons>
							</view>
						</view>

					</view>


					<button class="formButton" @click="submitOrderItem">加入购物袋</button>
				</view>
			</view>
		</uni-popup>


		<!-- 头部店铺 -->
		<view class="box">
			<view class="shop-name">
				<view class="name">星巴克咖啡店</view>
				<!-- TODO:增加查看店铺信息或更换店铺页面的跳转按钮 -->
				<view class="select">
					<view :class="[order.type == 1 ? 'on' : 'off']"  @click="changePeisongType">自取</view>
					<view :class="[order.type == 0 ? 'on' : 'off']"  @click="changePeisongType">外卖</view>
				</view>
			</view>
			<view class="distance-bar">
				<view class="distance">距离您88888千米</view>
				<navigator url="/pages/shop/detail" hover-class="none">
					<view class="shop-message">
						<view class="message">查看门店信息</view>
					</view>
				</navigator>
			</view>
		</view>

		<!-- TODO:可以加个纵向滚动公告 -->

		<!-- 滚动区域 -->
		<view class="scroll-panel" id="scroll-panel">
			<view class="list-box">
				<view class="left">
					<scroll-view scroll-y="true" :style="{ height: scrollHeight + 'px' }" :scroll-into-view="leftIntoView">
						<view class="item" v-for="(item, index) in menu" :key="index" :class="{ active: index == leftIndex }" :id="'left-' + index"
						 :data-index="index" @tap="leftTap">
							<!-- TODO：icon修改成指定类url -->
							<!-- <image class="icon" mode="aspectFit" :src="item.icon" @error="imageError"></image> -->
							<image class="icon" mode="aspectFit" src="https://s3.ax1x.com/2020/12/12/rZBzdK.png" @error="imageError"></image>
							<view class="name">{{item.name}}</view>
						</view>
						<!-- 为了不被tarBar或购物车遮盖，进行的填充 -->
						<view style="height: 120px;"></view>
					</scroll-view>
				</view>
				<view class="main">
					<scroll-view scroll-y="true" :style="{ height: scrollHeight + 'px' }" @scroll="mainScroll" :scroll-into-view="scrollInto"
					 scroll-with-animation="true">
						<view class="item main-item" v-for="(item, index) in menu" :key="index" :id="'item-' + index">
							<view class="title">
								<view>{{ item.name }}</view>
							</view>
							<view class="goods" v-for="(item2, index2) in item.categoryItems" :key="index2" @click="openProductPopup(item2.product)">
								<image :src="item.product.picture" mode="aspectFit"></image>
								<view>
									<view class="title">{{item2.product.name}}</view>
									<view class="describe">{{item2.product.description}}</view>
									<!-- TODO:加个销量显示 -->
									<view class="bottom">
										<!-- //TODO:现在显示的是中杯原价，如果有折扣的话，做划掉原价的ui -->
										<view class="price">￥{{item2.product.mediumOriginalPrice}}</view>
										<button hover-class='none' class="button" size="mini">选规格</button>

									</view>

								</view>
							</view>
							<!-- 为了不被tarBar或购物车遮盖，进行的填充 -->
							<view style="height: 50px;"></view>
						</view>
						<view class="fill-last" :style="{ height: fillHeight + 'px' }"></view>
					</scroll-view>
				</view>
			</view>
		</view>


		<shopcart class="shopcart" @delAll="clearShopCart" @dec="desOrderItemCount" @add="addOrderItemCount" @settle="settle" :order="order"></shopcart>

	</view>

</template>
cartcontrol
<script>
	import uniPopup from "@/components/uni-popup/uni-popup.vue"
	import shopcart from "@/myComponents/shopcart/shopcart.vue"
	import cartcontrol from '@/myComponents/cartcontrol/cartcontrol.vue'
	import uniTag from "@/components/uni-tag/uni-tag.vue"
	import axbCheckBox from '@/components/axb-checkbox/axb-checkbox.vue'
	export default {
		components: {
			uniPopup,
			shopcart,
			uniTag,
			axbCheckBox
		},

		data() {
			return {
				scrollHeight: 400, //滚动区域高度
				scrollTopSize: 0,
				fillHeight: 0, // 填充高度，用于最后一项低于滚动区域时使用
				leftList: [],
				menu: [],
				topArr: [],
				leftIndex: 0,
				scrollInto: '',
				sweet: [{
						name: '标准甜',
						value: '标准甜',
						checked: 1
					},
					{
						name: '七分甜',
						value: '七分甜',
						checked: 0
					},
					{
						name: '五分甜',
						value: '五分甜',
						checked: 0
					},
					{
						name: '三分甜',
						value: '三分甜',
						checked: 0
					},
					{
						name: '无糖',
						value: '无糖',
						checked: 0
					}
				],
				size: [{
						name: '中杯',
						value: '中杯',
						checked: 1
					},
					{
						name: '大杯',
						value: '大杯',
						checked: 0
					},
				],
				ice: [{
						name: '冰',
						value: '冰',
						checked: 1
					},
					{
						name: '少冰',
						value: '少冰',
						checked: 0
					},
					{
						name: '去冰',
						value: '去冰',
						checked: 0
					},
					{
						name: '热',
						value: '热',
						checked: 0
					},
				],
				topping: [
					//TODO:小料要从后端获取生成
					{
						name: '珍珠',
						value: '珍珠',
						checked: 0
					},
					{
						name: '椰果',
						value: '椰果',
						checked: 0
					},
					{
						name: '布丁',
						value: '布丁',
						checked: 0
					},
					{
						name: '芋泥',
						value: '芋泥',
						checked: 0
					},
				],
				selectProduct: {},
				formData: {
					pid: '',
					product_name: '',
					count: 1,
					product_original_price: 0, //商品原价
					product_promote_price: 0, //商品折扣价
					topping_pric: 0, //小料价
					product_actual_price: 0, //商品实际价（折扣价+小料价）
					sweet: '标准甜',
					ice: '冰',
					size: '中杯',
					topping: [],
					toppingTotal: '',
					price: 0,
					note: ''
				},
				order: {
					type:1,
					product_price:0,//商品总价
					delivery_price:0,//配送费
					order_price:0,
					consignee:'',
					consignee_phone:'',
					consignee_address:'',
					consignee_room:'',
					orderItemList: [],
					// TODO:配送费，多少以上免配送费，多少以上才配送
				},
				orderItem: {
					pid: '', //商品id
					product_name: '', //商品名称
					product_original_price: 0, //商品原价
					product_promote_price: 0, //商品折扣价
					topping_pric: 0, //小料价
					product_actual_price: 0, //商品实际价（折扣价+小料价）
					product_picture: '', //图片
					total_item_price: 0, //订单项
					note: '', //备注
				}



			}
		},
		methods: {
			changePeisongType() {
				this.order.type = this.order.type == 0 ? 1 : 0;
			},
			imageError: function(e) {
				console.error('image发生error事件，携带值为' + e.detail.errMsg)
			},
			openProductPopup(item) {
				this.selectProduct = item

				this.formData = {
					pid: item.id,
					product_name: item.name,
					product_picture:item.picture,
					count: 1,
					product_original_price: item.mediumOriginalPrice, //商品原价
					product_promote_price: item.mediumPromotePrice, //商品折扣价
					topping_price: 0, //小料价
					product_actual_price: 0, //商品实际价（折扣价+小料价）
					sweet: '标准甜',
					ice: '冰',
					size: '中杯',
					topping: [],
					toppingTotal: '',
					note: ''
				}
				//needAPI:把商品信息复制到formData和selectProduct
				this.formData.note = this.formData.size + ',' + this.formData.ice + ',' + this.formData.sweet + ','
				this.computedFormDataTotalPrice()
				this.$refs.productPopup.open()
				console.log("打开")
			},
			closeProductPopup() {
				this.$refs.productPopup.close()
				console.log("关掉")
			},
			selectSweet(val) {
				this.formData.sweet = val
			},
			selectSize(val) {
				this.formData.size = val
				this.computedFormDataTotalPrice()
				console.log(this.formData)
			},
			selectIce(val) {
				this.formData.ice = val
			},
			selectTopping(val) {
				this.formData.topping = val
				this.formData.toppingTotal = ''
				for (const val of this.formData.topping) {
					this.formData.toppingTotal = this.formData.toppingTotal + val + ','
				}
				//TODO:要算加料价格！！！
			},
			submitOrderItem() {
				let f=this.formData
				this.formData.note=f.size+','+f.ice+','+f.sweet+','+f.toppingTotal
				
				this.formData.size=this.formData.size=='中杯'?0:1;
				delete this.formData.topping
				delete this.formData.toppingTotal
				delete this.formData.sweet
				delete this.formData.ice

				console.log('成功将商品加入购物车')
				console.log(this.order)
				//needAPI:提交form的时候计算价格，拼接note，然后赋值到this.order.orderItems[this.order.orderItems.length-1]
				this.order.orderItemList.push(Object.assign({},this.formData))
				this.computedOrderTotalPrice()
				
				this.closeProductPopup()
				
			},
			desFormDataCount() {
				if (this.formData.count > 1) {
					this.formData.count--
					this.computedFormDataTotalPrice()
				}

			},
			addFormDataCount() {
				this.formData.count++
				this.computedFormDataTotalPrice()
			},
			computedFormDataTotalPrice() {
				let product=this.selectProduct
				if(this.formData.size=='中杯'){
					if(product.mediumPromotePrice!=null){
						this.formData.product_actual_price=product.mediumPromotePrice
					}else{
						this.formData.product_actual_price=product.mediumOriginalPrice
					}
				}else{
					if(product.bigPromotePrice!=null){
						this.formData.product_actual_price=product.bigPromotePrice
					}else{
						this.formData.product_actual_price=product.bigOriginalPrice
					}
				}
				this.formData.product_actual_price=this.formData.product_actual_price*this.formData.count
				this.formData.product_actual_price+=this.formData.topping_price
			},
			computedOrderTotalPrice(){
				this.order.product_price=0
				this.order.order_price=0
				let list=this.orderItemList
				for(let i=0;i<this.order.orderItemList.length;i++){
					this.order.product_price+=this.order.orderItemList[i].product_actual_price
				}
				
				this.computedOrderDeliveryPrice()
				this.order.order_price=this.order.product_price+this.order.delivery_price
				console.log('计算订单总价')
				console.log(this.order)
			},
			computedOrderDeliveryPrice(){
				//TODO:计算派送费
				
			},
			clearShopCart(){
				//清空购物车，直接重置订单
				this.order={
					type:1,
					product_price:0,//商品总价
					delivery_price:0,//配送费
					order_price:0,
					consignee:'',
					consignee_phone:'',
					consignee_address:'',
					consignee_room:'',
					orderItemList: [],
					// TODO:配送费，多少以上免配送费，多少以上才配送
				}
			},
			desOrderItemCount(index){
				//订单项减少
				if(this.order.orderItemList[index].count<=1){
					this.order.orderItemList.splice(index,1)
					this.computedOrderTotalPrice()
					return
				}
				let singlePrice=this.order.orderItemList[index].product_actual_price / this.order.orderItemList[index].count
				this.order.orderItemList[index].count--;
				
				this.order.orderItemList[index].product_actual_price=singlePrice*this.order.orderItemList[index].count
				this.computedOrderTotalPrice()
			},
			addOrderItemCount(index){
				//订单项增加
				let singlePrice=this.order.orderItemList[index].product_actual_price / this.order.orderItemList[index].count
				this.order.orderItemList[index].count++;
				
				this.order.orderItemList[index].product_actual_price=singlePrice*this.order.orderItemList[index].count
				this.computedOrderTotalPrice()
			},
			settle() {
				//结算函数
				uni.setStorageSync('settle_order',this.order)
				uni.navigateTo({
					url: '../orderPreview/orderPreview'
				})
			},
			/* 初始化滚动区域 */
			initScrollView() {
				return new Promise((resolve, reject) => {
					let view = uni.createSelectorQuery().select('#scroll-panel');
					let windowHeight = wx.getSystemInfoSync().windowHeight
					view.boundingClientRect(res => {
						this.scrollTopSize = res.top;
						this.scrollHeight = windowHeight - 50;
						this.$nextTick(() => {
							resolve();
						});
					}).exec();
				});
			},
			/* 主区域滚动监听 */
			mainScroll(e) {
				let top = e.detail.scrollTop;
				let index = 0;
				/* 查找当前滚动距离 */
				for (let i = this.topArr.length - 1; i >= 0; i--) {
					/* 在部分安卓设备上，因手机逻辑分辨率与rpx单位计算不是整数，滚动距离与有误差，增加2px来完善该问题 */
					if (top + 2 >= this.topArr[i]) {
						index = i;
						break;
					}
				}
				this.leftIndex = index < 0 ? 0 : index;
			},
			/* 左侧导航点击 */
			leftTap(e) {
				let index = e.currentTarget.dataset.index;
				console.log(index)
				this.scrollInto = `item-${index}`;
			}
		},
		computed: {
			/* 计算左侧滚动位置定位 */
			leftIntoView() {
				return `left-${this.leftIndex > 3 ? this.leftIndex - 3 : 0}`;
			},


		},
		mounted() {
			/* 等待DOM挂载完成 */
			this.$nextTick(() => {
				/* 在非H5平台，nextTick回调后有概率获取到错误的元素高度，则添加200ms的延迟来减少BUG的产生 */
				setTimeout(() => {
					/* 等待滚动区域初始化完成 */
					this.initScrollView().then(() => {
						/* 获取列表数据，你的代码从此处开始 */
						console.log("aaaaaaa")
					});
				}, 200);
			});
		},
		onLoad() {
			let that = this
			//获取菜单
			uni.request({
				url: 'http://localhost:8082/wechat/catalog/list',
				method: 'GET',
				success: (res) => {
					console.log('succeed')
					that.menu = res.data.data.list
					console.log(that.menu)
					that.menu.sort(function(a, b) {
						return a.index - b.index
					})
					for (let i = 0; i < that.menu.length; i++) {
						that.menu[i].categoryItems.sort(function(a, b) {
							return a.index - b.index
						})
					}
				}
			})



			//needApi：获取轮播图、获取小料、获取店铺信息、获取用户address并把default设进缓存

		}

	}
</script>

<style lang="scss">
	page {
		height: 100%;
	}

	body {
		position: fixed;
		top: 0;
		height: 100%;
		overflow: hidden;
	}

	.box {
		height: 150rpx;
		border-style: solid;
		box-shadow: 0px 2px 2px 1px #DDDDDD;
		border-width: 0rpx;
		border-bottom: #E9E9E9 solid 1px;
	}


	.shop-name {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 0rpx 30rpx 0rpx 30rpx;
	}

	.shop-name .name {
		font-size: 35rpx;
	}

	.shop-name .select {
		display: flex;
		align-items: center;
		font-size: 25rpx;
		width: 180rpx;
		height: 60rpx;
		border-radius: 32rpx;
		background-color: #f6f6f6;
	}

	.shop-name .select .on {
		width: 90rpx;
		height: 60rpx;
		background-color: #343434;
		color: #ffffff;
		border-radius: 32rpx;
		text-align: center;
		line-height: 60rpx;
	}

	.shop-name .select .off {
		width: 90rpx;
		height: 60rpx;
		color: #d2d2d2;
		border-radius: 25rpx;
		text-align: center;
		line-height: 60rpx;
	}

	.distance-bar {
		display: flex;
		justify-content: space-between;
		margin: 30rpx;
	}

	.distance-bar .distance {
		font-size: 28rpx;
	}

	.distance-bar .shop-message {
		display: flex;
		align-items: center;
	}

	.distance-bar .shop-message .message {
		font-size: 26rpx;
	}

	.distance-bar .shop-message .go {
		height: 32rpx;
		width: 32rpx;
	}




	.list-box {
		display: flex;
		flex-direction: row;
		flex-wrap: nowrap;
		justify-content: flex-start;
		align-items: flex-start;
		align-content: flex-start;
		font-size: 28rpx;
		height: 100%;
		margin-bottom: var(--window-bottom);

		.left {
			width: 180rpx;
			background-color: #f6f6f6;
			// line-height: 80rpx;
			box-sizing: border-box;

			.item {
				border-left: 5px #f6f6f6 solid;
				padding-top: 9px;
				padding-bottom: 9px;
				// position: relative;

				&:not(:first-child) {


					// &::after {
					// 	content: '';
					// 	display: block;
					// 	height: 0;
					// 	border-top: #d6d6d6 solid 1px;
					// 	width: 620upx;
					// 	position: absolute;
					// 	top: -1px;
					// 	right: 0;
					// 	transform: scaleY(0.5);
					// 	/* 1px像素 */
					// }
				}

				&.active {
					color: #343434;
					background-color: #fff;
					border-left: 5px #DBA871 solid;

					.icon {
						background-color: #fff;
					}

					.name {
						color: #343434;
						font-weight: 700;
					}
				}

				.icon {
					margin-left: 32px;
					width: 25px;
					height: 25px;
					background-color: #f6f6f6;
				}

				.name {
					color: #cdcdcd;
					font-size: 13px;
					font-weight: 400;
					display: flex;
					flex-direction: row;
					justify-content: center;
				}
			}

			.fill-last {
				height: 0;
				width: 100%;
				background: none;
			}
		}

		.main {
			background-color: #fff;
			padding-left: 20rpx;
			width: 0;
			flex-grow: 1;
			box-sizing: border-box;

			.title {
				line-height: 64rpx;
				font-size: 20rpx;
				font-weight: 800;
				color: #626262;
				background-color: #fff;
				// position: sticky;
				top: 0;
				z-index: 19;
			}

			.item {
				padding-bottom: 10rpx;
				border-bottom: #E9E9E9 solid 1px;
			}

			.goods {
				display: flex;
				flex-direction: row;
				flex-wrap: nowrap;
				justify-content: flex-start;
				align-items: center;
				align-content: center;
				margin-top: 15rpx;
				margin-bottom: 40rpx;

				&>image {
					width: 120rpx;
					height: 120rpx;
					margin-right: 16rpx;
					margin-left: 2px;
				}

				.title {
					color: #000000;
					font-size: 30rpx;
					font-weight: 700;
					margin-top: 10rpx;
					margin-bottom: 10rpx;
				}

				.describe {
					font-size: 24rpx;
					width: 360rpx;
					color: #999;
					overflow: hidden;
					word-break: break-all;
					/* break-all(允许在单词内换行。) */
					text-overflow: ellipsis;
					/* 超出部分省略号 */
					display: -webkit-box;
					/** 对象作为伸缩盒子模型显示 **/
					-webkit-box-orient: vertical;
					/** 设置或检索伸缩盒对象的子元素的排列方式 **/
					-webkit-line-clamp: 2;
					/** 显示的行数 **/
				}

				.bottom {
					display: flex;

					.price {
						width: 100px;
						margin-top: 10px;
						font-size: 35rpx;
						font-weight: 600;
						color: #000000;
					}


					.button {
						margin-left: 55px;
						margin-top: 10px;
						font-size: 8px;
						color: #FFFFFF;
						background-color: #DBA871;
						width: 100rpx;
						height: 40rpx;
						padding-left: 2px;
						padding-right: 2px;
						display: flex;
						align-items: center;
						justify-content: center;
						// padding-top: 0px;



						-webkit-border-radius: 30px;
						-moz-border-radius: 30px;
						border-radius: 30px;


						-webkit-transition: all 0.15s ease;
						-moz-transition: all 0.15s ease;
						-o-transition: all 0.15s ease;
						-ms-transition: all 0.15s ease;
						transition: all 0.15s ease;
					}

				}


			}
		}
	}

	.product-card {
		z-index: 200;
		display: flex;
		width: 100%;
		height: 100%;
		margin-bottom: var(--window-bottom);


		.card-content {
			height: 100vh; //vh指view hight，基于窗口高度设置
			display: flex;
			flex-direction: column;
			justify-content: center;
			// align-items:flex-end;

			.product-form {
				background-color: #FFFFFF;
				width: 660rpx;
				height: 65%;
				// margin-bottom: 80rpx;
				border-top-left-radius: 5px;
				border-top-right-radius: 5px;




				.product-image {
					height: 150px;
					width: 100%;
					display: flex;
					flex-direction: column;
					align-items: center;
					position: relative;
				}

				.ring {
					background-color: rgba(0, 0, 0, 0.3);
					width: 28px;
					height: 28px;
					border-radius: 100%;
					display: flex;
					align-items: center;
					justify-content: center;
					font-size: 16px;
				}

				.ctn {
					z-index: 2;
					/* border: 1px solid #e3e3e3; */
					width: 100%;
					display: flex;
					justify-content: flex-end;
					overflow: hidden;
					align-items: center;
					position: fixed;

					.leftBox {
						display: flex;
						width: 60px;
						justify-content: space-between;
						flex: none;
						margin-top: 10px;
						margin-right: 30px;
						align-items: center;
					}

					.jrNull {
						/* #ifdef MP */
						width: 95px;
						flex: none;
						/* #endif */
					}
				}

				.fillHeight {
					height: 50px;
				}

				.formContent {
					margin-left: 18px;
					margin-right: 18px;
					font-size: 13px;

					* {
						.title {
							color: #666666;
							margin-top: 7px;
							margin-bottom: 7px;

						}
					}


					.name {
						font-size: 20px;
						font-weight: 800;
						color: #000000;
					}

					.describe {
						font-size: 13px;
						color: #898989;
					}

					.sort {
						margin-top: 5px;
						margin-bottom: 5px;
						width: auto;
						display: inline-block !important;
						display: inline;
						background-color: #F6F6F6;
						color: #BABABA;
					}

					.ice {
						margin-top: 10px;
					}

					.sweet {
						margin-top: 25px;
					}

					.size {
						margin-top: 25px;
					}
				}

			}

			.formBottom {
				width: 660rpx;
				height: 15%;
				max-height: 130px;
				background-color: #FFFFFF;
				flex-direction: column;
				align-items: stretch;
				border-bottom-left-radius: 5px;
				border-bottom-right-radius: 5px;
				display: flex;
				justify-content: space-between;


				.textAndcontrol {
					display: flex;
					flex-direction: row;
					justify-content: space-between;


					.bottomText {
						display: flex;
						flex-direction: column;

						height: 40%;
						width: 70%;

						.price {
							width: 90%;
							height: 20%;
							margin-left: 20px;
							margin-right: 20px;
							margin-top: 5px;
							margin-bottom: 5px;
							font-size: 18px;
							color: #DBA871;
						}

						.mark {
							font-size: 10px;
							color: #878787;
							height: 80%;
							width: 90%;
							margin-left: 20px;
							margin-right: 20px;
							margin-top: 10px;
						}
					}

					.control {
						display: flex;
						flex-direction: row;
						justify-content: space-around;
						align-items: center;
						width: 30%;
						height: 100%;


						.des {
							display: flex;
							align-items: center;
							justify-content: center;
							width: 32px;
							height: 32px;
							box-sizing: border-box;
							border-radius: 100%;
						}

						.add {
							display: flex;
							align-items: center;
							justify-content: center;
							width: 25px;
							height: 25px;
							box-sizing: border-box;
							border-radius: 100%;
						}
					}
				}


				// TODO:修改formButtom样式，编写加入购物车逻辑（要计算价格）




				none::after {
					border: none;

				}

				.formButton {
					display: flex;
					height: 30%;
					width: 85%;
					justify-content: center;
					align-items: center;
					background-color: #DBA871;
					font-size: 14px;
					margin-bottom: 10px;
					color: #FFFFFF;
				}
			}
		}


	}
</style>
