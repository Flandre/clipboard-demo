<template>
	<div class="clipboard-main" :style="{width: `${width}px`}">
		<div class="print-panel">
			<div class="btn-group">
				<el-button type="primary" round @click="pasteClipboard">粘贴</el-button>
				<el-button type="primary" round @click="print()">打印</el-button>
			</div>
			<div class="select-group">
				<el-select style="width: 150px;" v-model="$store.state.imageType" placeholder="选择图片模式">
					<el-option
						v-for="item in imageTypeList"
						:key="item.value"
						:label="item.label"
						:value="item.value">
					</el-option>
				</el-select>
				<el-select style="width: 100px; margin-left: 10px;" v-model="width" placeholder="请选择宽度">
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
			imageTypeList: [
				{
					label: '原始',
					value: 'source'
				},
				{
					label: '不超过最大宽度',
					value: 'normal'
				},
				{
					label: '拉伸',
					value: 'fill'
				}
			]
		}
	},
	mounted() {
		document.addEventListener('paste', this.pasteClipboard)
	},
	beforeDestroy() {
		document.removeEventListener('paste', this.pasteClipboard)
	},
	computed: {
		widthList() {
			let start = 600, end = 1000, step = 50, out = []
			for(let value = start; value <= end; value += step){
				out.push({
					value,
					label: `${value}px`
				})
			}
			return out
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
				self.$message.error('Not allowed to read clipboard.')
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
						self.$message.error('Null blob data.')
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
				self.$message.error('Error clipboard item type.')
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
	.clipboard-main {
		.print-panel {
			display: none !important;
		}
	}
}
</style>