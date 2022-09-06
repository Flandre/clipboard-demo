<template>
	<p v-if="data" class="text-view">{{data}}</p>
</template>

<script>
export default {
	name: "TextView",
	props: ['data']
}
</script>

<style lang="scss" scoped>
.text-view {
	display: block;
	width: 100%;
	font-size: 16px;
	margin-bottom: 10px;
	word-break: break-all;
	text-align: left;
}

</style>