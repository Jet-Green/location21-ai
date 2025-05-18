<script setup lang="ts">
const router = useRouter()

function goTo(path: string) {
  router.push(path)
}
</script>
<template>
  <v-container class="d-flex justify-center align-center">
    <v-row class="d-flex justify-center align-center">

      <v-btn @click="goTo('/user')">to user index</v-btn>
      <v-btn @click="goTo('/user/cabinet')">to user cabinet</v-btn>

      <v-col cols="12" class="d-flex justify-center" style="margin-top: 30vh;">
        <img src="~/assets/images/location21-logo.png" alt="" class="logo">
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
<style lang="scss" scoped>
.logo {
  width: 50%;
}
</style>