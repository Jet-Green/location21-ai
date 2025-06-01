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

    if (!response.ok) {
      // the response isn't okay
      console.log("response status isn't 200");
      return;
    }
    const data = await response.json();
    // result.alternatives[0].message.text -- text of model response
    console.log(data);

  } catch (error) {
    console.log(error);
    aiResponse.value = JSON.stringify(error)
  }
}
</script>
<template>
  <v-container>
    <v-row class="d-flex justify-center">
      <v-col cols="12" md="10" xl="8">
        <PromptStepper></PromptStepper>

        <!-- <v-btn @click="submit">
          Отправить
        </v-btn>
        {{ aiResponse }} -->
      </v-col>
    </v-row>
  </v-container>
</template>