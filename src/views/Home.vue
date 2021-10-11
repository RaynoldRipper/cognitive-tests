<template>
	<div class="home">
		<img
			alt="Vue logo"
			src="../assets/logo.png"
		>
		<div class="container">
			<CorrectureComponent />
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
};
</script>
