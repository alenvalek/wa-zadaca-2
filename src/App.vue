<template>
	<v-app class="valekovaPozadina">
		<v-main>
			<router-view />
		</v-main>
	</v-app>
</template>

<script>
export default {
	name: "App",
	mounted() {},
	data: () => ({
		// no delete
	}),
};
</script>
<style scoped>
.valekovaPozadina {
	background-color: #b8c6db !important;
	background-image: linear-gradient(315deg, #b8c6db 0%, #f5f7fa 74%) !important;
}
</style>
