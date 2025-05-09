<script setup lang="ts">
async function submit() {
  try {
    let response = await fetch("https://functions.yandexcloud.net/d4eajvq0hfqcsdii8ge1", {
      method: "POST",
      mode: "cors",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        userInput: "Привет"
      })
    });
    if (response.status == 200) {
      console.log(response);
    }
  } catch (error) {
    console.log(error);
  }
}
</script>
<template>
  <v-container>
    <v-row class="d-flex justify-center">
      <v-col cols="12" md="10" xl="8">
        <v-btn @click="submit">
          сделать запрос в нейронку
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>