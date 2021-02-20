<template>
	<movable-area class="drag-sort" id="drag">
		<movable-view v-for="(item, index) in currentList" :key="index" :x="item.x" v-if="item.isShow === 1" :data-index="index"
		 @touchstart="touchstart" @touchmove.stop="touchmove" @touchend="touchend" :y="item.y" :style="{width:itemw + 'px',height:itemh + 'px'}"
		 direction="all" disabled damping="40" :animation="item.animation" class="drag-sort-item" :class="{'active': active == index}">
			<slot :item="item"></slot>
		</movable-view>
	</movable-area>
</template>

<script>
	export default {
		name: 'drag-sort',
		data() {
			return {
				currentList: [],
				active: -1, // 当前激活的item
				index: 0, // 当前激活的item的原index
				width: 0
			}
		},
		props: {
			list: {
				type: Array,
				default: () => {
					return []
				}
			},
			itemw: {
				type: Number,
				default: 0
			},
			itemh: {
				type: Number,
				default: 0
			},
			itemY: {
				type: Number,
				default: 0
			}
		},
		watch: {
			list: {
				handler() {
					this.onUpdateCurrentList()
				},
				deep: true
			}
		},
		computed: {
			collisionOffX() {
				return this.itemw / 2
			},
			collisionOffY() {
				return this.itemh / 2
			}
		},
		mounted() {
			const query = uni.createSelectorQuery().in(this)
			query.select('#drag').boundingClientRect()
			query.exec((res) => {
				this.topX = res[0].left
				this.topY = res[0].top
				this.width = res[0].width
				this.onUpdateCurrentList()
			})
		},
		methods: {
			onUpdateCurrentList(list = this.list) {
				let arr = []
				this.eachRow = Math.floor(this.width / this.itemw)
				this.itemX = (this.width % this.itemw) / (this.eachRow - 1)
				let x, y, cur_x, cur_y = 0
				for (let i = 0; i < list.length; i++) {
					cur_x = i % this.eachRow
					cur_y = Math.floor(i / this.eachRow)
					x = cur_x * this.itemw + (cur_x * this.itemX)
					y = cur_y * this.itemh + (cur_y * this.itemY)
					arr.push({
						...list[i],
						isShow: 1,
						index: i,
						SortNumber: i,
						x,
						y,
						animation: true
					})
				}
				this.currentList = arr
			},
			// 根据排序进行重新计算位置
			moveUpdateCurrentList(index) {
				let x, y, cur_x, cur_y, SortNumber = 0
				for (let i = 0; i < this.currentList.length; i++) {
					SortNumber = this.currentList[i].SortNumber
					cur_x = SortNumber % this.eachRow
					cur_y = Math.floor(SortNumber / this.eachRow)
					x = cur_x * this.itemw + (cur_x * this.itemX)
					y = cur_y * this.itemh + (cur_y * this.itemY)
					if (this.active != i) {
						this.currentList[i].x = x
						this.currentList[i].y = y
					} else {
						this.oldx = x;
						this.oldy = y;
					}
				}

				index == -1 && this.$emit('change', this.currentList)
			},
			touchstart(e) {
				// 计算 x y 轴点击位置
				let touchY = e.mp.touches[0].clientY - this.topY
				let touchX = e.mp.touches[0].clientX - this.topX
				this.active = Number(e.currentTarget.dataset.index)
				this.index = Number(e.currentTarget.dataset.index)
			},
			touchmove(e) {
				if (this.active < 0) return
				let temX = e.mp.touches[0].clientX - this.topX
				let temY = e.mp.touches[0].clientY - this.topY
				let touchX = temX - this.collisionOffX
				let touchY = temY - this.collisionOffY
				if (touchX <= 0) touchX = 0.001
				if (touchY <= 0) touchY = 0.001
				this.currentList[this.active].y = touchY
				this.currentList[this.active].x = touchX
				this.currentList[this.active].animation = false
				this.currentList.every((res, index) => {
					let absX = Math.abs(touchX - res.x)
					let absY = Math.abs(touchY - res.y)
					// 设置元素定点距离多少进行重排
					if (0 < absX && absX <= this.collisionOffX && absY > 0 && absY <= this.collisionOffY && this.active != index) {
						// debugger
						let temNumber = this.currentList[index].SortNumber
						this.currentList.every((_res, _index) => {
							// 判断从大像小移还是从小向大移
							if (this.currentList[this.active].SortNumber < this.currentList[index].SortNumber) {
								// 移动元素比目标元素所在位置小，之间元素排序--
								if (this.currentList[_index].SortNumber > this.currentList[this.active].SortNumber && this.currentList[
										_index].SortNumber <= this.currentList[index].SortNumber) {
									_res.SortNumber--
								}
							} else {
								// 反之++
								if (this.currentList[_index].SortNumber < this.currentList[this.active].SortNumber && this.currentList[
										_index].SortNumber >= this.currentList[index].SortNumber) {
									_res.SortNumber++
								}
							}
							return true
						}, this)
						this.currentList[this.active].SortNumber = temNumber
						this.moveUpdateCurrentList(temNumber)
						return false
					} else {
						return true
					}
				}, this)
			},
			touchend(e) {
				if (this.active < 0) return
				if (this.currentList[this.active]) {
					this.currentList[this.active].y = this.oldy
					this.currentList[this.active].x = this.oldx
					this.currentList[this.active].animation = true
				}
				this.moveUpdateCurrentList(-1)
				this.active = -1
			}
		}
	}
</script>

<style lang='less' scoped>
	.drag-sort {
		width: 100%;
		height: 100%;
	}

	.drag-sort-item {
		margin-bottom: 16px;
	}

	.active {
		box-shadow: 0 0 40rpx #DDDDDD;
		z-index: 99;
	}
</style>
