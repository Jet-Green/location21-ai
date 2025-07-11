<script setup lang="ts">
definePageMeta({
  layout: 'ai'
})

import { toast } from "vue3-toastify"

import type { PromptForm } from "~/types/prompt-form.interface"

let aiResponse = ref<{ id: string, description: string, images: string[] }[]>([
  {
    "id": "12",
    "description": "Buzz Cut(Ежик)\nОписание: Короткая машинная стрижка под одну насадку по всей голове.\nФейд: По желанию(чаще высокий).\nУниверсальность: Максимальная\nУход: Минимальный\nКоррекция: Часто(2–3 недели)\nФормальность: Высокая\nТип волос: Любой\nФорма лица: Овальное, квадратное\n",
    "images": [
      "https://storage.yandexcloud.net/location21/result-images/IMG_9528.JPG"
    ]
  },
  {
    "id": "13",
    "description": "Цезарь\nОписание: Короткая стрижка с прямой чёлкой и чётким контуром.\nФейд: Средний или высокий.\nУниверсальность: Высокая\nУход: Лёгкий\nКоррекция: Часто\nФормальность: Средне - высокая\nТип волос: Прямые, волнистые\nФорма лица: Овальное, круглое\n",
    "images": [
      "https://storage.yandexcloud.net/location21/result-images/IMG_9527.WEBP"
    ]
  }
])

let formStatus = ref<'filling' | 'submitted' | 'finished'>('filling')
let promptForm = ref<PromptForm>()
let copyResultDialog = ref(false)

let additionalInfo = computed(() => {
  if (promptForm.value?.additional) {
    return ", доп: " + promptForm.value?.additional;
  }
  return ""
})

async function submit(submittedForm: PromptForm) {
  formStatus.value = 'submitted';
  promptForm.value = submittedForm;
  let prompt = "";
  /*
  1. Универсальность (насколько подходит разным типам внешности и возрастам)
  2. Уход (сложность ежедневной укладки)
  3. Периодичность коррекции (как часто нужно обновлять)
  4. Формальность (насколько уместна в деловой обстановке)
  5. Тип волос (для какой структуры волос идеальна)
  6. Форма лица (каким формам лица наиболее подходит)
  */
  prompt += `1. Универсальность: ${submittedForm.universal ?? 'нет предпочтений'}\n`
  prompt += `2. Уход: ${submittedForm.hairStyling ?? 'нет предпочтений'}\n`
  prompt += `3. Периодичность коррекции: ${submittedForm.haircutFrequency ?? 'нет предпочтений'}\n`
  prompt += `4. Формальность: ${submittedForm.formalStyle ?? 'нет предпочтений'}\n`
  prompt += `5. Тип волос: ${submittedForm.hairType ?? 'нет предпочтений'}\n`
  prompt += `6. Форма лица: ${submittedForm.faceShape ?? 'нет предпочтений'}\n`

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
    aiResponse.value = data;
  } catch (error) {
    console.log(error);
  }
}

function goToFormBeginning() {
  formStatus.value = 'filling'
}

async function copy(cropDescription: string) {
  let copiedCropName = cropDescription.split('\n')[0] // cropDescription is "Buzz Cut(Ежик)\nОписание: Короткая маш..."
  let textToCopy = `Название: ${copiedCropName} \nВыбор:\n`;
  /*
  1. Универсальность (насколько подходит разным типам внешности и возрастам)
  2. Уход (сложность ежедневной укладки)
  3. Периодичность коррекции (как часто нужно обновлять)
  4. Формальность (насколько уместна в деловой обстановке)
  5. Тип волос (для какой структуры волос идеальна)
  6. Форма лица (каким формам лица наиболее подходит)
  */


  // short copy, 150 letters is limit
  textToCopy += `${promptForm.value?.universal ? promptForm.value?.universal + ', ' : ""}`
  textToCopy += `${promptForm.value?.hairStyling ? promptForm.value?.hairStyling + ', ' : ""}`
  textToCopy += `${promptForm.value?.haircutFrequency ? promptForm.value?.haircutFrequency + ', ' : ""}`
  textToCopy += `${promptForm.value?.formalStyle ? promptForm.value?.formalStyle + ', ' : ""}`
  textToCopy += `${promptForm.value?.hairType ? promptForm.value?.hairType + ', ' : ""}`
  textToCopy += `${promptForm.value?.faceShape ? promptForm.value?.faceShape : ""}`

  textToCopy += additionalInfo.value;

  if (navigator.clipboard && navigator.clipboard.writeText) {
    await navigator.clipboard.writeText(textToCopy)
    copyResultDialog.value = true;
  } else {
    const textarea = document.createElement("textarea");
    textarea.value = textToCopy;
    document.body.appendChild(textarea);
    textarea.select();
    try {
      document.execCommand("copy");
      copyResultDialog.value = true;
    } catch { }
    document.body.removeChild(textarea);
  }

  await new Promise(r => setTimeout(r, 5000))
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
          <v-row>
            <v-col v-for="(crop, index) of aiResponse" :key="index" cols="12">
              <v-card class="w-100">
                <v-col cols="12">
                  <div class="ai-response-text" v-text="crop.description"></div>
                </v-col>

                <v-col cols="12" v-for="(img, index) of crop.images">
                  <v-img :src="img" />
                </v-col>

                <v-col cols="12" class="d-flex justify-end">
                  <v-btn density="comfortable" color="accent" size="x-large" variant="tonal"
                    @click="copy(crop.description)">
                    скопировать
                  </v-btn>
                </v-col>
              </v-card>
            </v-col>
          </v-row>

          <NuxtLink to="https://n962263.yclients.com/company/894109/personal/menu?o=" class="w-100">
            <v-btn class="mt-10 w-100" color="accent" size="x-large">записаться</v-btn>
          </NuxtLink>
          <v-btn class="mt-5 mb-0" @click="goToFormBeginning" size="small">понял, на главную</v-btn>
        </div>
      </v-col>
    </v-row>

    <v-dialog v-model="copyResultDialog" width="auto">
      <v-card prepend-icon="mdi-check-circle-outline" title="Скопировано!">
        <v-card-text>
          <p class="text-sm text-center mt-4">Вставьте текст в <b>комментарий</b> к записи</p>
          <div class="d-flex justify-center">
            <v-icon>mdi-arrow-down-thin</v-icon>
          </div>
          <v-img src="../assets/images/copy-paste-example.png" min-width="400"></v-img>
          <div class="d-flex justify-center">
            <v-icon>mdi-arrow-down-thin</v-icon>
          </div>
          <p class="text-sm text-center mt-2">Мы <b>учтем</b> при стрижке!</p>
        </v-card-text>
        <template v-slot:actions>
          <NuxtLink to="https://n962263.yclients.com/company/894109/personal/menu?o=" class="w-100">
            <v-btn class="w-100" color="accent" size="x-large" text="понял, записаться" variant="tonal"
              @click="copyResultDialog = false"></v-btn>
          </NuxtLink>
        </template>
      </v-card>
    </v-dialog>
  </v-container>
</template>
<style scoped lang="scss">
.ai-response-text {
  white-space: pre-wrap;
}
</style>