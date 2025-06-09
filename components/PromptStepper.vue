<script setup lang="ts">
import type { PromptForm } from "~/types/prompt-form.interface"

const emit = defineEmits(['submit'])


let stepperItems = ref(['Универсальность', 'Уход', 'Периодичность коррекции', 'Формальность', 'Тип волос', 'Форма лица', 'Сгенерировать'])
let selectVariants = ref({
  universal: [
    "🧒 До 18 лет — подросткам",
    "🧑 18–35 лет — для молодёжи",
    "🧔‍♂️ 35+ лет — для зрелого возраста",
    "👥 Все возрасты",
  ],
  hairStyling: [
    "💨 — не требует укладки",
    "🪮 — лёгкая укладка",
    "🧴 — нужна стайлинг-продукция"
  ],
  haircutFrequency: [
    "🗓️ — раз в месяц",
    "⏳ — держит форму долго",
    "🔁 — требует частого обновления"
  ],
  formalStyle: [
    "💼 — деловой стиль",
    "👔 — универсально",
    "🧢 — повседневно"
  ],
  hairType: [
    "➿ — вьющиеся",
    "〰️ — прямые",
    "🔁 — любые"
  ],
  faceShape: [
    "🔵 — круглое",
    "⬛ — квадратное",
    "🔷 — ромбовидное",
    "⭕ — универсально"
  ]
})
let currentStep = ref<number>(1)
let isLastStep = computed<boolean>(() => {
  return currentStep.value === stepperItems.value.length;
});

let promptForm = ref<PromptForm>({
  universal: "",
  hairStyling: "",
  haircutFrequency: "",
  formalStyle: "",
  hairType: "",
  faceShape: "",
})

let universal = ref<number[]>([])
watch(universal, (newValue) => {
  if (newValue.includes(3) && newValue.length > 1) {
    universal.value = [3]
    return;
  }
  let res = "";
  for (let idx of newValue) {
    res += selectVariants.value.universal[idx] + " ";
  }
  promptForm.value.universal = res;
})

let hairStyling = ref<number[]>([])
watch(hairStyling, (newValue) => {
  let res = "";
  for (let idx of newValue) {
    res += selectVariants.value.hairStyling[idx] + " ";
  }
  promptForm.value.hairStyling = res;
})

let haircutFrequency = ref<number[]>([])
watch(haircutFrequency, (newValue) => {
  let res = "";
  for (let idx of newValue) {
    res += selectVariants.value.haircutFrequency[idx] + " ";
  }
  promptForm.value.haircutFrequency = res;
})

let formalStyle = ref<number | null>(null)
watch(formalStyle, (newValue: number | null) => {
  if (newValue != null) {
    promptForm.value.formalStyle = selectVariants.value.formalStyle[newValue];
  }
})
let hairType = ref<number | null>(null)
watch(hairType, (newValue: number | null) => {
  if (newValue != null) {
    promptForm.value.hairType = selectVariants.value.hairType[newValue];
  }
})
let faceShape = ref<number | null>(null)
watch(faceShape, (newValue: number | null) => {
  if (newValue != null) {
    promptForm.value.faceShape = selectVariants.value.faceShape[newValue];
  }
})
/*
  1. Универсальность (насколько подходит разным типам внешности и возрастам)
  🧒 До 18 лет — подходит подросткам
  🧑 18–35 лет — лучший выбор для молодёжи
  🧔‍♂️ 35+ лет — для зрелого возраста
  👥 Все возрасты — универсальный вариант
  2. Уход (сложность ежедневной укладки)
  💨 — не требует укладки
  🪮 — лёгкая укладка
  🧴 — нужна стайлинг-продукция
  3. Периодичность коррекции (как часто нужно обновлять)
  🗓️ — раз в месяц
  ⏳ — держит форму долго
  🔁 — требует частого обновления
  4. Формальность (насколько уместна в деловой обстановке)
  💼 — деловой стиль
  👔 — универсально
  🧢 — повседневно
  5. Тип волос (для какой структуры волос идеальна)
  ➿ — вьющиеся
  〰️ — прямые
  🔁 — любые
  6. Форма лица (каким формам лица наиболее подходит)
  🔵 — круглое
  ⬛ — квадратное
  🔷 — ромбовидное
  ⭕ — универсально
*/

