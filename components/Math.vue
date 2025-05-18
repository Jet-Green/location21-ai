<script setup lang="ts">
defineProps<{
  message: String,
  buttonText: String
}>()

let number1 = ref<number>(13);
let number2 = ref<number>(0);

let result = computed(() => {
  return Number(number1.value) + Number(number2.value);
});
</script>
<template>
  <p class="text-6xl">{{ message }}</p>

  калькулятор
  <v-text-field v-model="number1" type="number"></v-text-field>
  <v-text-field v-model="number2" type="number"></v-text-field>


  number1: {{ number1 }}<br />
  number2: {{ number2 }}<br />
  Результат: {{ result }}<br />
</template>