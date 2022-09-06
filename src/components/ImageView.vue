<template>
	<div class="image-view">
		<img v-if="data" :src="data" alt="image" :class="$store.state.imageType"/>
	</div>
</template>

<script>
export default {
	name: "ImageView",
	props: ['data']
}
</script>

<style lang="scss" scoped>
.image-view {
	display: flex;
	flex-direction: column;
	align-items: center;
	img {
		display: block;
		&.normal {
			max-width: 100%;
			height: auto;
		}
		&.source {

		}
		&.fill {
			width: 100%;
			height: auto;
		}
	}
}
</style>