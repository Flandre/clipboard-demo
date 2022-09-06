<template>
	<div class="clipboard-main" :style="{width: `${width}px`}">
		<div class="print-panel">
			<div class="btn-group">
				<el-button type="primary" round @click="pasteClipboard">粘贴</el-button>
				<el-button type="primary" round @click="print()">打印</el-button>
			</div>
			<div class="select-group">
				<el-select style="width: 100px;" v-model="width" placeholder="请选择宽度">
					<el-option
						v-for="item in widthList"
						:key="item.value"
						:label="item.label"
						:value="item.value">
					</el-option>
				</el-select>
			</div>
		</div>
		<div class="paste-board">
			<Adapter
				v-for="(item, index) in itemList"
				:item="item"
				v-on:delete="deleteItem(index)"
			/>
		</div>
	</div>
</template>

<script>
import Adapter from "../components/Adapter";
export default {
	name: "Clipboard",
	data() {
		return {
			itemList: [],
			width: 800,
			widthList: [
				{
					label: '600px',
					value: 600
				},
				{
					label: '650px',
					value: 650
				},
				{
					label: '700px',
					value: 700
				},
				{
					label: '750px',
					value: 750
				},
				{
					label: '800px',
					value: 800
				},
				{
					label: '850px',
					value: 850
				},
				{
					label: '900px',
					value: 900
				},
				{
					label: '950px',
					value: 950
				},
				{
					label: '1000px',
					value: 1000
				},
			]
		}
	},
	components: {
		Adapter
	},
	methods: {
		print() {
			window.print()
		},
		deleteItem(index) {
			this.itemList.splice(index, 1)
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
		display: flex;
		justify-content: space-between;
		align-items: center;
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