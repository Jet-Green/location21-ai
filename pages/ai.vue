<script setup lang="ts">
definePageMeta({
  layout: 'ai'
})

let prompt = ref<string>('')
let aiResponse = ref<string>('')

async function submit() {
  try {
    let response = await fetch("https://functions.yandexcloud.net/d4eajvq0hfqcsdii8ge1", {
      method: "POST",
      mode: "cors",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        userInput: prompt.value
      })
    });
    aiResponse.value = response
    if (response.status == 200) {
      console.log(response);
    }
  } catch (error) {
    console.log(error);
    aiResponse.value = error
  }
}
</script>
<template>
  <v-container>
    <v-row class="d-flex justify-center">
      <v-col cols="12" md="10" xl="8">
        <v-textarea label="Запрос" variant="outlined" v-model="prompt"></v-textarea>

        <v-btn @click="submit">
          Отправить
        </v-btn>
        {{ aiResponse }}
      </v-col>
    </v-row>
  </v-container>
</template>