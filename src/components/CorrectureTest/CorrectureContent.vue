<template>
	<div class="buttons-wrap">
		<button class="btn btn--main"
			@click="symbolGenrator"
			v-if="this.generated.length === 0"
		>Начать</button>
	</div>
	<div class="flex-container">
		<span
			class="symbol"
			v-for="(count, index) in generated"
			:key="count"
			@click="symbolCheck"
			:data-index="index"
		>
			{{generated[index]}}
		</span>
	</div>
</template>

<script>
export default {
	data() {
		return {
			count: 0,
			ABC: "а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я",
		};
	},
	props: {
		symbols: Number,
		modulo: Number,
		currentTime: Number,
		generated: Array,
		checked: Array,
	},
	emits: [
		"generated-push",
		"checked-push",
		"checked-unset",
		"check-missed",
		"start-timer",
	],
	methods: {
		symbolGenrator: function () {
			let ABCArray = this.ABC.split(" ");
			for (let i = 0; i < this.symbols; i++) {
				let rand = ABCArray[Math.floor(Math.random() * ABCArray.length)];
				this.$emit("generated-push", rand);
			}
			this.$emit("start-timer");
		},
		symbolCheck: function (event) {
			if (this.currentTime > 0) {
				let element = event.target;
				let elementIndex = parseInt(element.dataset.index);

				if (this.modulo === 0) {
					this.$emit("start-timer");
				}
				if (this.checked.indexOf(elementIndex) === -1) {
					this.$emit("checked-push", elementIndex);
					element.classList.add("active");
				} else {
					this.$emit("checked-unset", [element, elementIndex]);
				}
				this.$emit("check-missed", elementIndex);
			}
		},
	},
};
</script>

<style lang="scss">
.symbol {
	display: block;
	font-size: 22px;
	padding: 4px;
	cursor: pointer;
	user-select: none;
	&:hover {
		background-color: var(--success);
	}
	&.active {
		background-color: var(--success);
		filter: brightness(0.9);
	}
}
</style>