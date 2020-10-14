<template>
	<div class="bottom-bar">
		<div class="check-content">
			<check-button :is-checked="isSelectAll" 
			              class="check-button"
						  @click.native="checkClick"></check-button>
			<span>全选</span>
		</div>
		<div class="price">
			合计：{{totalPrice}}
		</div>
		<div class="calculate">
			去计算:{{checkLength}}
		</div>
	</div>
</template>

<script>
	import { mapGetters } from 'vuex'
	import CheckButton from '../../../components/content/checkButton/CheckButton.vue'
	export default {
		name: 'CartBottomBar',
		components: {
			CheckButton
		},
		computed: {
			...mapGetters(['cartList']),
			totalPrice() {
				return this.cartList.filter(item => {
					return item.checked
				}).reduce((preValue,item) => {
					return preValue + item.price * item.count
				}, 0).toFixed(2)
			},
			checkLength() {
				return this.cartList.filter(item => item.checked).length
			},
			isSelectAll() {
			return	!(this.cartList.filter(item => !item.checked).length)
			
			}
		},
		methods: {
			checkClick() {
				if (this.isSelectAll) {//全部选中
					this.cartList.forEach(item => item.checked = false)
				} else {
					this.cartList.forEach(item => item.checked = true)
				}
			}
		}
	}
</script>

<style>
	.bottom-bar {
		height: 40px;
		background-color: #eee;
		position: relative;
		/* line-height: 40px; */
		display: flex;
	}
	.check-content {
		display: flex;
		align-items: center;
	}
	.check-button {
		
		width: 22px;
		height: 22px;
	}
	.price {
		margin-top: 11px;
		margin-left: 10px;
	}
	.calculate {
		background-color: red;
		margin-top: 4px;
		margin-left: 130px;
		color: white;
		line-height: 40px;
	}
</style>
