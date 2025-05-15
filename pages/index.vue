<script setup lang="ts">
</script>
<template>
  <v-container class="d-flex justify-center align-center">
    <v-row class="d-flex justify-center align-center">
      <v-col cols="12" class="d-flex justify-center" style="margin-top: 30vh;">
        <div class="text-white">
          логотип
        </div>
      </v-col>
      <v-col cols="12" class="d-flex justify-center">
        <GradientButton :bg-color="'#000'" :borderRadius="8" :border-width="2" :blur="12"
          :colors="['#D9D9D9', '#FF7878', '#FC5B5B', '#FFFFFF']" style="font-weight: 600;">
          Записаться
        </GradientButton>
      </v-col>
    </v-row>
  </v-container>
</template>
<style lang="scss" scoped></style>