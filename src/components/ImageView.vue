<template>
	<div class="image-view">
		<img v-if="data" :src="data" alt="image"/>
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
	margin-bottom: 10px;
	img {
		display: block;
		max-width: 100%;
		height: auto;
	}
}
</style>