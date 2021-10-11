<template>
	<div class="home">
		<img
			alt="Vue logo"
			src="../assets/logo.png"
		>
		<div class="container">
			<CorrectureComponent
				@correcture-passed="this.correctureTest = true"
				@correcture-unpassed="this.correctureTest = false"
			/>
		</div>
	</div>
</template>

<script>
// @ is an alias to /src
import CorrectureComponent from "@/components/CorrectureTest/CorrectureComponent";

export default {
	name: "Home",
	components: {
		CorrectureComponent,
	},
	data() {
		return {
			correctureTest: false,
		};
	},
	methods: {
		testChecker() {},
	},
	mounted() {

	},
	watch: {
		correctureTest() {
			console.log(this.correctureTest)
		},
	},
};
</script>
