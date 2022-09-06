<template>
	<div class="clipboard-main">
		<div class="print-panel">
			<el-button type="primary" round @click="pasteClipboard">粘贴</el-button>
			<el-button type="primary" round @click="print()">打印</el-button>
		</div>
		<div class="paste-board">
			<component
				v-for="(item, index) in itemList"
				:is="item.component"
				:data="item.data"
			/>
		</div>
	</div>
</template>

<script>
import TextView from "../components/TextView";
import ImageView from "../components/ImageView";
export default {
	name: "Clipboard",
	data() {
		return {
			itemList: []
		}
	},
	components: {
		TextView,
		ImageView
	},
	methods: {
		print() {
			window.print()
		},
		async pasteClipboard() {
			let self = this
			const permission = await navigator.permissions.query({ name: 'clipboard-read' });
			if (permission.state === 'denied') {
				throw new Error('Not allowed to read clipboard.');
			}
			const clipboardContents = await navigator.clipboard.read();
			for (const item of clipboardContents) {
				if(item.types.includes('text/plain')) {
					const blob = await item.getType('text/plain')
					const data = await (new Response(blob)).text()
					self.itemList.push({
						component: 'TextView',
						data
					})
					return
				}
				if(item.types.filter(x => x.startsWith('image/'))) {
					const type = item.types.filter(x => x.startsWith('image/'))[0]
					const blob = await item.getType(type)
					if(!blob) {
						alert('no blob')
						return
					}
					const data = URL.createObjectURL(blob)
					self.itemList.push({
						component: 'ImageView',
						data
					})
					return
				}
				console.log(item)
				alert('error item type')
			}
		}
	}
}
</script>

<style lang="scss" scoped>
.clipboard-main {
	width: 800px;
	margin: 0 auto;
	.print-panel {
		margin-bottom: 20px;
	}
	.paste-board {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
}
@media print {
	.print-panel {
		display: none;
	}
}
</style>