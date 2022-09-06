<template>
  <div id="app">
		<Clipboard/>
  </div>
</template>

<script>
import Clipboard from "@/views/Clipboard";

export default {
  name: 'App',
  components: {
		Clipboard
  }
}
</script>

<style lang="scss">
* {
	padding: 0;
	margin: 0;
	border: 0;
}
html, body {
	min-height: 100vh;
}
#app {
	min-height: 100vh;
	background-color: #333;
	box-sizing: border-box;
	padding: 20px;
	display: flex;
	flex-direction: column;
	align-items: center;
}
</style>
