<template>
	<CorrecturePassed
		v-if="storageKeyType('correctureTest') !== 'undefined'"
		:showInfo="showInfo"
		@show-results="showResults"
		@hide-results="showResults"
		@pull-storage-data="pullStorageData"
		@clear-test="clearTest"
	/>
	<CorrectureInfo
		:accessSymbols="accessSymbols"
		:success="success"
		:errosrs="errosrs"
		:checked="checked"
		:intervalChecked="intervalChecked"
		:missed="missed"
		:currentTime="currentTime"
		v-if="(storageKeyType('correctureTest') === 'undefined' || showInfo === 1)"
	/>
	<CorrectureContent
		:symbols="1925"
		:generated="generated"
		:checked="checked"
		:currentTime="currentTime"
		:modulo="modulo"
		@generated-push="generatedPush"
		@checked-push="checkedPush"
		@checked-unset="checkedUnset"
		@start-timer="startTimer"
		@check-missed="pushMissed"
		v-if="storageKeyType('correctureTest') === 'undefined'"
	/>
	<CorrectureTimer
		:currentTime="currentTime"
		:timeLeft="timeLeft"
		:timer="timer"
		v-if="storageKeyType('correctureTest') === 'undefined'"
	/>
</template>

<script>
import CorrectureContent from "./CorrectureContent";
import CorrectureTimer from "./CorrectureTimer";
import CorrectureInfo from "./CorrectureInfo";
import CorrecturePassed from "./CorrecturePassed";

export default {
	data() {
		return {
			showInfo: 0,

			currentTime: 10,
			timeLeft: 0,
			timer: null,
			timerStatus: 0,
			modulo: null,

			accessSymbols: ["м", "а", "в", "д"],
			generated: [],
			checked: [],
			intervalChecked: [],
			success: [],
			errosrs: [],
			missed: [],
			results: [],
		};
	},
	emits: [
		"correcture-unpassed"
	],
	components: {
		CorrectureContent,
		CorrectureTimer,
		CorrectureInfo,
		CorrecturePassed,
	},
	methods: {
		showResults: function () {
			if (this.showInfo === 0) this.showInfo = 1;
			else if (this.showInfo === 1) this.showInfo = 0;
		},
		startTimer() {
			if (this.timerStatus == 0 && this.currentTime !== 0) {
				this.timer = setInterval(() => {
					this.currentTime--;
					this.timeLeft++;
					this.modulo = this.timeLeft % 60;
				}, 1000);
				this.timerStatus = 1;
			}
		},
		stopTimer() {
			clearTimeout(this.timer);
			this.timerStatus = 0;
		},
		generatedPush: function (data) {
			this.generated.push(data);
		},
		checkedPush: function (data) {
			this.checked.push(data);
			if (this.accessSymbols.indexOf(this.generated[data]) != -1) {
				this.success.push(data);
			} else {
				this.errosrs.push(data);
			}
		},
		checkedUnset: function (data) {
			let element = data[0];
			let elementIndex = data[1];

			let checkedIndex = this.checked.indexOf(elementIndex);
			let errorIndex = this.errosrs.indexOf(elementIndex);
			let successIndex = this.success.indexOf(elementIndex);

			if (checkedIndex !== -1) {
				this.checked.splice(checkedIndex, 1);
				element.classList.remove("active");
			}
			if (errorIndex != -1) {
				this.errosrs.splice(errorIndex, 1);
			}
			if (successIndex != -1) {
				this.success.splice(successIndex, 1);
			}
		},
		pushMissed: function () {
			// Подсчет пропущенных не правильных символов
			this.missed = [];
			let maxIndex = 0;
			for (let i = 0; i < this.checked.length; i++) {
				if (maxIndex < this.checked[i]) maxIndex = this.checked[i];
			}
			for (let i = 0; i < maxIndex; i++) {
				if (
					this.accessSymbols.indexOf(this.generated[i]) !== -1 &&
					this.missed.indexOf(i) === -1 &&
					this.checked.indexOf(i) === -1
				) {
					this.missed.push(i);
				}
			}
		},
		getResult: function () {
			let leftMinutes = this.intervalChecked.length;
			let prevIntervalIndex = 0;
			for (let i = 0; i < leftMinutes; i++) {
				let neededSymbols = 0,
					successSymbols = 0,
					errorSymbols = 0;
				let intervalIndex = this.intervalChecked[i];
				for (let e = prevIntervalIndex; e < intervalIndex; e++) {
					if (this.accessSymbols.indexOf(this.generated[e]) !== -1)
						neededSymbols++;
					if (this.success.indexOf(e) !== -1) successSymbols++;
					if (this.errosrs.indexOf(e) !== -1) errorSymbols++;
				}
				let accuracy = Math.round(
					((successSymbols - errorSymbols) / neededSymbols) * 100
				);
				let stability = Math.round(
					((intervalIndex - prevIntervalIndex) * accuracy) / 100
				);
				let result = {
					viewSymbols: intervalIndex - prevIntervalIndex,
					neededSymbols,
					successSymbols,
					errorSymbols,
					accuracy,
					stability,
				};
				prevIntervalIndex = intervalIndex;
				this.results.push(result);
			}
		},
		storageKeyType: function (key) {
			return typeof localStorage[key];
		},
		saveResults: function () {
			let json = {
				generated: this.generated,
				checked: this.checked,
				success: this.success,
				errosrs: this.errosrs,
				interval: this.intervalChecked,
				missed: this.missed,
				results: this.results,
			};
			localStorage.correctureTest = JSON.stringify(json);
		},
		pullStorageData: function (data) {
			this.generated = data[0];
			this.checked = data[1];
			this.success = data[2];
			this.errosrs = data[3];
			this.missed = data[4];
			this.intervalChecked = data[5];
			this.results = data[6];
		},
		clearTest: function () {
			this.generated = [];
			this.checked = [];
			this.success = [];
			this.errosrs = [];
			this.missed = [];
			this.intervalChecked = [];
			this.results = [];

			localStorage.removeItem("correctureTest");

			this.currentTime = 60;
			this.timeLeft = 0;
			this.showInfo = 1;

			this.$emit("correcture-unpadssed");
		},
	},
	unmounted() {
		this.stopTimer();
	},
	watch: {
		currentTime(time) {
			if (this.modulo === 0) {
				this.stopTimer();
				setTimeout(() => {
					if (
						this.modulo === 0 &&
						this.timeLeft / 60 > this.intervalChecked.length
					) {
						let maxIndex = 0;
						for (let i = 0; i < this.checked.length; i++) {
							if (maxIndex < this.checked[i]) maxIndex = this.checked[i];
						}
						this.intervalChecked.push(maxIndex);
					}
					this.startTimer();
				}, 5000);

				let maxIndex = 0;
				for (let i = 0; i < this.checked.length; i++) {
					if (maxIndex < this.checked[i]) maxIndex = this.checked[i];
				}
				this.intervalChecked.push(maxIndex);
			}
			// Для тестов
			if (time === 0) {
				this.stopTimer();
				this.getResult();
				this.saveResults();
			}
		},
		// intervalChecked(array) {
		// 	if (array.length !== 0) {
		// 		console.log(array);
		// 	}
		// },
	},
};
</script>

<style lang="scss">
</style>