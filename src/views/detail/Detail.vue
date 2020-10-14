<template>
	<div id="detail">
		
		<detail-nav-bar class="detail-nav" @titleClick="titleClick"></detail-nav-bar>
		
		<scroll class="content" ref="scroll">
			
			<detail-swiper :top-images="topImages"></detail-swiper>
			<detail-base-info :goods="goods"></detail-base-info>
			<detail-shop-info :shop="shop"></detail-shop-info>
			<detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad" ></detail-goods-info>
			
			<detail-param-info :param-info="paramInfo"></detail-param-info>
			<detail-comment-info :comment-info="commentInfo"></detail-comment-info>
			
		</scroll>
		<detail-bottom-bar @addCart="addToCart"></detail-bottom-bar>
		
	</div>
</template>

<script>
	import DetailNavBar from './childComps/DetailNavBar.vue'
	import DetailSwiper from './childComps/DetailSwiper.vue'
	import DetailBaseInfo from './childComps/DetailBaseInfo.vue'
	import DetailShopInfo from './childComps/DetailShopInfo.vue'
	import {getDetail,Goods,Shop,GoodsParam} from '../../network/detail.js'
	import Scroll from '../../components/common/scroll/Scroll.vue'
	import DetailGoodsInfo from './childComps/DetailGoodsInfo.vue'
	import DetailParamInfo from './childComps/DetailParamInfo.vue'
	import DetailCommentInfo from './childComps/DetailCommentInfo.vue'
	
	import DetailBottomBar from './childComps/DetailBottomBar.vue'
	
	
	export default {
		name: 'Detail',
		components:{
			DetailNavBar,
			DetailSwiper,
			DetailBaseInfo,
			DetailShopInfo,
			Scroll,
			DetailGoodsInfo,
			DetailParamInfo,
			DetailCommentInfo,
			DetailBottomBar,
			
		},
		data() {
			return {
				iid: null,
				topImages: [],
				goods: {},
				shop: {},
				detailInfo: {},
				paramInfo: {},
				commentInfo: {},
				themeYopYs: [],
				
				
			}
		},
		created() {
			//1.保存传入的iid
			this.iid = this.$route.params.iid
			
			//2.根据iid请求详情数据
			getDetail(this.iid).then(res => {
				console.log(res)
				
				//1.获取顶部图片轮播数据
				const data = res.result
				this.topImages = data.itemInfo.topImages
				
				//2.获取商品信息
				this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)
				
				
				//3.创建店铺信息的对象
				this.shop = new Shop(data.shopInfo)
				
				//4.保存商品的详情数据
				this.detailInfo = data.detailInfo
				
				//5.获取参数信息
				this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)
				
				//6.取出评论信息
				if (data.rate.cRate !== 0) {
					this.commentInfo = data.rate.list[0]
				}
			})
			
			
			
			
		},
		methods: {
			imageLoad() {
				this.$refs.scroll.refresh()
			},
			titleClick(index) {
				this.$refs.scroll.scrollTo(0, -this.themeYopYs[index], 400)
			},
			addToCart() {
				//1.获取购物车需要展示的信息
				const product = {}
				product.image = this.topImages[0]
				product.title = this.goods.title
				product.desc = this.goods.desc
				product.price = this.goods.realPrice
				product.iid = this.iid
				
				//2.将商品添加到购物车
				// this.$store.commit('addCart', product )
				this.$store.dispatch('addCart',product)
			}
		}
	}
</script>

<style scoped="scoped">
	#detail {
		height: 100vh;
		position: relative;
		z-index: 9;
		background-color: #fff;
	}
	.detail-nav {
		position: relative;
		z-index: 9;
		background-color: #fff;
	}
	.content {
		height: calc(100% - 44px);
	}
</style>
