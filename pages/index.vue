<script setup lang="ts">
</script>
<template>
  <v-container>
    <v-row class="d-flex justify-center">
      <v-col cols="12" md="10" xl="8">
        <v-card variant="outlined">
          главная
        </v-card>
        <v-btn variant="outlined">записаться</v-btn>
        <v-btn variant="outlined">
          Button
        </v-btn>
        <v-divider color="error"></v-divider>

        <v-expansion-panels>
          <v-expansion-panel title="Title"
            text="Lorem ipsum dolor sit amet consectetur adipisicing elit. Commodi, ratione debitis quis est labore voluptatibus! Eaque cupiditate minima">
          </v-expansion-panel>
        </v-expansion-panels>



        <h1>Домашка:</h1>
        <MyComponent1 />
        <MyComponent2 />
        <MyComponent3 />
        <MyComponent4 />
        <MyComponent5 />
        <MyComponent6 />
        <!-- сюда писать все 3-5 компонент написать -->
      </v-col>
    </v-row>
  </v-container>
</template>
<style></style>