<template>
	<div class="info">
		<div class="content">Тест пройден</div>
		<div class="buttons-wrapper">
			<button
				class="btn btn--main"
				@click="showResults"
				v-if="showInfo === 0"
			>
				Посмотреть результаты
			</button>
			<button
				class="btn btn--main"
				@click="hideResults"
				v-if="showInfo === 1"
			>
				Скрыть результаты
			</button>
			<button class="btn btn--main">Отправить результаты</button>
			<button
				class="btn btn--main"
				v-if="!passwordInput"
				@click="passwordInput = true"
			>Повторить тестированние</button>
		</div>
		<div
			class="modal"
			v-if="passwordInput"
		>
			<div class="modal-top">
				<button
					class="btn btn--close"
					@click="passwordInput = false"
				>
					<i class="i i-close"></i>
				</button>
			</div>
			<div class="modal-head">
				<h3>Введите пароль чтобы пройти тест заново</h3>
			</div>
			<div class="modal-body">
				<div class="input-field">
					<input
						class="input-main"
						type="text"
						placeholder="Введите пароль"
						v-model="passwordInputValue"
						@keyup.enter="passwordHandler"
					>
					<div
						class="notific notific--error"
						v-if="!passwordValid"
					>Неверный пароль</div>
				</div>
			</div>
			<div class="modal-footer">
				<button
					class="btn btn--main"
					@click="passwordHandler"
				>Подтвердить</button>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			passwordInput: false,
			passwordInputValue: "",
			password: "qwe123",
			passwordValid: true,
		};
	},
	props: {
		showInfo: Number,
	},
	emits: ["show-results", "hide-results", "pull-storage-data"],
	methods: {
		showResults: function () {
			this.$emit("show-results");
		},
		hideResults: function () {
			this.$emit("hide-results");
		},
		pullDatatFromStorage: function () {
			this.$emit("pull-storage-data", [
				JSON.parse(localStorage.generated),
				JSON.parse(localStorage.checked),
				JSON.parse(localStorage.success),
				JSON.parse(localStorage.errosrs),
				JSON.parse(localStorage.missed),
				JSON.parse(localStorage.interval),
				JSON.parse(localStorage.results),
			]);
		},
		passwordHandler() {
			if (this.password === this.passwordInputValue) {
				this.passwordValid = true;
				this.$emit("clear-test");
			} else {
				this.passwordValid = false;
				console.log("error");
			}
		},
	},
	computed: {},
	mounted() {
		this.pullDatatFromStorage();
	},
};
</script>

<style lang="scss">
.content {
	padding-top: 15px;
	padding-bottom: 15px;
}
.input-main {
	padding: 5px 10px;
}
.buttons-wrapper{
	margin-top: 15px;
	margin-bottom: 15px;
}
</style>