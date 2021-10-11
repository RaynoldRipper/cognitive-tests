<template>
	<div class="home">
		<img
			alt="Vue logo"
			src="../assets/logo.png"
		>
		<div class="container">
			<CorrectureComponent
				@correcture-passed="testChecker"
				@correcture-unpassed="this.correctureTest = false"
				v-if="this.correctureTest === false"
			/>
			<ColorTableComponent v-else-if="this.correctureTest === true" />
		</div>
	</div>
</template>

<script>
// @ is an alias to /src
import CorrectureComponent from "@/components/CorrectureTest/CorrectureComponent";
import ColorTableComponent from "@/components/ColorTable/ColorTableComponent";

export default {
	name: "Home",
	components: {
		CorrectureComponent,
		ColorTableComponent,
	},
	data() {
		return {
			correctureTest: false,
		};
	},
	methods: {
		testChecker() {
			this.correctureTest = true;
		},
	},
	mounted() {},
	watch: {
		correctureTest() {
			console.log(this.correctureTest);
		},
	},
};
</script>
