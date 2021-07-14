<template>
	<view class="container">
		<view class="handlebar">
			<button type="default" size="mini" @click="handleClear">清空</button>
			<button type="default" size="mini" @click="handleUnDraw">撤销</button>
			<button type="primary" size="mini" @click="getImage">确定</button>
		</view>
		<uni-mp-signature ref="signature" style="flex: 1; border: 1px dashed #c9d8db" />
	</view>
</template>

<script>
	import UniMpSignature from './mp-signature.vue'

	export default {
		components: {
			UniMpSignature
		},
		data() {
			return {
			};
		},
		methods: {
			handleClear(){
				this.$refs.signature.clear()
			},
			handleUnDraw(){
				this.$refs.signature.undraw()
			},
			getImage(){
				this.$refs.signature.getImage()
			}
		}
	}
</script>

<style lang="scss">
	.container {
		display: flex;
		flex-flow: row nowrap;
		height: 100vh;
		box-sizing: border-box;
		align-items: stretch;
	}


	.handlebar {
		display: flex;
		flex-flow: column;
		margin-right: 16rpx;

		button {
			margin: 120rpx 0;
			transform: rotate(90deg);
		}
	}
</style>
