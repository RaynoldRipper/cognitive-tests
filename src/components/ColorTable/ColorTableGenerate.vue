<template>
	<div class="flex-container">

	</div>
</template>

<script>
export default {
	data() {
		return {
			red: 25,
			black: 24,
			redSuccess: [],
			blackSuccess: [],
			alphabet:
				"а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я".split(
					" "
				),
		};
	},
	props: {
		// red: Number,
		// black: Number,
	},
	methods: {
		tableGenerator(count) {
			for (let i = 0; i < count; i++) {
				let rand =
					this.alphabet[Math.floor(Math.random() * this.alphabet.length)];
				this.$emit("generated-push", rand);
			}
		},
		dataGenerator(count, array) {
			for (let i = 0; i < count; i++) {
				let rand =
					this.alphabet[Math.floor(Math.random() * this.alphabet.length)];
				array.push(rand);
			}
		},
	},
	mounted() {
		this.dataGenerator(this.red, this.redSuccess);
		this.dataGenerator(this.black, this.blackSuccess);
	},
};
</script>

<style>
</style>