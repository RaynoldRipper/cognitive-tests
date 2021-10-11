<template>
	<div class="info">
		<p
			class="info__text"
			v-if="storageKeyType('correctureTest') === 'undefined'"
		>
			Буквы которые нужно выделять:
			<span
				v-for="symbol in accessSymbols"
				:key="symbol"
			>{{ symbol }}, </span>
		</p>
		<div
			class=""
			v-else-if="storageKeyType('correctureTest') !== 'undefined'"
		>
			<p>Ваши результаты:</p>
			<p>Ошибок: {{errosrs.length}}.
				Правильных выделений:{{success.length}}.
				Всего обработано символов: {{maxArrayValue(checked)}}.
				Пропущено символов: {{missed.length}}
			</p>
		</div>
	</div>
</template>

<script>
export default {
	props: {
		accessSymbols: Array,
		checked: Array,
		intervalChecked: Array,
		success: Array,
		errosrs: Array,
		missed: Array,
		currentTime: Number,
	},
	methods: {
		storageKeyType: function (key) {
			return typeof localStorage[key];
		},
		maxArrayValue: function (array) {
			let maxIndex = 0;
			for (let i = 0; i < array.length; i++) {
				if (maxIndex < array[i]) maxIndex = array[i];
			}
			return maxIndex;
		},
	},
};
</script>

<style lang="scss">
.info {
	padding: 10px 15px;
	margin-bottom: 15px;
	margin-top: 15px;
	&__text {
		font-weight: 500;
		font-size: 18px;
	}
}
</style>