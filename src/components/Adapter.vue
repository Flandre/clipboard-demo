<template>
	<div class="adapter" :class="[modal ? 'fix-height': '']" @mouseenter="modal = true" @mouseleave="modal=false">
		<div class="modal" v-if="modal">
			<el-button type="danger" icon="el-icon-delete" circle @click="$emit('delete')"></el-button>
		</div>
		<component
			:is="item.component"
			:data="item.data"
		/>
	</div>
</template>

<script>
import TextView from "./TextView";
import ImageView from "./ImageView";
export default {
	name: "Adapter",
	props: ['item'],
	data() {
		return {
			modal: false,
		}
	},
	components: {
		TextView,
		ImageView
	},
}
</script>

<style lang="scss" scoped>
.adapter {
	margin-bottom: 10px;
	display: block;
	width: 100%;
	position: relative;
	&.fix-height {
		min-height: 80px;
	}
	.modal {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(0, 0, 0, .7);
		display: flex;
		align-items: center;
		justify-content: center;
	}
}
</style>