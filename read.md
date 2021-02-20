# 流畅 拖动排序 拖拽排序 easydrag-sort
## 拖拽跳转，组件名：`easydrag-sort`

使用方式：

在 `script` 中引用组件

```
import dragNavigate from '@/components/easydrag-sort/easydrag-sort.vue';
export default {
    components: {easydrag-sort}
}
```
在 `template` 中使用组件

```
<easydrag-sort :list="tabs" :itemw="cellW" :itemh="cellH" :itemY="10" @change="onDragSortChange">
	<template v-slot="{item}">
		<view class="box" :style="{width:cellW + 'px',height:cellH + 'px',lineHeight:cellH+'px'}">{{item.name}}</view>
	</template>
</easydrag-sort>
```
属性说明：

|属性名		|类型		|默认值			|说明|
| --------  | -----:	|-----:			| :----:  |
|list		|Array	|[]				|列表对象|
|itemw		|Number	|0				|子项宽度(px)|
|itemh		|Number	|0					|子项高度(px)|
|itemY		|Number	|0				|行间距|

![avatar](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-e012c0ab-5ec3-47f7-a025-7bc1e09dd5b5/3d22e35c-81e9-4068-ab4f-fc29e63219de.gif)
## 有更多优化建议和需求，请联系作者。谢谢！

