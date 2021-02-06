<template>
	<view class="content">
		<easydrag-sort :list="tabs" :itemw="cellW" :itemh="cellH" :itemY="10" @change="onDragSortChange">
			<template v-slot="{item}">
				<view class="box" :style="{width:cellW + 'px',height:cellH + 'px',lineHeight:cellH+'px'}">{{item.name}}</view>
			</template>
		</easydrag-sort>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				cellW:50,
				cellH:50,
				tabs: [{
						"name": "DNA",
						"type": "1"
					},
					{
						"name": "英超",
						"type": "2"
					},
					{
						"name": "中超",
						"type": "3"
					},
					{
						"name": "CBA",
						"type": "4"
					},
					{
						"name": "电竞",
						"type": "5"
					},
					{
						"name": "西甲",
						"type": "6"
					},
					{
						"name": "意甲",
						"type": "7"
					},
					{
						"name": "德甲",
						"type": "8"
					},
					{
						"name": "法甲",
						"type": "9"
					},
					{
						"name": "欧冠",
						"type": "10"
					},
					{
						"name": "世界杯",
						"type": "11"
					},
					{
						"name": "欧洲杯",
						"type": "12"
					}
				]
			}
		},
		onLoad() {

		},
		methods: {
			onDragSortChange(currentList){
				//整理完成的列表对象
			}
		}
	}
</script>
<style>
	page{
		height: 100%;
	}
</style>
<style lang="scss" scoped>
	.content{
		width: 100%;
		height: 100%;
		.box{
			text-align: center;
			background-color: #ffaaff;
		}
	}
</style>
