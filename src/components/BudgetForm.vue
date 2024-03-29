<script setup>
import { useVuelidate } from '@vuelidate/core'
import { helpers, required } from '@vuelidate/validators'
import { computed, ref } from 'vue'

const props = defineProps({
	budget: {
		type: Number,
		required: true
	}
})
const emits = defineEmits(['setBudget', 'setIsValid'])

const budget = ref(props.budget)
const isValid = ref(null)

const mustBePositiveNumber = value => value > 0
const mustNotBeZero = value => value !== 0

const rules = computed(() => ({
	budget: {
		required,
		mustNotBeZero: helpers.withMessage('Must not be zero', mustNotBeZero),
		mustBePositiveNumber: helpers.withMessage(
			'Must be positive number',
			mustBePositiveNumber
		)
	}
}))
const v$ = useVuelidate(rules, { budget })

const handleSubmit = async () => {
	const isCorrect = await v$.value.$validate()
	if (!isCorrect) {
		return
	}
	emits('setBudget', budget)
	isValid.value = true
	emits('setIsValid', isValid)
}
</script>
<template>
	<form class="form" @submit.prevent="handleSubmit()">
		<div class="field">
			<label class="label" for="budget">Add Budget</label>
			<input
				id="budget"
				v-model="budget"
				name="budget"
				class="input"
				type="number"
				placeholder="Add your budget"
			/>
			<span v-if="v$.budget.$error" class="error-message">{{
				v$.budget.$errors[0].$message
			}}</span>
		</div>
		<input class="submit" type="submit" value="Add" />
	</form>
</template>
<style scoped>
::placeholder {
	color: rgb(128, 129, 129);
}
.form {
	width: 95%;
	margin: auto;
	padding: 5rem 0;
	text-align: center;
}
.field {
	margin-bottom: 2rem;
}
.label {
	display: block;
	font-size: 2.8rem;
	margin-bottom: 2rem;
	text-align: center;
}
.input {
	width: 100%;
	background-color: rgb(212, 212, 212);
	color: rgb(65, 63, 63);
	border-radius: 1.2rem;
	height: 45px;
	border: none;
	font-size: 2rem;
	font-weight: 400;
	text-align: center;
}
.submit {
	background-color: rgba(105, 35, 163, 0.51);
	border: none;
	height: 40px;
	padding: 0 6rem;
	color: white;
	font-size: 1.9rem;
	text-transform: uppercase;
	font-weight: 900;
	border-radius: 2rem;
	transition: background 400ms ease, color 400ms ease;
}
.error-message {
	padding-top: 0.6em;
	display: block;
	font-size: 1.2rem;
	color: red;
}
form > input:hover {
	cursor: pointer;
	background-color: rgba(173, 116, 218, 0.51);
	color: rgb(139, 137, 137);
}
div > input:focus {
	outline: none;
	border: none;
}
</style>
