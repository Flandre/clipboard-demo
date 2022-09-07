<template>
	<div class="adapter" :class="[isSelected ? 'selected': '']" @click.stop="$store.state.selectAdapter = index">
		<transition name="slide-left">
			<div class="right-controls" v-if="showControl">
				<el-button class="btn" type="primary" size="mini" icon="el-icon-top" circle @click="$emit('up')"></el-button>
				<el-button class="btn" type="primary" size="mini" icon="el-icon-bottom" circle @click="$emit('down')"></el-button>
				<el-button class="btn" type="danger" size="mini" icon="el-icon-delete" circle @click="$emit('delete')"></el-button>
			</div>
		</transition>
		<div class="main-item-container">
			<component
				:is="item.component"
				:data="item.data"
			/>
			<div class="modal"></div>
		</div>
	</div>
</template>

<script>
import TextView from "./TextView";
import ImageView from "./ImageView";
import { mapState } from 'vuex'
export default {
	name: "Adapter",
	props: ['item', 'index'],
	data() {
		return {
			modal: false,
			isSelected: false,
			showControl: false,
			timer1: 0,
			timer2: 0
		}
	},
	components: {
		TextView,
		ImageView
	},
	computed: {
		...mapState(['selectAdapter']),
	},
	beforeDestroy() {
		clearTimeout(this.timer1)
		clearTimeout(this.timer2)
	},
	watch: {
		selectAdapter(val) {
			if(val === this.index) {
				this.isSelected = true
				this.timer1 = setTimeout(() => {
					this.showControl = true
				}, 300)
			} else {
				this.showControl = false
				this.timer2 = setTimeout(() => {
					this.isSelected = false
				}, 300)
			}
		}
	}
}
</script>

<style lang="scss" scoped>

.slide-left-enter-active,
.slide-left-leave-active {
	transition: all .3s ease-in;
	width: 40px;
}
.slide-left-enter,
.slide-left-leave-to {
	width: 0px !important;
}
.adapter {
	margin-bottom: 10px;
	width: 100%;
	min-height: 1px;
	cursor: pointer;
	display: flex;
	align-items: stretch;
	justify-content: space-between;
	overflow: hidden;
	transition: min-height 300ms;
	&:hover {
		box-shadow: 0 2px 12px 0 rgb(0 0 0 / 10%);
	}
	.main-item-container {
		flex-grow: 1;
		display: flex;
		align-items: center;
		position: relative;
		.modal {
			position: absolute;
			top: 0;
			left: 0;
			right: 0;
			bottom: 0;
			background-color: rgba(0, 0, 0, 1);
			opacity: 0;
			transition: 1000ms;
			display: flex;
			align-items: center;
			justify-content: center;
		}
	}
	.right-controls {
		overflow: hidden;
		width: 40px;
		flex-shrink: 0;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		.btn {
			+ .btn {
				margin-top: 5px;
				margin-left: 0;
			}
		}
	}
	&.selected {
		box-shadow: 0 2px 12px 0 rgb(0 0 0 / 10%);
		min-height: 100px;
		.main-item-container {
			.modal {
				opacity: .5;
			}
		}
	}
}
</style>