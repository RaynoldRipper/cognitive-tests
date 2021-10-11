<template>
	<div class="timer-wrapper">
		<div
			class="timer"
			v-if="currentTime > 0"
		>{{timerStyle()}}</div>
		<div
			class="timer-out"
			v-else-if="currentTime === 0"
		>Время вышло!</div>
	</div>
</template>

<script>
export default {
	data() {
		return {};
	},
	props: {
		currentTime: Number,
		timeLeft: Number,
		timer: Number,
	},
	methods: {
		timerStyle() {
			let minutes =
				Math.floor(this.currentTime / 60) > 9
					? Math.floor(this.currentTime / 60)
					: "0" + Math.floor(this.currentTime / 60);
			let seconds =
				this.currentTime - minutes * 60 > 9
					? this.currentTime - minutes * 60
					: "0" + (this.currentTime - minutes * 60).toString();
			return `${minutes}:${seconds}`;
		},
	},
};
</script>

<style lang="scss">
.timer {
	&-wrapper {
		position: fixed;
		top: 150px;
		left: 50%;
		transform: translate(-50%, -50%);
		padding: 50px;
		z-index: 1;
		background-color: #fff;
		box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.3);
		border-radius: 8px;
	}
}
</style>