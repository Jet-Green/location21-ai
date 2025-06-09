<script setup lang="ts">
definePageMeta({
  layout: 'ai'
})

import type { PromptForm } from "~/types/prompt-form.interface"

let aiResponse = ref<string>(`Для вас могут подойти следующие стрижки:

2. Цезарь
Описание: Короткая стрижка с прямой чёлкой и чётким контуром.
Фейд: Средний или высокий.
Универсальность: Высокая
Уход: Лёгкий
Коррекция: Часто
Формальность: Средне-высокая
Тип волос: Прямые, волнистые
Форма лица: Овальное, круглое

3. Crew Cut
Описание: Короткие волосы с чуть большей длиной на макушке, укладываются вбок.
Фейд: Средний или высокий
Универсальность: Очень высокая
Уход: Лёгкий
Коррекция: Средняя
Формальность: Высокая
Тип волос: Прямые
Форма лица: Овальное, сердцевидное
`)

let formStatus = ref<'filling' | 'submitted' | 'finished'>('filling')

async function submit(promptForm: PromptForm) {
  formStatus.value = 'submitted';

  let prompt = "";
  /*
  1. Универсальность (насколько подходит разным типам внешности и возрастам)
  2. Уход (сложность ежедневной укладки)
  3. Периодичность коррекции (как часто нужно обновлять)
  4. Формальность (насколько уместна в деловой обстановке)
  5. Тип волос (для какой структуры волос идеальна)
  6. Форма лица (каким формам лица наиболее подходит)
  */
  prompt += `1. Универсальность: ${promptForm.universal ?? 'нет предпочтений'}\n`
  prompt += `2. Уход: ${promptForm.hairStyling ?? 'нет предпочтений'}\n`
  prompt += `3. Периодичность коррекции: ${promptForm.haircutFrequency ?? 'нет предпочтений'}\n`
  prompt += `4. Формальность: ${promptForm.formalStyle ?? 'нет предпочтений'}\n`
  prompt += `5. Тип волос: ${promptForm.hairType ?? 'нет предпочтений'}\n`
  prompt += `6. Форма лица: ${promptForm.faceShape ?? 'нет предпочтений'}\n`

  try {
    let response = await fetch("https://functions.yandexcloud.net/d4eajvq0hfqcsdii8ge1", {
      method: "POST",
      mode: "cors",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        userInput: prompt
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
    formStatus.value = 'finished';
    aiResponse.value = data.result.alternatives[0].message.text;

  } catch (error) {
    console.log(error);
    aiResponse.value = JSON.stringify(error)
  }
}

function goToFormBeginning() {
  formStatus.value = 'filling'
}
</script>
<template>
  <v-container>
    <v-row class="d-flex justify-center">
      <v-col cols="12" md="10" xl="8" class="d-flex justify-center align-center flex-column">
        <PromptStepper @submit="submit" v-if="formStatus == 'filling'"></PromptStepper>

        <div v-else-if="formStatus == 'submitted'">
          <FallingStarsBg :color="'#555'" />
          <div class="z-[1]">
          </div>
        </div>
        <div v-else-if="formStatus == 'finished'" class="d-flex justify-center flex-column">
          <div class="ai-response-text">{{ aiResponse }}</div>
          <NuxtLink to="https://n962263.yclients.com/company/894109/personal/menu?o=" class="w-100">
            <v-btn class="mt-10 w-100" color="accent" size="x-large">записаться</v-btn>
          </NuxtLink>
          <v-btn class="mt-5 mb-0" @click="goToFormBeginning" size="small">понял, на главную</v-btn>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>
<style scoped lang="scss">
.ai-response-text {
  white-space: pre-wrap;
}
</style>