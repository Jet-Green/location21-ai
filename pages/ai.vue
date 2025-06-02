<script setup lang="ts">
definePageMeta({
  layout: 'ai'
})

import type { PromptForm } from "~/types/prompt-form.interface"

let aiResponse = ref<string>(`Для вас могут подойти следующие стрижки:

1. **Фейд**
* Универсальность: 8/10 (современная классика)
* Уход: 7/10 (средней сложности)
* Периодичность коррекции: 2–3 недели
* Формальность: 7/10 (подходит для многих ситуаций)
* Тип волос: любой
* Форма лица: универсальная

2. **Цезарь**
* Универсальность: 7/10 (классическая римская стрижка)
* Уход: 8/10 (минимальный уход)
* Периодичность коррекции: 3–4 недели
* Формальность: 7/10 (достаточно формальная)
* Тип волос: прямые, волнистые
* Форма лица: овальная, треугольная

3. **Сайд парт**
* Универсальность: 9/10 (классика для мужчин любого возраста)
* Уход: 6/10 (нужна укладка)
* Периодичность коррекции: 3–4 недели
* Формальность: 10/10 (идеально для бизнес-среды)
* Тип волос: прямые, волнистые
* Форма лица: практически любая
`)

let formStatus = ref<'filling' | 'submitted' | 'finished'>('finished')

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
      <v-col cols="12" md="10" xl="8" class="d-flex justify-center align-center">
        <PromptStepper @submit="submit" v-if="formStatus == 'filling'"></PromptStepper>

        <div v-else-if="formStatus == 'submitted'">
          <FallingStarsBg :color="'#555'" />
          <div class="z-[1]">
            <p class="text-2xl mb-2">
              ИИ Думает
            </p>
            <v-progress-linear indeterminate></v-progress-linear>
          </div>
        </div>
        <div v-else-if="formStatus == 'finished'" class="d-flex justify-center flex-column"
          style="font-size: 10px; overflow-y: scroll;">
          <pre v-html="aiResponse"></pre>
          <v-btn class="mt-10" @click="goToFormBeginning">понял, на главную</v-btn>
        </div>
        <!-- <v-btn @click="submit">
          Отправить
        </v-btn>
        {{ aiResponse }} -->
      </v-col>
    </v-row>
  </v-container>
</template>