watch(promptForm, (newValue) => {
  console.log(newValue);

}, { deep: true })

function submit() {
  emit("submit", promptForm.value)
}
</script>
<template>
  <ClientOnly>

    <v-stepper v-model="currentStep" :items="stepperItems" next-text="далее" prev-text="назад" mobile>
      <template v-slot:item.1>
        <v-sheet>
          <v-responsive class="overflow-y-auto">
            <p class="text-2xl font-medium mb-4">{{ stepperItems[0] }}</p>

            <v-chip-group v-model="universal" selected-class="text-primary" multiple column>
              <v-chip v-for="tag in selectVariants.universal" :key="tag" :text="tag" density="default"></v-chip>
            </v-chip-group>
          </v-responsive>
        </v-sheet>
      </template>

      <template v-slot:item.2>
        <v-sheet>
          <v-responsive class="overflow-y-auto">
            <p class="text-2xl font-medium mb-4">{{ stepperItems[1] }}</p>

            <v-chip-group v-model="hairStyling" selected-class="text-primary" multiple column>
              <v-chip v-for="tag in selectVariants.hairStyling" :key="tag" :text="tag" density="default"></v-chip>
            </v-chip-group>
          </v-responsive>
        </v-sheet>
      </template>

      <template v-slot:item.3>
        <v-sheet>
          <v-responsive class="overflow-y-auto">
            <p class="text-2xl font-medium mb-4">{{ stepperItems[2] }}</p>

            <v-chip-group v-model="haircutFrequency" selected-class="text-primary" multiple column>
              <v-chip v-for="tag in selectVariants.haircutFrequency" :key="tag" :text="tag" density="default"></v-chip>
            </v-chip-group>
          </v-responsive>
        </v-sheet>
      </template>

      <template v-slot:item.4>
        <v-sheet>
          <v-responsive class="overflow-y-auto">
            <p class="text-2xl font-medium mb-4">{{ stepperItems[3] }}</p>

            <v-chip-group v-model="formalStyle" selected-class="text-primary" column>
              <v-chip v-for="tag in selectVariants.formalStyle" :key="tag" :text="tag" density="default"></v-chip>
            </v-chip-group>
          </v-responsive>
        </v-sheet>
      </template>

      <template v-slot:item.5>
        <v-sheet>
          <v-responsive class="overflow-y-auto">
            <p class="text-2xl font-medium mb-4">{{ stepperItems[4] }}</p>

            <v-chip-group v-model="hairType" selected-class="text-primary" column>
              <v-chip v-for="tag in selectVariants.hairType" :key="tag" :text="tag" density="default"></v-chip>
            </v-chip-group>
          </v-responsive>
        </v-sheet>
      </template>

      <template v-slot:item.6>
        <v-sheet>
          <v-responsive class="overflow-y-auto">
            <p class="text-2xl font-medium mb-4">{{ stepperItems[5] }}</p>

            <v-chip-group v-model="faceShape" selected-class="text-primary" column>
              <v-chip v-for="tag in selectVariants.faceShape" :key="tag" :text="tag" density="default"></v-chip>
            </v-chip-group>
          </v-responsive>
        </v-sheet>
      </template>

      <template v-slot:item.7>
        <v-sheet>
          <v-responsive class="overflow-y-auto py-5">
            <v-row>
              <v-col cols="12" class="d-flex justify-center">
                <v-btn @click="submit" block color="accent">отправить</v-btn>
              </v-col>
            </v-row>
          </v-responsive>
        </v-sheet>
      </template>
    </v-stepper>
  </ClientOnly>
</template>