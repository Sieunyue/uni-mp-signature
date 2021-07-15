<template>
	<view class="signature__container">
		<canvas class="signature__content" disable-scroll type="2d" @touchstart="_handleTouchStart"
			@touchmove="_handleTouchMove" @touchend="_handleTouchEnd" />
	</view>
</template>

<script>
	export default {
		name: 'UniMpSignature',
		props: {
			config: {
				type: Object,
				default: {},
			},
		},
		data() {
			return {
				ctx: null,
				canvas: null,
				ChirographyCache: [],
				currentChirography: [],
			};
		},
		mounted() {
			this._initCanvas()
		},
		computed: {
			canvasConfig() {
				const defaultConfig = {
					transparent: 1,
					lineColor: '#000',
					lineWidth: 2,
				}

				return Object.assign(defaultConfig, this.config)
			}
		},

		methods: {
			clear(){
				this._clearChirography()
			},
			reDraw(){
				this._reDrawChirography()
			},
			undraw(){
				this._unDrawChirography()
			},
			getImage(){
				if(!this.canvas){
					return
				}
				const bs64 = this.canvas.toDataURL()
				
				return bs64
			},
			_initCanvas() {
				uni.createSelectorQuery()
					.in(this)
					.select(`.signature__content`)
					.fields({
						node: true,
						size: true
					})
					.exec((res) => {
						const canvas = res[0].node
						const ctx = canvas.getContext('2d')
						this.canvas = canvas
						this.ctx = ctx

						// wx canvas 2d 只在style设置宽高会导致canvas拉伸变形
						canvas.height = res[0].height
						canvas.width = res[0].width

						this._setConfig()

					})
			},

			_setConfig() {
				const {
					transparent,
					lineWidth,
					lineColor
				} = this.canvasConfig
				this.ctx.globalAlpha = transparent
				this.ctx.lineWidth = lineWidth
				this.ctx.strokeStyle = lineColor
			},

			_drawLine(startX, startY, endX, endY) {
				const ctx = this.ctx

				ctx.beginPath();

				ctx.moveTo(startX, startY)
				ctx.lineTo(endX, endY)

				ctx.closePath()

				ctx.stroke()
			},

			_unDrawChirography() {
				this.ChirographyCache.pop()
				this.$nextTick(() => {
					this._reDrawChirography()
				})
			},

			_reDrawChirography() {
				this._clearChirography()

				this.ChirographyCache.forEach(store => {
					for (let i = 1; i < store.length; i++) {
						const lastPoint = store[i - 1]
						const currentPoint = store[i]

						this._drawLine(lastPoint.x, lastPoint.y, currentPoint.x, currentPoint.y)
					}
				})
			},

			_clearChirography() {
				this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)
			},

			_handleTouchStart(e) {
				const points = []
				const { x, y } = e.touches[0]

				points.push({ x, y })

				this.currentChirographys = points

			},
			_handleTouchMove(e) {
				const { x, y } = e.touches[0]

				const { x: lastX, y: lastY } = this.currentChirographys[this.currentChirographys.length - 1]

				this._drawLine(lastX, lastY, x, y)

				this.currentChirographys.push({ x, y })
			},
			_handleTouchEnd(e) {
				this.ChirographyCache.push([...this.currentChirographys])
				this.currentChirographys = []
			}
		}
	}
</script>

<style lang="scss">
	.signature__container {
		display: flex;
		flex-flow: row nowrap;
		box-sizing: border-box;
		padding: 32rpx 0;
		align-items: stretch;
	}

	.signature__content {
		height: 100%;
		width: 100%;
	}
</style>
